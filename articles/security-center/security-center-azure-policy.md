---
title: Integração de políticas de segurança da Central de Segurança do Azure com o Azure Policy | Microsoft Docs
description: Este documento ajuda você a configurar a integração de políticas de segurança da Central de Segurança do Azure com o Azure Policy.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2018
ms.author: terrylan
ms.openlocfilehash: b3d6d15d41fece613290deb2c77e980caa5dcfef
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018556"
---
# <a name="integrate-security-center-security-policies-with-azure-policy"></a>Integrar políticas de segurança da Central de Segurança com o Azure Policy
Este artigo ajuda você a configurar as políticas de segurança da Central de Segurança do Azure, fornecidas pelo [Azure Policy](../azure-policy/azure-policy-introduction.md).

## <a name="how-security-policies-work"></a>Como funcionam as políticas de segurança
A Central de Segurança cria automaticamente uma política de segurança padrão para cada uma de suas assinaturas do Azure. Você pode editar as políticas na Central de Segurança ou usar o Azure Policy para fazer o seguinte:
- Criar definições de políticas.
- Atribuir as políticas entre grupos de gerenciamento e assinaturas, que podem representar uma organização inteira ou uma unidade de negócios dentro da organização.
- Monitorar a conformidade da política.

Para obter mais informações sobre o Azure Policy, confira [Criar e gerenciar políticas para impor a conformidade](../azure-policy/create-manage-policy.md).

Uma política do Azure consiste nos seguintes componentes:

- Uma **política** é uma regra
- Uma **iniciativa** é uma coleção de políticas
- Uma **atribuição** é uma aplicação de uma iniciativa ou política a um escopo específico (grupo de gerenciamento, assinatura ou grupo de recursos)

Um recurso é avaliado em relação às políticas atribuídas a ele e recebe uma taxa de conformidade de acordo com o número de políticas com as quais o recurso tem conformidade.

## <a name="edit-security-policies"></a>Editar políticas de segurança
Você pode editar a política de segurança padrão para cada uma de suas assinaturas do Azure e grupos de gerenciamento na Central de Segurança. Para modificar uma política de segurança, você deve ser proprietário, colaborador ou administrador de segurança da assinatura ou do grupo de gerenciamento que a contém. Para exibir suas políticas de segurança na Central de Segurança:

1. No painel **Central de Segurança**, em **POLÍTICA E CONFORMIDADE**, selecione **Política de segurança**. **Gerenciamento de Política** é aberto.

    ![O painel Gerenciamento de Política](./media/security-center-azure-policy/security-center-policies-fig10.png)

  O Gerenciamento de Política exibe o número de grupos de gerenciamento, assinaturas e espaços de trabalho, bem como sua estrutura de grupo de gerenciamento.

  > [!NOTE]
  > O painel Central de Segurança pode mostrar um número maior de assinaturas em **Cobertura de assinatura** em comparação ao número de assinaturas mostrado em **Gerenciamento de Política**. Cobertura de assinatura mostra o número de assinaturas Padrão, Gratuitas e “não cobertas”. As assinaturas “não cobertas” não contêm a Central de Segurança habilitada e não são exibidas em **Gerenciamento de Política**.
  >
  >

  As colunas na tabela são exibidas:

 - Atribuição de Política de Iniciativa – Políticas internas da Central de Segurança e iniciativas atribuídas a uma assinatura ou grupo de gerenciamento.
 - Conformidade – Pontuação de conformidade geral para um grupo de gerenciamento, assinatura ou espaço de trabalho. A pontuação é a média ponderada das atribuições. Os fatores de média ponderada para o número de políticas em uma única atribuição e o número de recursos a que se aplica a atribuição.

 Por exemplo, se sua assinatura tiver duas VMs e uma iniciativa com cinco políticas atribuídas a ela, você tem 10 avaliações em sua assinatura. Se uma das VMs não tiver conformidade com duas das políticas, a pontuação de conformidade geral da atribuição da assinatura é 80%.

 - Cobertura – Identifica o tipo de preço, Gratuito ou Padrão, o grupo de gerenciamento, a assinatura ou o espaço de trabalho em execução.  Confira os [Preços](security-center-pricing.md) para saber mais sobre os tipos de preço da Central de Segurança.
 - Configurações – Assinaturas contêm o link **Editar configurações**. Selecionar **Editar configurações** permite que você atualize suas configurações de assinatura, como coleta de dados, tipo de preço e notificações por email.

2. Selecione a assinatura ou grupo de gerenciamento para o qual deseja habilitar uma política de segurança. A **Política de segurança** abre.

3.  Em **Política de segurança**, selecione os controles que você deseja que a Central de Segurança monitore e forneça recomendações selecionando **Ativar**.  Selecione **Desativar** se não quiser que a Central de Segurança monitore esse controle.

    ![Componentes da política](./media/security-center-azure-policy/security-policy.png)

4. Clique em **Salvar**.

## <a name="available-security-policy-definitions"></a>Definições de política de segurança disponíveis

Para entender as definições de política disponíveis na política de segurança padrão, confira a seguinte tabela:

| Política | O que a política habilitada faz |
| --- | --- |
| Atualizações do sistema |Recupera uma lista diária das atualizações de segurança e críticas do Windows Update ou dos Serviços de Atualização do Windows Server. A lista recuperada depende do serviço configurado para suas máquinas virtuais e recomenda que as atualizações que faltam sejam aplicadas. Para os sistemas Linux, a política usa o sistema de gerenciamento de pacotes fornecido pela distribuição para determinar os pacotes com atualizações disponíveis. Também verifica as atualizações de segurança e críticas das máquinas virtuais dos [Serviços de Nuvem do Azure](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Configurações de segurança |Analisa as configurações do sistema operacional diariamente para determinar os problemas que podem tornar a máquina virtual vulnerável a ataques. A política também recomenda alterações de configuração para tratar essas vulnerabilidades. Para obter mais informações sobre as configurações específicas que estão sendo monitoradas, confira a [lista de linhas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (No momento, não há suporte completo para o Windows Server 2016.) |
| Proteção do ponto de extremidade |Recomenda que seja configurada uma proteção do ponto de extremidade para todas as máquinas virtuais (VMs) do Windows para ajudar a identificar e remover vírus, spyware e outros software mal-intencionados. |
| Criptografia do disco |Recomenda-se habilitar a criptografia de disco em todas as máquinas virtuais para aprimorar a proteção de dados em repouso. |
| Grupos de segurança de rede |Recomenda que os [grupos de segurança da rede](../virtual-network/security-overview.md) sejam configurados para controlar os tráfegos de entrada e saída para as VMs com pontos de extremidade públicos. Os grupos de segurança da rede configurados para uma sub-rede são herdados por todas as interfaces de rede da máquina virtual, a menos que seja especificado o contrário. Além de verificar se um grupo de segurança de rede foi configurado, essa política avalia as regras de segurança de entrada para identificar as regras que permitem o tráfego de entrada. |
| Firewall do aplicativo Web |Recomenda que um firewall do aplicativo Web seja configurado nas máquinas virtuais quando um dos seguintes itens é verdadeiro: <ul><li>Um [IP público em nível de instância](../virtual-network/virtual-networks-instance-level-public-ip.md) é usado, e as regras de segurança de entrada para o grupo de segurança de rede associado são configuradas para permitir o acesso à porta 80/443.</li><li>Um IP de balanceamento de carga é usado, e o balanceamento de carga associado e as regras NAT (conversão do endereço de rede de entrada) são configurados para permitir o acesso à porta 80/443. Para saber mais, confira [Suporte do Azure Resource Manager para Balanceador de Carga](../load-balancer/load-balancer-arm.md).</li> |
| Firewall da próxima geração |Estende as proteções da rede para além dos grupos de segurança da rede, que são internos no Azure. A Central de Segurança descobre as implantações para as quais um firewall da próxima geração é recomendado, então você pode configurar uma solução de virtualização. |
| Auditoria do SQL e detecção de ameaças |Recomenda que a auditoria de acesso ao Banco de Dados do Azure esteja habilitada para conformidade e para detecção de ameaça avançada para fins de investigação. |
| Criptografia do SQL |Recomenda que a criptografia em repouso seja habilitada para o Banco de dados do SQL do Azure, backups associados e arquivos do log de transações. Mesmo que seus dados sejam violados, eles não podem ser lidos. |
| Avaliação de vulnerabilidade |Recomenda que você instale uma solução de avaliação de vulnerabilidade na VM. |
| Criptografia do armazenamento |Atualmente, esse recurso está disponível para o armazenamento do Azure Blobs e para Arquivos do Azure. Depois de habilitar a Criptografia do Serviço de Armazenamento, apenas dados novos são criptografados, e quaisquer arquivos existentes nessa conta de armazenamento permanecem não criptografados. |
| Acesso à rede JIT |Quando a rede Just-In-Time está habilitada, a Central de Segurança bloqueia o tráfego de entrada às suas VMs do Azure criando uma regra de grupo de segurança de rede. Selecione as portas na VM para as quais o tráfego de entrada deverá ser bloqueado. Para saber mais, confira [Gerenciar o acesso à máquina virtual usando o just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |

## <a name="management-groups"></a>Grupos de gerenciamento
Se sua organização tiver muitas assinaturas, talvez seja necessária uma maneira de gerenciar com eficiência o acesso, as políticas e a conformidade dessas assinaturas. Os Grupos de Gerenciamento do Azure fornecem um nível de escopo sobre assinaturas. Você organiza assinaturas em contêineres chamados “grupos de gerenciamento” e aplica as políticas de governança aos grupos de gerenciamento. Todas as assinaturas dentro de um grupo de gerenciamento herdam automaticamente as políticas aplicadas ao grupo de gerenciamento. Cada diretório recebe um único grupo de gerenciamento de nível superior chamado grupo de gerenciamento “raiz”. Esse grupo de gerenciamento raiz é compilado na hierarquia para que todos os grupos de gerenciamento e assinaturas sejam dobrados nele. Esse grupo de gerenciamento raiz permite que políticas globais e atribuições de RBAC sejam aplicados no nível de diretório. Para configurar grupos de gerenciamento para uso com a Central de Segurança do Azure, siga as instruções do artigo [Obter visibilidade de todo o locatário para a Central de Segurança do Azure](security-center-management-groups.md). 

> [!NOTE]
> É importante compreender a hierarquia dos grupos de gerenciamento e assinaturas. Consulte [Organizar seus recursos com Grupos de Gerenciamento do Azure](../azure-resource-manager/management-groups-overview.md#root-management-group-for-each-directory) para saber mais sobre grupos de gerenciamento, gerenciamento raiz e acesso ao grupo de gerenciamento.
>
>


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como configurar políticas de segurança na Central de Segurança. Para saber mais sobre a Central de Segurança, confira estes artigos:

* [Guia de operações e planejamento da Central de Segurança do Azure](security-center-planning-and-operations-guide.md): saiba como planejar e entender as considerações de design sobre a Central de Segurança do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciar e responder aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): saiba como gerenciar e responder aos alertas de segurança.
* [Monitorar as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorar o status de integridade de suas soluções de parceiros.
* [Obter visibilidade de todo o locatário para a Central de Segurança do Azure](security-center-management-groups.md): saiba como configurar grupos de gerenciamento para a Central de Segurança do Azure. 
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as respostas para as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): encontre postagens no blog sobre conformidade e segurança do Azure.

Para saber mais sobre o Azure Policy, confira [O que é o Azure Policy?](../azure-policy/azure-policy-introduction.md)
