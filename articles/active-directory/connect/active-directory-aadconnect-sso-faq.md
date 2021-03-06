---
title: 'Azure AD Connect: Logon Único Contínuo – perguntas frequentes | Microsoft Docs'
description: Respostas às perguntas frequentes sobre o Logon Único Contínuo do Azure Active Directory.
services: active-directory
keywords: o que é o Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 29ed96044ceaa914db3f8b7090a1be5f65827e54
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627467"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Logon Único Contínuo do Azure Active Directory: perguntas frequentes

Neste artigo abordamos perguntas frequentes sobre o Logon Único Contínuo do Azure Active Directory (SSO contínuo). Continue verificando para ver novo conteúdo.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Com quais métodos de entrada o SSO Contínuo funcionam?

O SSO Contínuo pode ser combinado com o método de entrada de [Sincronização de Hash de Senha](active-directory-aadconnectsync-implement-password-hash-synchronization.md) ou de [Autenticação de Passagem](active-directory-aadconnect-pass-through-authentication.md). No entanto esse recurso não pode ser usado com os Serviços de Federação do Active Directory (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>O SSO Contínuo é um recurso gratuito?

O SSO Contínuo é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>O SSO Contínuo está disponível na [nuvem do Microsoft Azure Alemanha](http://www.microsoft.de/cloud-deutschland) e na [nuvem do Microsoft Azure Governamental](https://azure.microsoft.com/features/gov/)?

Não. O SSO Contínuo está disponível apenas na instância mundial do Azure AD.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Quais aplicativos tiram proveito da capacidade de parâmetro `domain_hint` ou `login_hint` do SSO Contínuo?

Veja abaixo uma lista parcial de aplicativos que enviam esses parâmetros para o Microsoft Azure Active Directory e, portanto, fornecem aos usuários uma experiência de logon silenciosa usando o SSO contínuo (isto é, os usuários não precisam inserir seus nomes de usuário ou senha):

| Nome do aplicativo | URL do aplicativo a ser usada |
| -- | -- |
| Painel de acesso | https://myapps.microsoft.com/contoso.com |
| Outlook na Web | https://outlook.office365.com/contoso.com |
| Portal do Office 365 | https://portal.office.com?domain_hint=contoso.com |

Além disso, os usuários também terão uma experiência de logon silenciosa se um aplicativo enviar solicitações de entrada para pontos de extremidade com locatários do Microsoft Azure AD - ou seja, https://login.microsoftonline.com/contoso.com/<..> ou https://login.microsoftonline.com/<tenant_ID>/<..> - em vez do ponto de extremidade comum do Microsoft Azure AD - ou seja, https://login.microsoftonline.com/common/<...>. Abaixo está uma lista parcial de aplicativos que fazem esses tipos de solicitações de entrada.

| Nome do aplicativo | URL do aplicativo a ser usada |
| -- | -- |
| SharePoint Online | https://contoso.sharepoint.com |
| Portal do Azure | https://portal.azure.com/contoso.com |

Nas tabelas acima, substitua "contoso.com" por seu nome de domínio para obter as URLs do aplicativo certo para o seu locatário.

Se desejar que outros aplicativos usem nossa experiência de logon silenciosa, informe isso na seção de comentários.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>O SSO Contínuo dá suporte ao `Alternate ID` como o nome de usuário, em vez de `userPrincipalName`?

Sim. O SSO Contínuo dá suporte ao `Alternate ID` como o nome de usuário quando configurado no Azure AD Connect, conforme mostrado [aqui](active-directory-aadconnect-get-started-custom.md). Nem todos os aplicativos do Office 365 dão suporte ao `Alternate ID`. Consulte a documentação específica do aplicativo para obter o demonstrativo de suporte.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>Qual é a diferença entre a experiência de logon único fornecida pelo [Ingresso no Azure AD](../active-directory-azureadjoin-overview.md) e o SSO Contínuo?

O [Ingresso no Azure AD](../active-directory-azureadjoin-overview.md) fornece o SSO aos usuários se os dispositivos deles estiverem registrados no Azure AD. Esses dispositivos não precisam, necessariamente, ser ingressados no domínio. O SSO é fornecido com o uso de *tokens de atualização primários* ou *PRTs* e não Kerberos. A experiência do usuário é melhor em dispositivos Windows 10. O SSO acontece automaticamente no navegador Edge. Ele também funciona no Chrome com o uso de uma extensão de navegador.

Você pode usar tanto o Ingresso no Azure AD quanto o SSO Contínuo em seu locatário. Esses dois recursos são complementares. Se os dois recursos forem ativados, o SSO do Ingresso no Azure AD terá precedência sobre o SSO Contínuo.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Desejo registrar dispositivos que não sejam Windows 10 com o Azure AD, sem o uso do AD FS. Posso usar o SSO Contínuo em vez disso?

Sim, esse cenário precisa da versão 2.1 ou posterior do [cliente de ingresso no local de trabalho](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Como posso sobrepor a chave de descriptografia de Kerberos a `AZUREADSSOACC` conta de computador?

É importante sobrepor frequentemente a chave de descriptografia de Kerberos a `AZUREADSSOACC` conta de computador (que representa o AD do Azure) criada na floresta do AD do seu local.

>[!IMPORTANT]
>É altamente recomendável sobrepor a chave de descriptografia do Kerberos pelo menos a cada 30 dias.

Siga estas etapas no servidor local em que você está executando o Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Etapa 1. Obter lista de florestas do AD em que o SSO Contínuo foi habilitado

1. Primeiro, baixe e instale o [Assistente de Conexão do Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Em seguida, baixe e instale o [Módulo do Azure Active Directory de 64 bits para Windows PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).
3. Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importe o módulo do PowerShell de SSO Contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.
5. Execute o PowerShell como um Administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando deve fornecer a você um pop-up para inserir suas credenciais de Administrador Global do locatário.
6. Chame `Get-AzureADSSOStatus`. Esse comando fornece a lista de florestas do AD (consulte a lista “Domínios”) em que esse recurso foi habilitado.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Etapa 2. Atualizar a chave de descriptografia de Kerberos em cada floresta do AD que foi configurado

1. Chame `$creds = Get-Credential`. Quando solicitado, insira as credenciais de Administrador de Domínio da floresta do AD pretendida.

    >[!NOTE]
    >Usamos o nome de usuário do Administrador de Domínio, fornecido no formato de nome UPN (johndoe@contoso.com) ou no formato de conta SAM qualificada por domínio (contoso\johndoe ou contoso.com\johndoe) para localizar a floresta do AD pretendida. Se você usar o nome de conta SAM qualificado do domínio, usaremos a parte de domínio do nome de usuário para [localizar o Controlador de Domínio do Administrador do Domínio usando o DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Se você usar o UPN em vez disso, poderemos [traduzi-lo para um nome de conta SAM qualificado de domínio](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) antes de localizar o controlador de domínio apropriado.

2. Chame `Update-AzureADSSOForest -OnPremCredentials $creds`. Esse comando atualiza a chave de descriptografia do Kerberos para a `AZUREADSSOACC` conta de computador nessa floresta do AD específico e a atualiza no AD do Azure.
3. Repita as etapas anteriores para cada floresta do AD em que você configurou o recurso.

>[!IMPORTANT]
>Certifique-se de _não_ executar o `Update-AzureADSSOForest` comando mais de uma vez. Caso contrário, o recurso deixará de funcionar até o momento em que os tíquetes Kerberos dos usuários expirarem e forem reemitidos pelo Active Directory local.

## <a name="how-can-i-disable-seamless-sso"></a>Como faço para desabilitar o SSO Contínuo?

### <a name="step-1-disable-the-feature-on-your-tenant"></a>Etapa 1. Desabilitar o recurso no locatário

#### <a name="option-a-disable-using-azure-ad-connect"></a>Opção A: desabilitar usando o Azure AD Connect

1. Execute o Azure AD Connect, escolha **Alterar página de entrada do usuário** e clique em **Avançar**.
2. Desmarque a opção **Habilitar logon único**. Continue com o assistente.

Após a conclusão do assistente, o SSO Contínuo é desabilitado no locatário. No entanto, uma mensagem será exibida na tela, informando o seguinte:

“O logon único agora está desabilitado, mas há etapas manuais adicionais a serem realizadas para concluir a limpeza. Saiba mais”

Para concluir o processo de limpeza, siga as etapas 2 e 3 no servidor local em que você está executando o Azure AD Connect.

#### <a name="option-b-disable-using-powershell"></a>Opção B: desabilitar usando o PowerShell

Siga estas etapas no servidor local em que o Azure AD Connect está sendo executado:

1. Primeiro, baixe e instale o [Assistente de Conexão do Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Em seguida, baixe e instale o [Módulo do Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importe o módulo do PowerShell de SSO Contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.
5. Execute o PowerShell como um Administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando deve fornecer a você um pop-up para inserir suas credenciais de Administrador Global do locatário.
6. Chame `Enable-AzureADSSO -Enable $false`.

>[!IMPORTANT]
>Desabilitar o SSO Contínuo usando o PowerShell não alterará o estado no Azure AD Connect. O SSO Contínuo aparecerá como habilitado na página **Alterar entrada do usuário**.

### <a name="step-2-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Etapa 2. Obter lista de florestas do AD em que o SSO Contínuo foi habilitado

Siga as etapas de 1 a 5 abaixo caso tenha desabilitado o SSO Contínuo usando o Azure AD Connect. Se você desabilitou o SSO Contínuo usando o PowerShell, vá diretamente até a etapa 6 abaixo.

1. Primeiro, baixe e instale o [Assistente de Conexão do Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Em seguida, baixe e instale o [Módulo do Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navegue até a pasta `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importe o módulo do PowerShell de SSO Contínuo usando este comando: `Import-Module .\AzureADSSO.psd1`.
5. Execute o PowerShell como um Administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Esse comando deve fornecer a você um pop-up para inserir suas credenciais de Administrador Global do locatário.
6. Chame `Get-AzureADSSOStatus`. Esse comando fornece a lista de florestas do AD (consulte a lista “Domínios”) em que esse recurso foi habilitado.

### <a name="step-3-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Etapa 3. Exclua manualmente a conta do computador `AZUREADSSOACCT` de cada floresta do AD que você vir listada.

## <a name="next-steps"></a>Próximas etapas

- [**Início Rápido** ](active-directory-aadconnect-sso-quick-start.md) – colocar o SSO Contínuo do Azure AD em funcionamento.
- [**Aprofundamento técnico**](active-directory-aadconnect-sso-how-it-works.md) – entenda como esse recurso funciona.
- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-sso.md) – Saiba como resolver problemas comuns do recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.
