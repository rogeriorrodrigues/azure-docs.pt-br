---
title: Usar o recurso de coleta de dados de modelos no Azure Machine Learning Workbench| Microsoft Docs
description: Este artigo aborda como usar o recurso de coleta de dados de modelos no Azure Machine Learning Workbench
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 5c1a884ebe6216c4e8099f2ada2182ccff68b63e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450326"
---
# <a name="collect-model-data-by-using-data-collection"></a>Coletar dados de modelo usando a coleta de dados

Você pode usar o recurso de coleta de dados de modelo no Azure Machine Learning para arquivar entradas e previsões do modelo de um serviço Web.

## <a name="install-the-data-collection-package"></a>Instalar o pacote de coleta de dados
Você pode instalar a biblioteca de coleta de dados nativamente no Linux e no Windows.

### <a name="windows"></a>Windows
No Windows, instale o módulo de coletor de dados usando o seguinte comando:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
No Linux, instale primeiro a biblioteca libxml ++. Execute o comando a seguir, que deve ser emitido em sudo:

    sudo apt-get install libxml++2.6-2v5

Em seguida, execute o seguinte comando:

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>Configurar variáveis de ambiente

A coleta de dados do modelo depende de duas variáveis de ambiente. AML_MODEL_DC_STORAGE_ENABLED deve ser definida como **true** (todas as letras minúsculas) e AML_MODEL_DC_STORAGE deve ser definida como a cadeia de conexão para a conta de Armazenamento do Azure onde você deseja armazenar os dados.

Essas variáveis de ambiente já estão definidas para você quando o serviço Web está em execução em um cluster no Azure. Ao executar localmente, você mesmo precisa configurá-las. Se você estiver usando o Docker, use o parâmetro -e do comando docker run para passar variáveis de ambiente.

## <a name="collect-data"></a>Coletar dados

Para usar a coleta de dados de modelo, faça as seguintes alterações ao seu arquivo de pontuação:

1. Adicione o seguinte código na parte superior do arquivo:
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

1. Adicione as seguintes linhas de código à função `init()`:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1. Adicione as seguintes linhas de código à função `run(input_df)`:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Verifique se as variáveis `input_df` e `pred` (valor de previsão de `model.predict()`) estão inicializadas antes de chamar a função `collect()` nelas.

1. Use o comando `az ml service create realtime` com a opção `--collect-model-data true` para criar um serviço Web em tempo real. Essa etapa faz com que os dados de modelo sejam coletados quando o serviço é executado.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
1. Para testar a coleta de dados, execute o comando `az ml service run realtime`:

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>Exibir os dados coletados
Para exibir os dados coletados no armazenamento de blobs:

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços**.
1. Na caixa de pesquisa, digite **Contas de armazenamento** e selecione a tecla Enter.
1. Na folha de pesquisa **Contas de armazenamento**, selecione o recurso **Conta de armazenamento**. Para determinar sua conta de armazenamento, use as seguintes etapas:

    a. Vá para o Azure Machine Learning Workbench, selecione o projeto em que você está trabalhando e abra o prompt de comando do menu **Arquivo**.
    
    b. Insira `az ml env show -v` e verifique o valor *storage_account*. Esse é o nome da sua conta de armazenamento.

1. Selecione **Contêineres** no menu da folha de recursos e no contêiner chamado **modeldata**. Para ver o início da propagação dos dados para a conta de armazenamento, pode ser necessário aguardar até 10 minutos após a primeira solicitação de serviço Web. Os dados fluem para blobs com o seguinte caminho de contêiner:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Os dados podem ser consumidos de blobs do Azure de diversas maneiras, tanto por meio de ferramentas de software livre quanto de software da Microsoft. Estes são alguns exemplos:
- Machine Learning Workbench do Azure: abra o arquivo .csv no Azure Machine Learning Workbench adicionando o arquivo .csv como uma fonte de dados.
- Excel: abra os arquivos csv diários como uma planilha.
- [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/): crie gráficos contendo dados extraídos por pull de dados .csv em blobs.
- [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): crie um dataframe com uma grande porção de dados csv.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): carregue dados csv em uma tabela de Hive e execute consultas SQL diretamente no blob.

