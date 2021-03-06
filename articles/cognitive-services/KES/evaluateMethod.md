---
title: Método Avaliar na API de Serviço de Exploração de Conhecimento | Microsoft Docs
description: Saiba como usar o método Avaliar na API de KES (Serviço de Exploração de Conhecimento) em serviços Cognitivos.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: fc3d73b326b565cfe40d1b82cc419357b28a801a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363445"
---
# <a name="evaluate-method"></a>Método Avaliar
O método *Avaliar* avalia e retorna o resultado de uma expressão de consulta estruturada com base em dados de índice.

Normalmente, uma expressão será obtida de uma resposta ao método Interpretar.  Mas também, é possível compor expressões de consulta (consulte [Expressão de Consulta Estruturada](Expressions.md)).  

## <a name="request"></a>Solicitação 
`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

NOME|Valor|DESCRIÇÃO
----|----|----
expr       | Cadeia de caracteres de texto | Expressão de consulta estruturada que seleciona um subconjunto de entidades de índice.
atributos | Cadeia de caracteres de texto | Lista delimitada por vírgulas de atributo a ser incluído na resposta.
count      | Número (padrão = 10) | Número máximo de resultados a serem retornados.
deslocamento     | Número (padrão = 0) | Índice do primeiro resultado para retornar.
orderby |   Cadeia de caracteres de texto | Nome do atributo usado para classificar os resultados, seguidos por ordem de classificação opcional (padrão = asc):"*attrname*[:(asc&#124;desc)]".  Se não for especificado, os resultados são retornados, diminuindo a probabilidade de log natural.
Tempo limite  | Número (padrão = 1000) | Tempo limite em milissegundos. Somente resultados computados antes que o tempo limite tenha decorrido serão retornados.

Usando os parâmetros *contagem* e *deslocamento*, um grande número de resultados pode ser obtido incrementalmente por várias solicitações.
  
## <a name="response-json"></a>Resposta (JSON)
JSONPath|DESCRIÇÃO
----|----
$.expr | O parâmetro *expr* da solicitação.
$.entities | Matriz de 0 ou mais entidades de objeto que correspondem à expressão de consulta estruturada. 
$.aborted | Verdadeiro se a solicitação atingiu o tempo limite.

Cada entidade contém um valor *logprob* e os valores dos atributos solicitados.

## <a name="example"></a>Exemplo
O exemplo de publicações acadêmicas, a solicitação a seguir passa uma expressão de consulta estruturada (potencialmente da saída de uma solicitação *interpretar*) e recupera alguns atributos para as 2 principais entidades de correspondência:

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

A resposta contém as 2 principais ("count = 2") entidades correspondentes mais prováveis.  Para cada entidade são retornados o título, ano, nome do autor e atributos ID de autor.  Observe como a estrutura de valores de atributo de composição corresponde à maneira que eles são especificados no arquivo de dados. 

```json
{
  "expr": "Composite(Author.Name=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -6.645,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1968481722
        },
        {
          "Name": "susan t dumais",
          "Id": 676500258
        },
        {
          "Name": "eric horvitz",
          "Id": 1470530979
        }
      ]
    },
    {
      "logprob": -6.764,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1982462162
        },
        {
          "Name": "christine alvarado",
          "Id": 2163512453
        },
        {
          "Name": "mark s ackerman",
          "Id": 2055132526
        },
        {
          "Name": "david r karger",
          "Id": 2012534293
        }
      ]
    }
  ]
}
```
