---
title: Latências de relatórios do Azure Active Directory | Microsoft Docs
description: Saiba quanto tempo leva para que os eventos de relatório sejam exibidos no seu portal do Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 12/15/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f0de2f8700bef83b5a8a9303e90c97aab29722a3
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2018
ms.locfileid: "42146076"
---
# <a name="azure-active-directory-reporting-latencies"></a>Latências de relatórios do Azure Active Directory

Com o [relatório](../active-directory-preview-explainer.md) no Azure Active Directory, você obtém todas as informações necessárias para determinar como seu ambiente está se comportando. A quantidade de tempo que leva para que os dados de relatório sejam exibidos no portal do Azure também é conhecida como latência. 

Este tópico lista as informações de latência para todas as categorias de relatórios no portal do Azure. 


## <a name="activity-reports"></a>Relatórios de atividades

Há duas áreas de relatórios de atividade:

- **Atividades de entrada** – informações sobre o uso de aplicativos gerenciados e de atividades de entrada do usuário
- **Logs de auditoria** - informações de auditoria sobre o gerenciamento de usuários e de grupos, os aplicativos gerenciados e as atividades de diretório

A tabela a seguir lista as informações de latência para relatórios de atividade.

| Relatório | Latência (P95) |Latência (P99)|
| :-- | --- | --- | 
| Logs de auditoria | 2 minutos  | 5 min  |
| Entradas | 2 minutos  | 5 min |







## <a name="security-reports"></a>Relatórios de segurança

Há duas áreas de relatórios de segurança:

- **Entradas arriscadas** - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. 
- **Usuários sinalizados para riscos** - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida. 

A tabela a seguir lista as informações de latência para relatórios de segurança.

| Relatório | Mínimo | Média | Máximo |
| :-- | --- | --- | --- |
| Usuários em risco          | 5 minutos   | 15 minutos  | 2 horas  |
| Entradas de risco         | 5 minutos   | 15 minutos  | 2 horas  |

## <a name="risk-events"></a>Eventos de risco

O Azure Active Directory usa algoritmos de aprendizado de máquina e heurística adaptáveis para detectar ações suspeitas relacionadas às contas do usuário. Cada ação suspeita detectada é armazenada em um registro chamado evento de risco.

A tabela a seguir lista as informações de latência para eventos de risco.

| Relatório | Mínimo | Média | Máximo |
| :-- | --- | --- | --- |
| Entradas de endereços IP anônimos |5 minutos |15 minutos |2 horas |
| Entradas de locais desconhecidos |5 minutos |15 minutos |2 horas |
| Usuários com credenciais vazadas |2 horas |4 horas |8 horas |
| Viagem impossível a locais atípicos |5 minutos |1 hora |8 horas  |
| Entradas de dispositivos infectados |2 horas |4 horas |8 horas  |
| Entradas de endereços IP com atividade suspeita |2 horas |4 horas |8 horas  |



## <a name="next-steps"></a>Próximas etapas

Se você quiser saber mais sobre os relatórios de atividade no portal do Azure, consulte:

- [Relatórios de atividades de entrada no portal do Azure Active Directory](concept-sign-ins.md)
- [Audit activity reports in the Azure Active Directory portal (Relatórios de atividades de auditoria no portal do Azure Active Directory)](concept-audit-logs.md)

Se você quiser saber mais sobre os relatórios de segurança no portal do Azure, consulte:

- [Relatório de segurança de usuários em risco no portal do Azure Active Directory](concept-user-at-risk.md)
- [Relatório de entradas de risco no portal do Azure Active Directory](concept-risky-sign-ins.md)

Se você quiser saber mais sobre eventos de risco, consulte [Eventos de risco do Azure Active Directory](concept-risk-events.md).
