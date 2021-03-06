---
title: Trabalhando com cadeias de caracteres em consultas de análise de Log do Azure | Microsoft Docs
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
ms.openlocfilehash: f027754f26a9063aa5faa548fd01576624811005
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190633"
---
# <a name="working-with-json-and-data-structures-in-log-analytics-queries"></a>Trabalhando com JSON e estruturas de dados em consultas do Log Analytics

> [!NOTE]
> Você deve concluir [Primeiros passos com o portal do Google Analytics](get-started-analytics-portal.md) e [Primeiros passos com as consultas](get-started-queries.md) antes de concluir esta lição.


Objetos aninhados são objetos que contêm outros objetos em uma matriz ou um mapa de pares de valores-chave. Esses objetos são representados como strings JSON. Este artigo descreve como o JSON é usado para recuperar dados e analisar objetos aninhados.

## <a name="working-with-json-strings"></a>Trabalhando com cadeias de caracteres JSON
Use `extractjson` para acessar um elemento específico de JSON em um caminho conhecido. Essa função requer uma expressão de caminho que usa as convenções a seguir.

- _$_ Para fazer referência à pasta raiz
- Use o colchete ou a notação de ponto para se referir a índices e elementos, conforme ilustrado nos exemplos a seguir.


Use parênteses para índices e pontos para separar elementos:

```OQL
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Este é o mesmo resultado usando apenas a notação de colchetes:

```OQL
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Se houver apenas um elemento, você poderá usar apenas a notação de ponto:

```OQL
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Trabalhando com objetos

### <a name="parsejson"></a>parsejson
Para acessar vários elementos em sua estrutura json, é mais fácil acessá-lo como um objeto dinâmico. Use `parsejson` para converter dados de texto para um objeto dinâmico. Quando convertido em um tipo dinâmico, funções adicionais podem ser usadas para analisar os dados.

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Use `arraylength` para contar o número de elementos em uma matriz:

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Use `mvexpand` para dividir as propriedades de um objeto em linhas separadas.

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
Use `buildschema` para obter o esquema que admite todos os valores de um objeto:

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

A saída é um esquema no formato JSON:
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
Esta saída descreve os nomes dos campos do objeto e seus tipos de dados correspondentes. 

Objetos aninhados podem ter diferentes esquemas, como no exemplo a seguir:

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Criar esquema](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Próximas etapas
Consulte outras lições para usar a linguagem de consulta do Log Analytics:

- [Operações de cadeia de caracteres](string-operations.md)
- [Operações de data e hora](datetime-operations.md)
- [Funções de agregação](aggregations.md)
- [Agregações avançadas](advanced-aggregations.md)
- [Gravação de consulta avançada](advanced-query-writing.md)
- [Junções](joins.md)
- [Gráficos](charts.md)