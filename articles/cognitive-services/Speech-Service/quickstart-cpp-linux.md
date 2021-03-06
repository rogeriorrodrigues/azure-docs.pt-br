---
title: 'Início Rápido: Saiba como reconhecer fala em C++ no Linux usando o SDK de Fala dos Serviços Cognitivos'
titleSuffix: Microsoft Cognitive Services
description: Saiba como reconhecer fala em C++ no Linux usando o SDK de Fala dos Serviços Cognitivos
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 92bd5980ac2e6befbe352df6ddf8644f04d37d34
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126858"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em C++ no Linux usando o SDK de Fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprende como criar um aplicativo de console C++ no Linux (Ubuntu 16.04) usando o SDK de Fala dos Serviços Cognitivos para transcrever conversão de fala em texto.

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura para o serviço de fala. Veja [Experimente o serviço de fala gratuitamente](get-started.md).
* Um PC com Ubuntu 16.04 com um microfone funcionando.
* Para instalar os pacotes necessários para compilar e executar esse exemplo, execute o seguinte:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="get-the-speech-sdk"></a>Obter o SDK de Fala

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Fala dos Serviços Cognitivos é `0.6.0`.

O SDK de Fala dos Serviços Cognitivos para Linux está disponível para criação de aplicativos de 64 bits e 32 bits.
Os arquivos necessários podem ser baixados como um arquivo tar de https://aka.ms/csspeech/linuxbinary.
Baixe e instale o SDK conforme a seguir:

1. Escolha um diretório (caminho absoluto) onde você gostaria de colocar os cabeçalhos e os binários do SDK de fala.
   Por exemplo, escolha o caminho `speechsdk` em seu diretório inicial:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Crie o diretório, se ainda não existir:

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Realize o download e extraia o `.tar.gz` arquivo com os binários SDK de Fala:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Valide o conteúdo do diretório de nível superior do pacote extraído:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Deve mostrar arquivos de notificação e licença de terceiros, bem como um diretório `include` para cabeçalhos e um diretório `lib` para bibliotecas.

   [!INCLUDE [Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Adicione o código a seguir em um arquivo nomeado`helloworld.cpp`:

  [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

## <a name="building"></a>Construção

> [!NOTE]
> Certifique-se de copiar e colar os comandos de compilação abaixo como uma _única linha_.

* Em um computador **x64**, execute o comando a seguir para compilar o aplicativo:

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* Em um computador **x86**, execute o comando a seguir para compilar o aplicativo:

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="run-the-sample"></a>Execute o exemplo

1. Defina a configuração do caminho da biblioteca do carregador para apontar para a biblioteca do SDK de Fala.

   * Em um computador **x64**, execute:

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * Em um computador **x86**, execute:

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. Execute o aplicativo conforme a seguir:

   ```sh
   ./helloworld
   ```

1. Você deve ver saídas semelhantes às seguintes:

   ```text
   Say something...
   We recognized: What's the weather
   ```

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure esse exemplo na pasta `quickstart/cpp-linux`.

## <a name="next-steps"></a>Próximas etapas

* [Obtenha nossas amostras](speech-sdk.md#get-the-samples)
