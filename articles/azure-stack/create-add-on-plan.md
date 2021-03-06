---
title: Neste artigo, você aprenderá a atualizar planos e ofertas do Azure Stack | Microsoft Docs
description: Este artigo descreve como exibir e modificar planos e ofertas do Azure Stack existentes.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 07/30/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a35ba993e6fd1162fa4a18bc0d6bc9351fe7dfa2
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358242"
---
# <a name="azure-stack-add-on-plans"></a>Planos de complemento de pilha do Azure

Como um operador do Azure Stack, você cria planos de complemento para modificar uma [ *plano base* ](azure-stack-create-plan.md) quando você deseja oferecer serviços adicionais ou estender *computador*, *armazenamento* , ou *rede* cotas além da oferta inicial de planos de base. Planos de complemento modificar o plano de base e as extensões opcionais que os usuários podem escolher assinar. 

Há ocasiões em que a combinação de tudo em um único plano é ideal. Outras vezes, você talvez queira ter uma base de plano e oferta, em seguida, os serviços adicionais por meio de planos de complemento. Por exemplo, você poderia optar por oferecer serviços de IaaS como parte de um plano de base, com todos os serviços de PaaS, tratados como planos de complemento.

Outro motivo para usar planos de complemento é ajudar os usuários Lembre-se de que o uso de recursos. Para fazer isso, você pode começar com um plano de base que inclui cotas relativamente pequenas (dependendo dos serviços necessários). Em seguida, como os usuários atingem a capacidade, eles seriam sejam alertados de que eles consumiu a alocação de recursos com base em seu plano atribuído. A partir daí, os usuários, em seguida, pode selecionar um plano de complemento que fornece os recursos adicionais.

> [!NOTE]
> Quando você não quiser usar um plano de complemento para estender uma cota, você também pode optar por [editar a configuração original da cota](azure-stack-quota-types.md#to-edit-a-quota). 

Quando um usuário adiciona um plano de complemento a uma assinatura existente da oferta, os recursos adicionais pode levar até uma hora para aparecer. 

## <a name="create-an-add-on-plan"></a>Criar um plano de complemento
Planos de complemento são criados, modificando uma oferta existente:

1. Entre no portal do administrador do Azure Stack como um administrador de nuvem.
2. Siga as mesmas etapas usadas para [criar um novo plano base](azure-stack-create-plan.md) para criar um novo plano de oferta de serviços que não foram anteriormente oferecidos. Neste exemplo, serviços do Key Vault (Microsoft. keyvault) serão incluídos no novo plano.
3. No portal do administrador, clique em **oferece** e, em seguida, selecione a oferta a ser atualizada com um plano de complemento.

   ![](media/create-add-on-plan/1.PNG)

4.  Role até a parte inferior das propriedades de oferta e selecione **planos de complemento**. Clique em **Adicionar**.
   
    ![](media/create-add-on-plan/2.PNG)

5. Selecione o plano para adicionar. Neste exemplo, o plano é chamado **plano do Cofre de chave**. Depois de selecionar o plano, clique em **selecionar** para adicionar o plano à oferta. Você deve receber uma notificação de que o plano foi adicionado com êxito para a oferta.
   
    ![](media/create-add-on-plan/3.PNG)

6. Examine a lista de complemento planos incluídos com a oferta para verificar que o novo complemento planeje listados.
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Próximas etapas
[Criar uma oferta](azure-stack-create-offer.md)