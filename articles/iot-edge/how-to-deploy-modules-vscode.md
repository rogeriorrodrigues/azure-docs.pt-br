---
title: Implantar módulos do Azure IoT Edge (VS Code) | Microsoft Docs
description: Usar o Visual Studio Code para implantar módulos em um dispositivo do IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5b43f81cbb3bbebb231a8a9738f6138b62ef7f6
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046022"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Implantar módulos do Azure IoT Edge do Visual Studio Code

Após criar os módulos do IoT Edge com a lógica de negócios, você pretende implantá-los nos dispositivos para operar na borda. Se houver vários módulos trabalhando em conjunto para coletar e processar dados, será possível implantá-los todos de uma vez e declarar as regras de roteamento que conecta-os. 

Este artigo mostra como criar um manifesto de implantação JSON e usar esse arquivo para enviar a implantação a um dispositivo do IoT Edge. Para obter informações sobre como criar uma implantação voltada a vários dispositivos com base nas marcações compartilhadas, consulte [Implantar e monitorar módulos do IoT Edge em escala](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Pré-requisitos

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na assinatura do Azure. 
* Um [Dispositivo do IoT Edge](how-to-register-device-portal.md) com o tempo de execução do IoT Edge instalado. 
* [Visual Studio Code](https://code.visualstudio.com/).
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) para Visual Studio Code. 

## <a name="configure-a-deployment-manifest"></a>Configurar um manifesto de implantação

Um manifesto de implantação é um documento JSON que descreve quais módulos implantar, como os dados fluem entre os módulos e as propriedades desejadas dos módulos gêmeos. Para obter mais informações sobre como os manifestos de implantação funcionam e como criá-los, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

Para implantar módulos usando o Visual Studio Code, salve o manifesto de implantação localmente como um arquivo .JSON. Você usará o caminho do arquivo na próxima seção quando executar o comando para aplicar a configuração ao dispositivo.

A seguir, é apresentado um manifesto básico de implantação com um módulo como um exemplo:

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "tempSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "tempSensor": {
         "properties.desired": {}
       }
     }
   }
   ```
   
## <a name="sign-in-to-access-your-iot-hub"></a>Entre para acessar o Hub IoT

É possível usar as extensões do Azure IoT para Visual Studio Code para executar operações com o Hub IoT. Para que essas operações funcionem, é necessário entrar na conta do Azure e selecionar o hub IoT em que está trabalhando.

1. No Visual Studio Code, abra a exibição do **Explorer**.

2. Na parte inferior do Explorer, expanda a seção **Dispositivos do Hub IoT**. 

   ![Expanda Dispositivos do Azure Hub IoT](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

3. Clique em **...** no cabeçalho da seção **Dispositivos do Hub IoT**. Se você não visualizar as reticências, passe o mouse sobre o cabeçalho. 

4. Escolha **Selecionar Hub IoT**.

5. Se não estiver conectado à conta do Azure, siga as instruções para fazer isso. 

6. Selecione sua assinatura do Azure. 

7. Selecione seu Hub IoT. 


## <a name="deploy-to-your-device"></a>Implantar no seu dispositivo

Você implanta módulos no dispositivo, aplicando o manifesto de implantação configurado com as informações do módulo. 

1. Na exibição do Explorer do Visual Studio Code, expanda a seção **Dispositivos do Hub IoT**. 

2. Clique com o botão direito do mouse no dispositivo que você quer configurar com o manifesto de implantação. 

3. Selecione **Criar implantação para dispositivo único**. 

4. Navegue até o arquivo JSON do manifesto de implantação que você quer usar e clique em **Selecionar manifesto de implantação do Edge**. 

   ![Selecionar manifesto de implantação do Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)


Os resultados da implantação são impressos na saída do VS Code. Implantações com êxito são aplicadas em poucos minutos se o dispositivo de destino estiver em execução e conectado à Internet. 

## <a name="view-modules-on-your-device"></a>Exibir módulos no dispositivo

Após implantar os módulos no dispositivo, será possível exibir todos eles na seção **Dispositivos do Hub IoT**. Selecione a seta ao lado do dispositivo do IoT Edge para expandi-lo. Todos os módulos atualmente em execução serão exibidos. 

Se você implantou recentemente novos módulos em um dispositivo, passe o mouse sobre o cabeçalho da seção **Dispositivos do Hub IoT** e selecione o ícone de atualização para atualizar a exibição. 

Clique com o botão direito do mouse no nome de um módulo para exibir e editar o módulo gêmeo. 

## <a name="next-steps"></a>Próximas etapas

Saiba como [Implantar e monitorar módulos do IoT Edge em escala](how-to-deploy-monitor.md)
