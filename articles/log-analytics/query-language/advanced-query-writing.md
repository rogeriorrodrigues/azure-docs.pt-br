---
title: Consultas avançadas no Azure Log Analytics | Microsoft Docs
description: Este artigo fornece um tutorial para usar o portal do Google Analytics para escrever consultas no Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 72c151fec0637822411f8cac44f4e13a8df96445
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190649"
---
# <a name="writing-advanced-queries-in-log-analytics"></a>Escrever consultas avançadas no Log Analytics

> [!NOTE]
> Você deve concluir [Introdução ao portal do Analytics](get-started-analytics-portal.md) e [Introdução às consultas](get-started-queries.md) antes de concluir esta lição.

## <a name="reusing-code-with-let"></a>Reutilizar código com let
Use `let` para atribuir os resultados a uma variável e fazer referência a ela mais tarde na consulta:

```OQL
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Também é possível atribuir valores de constante para variáveis. Isso dá suporte a um método para configurar os parâmetros para os campos que você precisa alterar toda vez que executar a consulta. Modifique esses parâmetros conforme necessário. Por exemplo, para calcular o espaço livre em disco e a memória livre (em percentuais) em um determinado período de tempo:

```OQL
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

Isso facilita alterar o início da hora de término na próxima vez que você executar a consulta.

### <a name="local-functions-and-parameters"></a>Funções e parâmetros locais
Use as instruções `let` para criar funções que podem ser usadas na mesma consulta. Por exemplo, defina uma função que usa um campo datetime (no formato UTC) e o converte em um formato dos EUA padrão. 

```OQL
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="functions"></a>Funções
É possível salvar uma consulta com um alias da função para que ele possa ser referenciado por outras consultas. Por exemplo, a consulta padrão a seguir retorna todas as atualizações de segurança ausentes relatadas no último dia:

```OQL
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Você pode salvar essa consulta como uma função e dar a ela um alias como _security_updates_last_day_. Em seguida, você pode usar esse alias em outra consulta para procurar atualizações de segurança necessárias relacionadas ao SQL:

```OQL
security_updates_last_day | where Title contains "SQL"
```

Para salvar uma consulta como uma função, selecione o botão **Salvar** no portal e altere **Salvar como** para _Função_. O alias da função pode conter letras, dígitos ou sublinhados, mas deve começar com uma letra ou um sublinhado.

> [!NOTE]
> Salvar uma função é possível em consultas do Log Analytics, mas atualmente não para consultas do Application Insights.


## <a name="print"></a>Imprimir
`print` retornará uma tabela com uma única coluna e uma única linha, mostrando o resultado de um cálculo. Isso geralmente é usado em casos em que é necessário um cálculo simples. Por exemplo, para localizar a hora atual em PST e adicionar uma coluna com EST:

```OQL
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable` permite a você definir um conjunto de dados. Você fornece um esquema e um conjunto de valores e, em seguida, redireciona a tabela para todos os outros elementos de consulta. Por exemplo, para criar uma tabela de uso de RAM e calcular seu valor médio por hora:

```OQL
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

Construções de Datatable também são muito úteis durante a criação de uma tabela de pesquisa. Por exemplo, para mapear dados de tabela, como IDs de eventos da tabela _SecurityEvent_, para tipos de eventos listados em outro lugar, crie uma tabela de pesquisa com os tipos de evento usando `datatable` e faça a junção dessa datatable com os dados _SecurityEvent_:

```OQL
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>Próximas etapas
Consulte outras lições para usar a linguagem de consulta do Log Analytics:

- [Operações de cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [JSON e estruturas de dados](json-data-structures.md)
- [Junções](joins.md)
- [Gráficos](charts.md)