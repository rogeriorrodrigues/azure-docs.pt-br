---
title: Referência de configurações de aplicativo para Azure Functions
description: Documentação de referência para as configurações de aplicativo ou variáveis de ambiente do Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/22/2018
ms.author: glenga
ms.openlocfilehash: 46c1cb0a0cb3104e3705e4a7d4ef0dd894a7c2d7
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819039"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referência de configurações de aplicativo para Azure Functions

As configurações de aplicativo em um aplicativo de funções contém opções de configuração global que afetam todas as funções desse aplicativo de funções. Quando você executa localmente, essas configurações estão em variáveis de ambiente. Este artigo lista as configurações de aplicativo disponíveis nos aplicativos de funções.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md]

Há outras opções de configuração global no arquivo [host.json](functions-host-json.md) e no arquivo [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Chave de instrumentação do Application Insights se você estiver usando o Application Insights. Consulte [Monitorar Azure Functions](functions-monitoring.md).

|Chave|Valor de exemplo|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Cadeia de conexão da conta de armazenamento opcional para armazenar logs e exibi-los na guia **Monitor** do portal. A conta de armazenamento deve ser de uso geral, com suporte para blobs, filas e tabelas. Consulte [Conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e [Requisitos da conta de armazenamento](functions-create-function-app-portal.md#storage-account-requirements).

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` significa desabilitar a página de aterrissagem padrão mostrada para a URL raiz de um aplicativo de funções. O padrão é `false`.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsDisableHomepage|verdadeiro|

Quando essa configuração de aplicativo é omitida ou definida como `false`, uma página semelhante ao exemplo a seguir é exibida na resposta à URL `<functionappname>.azurewebsites.net`.

![Página de aterrissagem do aplicativo de funções](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` significa usar o modo de versão durante a compilação do código .NET; `false` significa usar o modo de depuração. O padrão é `true`.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|verdadeiro|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Uma lista delimitada por vírgulas de recursos beta a habilitar. Os recursos beta habilitados por esses sinalizadores não estão prontos para produção, mas podem ser habilitados para uso experimental antes de serem lançados.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

O caminho para o diretório raiz onde o arquivo *host.json* e as pastas de funções estão localizados. Em um aplicativo de funções, o padrão é `%HOME%\site\wwwroot`.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsScriptRoot|%HOME%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Especifica o repositório ou o provedor a ser usado para armazenar chaves. Atualmente, os repositórios com suporte são blob ("Blob") e sistema de arquivos ("desabilitado"). O padrão é sistema de arquivos ("desabilitado").

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsSecretStorageType|desabilitado|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

O tempo de execução do Azure Functions usa essa cadeia de conexão da conta armazenamento para todas as funções, exceto para as funções disparadas por HTTP. A conta de armazenamento deve ser de uso geral, com suporte para blobs, filas e tabelas. Consulte [Conta de armazenamento](functions-infrastructure-as-code.md#storage-account) e [Requisitos da conta de armazenamento](functions-create-function-app-portal.md#storage-account-requirements).

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

Caminho para o compilador usado para TypeScript. Permite substituir o padrão se necessário.

|Chave|Valor de exemplo|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="functionappeditmode"></a>FUNCTION\_APP\_EDIT\_MODE

Os valores válidos são "readwrite" e "readonly".

|Chave|Valor de exemplo|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functionsextensionversion"></a>FUNCTIONS\_EXTENSION\_VERSION

A versão do tempo de execução do Azure Functions para usar nesse aplicativo de funções. Um til com a versão principal significa usar a versão mais recente da versão principal (por exemplo, "~1"). Quando novas versões da mesma versão principal estão disponíveis, elas são instaladas automaticamente no aplicativo de funções. Para fixar o aplicativo a uma versão específica, use o número de versão completo (por exemplo, "1.0.12345"). O padrão é "~1".

|Chave|Valor de exemplo|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~1|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Apenas para planos de consumo. Cadeia de conexão para a conta de armazenamento na qual o código do aplicativo de funções e a configuração são armazenados. Consulte [Criar um aplicativo de funções](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="websitecontentshare"></a>WEBSITE\_CONTENTSHARE

Apenas para planos de consumo. O caminho do arquivo para o código do aplicativo de funções e a configuração. Usado com WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. O padrão é uma cadeia única que começa com o nome do aplicativo de funções. Consulte [Criar um aplicativo de funções](functions-infrastructure-as-code.md#create-a-function-app).

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

O número máximo de instâncias que o aplicativo de funções pode alcançar. O padrão é sem limites.

> [!NOTE]
> Essa configuração é para um recurso de visualização.

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|10|

## <a name="websitenodedefaultversion"></a>WEBSITE\_NODE\_DEFAULT_VERSION

O padrão é "6.5.0".

|Chave|Valor de exemplo|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|6.5.0|

## <a name="websiterunfromzip"></a>EXECUTAR\_SITE\_DO\_ZIP

Permite que seu aplicativo de funções execute de um arquivo de pacote montado.

> [!NOTE]
> Essa configuração é para um recurso de visualização.

|Chave|Valor de exemplo|
|---|------------|
|EXECUTAR\_SITE\_DO\_ZIP|1|

Os valores válidos são `1` ou uma URL que resolve para o local de um arquivo de pacote de implantação. Quando definido como `1`, o pacote deve estar na pasta `d:\home\data\SitePackages`. Ao usar a implantação em zip com essa configuração, o pacote é automaticamente carregado para esse local.  Para obter mais informações, veja [Executar suas funções de um arquivo de pacote](run-functions-from-deployment-package.md).

## <a name="next-steps"></a>Próximas etapas

[Saiba como atualizar as configurações do aplicativo](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

[Consulte as configurações globais no arquivo host.json](functions-host-json.md)

[Consulte outras configurações de aplicativo para aplicativos do Serviço de Aplicativo](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
