---
title: Perguntas frequentes sobre o API de Sugestão Automática do Azure | Microsoft Docs
description: Obtenha respostas para perguntas comuns sobre a API de Sugestão Automática dos Serviços Cognitivos do Azure no Azure.
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 00b91728bcfec52ff30697f080d5c2619bab79a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364382"
---
# <a name="frequently-asked-questions-faq-about-autosuggest-api-cognitive-services"></a>Perguntas frequentes sobre a API de Sugestão Automática (Serviços Cognitivos)
 
 Encontre respostas para perguntas frequentes sobre conceitos, código e cenários relacionados à API de Sugestão Automática dos Serviços Cognitivos do Azure.

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>Como fazer para obter os cabeçalhos opcionais do cliente ao chamar a API de Sugestão Automática do Bing por meio do JavaScript?

Os cabeçalhos a seguir são opcionais, mas recomendamos que sejam tratados como obrigatórios. Esses cabeçalhos ajudam a API de Sugestão Automática do Bing a retornar resultados mais precisos.

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

No entanto, quando você chama a API de Sugestão Automática do Bing por meio do JavaScript, os recursos de segurança interna do navegador podem impedi-lo de acessar os valores desses cabeçalhos.

Para resolver esse problema, faça a solicitação da API de Sugestão Automática do Bing por meio de um proxy CORS. A resposta desse proxy tem um cabeçalho `Access-Control-Expose-Headers` que inclui os cabeçalhos de resposta na lista de permissões e disponibiliza-os para o JavaScript.

É fácil instalar um proxy CORS para permitir que nosso [aplicativo de tutorial](tutorials/autosuggest.md) acesse os cabeçalhos opcionais do cliente. Primeiro, caso ainda não tenha, [instale o Node.js](https://nodejs.org/en/download/). Em seguida, insira o comando a seguir em um prompt de comando.

    npm install -g cors-proxy-server

Em seguida, altere o ponto de extremidade da API de Sugestão Automática do Bing no arquivo HTML para:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Por fim, inicie o proxy CORS com o seguinte comando:

    cors-proxy-server

Deixe a janela Comando aberta enquanto usa o aplicativo de tutorial, porque se você fechá-la, isso interromperá o proxy. Na seção expansível Cabeçalhos HTTP abaixo dos resultados da pesquisa, agora você pode ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e verificar se é o mesmo para cada solicitação.

## <a name="next-steps"></a>Próximas etapas

A sua pergunta é sobre a falta de um recurso ou funcionalidade? Considere solicitar ou votar nisso em nosso [site UserVoice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Consulte também

- [Stack Overflow: Serviços Cognitivos](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
