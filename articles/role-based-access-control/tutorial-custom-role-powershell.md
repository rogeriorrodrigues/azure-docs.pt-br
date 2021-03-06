---
title: 'Tutorial: Criar uma função personalizada usando o Azure PowerShell | Microsoft Docs'
description: Introdução à criação de uma função personalizada usando o Azure PowerShell.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/12/2018
ms.author: rolyon
ms.openlocfilehash: 1e3043a6053625f8363e9035e59b53275714a827
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300602"
---
# <a name="tutorial-create-a-custom-role-using-azure-powershell"></a>Tutorial: Criar uma função personalizada usando o Azure PowerShell

Se as [funções internas](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias funções personalizadas. Para este tutorial, crie uma função personalizada chamada Tíquetes de suporte do leitor usando o Azure PowerShell. A função personalizada permite que o usuário veja tudo na assinatura e também os tíquetes de suporte abertos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma função personalizada
> * Listar funções personalizadas
> * Atualizar uma função personalizada
> * Excluir uma função personalizada

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará de:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de acesso do usuário](built-in-roles.md#user-access-administrator)
- [Azure PowerShell](/powershell/azure/install-azurerm-ps) instalado localmente

## <a name="sign-in-to-azure-powershell"></a>Entre no Azure PowerShell

Entre no [Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Criar uma função personalizada

A maneira mais fácil de criar uma função personalizada é começar com uma função interna, editá-la e criar uma nova função.

1. No PowerShell, use o comando [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) a fim de obter a lista de operações para o provedor de recursos Microsoft.Support. É útil conhecer as operações disponíveis para criar as permissões. Você também pode ver uma lista de todas as operações em [Operações do provedor de recursos do Azure Resource Manager](resource-provider-operations.md#microsoftsupport).

    ```azurepowershell
    Get-AzureRMProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Use o comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) para exportar a função [Leitor](built-in-roles.md#reader) no formato JSON.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Abra o arquivo **ReaderSupportRole.json** em um editor.

    O exemplo a seguir mostra a saída JSON. Para obter informações sobre as diversas propriedades, confira [Funções personalizadas](custom-roles.md).

    ```json
    {
        "Name":  "Reader",
        "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "IsCustom":  false,
        "Description":  "Lets you view everything, but not make any changes.",
        "Actions":  [
                        "*/read"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/"
                             ]
    }
    ```
    
1. Edite o arquivo JSON para adicionar a operação `"Microsoft.Support/*"` à propriedade `Actions`. Inclua uma vírgula após a operação de leitura. Essa ação permitirá que o usuário crie tíquetes de suporte.

1. Obtenha a ID de sua assinatura usando o comando [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

    ```azurepowershell
    Get-AzureRmSubscription
    ```

1. Em `AssignableScopes`, adicione sua ID de assinatura com o seguinte formato: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    Você deve adicionar IDs de assinatura explícitas; caso contrário, não será possível importar a função para a assinatura.

1. Exclua a linha da propriedade `Id` e altere a propriedade `IsCustom` para `true`.

1. Alterar as propriedades `Name` e `Description` para "Tíquetes de suporte do leitor" e "Exibir tudo na assinatura e também abrir tíquetes de suporte".

    Seu arquivo JSON deverá ter a seguinte aparência:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
    
1. Para criar a nova função personalizada, use o comando [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) e especifique o arquivo de definição da função JSON.

    ```azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    A nova função personalizada agora está disponível no portal do Azure e pode ser atribuída a usuários, grupos ou entidades de serviço da mesma forma que as funções internas.

## <a name="list-custom-roles"></a>Listar funções personalizadas

- Para listar todas as suas funções personalizadas, use o comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

    ```azurepowershell
    Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    Você também pode ver a função personalizada no portal do Azure.

    ![captura de tela de função personalizada importada no Portal do Azure](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar a função personalizada, você pode atualizar o arquivo JSON ou usar o objeto `PSRoleDefinition`.

1. Para atualizar o arquivo JSON, use o comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) para produzir a função personalizada no formato JSON.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Abra o arquivo em um editor.

1. Em `Actions`, adicione a operação de criar e gerenciar implantações de grupos de recursos `"Microsoft.Resources/deployments/*"`.

    O arquivo JSON atualizado deve ficar mais ou menos assim:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "Id":  "22222222-2222-2222-2222-222222222222",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*",
                        "Microsoft.Resources/deployments/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
        
1. Para atualizar a função personalizada, use o comando [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) e especifique o arquivo JSON atualizado.

    ```azurepowershell
    Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. Para usar o objeto `PSRoleDefintion` e atualizar a função personalizada, primeiro use o comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) para obter a função.

    ```azurepowershell
    $role = Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```
    
1. Chame o método `Add` para adicionar a operação de ler as configurações de diagnóstico.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Use o comando [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) para atualizar a função.

    ```azurepowershell
    Set-AzureRmRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Excluir uma função personalizada

1. Use o comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) para obter a ID da função personalizada.

    ```azurepowershell
    Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```

1. Use o comando [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) e especifique a ID de função para excluir a função personalizada.

    ```azurepowershell
    Remove-AzureRmRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. Quando solicitado a confirmar, digite **Y**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar funções personalizadas usando o PowerShell](custom-roles-powershell.md)
