---
title: Publish-WebApplicationWebSite (script do Windows PowerShell) | Microsoft Docs
description: Saiba como publicar um projeto Web em um site do Azure. Se os recursos necessários não existirem, este script criará tais recursos em sua assinatura do Azure.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 63cfaa2d-f04d-40dc-8677-345385c278d5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: ea8e36aabb75839a9c301f45a82241e3a859d42a
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42142233"
---
# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Publish-WebApplicationWebSite (script do Windows PowerShell)
## <a name="syntax"></a>Sintaxe
Publica um projeto Web em um site do Azure. Se os recursos necessários não existirem, o script criará tais recursos em sua assinatura do Azure.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Configuração
O caminho para o arquivo de configuração de JSON que descreve os detalhes da implantação.

| Parâmetro | Valor padrão |
| --- | --- |
| Aliases |Nenhum |
| Obrigatório? |verdadeiro |
| Position |nomeado |
| Valor padrão |Nenhum |
| Aceitar entrada do Pipeline? |falso |
| Aceitar caracteres curinga? |falso |

## <a name="subscriptionname"></a>SubscriptionName
O nome da assinatura do Azure na qual você deseja criar o site.

| Parâmetro | Valor padrão |
| --- | --- |
| Aliases |Nenhum |
| Obrigatório? |falso |
| Position |nomeado |
| Valor padrão |Nenhum |
| Aceitar entrada do Pipeline? |falso |
| Aceitar caracteres curinga? |falso |

## <a name="webdeploypackage"></a>WebDeployPackage
O caminho para o pacote de implantação Web a publicar no site. Você pode criar este pacote usando o assistente Publicar Web no Visual Studio. Para obter mais informações, consulte [Introdução aos serviços de nuvem do Azure e ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

| Parâmetro | Valor padrão |
| --- | --- |
| Aliases |Nenhum |
| Obrigatório? |falso |
| Position |nomeado |
| Valor padrão |Nenhum |
| Aceitar entrada do Pipeline? |falso |
| Aceitar caracteres curinga? |falso |

## <a name="databaseserverpassword"></a>DatabaseServerPassword
O nome do administrador e a senha do Banco de Dados SQL no Azure.

| Parâmetro | Valor padrão |
| --- | --- |
| Aliases |Nenhum |
| Obrigatório? |falso |
| Position |nomeado |
| Valor padrão |Nenhum |
| Aceitar entrada do Pipeline? |falso |
| Aceitar caracteres curinga? |falso |

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Se seu valor for true, imprimir mensagens do script para o fluxo de saída.

| Parâmetro | Valor padrão |
| --- | --- |
| Aliases |Nenhum |
| Obrigatório? |falso |
| Position |nomeado |
| Valor padrão |falso |
| Aceitar entrada do Pipeline? |falso |
| Aceitar caracteres curinga? |falso |

## <a name="remarks"></a>Comentários
Para obter uma explicação completa de como usar o script para criar ambientes de desenvolvimento e teste, consulte [Usando scripts do Windows PowerShell para publicar para ambientes de desenvolvimento e teste](vs-azure-tools-publishing-using-powershell-scripts.md).

O arquivo de configuração JSON especifica os detalhes daquilo que está para ser implantado. Ele inclui as informações que você especificou quando criou o projeto, como o nome e também o nome de usuário para o site. Ele também inclui o banco de dados a provisionar, se houver. O código a seguir mostra um exemplo de arquivo de configuração JSON:

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

Você pode editar o arquivo de configuração JSON para alterar o que é implantado. Uma seção de site é obrigatória, mas a seção de banco de dados é opcional.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte [WebApplicationVM de publicação (script do Windows PowerShell)](vs-azure-tools-publish-webapplicationvm.md)

