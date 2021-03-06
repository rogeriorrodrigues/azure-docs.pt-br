---
title: Criar um aplicativo simples com duas intenções - Azure | Microsoft Docs
description: Saiba como criar um aplicativo LUIS simples usando intenções e nenhuma entidades para identificar enunciados neste início rápido.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 3f23ade2b0256c72c344e2a619227a79e3c79a47
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160108"
---
# <a name="tutorial-1-build-app-with-custom-domain"></a>Tutorial: 1. Compilar aplicativo com domínio personalizado
Neste tutorial, crie um aplicativo que demonstre como usar **intenções** para determinar a _intenção_ do usuário com base no enunciado (texto) que eles enviam para o aplicativo. Quando terminar, você terá um ponto de extremidade do LUIS em execução na nuvem.

Este aplicativo é o tipo mais simples de aplicativo LUIS porque ele não extrai dados dos enunciados. Ele apenas determina a intenção do enunciado do usuário.

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um novo aplicativo para um domínio de Recursos Humanos (RH) 
> * Adicionar a intenção GetJobInformation
> * Adicione enunciados de exemplo para a intenção GetJobInformation 
> * Treinar e publicar o aplicativo
> * Consulte ponto de extremidade do aplicativo para ver a resposta JSON do LUIS
> * Adicionar a intenção ApplyForJob
> * Adicione enunciados de exemplo para a intenção ApplyForJob 
> * Treinar, publicar e consultar novamente o ponto de extremidade 

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="purpose-of-the-app"></a>Finalidade do aplicativo
Este aplicativo possui algumas intenções. A primeira intenção, **`GetJobInformation`**, identifica quando um usuário deseja obter informações sobre trabalhos disponíveis dentro de uma empresa. A segunda intenção **`None`**, identifica todos os outros tipos de enunciado. Posteriormente no guia de início rápido, uma terceira intenção `ApplyForJob`, é adicionada. 

## <a name="create-a-new-app"></a>Criar um novo aplicativo
1. Faça logon no site do [LUIS](luis-reference-regions.md#luis-website). Certifique-se de fazer logon na [região](luis-reference-regions.md#publishing-regions) onde você precisa dos pontos de extremidade de LUIS publicados.

2. No site do [LUIS](luis-reference-regions.md#luis-website), selecione **Criar novo aplicativo**.  

    [![](media/luis-quickstart-intents-only/app-list.png "Captura de tela da página Meus aplicativos")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. Na caixa de diálogo pop-up, insira o nome `HumanResources`. Este aplicativo aborda perguntas sobre o departamento de Recursos Humanos da sua empresa. Esse tipo de departamento lida com problemas relacionados ao emprego, como cargos da empresa que precisam ser preenchidos.

    ![Novo aplicativo LUIS](./media/luis-quickstart-intents-only/create-app.png)

4. Após a conclusão desse processo, o aplicativo mostrará a página **Intenções** com a intenção **None**. 

## <a name="create-getjobinformation-intention"></a>Criar a intenção GetJobInformation
1. Selecione **Criar nova intenção**. Insira o nome da nova intenção `GetJobInformation`. Essa intenção é prevista sempre que um usuário deseja obter informações sobre vagas abertas em sua empresa.

    ![](media/luis-quickstart-intents-only/create-intent.png "Captura de tela da caixa de diálogo Nova intenção")

    Ao criar uma intenção, você está criando um categoria de informações que você deseja identificar. Ao nomear a categoria, qualquer outro aplicativo que use os resultados de consulta do LUIS poderá usar esse nome de categoria para encontrar uma resposta apropriada. O LUIS não responde a essas perguntas, apenas identifica o tipo de informação que está sendo solicitado em linguagem natural. 

2. Adicione sete enunciados a essa intenção a qual você espera que um usuário solicite, por exemplo:

    | Exemplo de enunciados|
    |--|
    |Nenhuma nova vaga publicada hoje?|
    |Quais cargos estão disponíveis para engenheiros seniores?|
    |Há algum trabalho com bancos de dados?|
    |Procurando uma nova vaga com responsabilidades de contabilidade|
    |Onde estão as listagens de vagas|
    |Novas vagas?|
    |Existe alguma nova vaga no escritório de Seattle?|

    [![](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Captura de tela da inserção de novos enunciados para o MyStore")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

3. No momento, o aplicativo LUIS não tem enunciados para a intenção **None**. Ele precisa de enunciados que o aplicativo não responde. Não deixe em branco. Selecione **Intenções** no painel esquerdo. 

4. Selecione a intenção **None**. Adicione três enunciados que o usuário pode inserir, mas que não são relevantes para o seu aplicativo. Se o aplicativo é para suas publicações de vagas de emprego, alguns bons exemplos de enunciados **None** são:

    | Exemplo de enunciados|
    |--|
    |Cachorros que latem demais são irritantes|
    |Peça uma pizza para mim|
    |Pinguins no oceano|

    No aplicativo que chama o LUIS, como um chatbot, se o LUIS retornar a intenção **None** para um enunciado, o bot poderá perguntar se o usuário deseja terminar a conversa. O chatbot também pode fornecer mais orientações para continuar a conversa, se o usuário não quiser encerrá-lo. 

## <a name="train-and-publish-the-app"></a>Treinar e publicar o aplicativo

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>Publicar o aplicativo para o ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="query-endpoint-for-getjobinformation-intent"></a>Ponto de extremidade de consulta para a intenção GetJobInformation

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá até o final da URL no endereço e insira `I'm looking for a job with Natual Language Processing`. O último parâmetro da cadeia de consulta é `q`, a declaração **query**. Esse enunciado não é igual a nenhum dos exemplos de enunciado na etapa 4, portanto, ele é um bom teste e deve retornar a intenção `GetJobInformation` como a intenção com maior pontuação. 

    ```
    {
      "query": "I'm looking for a job with Natual Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.8965092
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.8965092
        },
        {
          "intent": "None",
          "score": 0.147104025
        }
      ],
      "entities": []
    }
    ```

## <a name="create-applyforjob-intention"></a>Criar intenção ApplyForJob
Volte para a guia do navegador para o site do LUIS e crie uma novo intenção para se candidatar a uma vaga.

1. Selecione **Compilar** no menu no canto superior direito para retornar  parte superior, com o botão direito menu para retornar à compilação de aplicativos.

2. Selecione **Intents** no menu esquerdo.

3. Selecione **Criar nova intenção** e insira o nome `ApplyForJob`. 

    ![Caixa de diálogo LUIS para criar a nova intenção](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

4. Adicione vários enunciados a essa intenção a qual você espera que um usuário solicite, por exemplo:

    | Exemplo de enunciados|
    |--|
    |Desejo me candidatar para o novo trabalho de contabilidade|
    |Preencher o aplicativo para a vaga 123456|
    |Enviar currículo para o cargo de engenharia|
    |Este é o meu CV para a vaga 654234|
    |Vaga 567890 e minha papelada|

    [![](media/luis-quickstart-intents-only/utterance-applyforjob.png "Captura de tela da inserção de novos enunciados para a intenção ApplyForJob")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    A intenção rotulada é contornada em vermelho porque LUIS não está certo de que esta é a intenção correta. Treinar o aplicativo informa LUIS que os enunciados estão na intenção correta. 

    [Treinar e publicar](#train-and-publish-the-app) novamente. 

## <a name="query-endpoint-for-applyforjob-intent"></a>Ponto de extremidade de consulta para a intenção ApplyForJob

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na nova janela do navegador, digite `Can I submit my resume for job 235986` no final da URL. 

    ```
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9166808
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9166808
        },
        {
          "intent": "GetJobInformation",
          "score": 0.07162977
        },
        {
          "intent": "None",
          "score": 0.0262826588
        }
      ],
      "entities": []
    }
    ```

## <a name="what-has-this-luis-app-accomplished"></a>O que esse aplicativo de LUIS realizou?
Este aplicativo, com apenas algumas intenções, identificou uma consulta de linguagem natural da mesma intenção, mas identificada por outro nome. 

O resultado em JSON identifica a intenção com maior pontuação. Todas as pontuações estão entre 1 e 0, com a melhor pontuação mais próxima a 1. A pontuação das intenções `GetJobInformation` e `None` está muito próxima de zero. 

## <a name="where-is-this-luis-data-used"></a>Onde esses dados do LUIS são usados? 
O LUIS é feito com essa solicitação. O aplicativo de chamada, como um chatbot, pode levar o resultado de topScoringIntent e localizar informações (não armazenadas no LUIS) para responder a perguntas ou terminar a conversa. Estas são opções programáticas para o bot ou aplicativo de chamada. O LUIS não faz esse trabalho. O LUIS só determina qual é a intenção do usuário. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar intenções e entidades predefinidas a este aplicativo](luis-tutorial-prebuilt-intents-entities.md)
