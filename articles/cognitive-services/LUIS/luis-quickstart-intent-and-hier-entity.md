---
title: Tutorial para criar um aplicativo LUIS para obter dados de localização - Azure | Microsoft Docs
description: Neste tutorial, saiba como criar um aplicativo de LUIS simples usando intenções e uma entidade hierárquica para extrair dados.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 65c7aabb984ad0a6b3e77d0f98003803821e06cc
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158612"
---
# <a name="tutorial-5-add-hierarchical-entity"></a>Tutorial: 5. Adicionar entidade hierárquica
Neste tutorial, crie um aplicativo que demonstra como localizar as partes relacionadas dos dados com base no contexto. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Entender as entidades hierárquicas e filhos contextualmente adquiridos 
> * Usar o aplicativo LUIS no domínio de RH (recursos humanos) 
> * Adicionar entidade hierárquica local com os filhos de origem e destino
> * Treinar e publicar o aplicativo
> * Consulte o ponto de extremidade do aplicativo para ver a resposta JSON do LUIS incluindo os filhos hierárquicos 

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Antes de começar
Caso não tenha o aplicativo de recursos humanos do tutorial de [entidades de lista](luis-quickstart-intent-and-list-entity.md), [importe](luis-how-to-start-new-app.md#import-new-app) o JSON em um aplicativo novo no site do [LUIS](luis-reference-regions.md#luis-website). O aplicativo a ser importado pode ser encontrado no repositório Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-list-HumanResources.json).

Caso queira manter o aplicativo de recursos humanos original, clone a versão na página [Configurações](luis-how-to-manage-versions.md#clone-a-version) e nomeie-a como `hier`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. 

## <a name="purpose-of-the-app-with-this-entity"></a>Finalidade do aplicativo com essa entidade
Esse aplicativo determina para onde o funcionário deve ser movido, do local de origem (prédio e sala) para o local de destino (prédio e sala). Ele usa a entidade hierárquica para determinar os locais dentro do enunciado. 

A entidade hierárquica é uma boa opção para esse tipo de dado porque as duas partes dos dados:

* Estão relacionadas entre si no contexto do enunciado.
* Usam escolhas de palavras específicas para indicar cada local. Exemplos dessas palavras: de/para, deixando/indo, saindo/entrando.
* Os locais estão frequentemente no mesmo enunciado. 

A finalidade da entidade **hierárquica** é encontrar dados relacionados dentro do enunciado com base no contexto. Considere o enunciado a seguir:

```JSON
mv Jill Jones from a-2349 to b-1298
```
O enunciado tem dois locais especificados, `a-2349` e `b-1298`. Suponha que a letra corresponda a um nome de prédio e o número indique a sala dentro desse prédio. Faz sentido que são ambos sejam agrupados como filhos de uma entidade hierárquica, `Locations`, pois ambas as partes de dados precisam ser extraídas do enunciado e elas estão relacionadas umas às outras. 
 
Se apenas um filho (origem ou destino) de uma entidade hierárquica estiver presente, ele ainda assim é extraído. Não é necessário que se tenha todos filhos para que seja extraído um ou alguns. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Remover entidade número predefinida do aplicativo
Para ver a expressão inteira e marcar os filhos hierárquicos, remova temporariamente a entidade de número predefinida.

1. Verifique se o seu aplicativo de recursos humanos está na seção **Compilar** do LUIS. Você pode alterar essa seção selecionando **Compilar** na barra de menus da parte superior direita. 

2. Selecione **Entidades** no menu à esquerda.

3. Selecione o botão de reticências (***...***) à direita da entidade número na lista. Selecione **Excluir**. 

    [ ![Captura de tela do aplicativo LUIS na página de lista de entidades, com o botão de exclusão realçado para a entidade pré-compilada Número](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png)](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png#lightbox)


## <a name="add-utterances-to-moveemployee-intent"></a>Adicionar enunciados à intenção MoveEmployee

1. Selecione **Intents** no menu esquerdo.

2. Selecione **MoveEmployee** na lista de intenções.

    [ ![Captura de tela do aplicativo LUIS com a intenção MoveEmployee realçada no menu esquerdo](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png)](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png#lightbox)

3. Adicione os seguintes enunciados de exemplo:

    |Exemplo de enunciados|
    |--|
    |Mover John W. Smith **para** a-2345|
    |Direcionar Jones Jill **para** b-3499|
    |Organizar a movimentação de x23456 **de** hh-2345 **para** e-0234|
    |Começar a documentação para definir x12345 **deixando** a-3459 **em direção a** f-34567|
    |Deslocar 425-555-0000 **de** g-2323 **para** hh-2345|

    [ ![Captura de tela do LUIS com novos enunciados na intenção MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

    No tutorial de [entidade de lista](luis-quickstart-intent-and-list-entity.md), um funcionário pode ser designado por nome, endereço de email, ramal do telefone, número de telefone celular ou número do seguro social. Esses números de funcionário são usados nos enunciados. Os enunciados do exemplo anterior incluem formas de anotar os locais de origem e destino, marcados em negrito. Alguns enunciados têm somente destinos propositalmente. Isso ajuda o LUIS a compreender como esses locais são colocados no enunciado quando a origem não é especificada.     

## <a name="create-a-location-entity"></a>Criar uma entidade de local
LUIS precisa entender o que é um local rotulando a origem e o destino nos enunciados. Se você precisar ver a expressão no modo de exibição de token (bruto), selecione a alternância na barra acima dos enunciados rotulada **Modo de Exibição de Entidades**. Depois de alternar a opção, o controle é rotulado **exibição Tokens**.

1. No enunciado, `Displace 425-555-0000 away from g-2323 toward hh-2345`, selecione a palavra `g-2323`. Um menu suspenso é exibido com uma caixa de texto na parte superior. Insira o nome da entidade `Locations` na caixa de texto, selecione **Criar nova entidade** no menu suspenso. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "Captura de tela da criação de nova entidade na página de intenção")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. Na janela pop-up, selecione o tipo de entidade **Hierárquica** com `Origin` e `Destination` como as entidades filho. Selecione **Concluído**.

    ![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Captura de tela do diálogo pop-up de criação de entidade para a nova entidade de Local")

3. O rótulo de `g-2323` está marcado como `Locations` porque o LUIS não sabe se o termo foi a origem, o destino ou nenhum deles. Selecione `g-2323`; em seguida, selecione **Locais**, siga o menu à direita e selecione `Origin`.

    [![](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "Captura de tela do diálogo pop-up de rotulação de entidade para alterar o filho da entidade Locais")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. Rotule os outros locais em todos os outros enunciados selecionando o prédio e a sala no enunciado, selecionando Locais e seguindo o menu à direita para selecionar `Origin` ou `Destination`. Quando todos os locais estiverem rotulados, os enunciados no **Modo de Exibição de Tokens** começará a mostrar um padrão. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "Captura de tela da entidade Locais rotulada nos enunciados")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>Adicionar entidade de número predefinida ao aplicativo
Adicione a entidade numérica predefinida novamente ao aplicativo.

1. Selecione **Entidades** no menu de navegação à esquerda.

2. Selecione o botão **Gerenciar entidades predefinidas**.

    [ ![Captura de tela da lista Entidades com a opção Gerenciar entidades predefinidas realçada](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png#lightbox)

3. Selecione **número** na lista de entidades predefinidas, e selecione **Concluído**.

    ![Captura de tela do número selecionado no diálogo de entidades predefinidas](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>Treinar o aplicativo LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicar o aplicativo para obter a URL do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Consultar o ponto de extremidade com um enunciado diferente

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


2. Vá até o final da URL na barra de endereços e insira `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`. O último parâmetro de querystring é `q`, o enunciado **consulta**. Esse enunciado não é igual a nenhum dos enunciados rotulados, portanto, ele é um bom teste e deve retornar a intenção `MoveEmployee` com a entidade hierárquica extraída.

  ```JSON
  {
    "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9966052
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9966052
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0325253047
      },
      {
        "intent": "FindForm",
        "score": 0.006137873
      },
      {
        "intent": "GetJobInformation",
        "score": 0.00462633232
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00415637763
      },
      {
        "intent": "ApplyForJob",
        "score": 0.00382325822
      },
      {
        "intent": "Utilities.Help",
        "score": 0.00249120337
      },
      {
        "intent": "None",
        "score": 0.00130756292
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00119622645
      },
      {
        "intent": "Utilities.Confirm",
        "score": 1.26910036E-05
      }
    ],
    "entities": [
      {
        "entity": "jill - jones @ mycompany . com",
        "type": "Employee",
        "startIndex": 18,
        "endIndex": 41,
        "resolution": {
          "values": [
            "Employee-45612"
          ]
        }
      },
      {
        "entity": "x - 2345",
        "type": "Locations::Origin",
        "startIndex": 48,
        "endIndex": 53,
        "score": 0.8520272
      },
      {
        "entity": "g - 23456",
        "type": "Locations::Destination",
        "startIndex": 58,
        "endIndex": 64,
        "score": 0.974032
      },
      {
        "entity": "-2345",
        "type": "builtin.number",
        "startIndex": 49,
        "endIndex": 53,
        "resolution": {
          "value": "-2345"
        }
      },
      {
        "entity": "-23456",
        "type": "builtin.number",
        "startIndex": 59,
        "endIndex": 64,
        "resolution": {
          "value": "-23456"
        }
      }
    ]
  }
  ```

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Uma expressão regular poderia ser usada para cada local?
Sim, crie a expressão regular com funções de origem e destino e use-a em um padrão.

Os locais neste exemplo, como `a-1234`, seguem um formato específico de uma ou duas letras com um traço e, em seguida, uma série de 4 ou 5 números. Esses dados podem ser descritos como uma entidade de expressão regular com uma função para cada local. As funções estão disponíveis para padrões. Você pode criar padrões com base nesses enunciados, criar uma expressão regular para o formato de local e adicioná-la aos padrões. <!-- Go to this tutorial to see how that is done -->

## <a name="patterns-with-roles"></a>Padrões com funções

[!INCLUDE [LUIS Compare hierarchical entities to patterns with roles](../../../includes/cognitive-services-luis-hier-roles.md)]

## <a name="what-has-this-luis-app-accomplished"></a>O que esse aplicativo de LUIS realizou?
Esse aplicativo, com apenas algumas intenções e uma entidade hierárquica, identificou uma intenção de consulta de linguagem natural e retornou os dados extraídos. 

Agora seu chatbot tem informações suficientes para determinar a ação primária, `MoveEmployee`, e as informações de localização encontradas no enunciado. 

## <a name="where-is-this-luis-data-used"></a>Onde esses dados do LUIS são usados? 
O LUIS é feito com essa solicitação. O aplicativo de chamada, como um chatbot, pode levar o resultado de topScoringIntent e os dados da entidade para realizar a próxima etapa. O LUIS não realiza esse trabalho de programação para o bot ou para o aplicativo de chamada. O LUIS só determina qual é a intenção do usuário. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Saiba como adicionar uma entidade de composição](luis-tutorial-composite-entity.md) 