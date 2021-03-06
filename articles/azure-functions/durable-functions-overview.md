---
title: Visão Geral das Funções Duráveis – Azure
description: Introdução à extensão de Funções Duráveis do Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: 25f7cf6de4f217219e510ae00ce21762e755d2e8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627399"
---
# <a name="durable-functions-overview"></a>Visão geral das Funções Duráveis

As *Funções Duráveis* são uma extensão do [Azure Functions](functions-overview.md) e do [Azure WebJobs](../app-service/web-sites-create-web-jobs.md) que permitem que você escreva funções com estado em um ambiente sem servidor. A extensão gerencia estado, pontos de verificação e reinicializações para você.

A extensão permite definir fluxos de trabalho com estado em um novo tipo de função chamado [*função de orquestrador*](durable-functions-types-features-overview.md#orchestrator-functions). Veja algumas das vantagens das funções orquestradoras:

* Elas definem fluxos de trabalho no código. Não são necessários designers ou esquemas JSON.
* Elas podem chamar outras funções de forma síncrona e assíncrona. A saída das funções chamadas pode ser salva em variáveis locais.
* Elas fazem automaticamente o ponto de verificação pontual do progresso sempre que a função esperar. O estado local nunca será perdido se o processo for reciclado ou se a VM for reiniciada.

> [!NOTE]
> As Funções Duráveis são uma extensão avançada do Azure Functions que não é apropriada para todos os aplicativos. O restante deste artigo pressupõe que você tenha uma forte familiaridade com os conceitos do [Azure Functions](functions-overview.md) e com os desafios envolvidos no desenvolvimento de aplicativos serverless.

O caso de uso principal das Funções Duráveis é simplificar problemas complexos de coordenação com estado em aplicativos sem servidor. As seções a seguir descrevem alguns padrões de aplicativo típicos que podem se beneficiar das Funções Duráveis.

## <a name="pattern-1-function-chaining"></a>Padrão 1: encadeamento de funções

*Encadeamento de funções* é o padrão de executar uma sequência de funções em uma ordem específica. Frequentemente, a saída de uma função precisa ser aplicada à entrada de outra função.

![Diagrama de encadeamento de funções](media/durable-functions-overview/function-chaining.png)

As Funções Duráveis permitem implementar esse padrão de forma concisa no código.

#### <a name="c-script"></a>Script C#

```cs
public static async Task<object> Run(DurableOrchestrationContext ctx)
{
    try
    {
        var x = await ctx.CallActivityAsync<object>("F1");
        var y = await ctx.CallActivityAsync<object>("F2", x);
        var z = await ctx.CallActivityAsync<object>("F3", y);
        return  await ctx.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // error handling/compensation goes here
    }
}
```
> [!NOTE]
> Há diferenças sutis durante a gravação de uma função durável pré-compilada no C# vs o exemplo de script C# mostrado anteriormente. Uma função C# pré-compilada exigiria que parâmetros duráveis fossem decorados com os respectivos atributos. Um exemplo é o `[OrchestrationTrigger]` atributo o `DurableOrchestrationContext` parâmetro. Se os parâmetros não são decorados adequadamente, o tempo de execução não será capaz de injetar as variáveis para a função e resultará em erro. Visite [exemplo](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples) para obter mais exemplos.

#### <a name="javascript-functions-v2-only"></a>JavaScript (apenas Functions v2)

```js
const df = require("durable-functions");

module.exports = df(function*(ctx) {
    const x = yield ctx.df.callActivityAsync("F1");
    const y = yield ctx.df.callActivityAsync("F2", x);
    const z = yield ctx.df.callActivityAsync("F3", y);
    return yield ctx.df.callActivityAsync("F4", z);
});
```

Os valores de "F1", "F2", "F3" e "F4" são os nomes de outras funções no aplicativo de funções. O fluxo de controle é implementado usando construtos de codificação imperativa normal. Ou seja, o código é executado de cima para baixo e pode envolver a semântica do fluxo de controle da linguagem existente, como condicionais e loops.  A lógica de tratamento de erro pode ser incluída em blocos try/catch/finally.

O parâmetro `ctx` ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) fornece métodos para invocar outras funções por nome, passar parâmetros e retornar a saída da função. Cada vez que o código chama `await`, a estrutura das Funções Duráveis faz a *verificação pontual* do progresso da instância da função atual. Se o processo ou a VM for reciclada no meio da execução, a instância da função continuará da chamada `await` anterior. Mais informações sobre esse comportamento de reinicialização são fornecidas mais adiante.

## <a name="pattern-2-fan-outfan-in"></a>Padrão 2: fan-out/fan-in

*Fan-out/fan-in* é o padrão de executar várias funções em paralelo e, em seguida, esperar que todas sejam concluídas.  Frequentemente, algum trabalho de agregação é feito nos resultados retornados pelas funções.

![Diagrama de fan-out/fan-in](media/durable-functions-overview/fan-out-fan-in.png)

Com funções normais, o fan-out pode ser feito fazendo com que a função envie várias mensagens para uma fila. No entanto, o processo de fazer fan-in é muito mais desafiador. Você precisaria escrever um código para controlar quando as funções disparadas pela fila terminam e armazenar as saídas da função. A extensão de Funções Duráveis lida com esse padrão com um código relativamente simples.

#### <a name="c-script"></a>Script C#

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    var parallelTasks = new List<Task<int>>();
 
    // get a list of N work items to process in parallel
    object[] workBatch = await ctx.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = ctx.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }
 
    await Task.WhenAll(parallelTasks);
 
    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await ctx.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (apenas Functions v2)

```js
const df = require("durable-functions");

module.exports = df(function*(ctx) {
    const parallelTasks = [];

    // get a list of N work items to process in parallel
    const workBatch = yield ctx.df.callActivityAsync("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(ctx.df.callActivityAsync("F2", workBatch[i]));
    }

    yield ctx.df.task.all(parallelTasks);

    // aggregate all N outputs and send result to F3
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield ctx.df.callActivityAsync("F3", sum);
});
```

O trabalho de fan-out é distribuído para várias instâncias da função `F2` e o trabalho é controlado usando uma lista dinâmica de tarefas. A API `Task.WhenAll` .NET é chamada para aguardar até que todas as funções chamadas sejam concluídas. Em seguida, as `F2`saídas da função são agregadas de uma função lista de tarefas dinâmicas e passadas para a função `F3`.

A verificação pontual automática que ocorre na chamada `await` em `Task.WhenAll` garante que qualquer falha ou reinicialização no meio do processo não exija uma reinicialização de tarefas já concluídas.

## <a name="pattern-3-async-http-apis"></a>Padrão 3: APIs de HTTP assíncrono

O terceiro padrão trata do problema de coordenar o estado de operações de longa execução com clientes externos. Uma maneira comum de implementar esse padrão é fazer com que a ação de longa execução seja disparada por uma chamada HTTP e, em seguida, redirecionar o cliente para um ponto de extremidade de status que ele possa sondar para saber quando a operação for concluída.

![Diagrama de API HTTP](media/durable-functions-overview/async-http-api.png)

As Funções Duráveis fornecem APIs internas que simplificam o código que você escreve para interagir com execuções de função de longa execução. Os [exemplos](durable-functions-install.md) mostram um comando REST simples que pode ser usado para iniciar novas instâncias de função de orquestrador. Depois que uma instância é iniciada, a extensão expõe as APIs HTTP de webhook que consultam o status da função de orquestrador. O exemplo a seguir mostra os comandos REST para iniciar um orquestrador e para consultar seu status. Para maior clareza, alguns detalhes foram omitidos do exemplo.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Como o estado é gerenciado pelo tempo de execução das Funções Duráveis, você não precisa implementar seu próprio mecanismo de controle de status.

Embora a extensão de Funções Duráveis tenha webhooks internos para gerenciar orquestrações de longa execução, você mesmo pode implementar esse padrão usando seus próprios gatilhos de função (como HTTP, fila ou Hub de Eventos) e a associação `orchestrationClient`. Por exemplo, você pode usar uma mensagem da fila para disparar o encerramento.  Ou você pode usar um gatilho HTTP protegido por uma política de autenticação do Azure Active Directory, em vez de webhooks internos que usam uma chave gerada para autenticação. 

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    TraceWriter log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);
    
    log.Info($"Started orchestration with ID = '{instanceId}'.");
    
    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

O parâmetro de `starter` [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) é um valor da associação de saída `orchestrationClient`, que faz parte da extensão de Funções Duráveis. Ele fornece métodos para iniciar, enviar eventos, encerrar e consultar instâncias novas ou existentes da função orquestradora. No exemplo anterior, uma função disparada por HTTP assume um valor de `functionName` da URL de entrada e passa esse valor para [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Em seguida, essa associação de API retorna uma resposta que contém um cabeçalho `Location` e informações adicionais sobre a instância, que posteriormente podem ser usadas para pesquisar o status da instância iniciada ou encerrá-la.

## <a name="pattern-4-monitoring"></a>Padrão 4: Monitoramento

O padrão de monitoramento refere-se a um processo *recorrente* flexível em um fluxo de trabalho - por exemplo, sondagem até que determinadas condições sejam atendidas. Um disparador com timer regular pode endereçar um cenário simples, como um trabalho de limpeza periódico, mas seu intervalo é estático e o gerenciamento do tempo de vida da instância torna-se complexo. As Funções Duráveis permitem intervalos de recorrência flexíveis, gerenciamento de tempo de vida da tarefa e a capacidade de criar vários processos de monitoramento a partir de uma única orquestração.

Um exemplo seria reverter o cenário anterior da API HTTP assíncrona. Em vez de expor um ponto de extremidade para um cliente externo monitorar uma operação de execução longa, o monitor de execução longa consome um ponto de extremidade externo, aguardando alguma alteração de estado.

![Diagrama de monitoramento](media/durable-functions-overview/monitor.png)

Usando Funções Duráveis, vários monitores que observam pontos de extremidade arbitrários podem ser criados em poucas linhas de código. Os monitores podem encerrar a execução quando alguma condição for atendida ou serem terminados pelo [DurableOrchestrationClient](durable-functions-instance-management.md) e o intervalo de espera pode ser alterado com base em alguma condição (por exemplo, retirada exponencial.) O código a seguir implementa um monitor básico.

#### <a name="c-script"></a>Script C#

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    int jobId = ctx.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();
    
    while (ctx.CurrentUtcDateTime < expiryTime) 
    {
        var jobStatus = await ctx.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform action when condition met
            await ctx.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        var nextCheck = ctx.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await ctx.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform further work here, or let the orchestration end
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (apenas Functions v2)

```js
const df = require("durable-functions");
const df = require("moment");

module.exports = df(function*(ctx) {
    const jobId = ctx.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(ctx.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield ctx.df.callActivityAsync("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform action when condition met
            yield ctx.df.callActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        const nextCheck = moment.utc(ctx.df.currentUtcDateTime).add(pollingInterval, 's');
        yield ctx.df.createTimer(nextCheck.toDate());
    }

    // Perform further work here, or let the orchestration end
});
```

Quando uma solicitação é recebida, uma nova instância de orquestração é criada para essa ID do trabalho. A instância sonda um status até que uma condição seja atendida e o loop seja encerrado. Um temporizador durável é usado para controlar o intervalo de sondagem. Trabalho adicional pode ser realizado, ou a orquestração pode encerrar. Quando `ctx.CurrentUtcDateTime` excede `expiryTime`, o monitor encerra.

## <a name="pattern-5-human-interaction"></a>Padrão 5: interação humana

Muitos processos envolvem algum tipo de interação humana. O aspecto complicado de envolver pessoas em um processo automatizado é que as pessoas nem sempre são tão disponíveis e ágeis como os serviços de nuvem. Processos automatizados devem levar isso em conta e eles normalmente fazem isso usando a lógica de tempos limite e compensação.

Um exemplo de um processo de negócios que envolve a interação humana é um processo de aprovação. Por exemplo, a aprovação de um gerente pode ser necessária para um relatório de despesas que ultrapasse um determinado valor. Se o gerente não aprová-lo dentro de 72 horas (talvez ele esteja de férias), um processo de escalonamento é acionado para obter a aprovação de outra pessoa (talvez o gerente do gerente).

![Diagrama de interação humana](media/durable-functions-overview/approval.png)

Esse padrão pode ser implementado usando uma função de orquestrador. O orquestrador usaria um [temporizador durável](durable-functions-timers.md) para solicitar a aprovação e escalar em caso de tempo limite. Ele esperaria por um [evento externo](durable-functions-external-events.md), que seria a notificação gerada por alguma interação humana.

#### <a name="c-script"></a>Script C#

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    await ctx.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = ctx.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = ctx.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = ctx.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await ctx.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await ctx.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (apenas Functions v2)

```js
const df = require("durable-functions");
const df = require('moment');

module.exports = df(function*(ctx) {
    yield ctx.df.callActivityAsync("RequestApproval");

    const dueTime = moment.utc(ctx.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = ctx.df.createTimer(dueTime.toDate());

    const approvalEvent = ctx.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield ctx.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield ctx.df.callActivityAsync("ProcessApproval", approvalEvent.result);
    } else {
        yield ctx.df.callActivityAsync("Escalate");
    }
});
```

O temporizador durável é criado chamando `ctx.CreateTimer`. A notificação é recebida por `ctx.WaitForExternalEvent`. E `Task.WhenAny` é chamado para decidir se o próximo passo é escalonar (o tempo limite acontece primeiro) ou processar a aprovação (aprovação recebida antes do tempo limite).

Um cliente externo pode entregar a notificação de eventos para uma função do orchestrator em espera usando o [APIs de HTTP interna](durable-functions-http-api.md#raise-event) ou usando a API [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) a partir de outra função:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

## <a name="the-technology"></a>A tecnologia

Nos cenários, a extensão Funções Duráveis é compilada no [Framework de Tarefa Durável](https://github.com/Azure/durabletask), uma biblioteca de software livre no GitHub para compilar orquestrações de tarefas duráveis. Assim como o Azure Functions é a evolução sem servidor do Azure WebJobs, as Funções Duráveis são a evolução sem servidor do Framework de Tarefa Durável. O Framework de Tarefa Durável é muito usado dentro e fora da Microsoft para automatizar processos essenciais. Ele é uma opção natural para o ambiente sem servidor do Azure Functions.

### <a name="event-sourcing-checkpointing-and-replay"></a>Fornecimento de eventos, ponto de verificação e reprodução

As funções de orquestrador mantém seu estado de execução de forma confiável usando um padrão de design em nuvem conhecido como [Fornecimento do eventos](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Em vez de armazenar diretamente o estado *atual* de uma orquestração, a extensão durável usa um armazenamento somente de acréscimo para registrar a *série completa de ações* realizadas pela orquestração de função. Isso traz muitos benefícios, incluindo a melhorar o desempenho, a escalabilidade e a capacidade de resposta, em comparação com "despejar" todo o estado de tempo de execução. Outros benefícios incluem fornecer consistência eventual para dados transacionais e manter históricos e trilhas de auditoria completas. As próprias trilhas de auditoria habilitam ações de compensação confiáveis.

O uso do Fornecimento de eventos por esta extensão é transparente. Nos bastidores, o operador `await` de uma função de orquestrador leva o controle do thread do orquestrador de volta para o dispatcher do Framework de Tarefa Durável. O dispatcher, em seguida, confirma novas ações agendadas pela função de orquestrador (como chamar uma ou mais funções filho ou agendar um temporizador durável) no armazenamento. Essa ação de confirmação transparente é acrescentada ao *histórico de execução* da instância de orquestração. O histórico é armazenado na tabela de armazenamento. A ação de confirmação, em seguida, adiciona mensagens a uma fila para agendar o trabalho de fato. Neste ponto, a função de orquestrador pode ser descarregada da memória. A cobrança por ela para se você estiver usando o Plano Consumo do Azure Functions.  Quando há mais trabalho a ser feito, a função é reiniciada e seu estado é reconstruído.

Depois que uma função de orquestração recebe mais trabalho a fazer (por exemplo, uma mensagem de resposta é recebida ou um temporizador durável expira), o orquestrador é ativado novamente e executa mais uma vez a função inteira, desde o início, para reconstruir o estado local. Se, durante essa reprodução, o código tentar chamar uma função (ou qualquer outro tipo de trabalho assíncrono), o Framework de Tarefa Durável consultará o *histórico de execução* da orquestração atual. Se ele detectar que a [função de atividade](durable-functions-types-features-overview.md#activity-functions) já foi executada e gerou alguns resultados, ele reproduzirá o resultado da função e o código do orquestrador continuará em execução. Isso continuará acontecendo até que o código da função chegue a um ponto em que seja concluído ou tenha um novo trabalho assíncrono agendado.

### <a name="orchestrator-code-constraints"></a>Restrições de código do orquestrador

O comportamento de reprodução cria restrições quanto ao tipo do código que pode ser escrito em uma função de orquestrador. Por exemplo, código de orquestrador deve ser determinístico, uma vez que ele será reproduzido várias vezes e deve produzir o mesmo resultado toda vez. A lista completa de restrições pode ser encontrada na seção [Restrições de código do orquestrador](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) no artigo **Ponto de verificação e reinicialização**.

## <a name="language-support"></a>Suporte ao idioma

Atualmente, o C# (Functions v1 e v2) e o JavaScript (somente Functions v2) são as únicas linguagens compatíveis com Funções Duráveis. Isso inclui funções orquestradoras e de atividade. No futuro, adicionaremos suporte para todas as linguagens a que o Azure Functions dá suporte. Consulte a [Lista de problemas do Azure Functions no repositório do GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) para ver o status mais recente de nosso trabalho de suporte a linguagens adicionais.

## <a name="monitoring-and-diagnostics"></a>Monitoramento e diagnóstico

A extensão de Funções Duráveis emite automaticamente dados de acompanhamento estruturados para o [Application Insights](functions-monitoring.md) quando o aplicativo de funções é configurado com uma chave de instrumentação do Application Insights. Dados de acompanhamento podem ser usados para monitorar o comportamento e o progresso de suas orquestrações.

Veja um exemplo dos eventos de acompanhamento das Funções Duráveis no portal do Application Insights usando o [Application Insights Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics):

![Resultados de consulta do Application Insights](media/durable-functions-overview/app-insights-1.png)

Há muito dados estruturados úteis empacotados no campo `customDimensions` em cada entrada de log. Este é um exemplo de uma entrada desse tipo totalmente expandida.

![Campo customDimensions na consulta do Application Insights](media/durable-functions-overview/app-insights-2.png)

Devido ao comportamento de reprodução do dispatcher do Framework de Tarefa Durável, você pode esperar ver entradas de log redundantes para ações reproduzidas. Isso pode ser útil para entender o comportamento de reprodução do mecanismo central. O artigo [Diagnóstico](durable-functions-diagnostics.md) mostra exemplos de consultas que filtram logs de reprodução para que você possa ver apenas os logs "em tempo real".

## <a name="storage-and-scalability"></a>Armazenamento e a escalabilidade

A extensão de Funções Duráveis usa blobs, tabelas e filas de Armazenamento do Azure para persistir o estado do histórico de execução e disparar a execução da função. A conta de armazenamento padrão do aplicativo de funções pode ser usada ou você pode configurar uma conta de armazenamento separada. Talvez você queira uma conta separada devido aos limites de taxa de transferência de armazenamento. O código do orquestrador que você escrever não precisa (e não deve) interagir com as entidades nessas contas de armazenamento. As entidades são gerenciadas diretamente pelo Framework de Tarefa Durável como um detalhe de implementação.

As funções de orquestrador agendam funções de atividade e recebem suas respostas por meio de mensagens de fila interna. Quando um aplicativo de funções é executado no Plano de Consumo do Azure Functions, essas filas são monitoradas pelo [Controlador de escala do Azure Functions](functions-scale.md#how-the-consumption-plan-works) e novas instâncias de computação são adicionadas conforme necessário. Quando expandida para várias VMs, uma função de orquestrador pode ser executada em uma VM enquanto as funções de atividade chamadas por ele são executadas em várias VMs diferentes. Você pode encontrar mais detalhes sobre o comportamento de escala das Funções Duráveis em [Desempenho e escalabilidade](durable-functions-perf-and-scale.md).

O armazenamento de tabela é usado para armazenar o histórico de execução das contas do orquestrador. Sempre que uma instância for reidratada em uma VM específica, ela buscará seu histórico de execução do armazenamento de tabelas para que possa recriar seu estado local. Um dos aspectos convenientes de ter o histórico disponível no armazenamento de tabelas é que você pode dar uma olhada para ver o histórico de sua orquestrações usando ferramentas como o [Gerenciador de Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Captura de tela do Gerenciador de Armazenamento do Azure](media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> Embora seja fácil e conveniente ver o histórico de execução no armazenamento de tabela, evite depender desta tabela. Ela pode mudar à medida que a extensão de Funções Duráveis evoluir.

## <a name="known-issues-and-faq"></a>Problemas conhecidos e perguntas frequentes

Todos os problemas conhecidos devem estar registrados na lista de [Problemas no GitHub](https://github.com/Azure/azure-functions-durable-extension/issues). Se você tiver um problema e não for possível localizá-lo no GitHub, abra um novo problema e inclua uma descrição detalhada dele.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Continuar lendo a documentação das Funções Duráveis](durable-functions-types-features-overview.md)

> [!div class="nextstepaction"]
> [Instalar a extensão de Funções Duráveis e exemplos](durable-functions-install.md)

