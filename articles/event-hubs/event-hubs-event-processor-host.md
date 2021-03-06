---
title: O que é o Host Processador de Evento do Hub de Eventos do Azure e por que usá-lo | Microsoft Docs
description: Visão geral e introdução ao Host Processador de Evento do Hubs de Eventos do Azure
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: ede76e8161cc15d6a87041ef5ef9525570fec794
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745874"
---
# <a name="azure-event-hubs-event-processor-host-overview"></a>Visão geral do Host do Processador de Eventos do Hubs de Eventos do Azure

Os Hubs de Evento do Azure são um poderoso serviço de ingestão de telemetria que pode ser usado para transmitir milhões de eventos a baixo custo. Este artigo descreve como consumir eventos ingeridos usando o *Host do Processador de Eventos* (EPH); um agente do consumidor inteligente que simplifica o gerenciamento de leitores de pontos de verificação, leasing e eventos paralelos.  

A chave para reduzir os Hubs de Eventos horizontalmente é o modelo de consumidor particionado. Em contraste com o [consumidores concorrentes](http://msdn.microsoft.com/en-us/library/dn568101.aspx) padrão, o padrão de consumidor particionado permite alta escala, removendo o gargalo de contenção e promovendo o paralelismo de ponta a ponta.

## <a name="home-security-scenario"></a>Cenário de segurança inicial

Como um cenário de exemplo, considere uma empresa de segurança inicial que monitora 100.000 residências. A cada minuto, ele obtém dados de vários sensores, como um detector de movimento, sensor de porta/janela aberta, detector de quebra de vidro etc., instalado em cada página inicial. A empresa oferece um site da web para residentes monitorarem a atividade da sua casa quase em tempo real.

Cada sensor envia dados para um hub de eventos. O hub de eventos é configurado com 16 partições. No lado do consumidor, você precisa de um mecanismo que possa ler esses eventos, consolidá-los (filtrar, agregar, etc.) e despejar o agregado em um blob de armazenamento, que é então projetado para uma página da web amigável.

## <a name="write-the-consumer-application"></a>Escrever o aplicativo de consumidor

Ao projetar o consumidor em um ambiente distribuído, o cenário deve lidar com os seguintes requisitos:

1. **Escala:** criar vários consumidores, com cada consumidor assumindo a propriedade de leitura de algumas partições de Hubs de eventos.
2. **O balanceamento de carga:** aumentar ou reduzir os consumidores dinamicamente. Por exemplo, quando um novo tipo de sensor (por exemplo, um detector de monóxido de carbono) é adicionado a cada página inicial, aumenta o número de eventos. Nesse caso, o operador (um ser humano) aumenta o número de instâncias de consumidor. Em seguida, o pool de consumidores pode redistribuir o número de partições que são proprietárias, para compartilhar a carga com os consumidores recém-adicionados.
3. **Retorno contínuo de falhas:** Se um consumidor (**consumidor A**) falhar (por exemplo, a máquina virtual que hospeda o consumidor falha repentinamente), outros consumidores devem poder selecionar as partições de propriedade de **consumidor A** e continue. Além disso, o ponto de continuação, chamado de *checkpoint* ou *offset*, deve estar no ponto exato em que **consumidor A** falhou, ou um pouco antes disso.
4. **Consumir eventos:** enquanto os três pontos anteriores lidam com o gerenciamento do consumidor, deve haver código para consumir os eventos e fazer algo útil com ele; por exemplo, agregá-lo e carregá-lo no armazenamento de BLOBs.

Em vez de criar sua própria solução para isso, os Event Hubs fornecem essa funcionalidade por meio da interface [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) e da classe [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost).

## <a name="ieventprocessor-interface"></a>Interface IEventProcessor

Primeiro, consumir aplicativos implementam a interface [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor), que tem quatro métodos: [OpenAsync, CloseAsync, ProcessErrorAsync e ProcessEventsAsnyc](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Essa interface contém o código real para consumir os eventos que envia os Hubs de eventos. O código a seguir mostra uma implementação simples:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Em seguida, criar uma instância de uma instância [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Dependendo da sobrecarga, ao criar a instância [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) no construtor, os seguintes parâmetros são usados:

- **hostName:** o nome de cada instância do consumidor. Cada instância do **EventProcessorHost** deve ter um valor exclusivo para essa variável em um grupo de consumidores, portanto, é melhor não codificar esse valor.
- **eventHubPath:** O nome do Hub de Eventos.
- **consumerGroupName:** Event Hubs usa **$Default** como o nome do grupo de consumidores padrão, mas é uma boa prática criar um grupo de consumidores para seu aspecto específico de processamento.
- **eventHubConnectionString:** a cadeia de caracteres de conexão ao hub de eventos, que pode ser recuperada do portal do Azure. Essa cadeia de conexão deve ter as permissões **Listen** no hub de eventos.
- **storageConnectionString:** a conta de armazenamento usada para o gerenciamento de recursos internos.

Por fim, os consumidores registram a instância [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) com o serviço Hub de Eventos. Registrando instrui o serviço Hub de Eventos a esperar que o aplicativo consumidor consuma eventos de algumas de suas partições e invoque o código de implementação [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) sempre que ele enviar eventos para consumo.

### <a name="example"></a>Exemplo

Por exemplo, imagine que há 5 máquinas virtuais (VMs) dedicadas para consumo de eventos e um aplicativo de console simples em cada VM, que o consumo real funciona. Cada aplicativo de console, em seguida, cria um [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) da instância e o registra com o serviço de Hubs de eventos.

Nesse cenário de exemplo, digamos que 16 partições são alocadas para as 5 instâncias **EventProcessorHost**. Algumas instâncias **EventProcessorHost** talvez você tenha algumas partições mais que outros. Para cada partição que uma instância **EventProcessorHost** possui, ela cria uma instância da classe `SimpleEventProcessor`. Portanto, existem 16 instâncias de `SimpleEventProcessor` geral, com um atribuído a cada partição.

A lista a seguir resume este exemplo:

- 16 partições de Hubs de eventos.
- 5 VMs, o aplicativo de 1 consumidor (por exemplo, Consumer.exe) em cada VM.
- 5 instâncias EPH registradas, 1 em cada VM pelo Consumer.exe.
- 16 `SimpleEventProcessor` objetos criados pelas instâncias do EPH 5.
- 1 aplicativo Consumer.exe pode conter 4 `SimpleEventProcessor` objetos, uma vez que a instância EPH 1 pode possuir 4 partições.

## <a name="partition-ownership-tracking"></a>Acompanhamento de propriedade da partição

Propriedade de uma partição para uma instância EPH (ou um consumidor) é controlada por meio da conta de armazenamento do Azure que é fornecida para o rastreamento. Você pode visualizar o rastreamento como uma tabela simples, da seguinte maneira. Você pode ver a implementação real, examinando os blobs na conta de armazenamento fornecida:

| **Nome do grupo de consumidor** | **Identificação da Partição** | **Nome do host (proprietário)** | **Tempo de concessão (ou propriedade) adquirido** | **Deslocamento na partição (ponto de verificação)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Consumidor\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Consumidor\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Consumidor\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Consumidor\_VM3 | 2018-04-15T01:22:56 | 976 |

Aqui, cada host adquire a propriedade de uma partição para uma determinada duração (a duração da concessão). Se um host falhar (VM desliga), a concessão expira. Outros hosts tentam obter a propriedade da partição, e um dos hosts é bem-sucedido. Esse processo redefine a concessão na partição com um novo proprietário. Dessa forma, somente um único leitor por vez pode ler de qualquer determinada partição dentro de um grupo de consumidores.

## <a name="receive-messages"></a>Receber mensagens

Cada chamada para [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) oferece uma coleção de eventos. Ele é responsável por gerenciar o estes eventos. É recomendável que você faça coisas relativamente rápidas; ou seja, faça o processamento menor possível. Em vez disso, use grupos de consumidores. Se você precisar gravar no armazenamento e fazer algum roteamento, geralmente é melhor usar dois grupos de consumidores e ter duas implementações [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) que são executadas separadamente.

Em algum momento durante o processamento, convém manter o controle de que você tenha lido e concluído. Manter o controle é crítico se você precisar reiniciar a leitura para não retornar ao início do fluxo. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) simplifica esse acompanhamento usando *pontos de verificação*. Um ponto de verificação é um local, ou deslocamento, para uma determinada partição, dentro de um determinado grupo de consumidores, ponto no qual você está satisfeito com o processamento das mensagens. Marcar um ponto de verificação em **EventProcessorHost** é realizado chamando o método [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) no objeto [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext). Essa operação geralmente é feita dentro do método [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync), mas também pode ser feita em [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Ponto de verificação

O método [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) tem duas sobrecargas: a primeira, sem parâmetros, pontos de verificação para o deslocamento de evento mais alto dentro da coleção retornada por [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Esse deslocamento é uma marca de "HIGHWATER"; Ele pressupõe que você processou todos os eventos recentes quando você chamá-lo. Se você usar esse método dessa forma, lembre-se de que você deve chamá-la depois que o seu outro código de processamento de eventos foi retornado. A segunda sobrecarga permite que você especifique uma instância [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) para o ponto de verificação. Esse método permite que você use um tipo diferente de marca d'água para o ponto de verificação. Com essa marca d'água, você pode implementar uma marca de "baixo nível de água": o menor evento sequenciado que você tenha certeza de que foi processado. Essa sobrecarga é fornecida para permitir flexibilidade no gerenciamento de deslocamento.

Quando o ponto de verificação é executado, um arquivo JSON com informações específicas da partição (especificamente, o deslocamento) é gravado para a conta de armazenamento fornecida no construtor para [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Esse arquivo é atualizado continuamente. É importante considerar o ponto de verificação no contexto – seria aconselhável para o ponto de verificação de todas as mensagens. A conta de armazenamento usada para o ponto de verificação provavelmente não manipularia essa carga, mas o mais importante é que o ponto de verificação de cada evento é indicativo de um padrão de mensagens enfileiradas para o qual uma fila do Barramento de Serviços pode ser uma opção melhor do que um hub de eventos. A ideia por trás dos Hubs de eventos é que você obtenha entregas "pelo menos uma vez" em grande escala. Tornando seus sistemas downstream idempotentes, é fácil de se recuperar de falhas ou reinicia que resultam nos mesmos eventos que está sendo recebidos várias vezes.

## <a name="thread-safety-and-processor-instances"></a>Instâncias de processador e de segurança do thread

Por padrão, [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) é thread-safe e se comporta de forma síncrona em relação à instância do [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Quando os eventos chegam para uma partição, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) é chamado na instância **IEventProcessor** para essa partição e bloqueará chamadas adicionais para **ProcessEventsAsync** para a partição. As mensagens subsequentes e chamadas para **ProcessEventsAsync** colocar na fila em segundo plano enquanto você continua a bomba de mensagens ser executado em segundo plano em outros threads. Esse thread-safe elimina a necessidade de coleções thread-safe e aumenta drasticamente o desempenho.

## <a name="shut-down-gracefully"></a>Desligar normalmente

Finalmente, [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) permite um desligamento limpo de todos os leitores de partição e deve sempre ser chamado ao encerrar uma instância de [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Falha ao fazer isso pode causar atrasos ao iniciar outras instâncias do **EventProcessorHost** devido à expiração de concessão e conflitos de época. Gerenciamento de época é abordado em detalhes nesta [postagem de blog](https://blogs.msdn.microsoft.com/gyan/2014/09/02/event-hubs-receiver-epoch/)

## <a name="lease-management"></a>Gerenciamento de concessão

Conforme explicado anteriormente, a tabela de controle simplifica bastante a natureza de dimensionamento automático do [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Como uma instância de **EventProcessorHost** é iniciado, ele adquire concessões tantos quanto possível e começa a ler eventos. Como as concessões perto da expiração, **EventProcessorHost** tenta renová-los, colocando uma reserva. Se a concessão está disponível para a renovação, o processador continua a leitura, mas se não for, o leitor está fechado e [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) é chamado. **CloseAsync** é um bom momento para executar qualquer limpeza final para essa partição.

**EventProcessorHost** inclui uma propriedade [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions). Essa propriedade permite que controle sobre o gerenciamento de concessão. Definir essas opções antes de registrar sua implementação [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor).

## <a name="control-event-processor-host-options"></a>Opções de Host do Processador de Eventos de Controle

Além disso, uma sobrecarga de [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) leva um objeto [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) como um parâmetro. Use esse parâmetro para controlar o comportamento de [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) em si. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) define quatro propriedades e um evento:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): O tamanho máximo da coleção que você deseja receber em uma invocação de [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Esse tamanho não é o mínimo, apenas o tamanho máximo. Se houver menos mensagens para serem recebidas, **ProcessEventsAsync** executa com quantos estiverem disponíveis.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): um valor usado pelo canal de AMQP subjacente para determinar o limite superior de quantas mensagens o cliente deve receber. Este valor deve ser maior ou igual a [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Se este parâmetro for **true**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) será chamado quando a chamada subjacente para receber eventos em uma partição expirar. Esse método é útil para a execução de ações com base no tempo durante períodos de inatividade na partição.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): permite que uma função ponteiro ou expressão lambda deve ser definido, que é chamada para fornecer o inicial quando um leitor começa a ler uma partição de deslocamento. Sem especificar esse deslocamento, o leitor começa no evento mais antigo, a menos que um arquivo JSON com um deslocamento já tiver sido salvo na conta de armazenamento fornecida para o construtor [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Esse método é útil quando você deseja alterar o comportamento da inicialização do leitor. Quando este método é chamado, o parâmetro de objeto contém a ID de partição para a qual o leitor está sendo iniciado.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): permite que você receba uma notificação de subjacentes exceções que ocorrem no [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Se as coisas não estão funcionando conforme o esperado, esse evento é um bom lugar para começar a procurar.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com o Host do processador de eventos, consulte os seguintes artigos para saber mais sobre os Hubs de eventos:

* Introdução com um [Tutorial de Hubs de Eventos](event-hubs-dotnet-standard-getstarted-send.md)
* [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md)
* [Disponibilidade e consistência nos Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
* [Exemplos de Hubs de Eventos no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)