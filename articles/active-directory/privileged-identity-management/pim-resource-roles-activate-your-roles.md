---
title: Ativar minhas funções de recurso do Azure no PIM | Microsoft Docs
description: Saiba como ativar suas funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 08/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 234c1d71f0ec17d15a4dd589e3db92fd9bf68df2
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189482"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Ativar minhas funções de recurso do Azure no PIM
O Privileged Identity Management (PIM) apresenta uma nova experiência em ativar funções para recursos do Azure. Membros de função qualificados podem agendar a ativação para uma data e hora no futuro. Eles também podem selecionar uma duração de ativação específica até o valor máximo (configurado pelos administradores). Para obter mais informações, consulte [Como ativar ou desativar funções no Azure AD Privileged Identity Management](pim-how-to-activate-role.md).

## <a name="activate-a-role"></a>Ativar uma função
Navegue até a seção **Minhas funções** no painel esquerdo. Selecione **Ativar** para a função que você deseja ativar.

![Guia "Funções qualificadas" no painel "Minhas funções".](media/azure-pim-resource-rbac/rbac-roles.png)

No menu de **Ativações**, insira a data e a hora de início para ativar a função. Você pode optar por diminuir a duração da ativação (o período de tempo durante o qual a função está ativa) e inserir uma justificativa se necessário. Em seguida, selecione **Ativar**.

Se a data de início e a hora não forem modificadas, a função será ativada em alguns segundos. No painel **Minhas funções**, uma mensagem do cabeçalho mostra que uma função está na fila para ativação. Selecione o botão para atualizar e essa mensagem será eliminada.

![Painel de "Minhas funções" com uma mensagem de cabeçalho e uma notificação sobre uma aprovação pendente](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Se a ativação for agendada para uma data e hora futuras, a solicitação pendente será exibida na guia **Solicitações pendentes** do painel esquerdo. Se a ativação de função não for mais necessária, você pode cancelar a solicitação selecionando o botão **Cancelar**.

![Lista de solicitações pendentes com os botões "Cancelar"](media/azure-pim-resource-rbac/rbac-activate-pending.png)

## <a name="use-a-role-immediately-after-activation"></a>Usar uma função imediatamente após a ativação

Devido ao armazenamento em cache, as ativações não ocorrem imediatamente no portal do Azure sem uma atualização. Se precisar reduzir a possibilidade de atrasos depois de ativar uma função, você poderá usar a página **Acesso a aplicativos** no portal. Aplicativos acessados por meio dessa página verificam imediatamente se há novas atribuições de função.

1. Abra o Azure AD Privileged Identity Management.

1. Clique na página **Acesso a aplicativos**.

    ![Acesso do aplicativo PIM – captura de tela](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Clique em **Recursos do Azure** ao reabrir o portal na página **Todos os recursos**.

    Quando clica nesse link, você força uma atualização e há uma verificação em busca de novas atribuições de função de recurso do Azure.

## <a name="apply-just-enough-administration-practices"></a>Aplicar as práticas Administração Just Enough

Usar as práticas recomendadas da Administração Just Enough (JEA) nas suas atribuições de função de recurso é simples com o PIM para recursos do Azure. Usuários e membros de grupo com atribuições nas assinaturas ou grupos de recursos do Azure podem ativar sua atribuição de função existente em escopo reduzido. 

Na página de pesquisa, localize o recurso subordinado que você precisa gerenciar.

![Selecionando um recurso](media/azure-pim-resource-rbac/azure-resources-02.png)

Selecione **Minhas funções** no painel esquerdo e escolha a função apropriada para ativação. O tipo de atribuição é **Herdada**, pois a função foi atribuída na assinatura e não para no grupo de recursos.

![Lista de atribuições de função qualificada, com o tipo de atribuição realçado](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Próximas etapas

- [Ativar minhas funções de diretório do Azure AD no PIM](pim-how-to-activate-role.md)