---
title: Python de exemplo para derivar novas colunas no preparo dos dados do Azure Machine Learning | Microsoft Docs
description: Este documento fornece exemplos de código do Python para criar novas colunas na preparação de dados do Azure Machine Learning.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: babe7b8dd1ea459fd3478d20dd7d69138c9ca163
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830088"
---
# <a name="sample-of-custom-column-transforms-python"></a>Exemplo de transformações de coluna personalizadas (Python) 
O nome dessa transformação no menu é **Adicionar Coluna (Script)**.

Antes de ler este apêndice, leia [Visão geral da extensibilidade do Python](data-prep-python-extensibility-overview.md).

## <a name="test-equivalence-and-replace-values"></a>Testar equivalência e substituir valores 
Se o valor na Col1 for menor do que 4, a nova coluna deverá ter um valor 1. Se o valor na Col1 for maior do que 4, a nova coluna terá o valor 2. 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>Data e hora atuais 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>Typecasting 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>Avaliar quanto à nulidade 
Se Col1 contiver um valor nulo, então marque a nova coluna como **Incorreta**. Caso contrário, marque-a como **Boa.** 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>Nova coluna computada 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Computação de época 
Número de segundos desde a Época Unix (supondo que Col1 já é uma data): 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```

## <a name="hash-a-column-value-into-a-new-column"></a>Adicionar hash a um valor de coluna em uma nova coluna
```python
    import hashlib
    hash(row["MyColumnToHashCol1"])

```




