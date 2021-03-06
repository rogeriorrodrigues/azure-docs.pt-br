---
title: Introdução às Conexões Híbridas de Retransmissão do Azure de pedidos HTTP no Nó | Microsoft Docs
description: Escreva um aplicativo de console Node.js para Conexões Híbridas da Retransmissão do Azure de pedidos HTTP em Nó.
services: service-bus-relay
documentationcenter: node
author: clemensv
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 05/02/2018
ms.author: clemensv
ms.openlocfilehash: 2bc923650425c76562161dd6f44f3a5722b5cefe
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630438"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Introdução às Conexões Híbridas de Retransmissão de pedidos HTTP no Nó

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Este tutorial fornece uma introdução ao [Conexões Híbridas de Retransmissão do Azure em Pedidos HTTP](relay-what-is-it.md#hybrid-connections)e mostra como usar o Node.js para criar um aplicativo cliente que envia mensagens para um aplicativo de escuta correspondente.

## <a name="what-will-be-accomplished"></a>O que será realizado

Já que as Conexões Híbridas exigem um componente de cliente e de servidor, crie dois aplicativos de console neste tutorial. Siga estas etapas:

1. Criar um namespace de retransmissão usando o Portal do Azure.
2. Criar uma conexão híbrida usando o portal do Azure.
3. Escrever um aplicativo de console do servidor para enviar mensagens.
4. Escrever um aplicativo de console de cliente para enviar mensagens.

## <a name="prerequisites"></a>pré-requisitos

1. [Node.js](https://nodejs.org/en/).
2. Uma assinatura do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Criar um namespace usando o portal do Azure

Se você já tiver um namespace de retransmissão criado, vá até a seção [Criar uma conexão híbrida usando o portal do Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Criar uma conexão híbrida usando o portal do Azure

Se você já tiver uma conexão híbrida criada, vá até a seção [Criar um aplicativo de servidor](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Criar um aplicativo de servidor (escuta)

Para escutar e receber mensagens da retransmissão, grave um aplicativo de console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Criar um aplicativo de cliente (remetente)

Para enviar mensagens à Retransmissão, você pode usar qualquer cliente HTTP ou gravar um aplicativo de console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Executar os aplicativos

1. Execute o aplicativo de servidor: de um tipo de prompt de comando `node listener.js` do Node.js.
2. Execute o aplicativo cliente: de um prompt de comando do Node.js, digite `node sender.js` e insira um texto.
3. Certifique-se de que o console do aplicativo de servidor exiba o texto inserido no aplicativo de cliente.

Parabéns, você criou um aplicativo de Conexões Híbridas de ponta a ponta usando o Node.js!

## <a name="next-steps"></a>Próximas etapas

* [Perguntas frequentes sobre retransmissão](relay-faq.md)
* [Criar um namespace](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)

