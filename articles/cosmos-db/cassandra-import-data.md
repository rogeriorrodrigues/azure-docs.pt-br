---
title: Importar dados do Cassandra para o Azure Cosmos DB | Microsoft Docs
description: Saiba como usar o comando CQL Copy para copiar dados do Cassandra para o Azure Cosmos DB.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 55a6fec1b6ac018b4b24c0d27dcfdd5812455800
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189669"
---
# <a name="migrate-your-data-to-azure-cosmos-db-cassandra-api-account"></a>Migrar seus dados para a conta da API do Cassandra do Azure Cosmos DB

Este tutorial fornece instruções sobre como importar dados do Cassandra para o Azure Cosmos DB usando o comando COPY da CQL (Linguagem de Consulta do Cassandra). 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Recuperando sua cadeia de conexão
> * Importação de dados usando o comando cqlsh COPY
> * Importação usando o conector Spark 

# <a name="prerequisites"></a>Pré-requisitos

* Instale o [Apache Cassandra](http://cassandra.apache.org/download/) e certifique-se especificamente de que *cqlsh* esteja presente.  

* Aumentar a taxa de transferência: a duração da sua migração de dados depende da taxa de transferência provisionada para suas Tabelas. Certifique-se de aumentar a taxa de transferência para migrações de dados maiores. Depois de concluir a migração, diminua a taxa de transferência para economizar custos. Para saber mais sobre como aumentar a taxa de transferência no [portal do Azure](https://portal.azure.com), veja [Definir a taxa de transferência para contêineres do Azure Cosmos DB](set-throughput.md).  

* Habilitar SSL: o Azure Cosmos DB tem padrões e requisitos de segurança rígidos. Certifique-se de habilitar o SSL ao interagir com sua conta. Quando você usar a CQL com SSH, terá a opção de fornecer informações de SSL. 

## <a name="get-your-connection-string"></a>Obter sua cadeia de conexão

1. No [portal do Azure](https://portal.azure.com), na extremidade esquerda, clique em **Azure Cosmos DB**.

2. No painel **Assinaturas**, selecione o nome da sua conta.

3. Clique em **Cadeia de Conexão**. O painel direito contém todas as informações necessárias para conectar-se à sua conta com êxito.

    ![Página Cadeia de conexão](./media/cassandra-import-data/keys.png)

## <a name="migrate-data-by-using-cqlsh-copy"></a>Migrar dados usando cqlsh COPY

Para importar dados do Cassandra para o Azure Cosmos DB para uso com a API do Cassandra, use as diretrizes a seguir:

1. Faça logon no cqhsh usando as informações de conexão do portal.
2. Use o [comando CQL COPY](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) para copiar os dados locais para o ponto de extremidade da API do Apache Cassandra. Certifique-se de que a origem e o destino estejam no mesmo data center para minimizar os problemas de latência.

### <a name="steps-to-move-data-with-cqlsh"></a>Etapas para mover dados com cqlsh

1. Crie previamente e dimensione sua tabela:
    * Por padrão, o Azure Cosmos DB provisiona uma nova tabela de API do Cassandra com 1.000 unidades de solicitação por segundo (RU/s) (a criação baseada em CQL está provisionada com 400 RU/s). Antes de iniciar a migração usando cqlsh, crie previamente todas as suas tabelas desde o [portal do Azure](https://portal.azure.com) ou do cqlsh. 

    * No [portal do Azure](https://portal.azure.com), aumente a taxa de transferência das tabelas da taxa de transferência padrão (400 ou 1000 RU/s) para 10.000 RU/s durante a migração. Com a taxa de transferência mais elevada, você pode evitar a limitação de taxa e migrar em menos tempo. Com cobrança horária no Azure Cosmos DB, você poderá reduzir a taxa de transferência imediatamente após a migração para economizar custos.

2. Determine o custo de RU de uma operação. Você pode fazer isso usando o SDK da API do Cassandra do Azure Cosmos DB de sua escolha. Este exemplo mostra a versão do .NET da obtenção de custos de RU. 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. Determine a latência do computador para o serviço do Azure Cosmos DB. Se você estiver em um Datacenter do Azure, a latência deverá ser um número de milissegundos baixo de dígito único. Se você estiver fora do Datacenter do Azure, poderá usar psping ou azurespeed.com para obter a latência aproximada do seu local.   

4. Calcule os valores apropriados para os parâmetros (NUMPROCESS, INGESTRATE, MAXBATCHSIZE ou MINBATCHSIZE) que fornecem um bom desempenho. 

5. Execute o comando de migração final. A execução desse comando assume que você iniciou o cqlsh usando as informações de cadeia de conexão.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-by-using-spark"></a>Migrar dados usando o Spark

Para os dados que residem em um cluster existente em máquinas virtuais do Azure, a importação de dados usando Spark também é uma opção viável. Isso requer que o Spark seja configurado como um intermediário para ingestão regular ou única. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como concluir as seguintes tarefas:

> [!div class="checklist"]
> * Recuperar sua cadeia de conexão
> * Importar dados usando o comando cql copy
> * Importar usando o conector Spark 

Agora você pode seguir para a seção Conceitos para saber mais sobre o Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Níveis ajustáveis de consistência de dados no Azure Cosmos DB](../cosmos-db/consistency-levels.md)
