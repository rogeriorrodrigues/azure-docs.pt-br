---
title: 'Azure AD Connect: Logon Único Contínuo – Início Rápido| Microsoft Docs'
description: Este artigo descreve como começar a usar o Logon Único Contínuo do Azure Active Directory
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
ms.date: 08/01/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4710e30ad38485f47c115ac2e0e914e91c96d582
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522174"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Logon Único Contínuo do Azure Active Directory: Início Rápido

## <a name="deploy-seamless-single-sign-on"></a>Implantar Logon Único Contínuo

O SSO Contínuo (Logon Único Contínuo) do Azure Active Directory (Azure AD) conecta os usuários automaticamente quando estão nos respectivos desktops corporativos conectados à rede corporativa. O SSO Contínuo fornece aos usuários acesso fácil aos aplicativos baseados em nuvem, sem necessidade de componentes locais adicionais.

Para implantar o SSO Contínuo, siga estas etapas.

## <a name="step-1-check-the-prerequisites"></a>Etapa 1: Verificar os pré-requisitos

Verifique se os seguintes pré-requisitos estão em vigor:

* **Configurar seu servidor do Azure AD Connect**: se você usa a [Autenticação de Passagem](active-directory-aadconnect-pass-through-authentication.md) como seu método de entrada, não é necessária nenhuma verificação de pré-requisitos adicional. Se você usa a [sincronização de hash de senha](active-directory-aadconnectsync-implement-password-hash-synchronization.md) como seu método de entrada e se há um firewall entre o Azure AD Connect e Azure AD, verifique se:
   - Está usando a versão 1.1.644.0 ou superior do Azure AD Connect. 
   - Se o seu firewall ou proxy permite a lista de permissões de DNS, adicione as conexões às URLs **\*.msappproxy.net** pela porta 443 à lista de permissões. Caso contrário, permita acesso aos [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizados semanalmente. Esse pré-requisito é aplicável somente quando você habilita o recurso. Não é obrigatório para logons de usuário real.

    >[!NOTE]
    >O Azure AD Connect versões 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 têm um problema relacionado à sincronização de hash de senha. Se você _não_ pretende usar a sincronização de hash de senha em conjunto com a Autenticação de Passagem, leia as [Notas de versão do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) para saber mais.

* **Usar uma topologia do Azure AD Connect com suporte**: verifique se você está usando uma das topologias com suporte do Azure AD Connect descritas [aqui](active-directory-aadconnect-topologies.md).

* **Configurar credenciais de administrador de domínio**: você precisa ter credenciais de administrador de domínio para cada floresta do Active Directory que:
    * Você sincroniza com o Azure AD por meio do Azure AD Connect.
    * Contém os usuários que você deseja habilitar para o SSO Contínuo.
    
* **Habilitar autenticação moderna**: você precisa habilitar [autenticação moderna](https://aka.ms/modernauthga) em seu locatário para que esse recurso funcione.

* **Use as versões mais recentes dos clientes do Office 365**: para obter uma experiência de logon silencioso com clientes do Office 365 (Outlook, Word, Excel e outros), seus usuários precisam usar as versões 16.0.8730.xxxx ou acima.

## <a name="step-2-enable-the-feature"></a>Etapa 2: habilitar o recurso

Habilite o SSO Contínuo por meio do [Azure AD Connect](active-directory-aadconnect.md).

Se você está realizando uma instalação nova do Azure AD Connect, escolha o [caminho de instalação personalizada](active-directory-aadconnect-get-started-custom.md). Na página **Entrada do usuário**, marque a opção **Habilitar logon único**.

>[!NOTE]
> A opção estará disponível para seleção apenas se o método de logon é **Sincronização de Hash de Senha** ou **Autenticação de Passagem**.

![Azure AD Connect: entrada do usuário](./media/active-directory-aadconnect-sso/sso8.png)

Se você já tem uma instalação do Azure AD Connect, selecione **Alterar página de entrada do usuário** no Azure AD Connect e selecione **Avançar**. Se você estiver usando o Azure AD Connect versões 1.1.880.0 ou superior, a opção **Habilitar o logon único** será selecionada por padrão. Se você estiver usando versões mais antigas do Azure AD Connect, selecione a opção **Habilitar o logon único**.

![Azure AD Connect: Alterar entrada do usuário](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Prossiga com o assistente até chegar à página **Habilitar logon único**. Forneça credenciais de administrador de domínio para cada floresta do Active Directory que:
    * Você sincroniza com o Azure AD por meio do Azure AD Connect.
    * Contém os usuários que você deseja habilitar para o SSO Contínuo.

Após a conclusão do assistente, o SSO Contínuo está habilitado no seu locatário.

>[!NOTE]
> As credenciais de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. Elas são usadas somente para habilitar o recurso.

Siga estas instruções para verificar se você habilitou o SSO Contínuo corretamente:

1. Entre no [centro administrativo do Azure Active Directory](https://aad.portal.azure.com) com as credenciais do administrador global do seu locatário.
2. Selecione **Azure Active Directory** no painel esquerdo.
3. Selecione **Azure AD Connect**.
4. Verifique se o recurso **Logon único contínuo** aparece como **Habilitado**.

![Portal do Azure: painel do Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

>[!IMPORTANT]
> O SSO perfeito cria uma conta de computador chamada `AZUREADSSOACC` (que representa o Azure AD) em seu Active Directory (AD) local em cada floresta do AD. Esta conta de computador é necessária para o recurso funcionar. Mova a conta do computador `AZUREADSSOACC` para uma UO (unidade organizacional) em que outras contas de computador são armazenadas para garantir que ela seja gerenciada da mesma maneira e não seja excluída.

## <a name="step-3-roll-out-the-feature"></a>Etapa 3: distribuir o recurso

Você pode gradualmente implantar o SSO Contínuo para seus usuários utilizando as instruções abaixo. Comece adicionando a seguinte URL do Azure AD para as configurações de zona de Intranet de todos ou de alguns usuários usando a Política de Grupo no Active Directory:

- https://autologon.microsoftazuread-sso.com

Além disso, você precisa habilitar uma configuração da política de Zona de intranet chamada **Permitir atualizações à barra de status por meio de script** usando a Política de Grupo. 

>[!NOTE]
> As instruções a seguir só funcionam para o Internet Explorer e o Google Chrome no Windows (se ele usa o mesmo conjunto de URLs de sites confiáveis que o Internet Explorer). Leia a próxima seção para obter instruções sobre como configurar o Mozilla Firefox e o Google Chrome no macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Por que você precisa modificar as configurações de Zona da Intranet dos usuários?

Por padrão, o navegador calcula automaticamente a zona correta, Internet ou Intranet, de uma URL específica. Por exemplo, "http://contoso/" é mapeada para a Zona da Intranet, enquanto "http://intranet.contoso.com/" é mapeada para a Zona da Internet (porque a URL contém um ponto). Os navegadores não enviarão tickets Kerberos para um ponto de extremidade da nuvem, como a URL do Azure Active Directory, a menos que a URL seja explicitamente adicionada à zona da Intranet do navegador.

Há duas maneiras de modificar as configurações de zona de Intranet dos usuários:

| Opção | Consideração de administração | Experiência do usuário |
| --- | --- | --- |
| Política de grupo | O administrador bloqueia a edição das configurações da zona da Intranet | Os usuários não podem modificar as próprias configurações |
| Preferência de política de grupo |  O administrador permite edição nas configurações da zona de Intranet | Os usuários podem modificar as próprias configurações |

### <a name="group-policy-option---detailed-steps"></a>Opção "Política de grupo" – etapas detalhadas

1. Abra a ferramenta Editor de Gerenciamento de Política de Grupo.
2. Edite a política de grupo que é aplicada a alguns ou todos os seus usuários. Este exemplo usa a **Política de domínio padrão**.
3. Navegue até **Configuração do Usuário** > **Modelos Administrativos** > **Componentes do Windows** > **Internet Explorer** > **Painel de controle da Internet** > **Página de Segurança**. Em seguida, selecione **Lista de atribuição de sites a zonas**.
    ![Logon Único](./media/active-directory-aadconnect-sso/sso6.png)
4. Habilite a política e insira os valores a seguir na caixa de diálogo:
   - **Nome do valor**: a URL do Azure Active Directory para as quais os tíquetes Kerberos são encaminhados.
   - **Valor** (dados): **1** indica a zona da Intranet.

    O resultado é semelhante a:

    Valor: https://autologon.microsoftazuread-sso.com
  
    Data: 1

   >[!NOTE]
   > Se você quiser cancelar a permissão de uso do SSO Contínuo de alguns usuários (por exemplo, se esses usuários estiverem entrando em quiosques compartilhados), defina os valores anteriores como **4**. Essa ação adiciona s URL do Azure Active Directory à zona Restrita e ocasiona a falha do SSO Contínuo todas as vezes.
   >

5. Selecione **OK** e, em seguida, selecione **OK** novamente.

    ![Logon único](./media/active-directory-aadconnect-sso/sso7.png)

6. Navegue até **Configuração do Usuário** > **Modelos Administrativos** > **Componentes do Windows** > **Internet Explorer** > **Painel de Controle da Internet** > **Página de Segurança** > **Zona de Intranet**. Em seguida, selecione **Permitir atualizações à barra de status por meio de script**.

    ![Logon único](./media/active-directory-aadconnect-sso/sso11.png)

7. Habilite a configuração de política e, em seguida, selecione **OK**.

    ![Logon único](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Opção "Preferência de política de grupo" – etapas detalhadas

1. Abra a ferramenta Editor de Gerenciamento de Política de Grupo.
2. Edite a política de grupo que é aplicada a alguns ou todos os seus usuários. Este exemplo usa a **Política de domínio padrão**.
3. Navegue para **Configuração do Usuário** > **Preferências** > **Configurações do Windows** > **Registro** > **Novo** > **Item de Registro**.

    ![Logon único](./media/active-directory-aadconnect-sso/sso15.png)

4. Insira os seguintes valores nos campos apropriados e clique em **OK**.
   - **Caminho da Chave**: ***Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon***
   - **Nome do valor**: ***https***.
   - **Tipo de valor**: ***REG_DWORD***.
   - **Dados do valor**: ***00000001***.
 
    ![Logon único](./media/active-directory-aadconnect-sso/sso16.png)
 
    ![Logon único](./media/active-directory-aadconnect-sso/sso17.png)

6. Navegue até **Configuração do Usuário** > **Modelos Administrativos** > **Componentes do Windows** > **Internet Explorer** > **Painel de Controle da Internet** > **Página de Segurança** > **Zona de Intranet**. Em seguida, selecione **Permitir atualizações à barra de status por meio de script**.

    ![Logon único](./media/active-directory-aadconnect-sso/sso11.png)

7. Habilite a configuração de política e, em seguida, selecione **OK**.

    ![Logon único](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>Considerações de navegador

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (todas as plataformas)

O Mozilla Firefox não usa a autenticação Kerberos automaticamente. Cada usuário precisa adicionar manualmente as URLs do Azure Active Directory às configurações do Firefox com as seguintes etapas:
1. Execute o Firefox e digite `about:config` na barra de endereços. Ignore as notificações que aparecerem.
2. Pesquise a preferência **network.negotiate-auth.trusted-uris**. Esta preferência lista os sites confiáveis do Firefox para a autenticação Kerberos.
3. Clique com o botão direito do mouse e selecione **Modificar**.
4. Insira https://autologon.microsoftazuread-sso.com no campo.
5. Selecione **OK** e, em seguida, reabra o navegador.

#### <a name="safari-macos"></a>Safari (macOS)

Verifique se o computador que está executando o macOS está associado ao AD. Para obter instruções sobre como associar o Azure AD, veja [Práticas recomendadas para integrar o OS X ao Active Directory](http://www.isaca.org/Groups/Professional-English/identity-management/GroupDocuments/Integrating-OS-X-with-Active-Directory.pdf).

#### <a name="google-chrome-all-platforms"></a>Google Chrome (todas as plataformas)

Caso tenha substituído as configurações de política [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) ou [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) no seu ambiente, adicione também a URL do Azure AD (https://autologon.microsoftazuread-sso.com)).

#### <a name="google-chrome-macos-only"></a>Google Chrome (somente macOS)

Para o Google Chrome no Mac OS e outras plataformas que não sejam Windows, veja [The Chromium Project Policy List](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) para obter informações sobre como adicionar a URL do Azure Active Directory à lista de permissões para uma autenticação integrada.

O uso de extensões de Política de Grupo do Active Directory de terceiros para distribuir a URL do Azure Active Directory para usuários do Firefox e do Google Chrome no Mac está fora do escopo deste artigo.

#### <a name="known-browser-limitations"></a>Limitações conhecidas do navegador

O SSO Contínuo não funciona no modo de navegação particular em navegadores Firefox e Edge. Também não funciona no Internet Explorer se o navegador estiver em execução no modo de proteção aprimorada.

## <a name="step-4-test-the-feature"></a>Etapa 4: testar o recurso

Para testar o recurso para um usuário específico, verifique se todas as seguintes condições estão em vigor:
  - O usuário entra em um dispositivo corporativo.
  - O dispositivo está associado ao domínio do Active Directory. O dispositivo _não_ precisa ser [ingressado no Azure AD](../active-directory-azureadjoin-overview.md).
  - O dispositivo tem uma conexão direta com seu DC (controlador de domínio), seja na rede corporativa com ou sem fio ou por meio de uma conexão de acesso remoto, como uma conexão VPN.
  - Você [distribuiu o recurso](##step-3-roll-out-the-feature) a esse usuário por meio da Política de Grupo.

Para testar o cenário em que o usuário insere somente o nome de usuário, mas não a senha:
   - Entre no https://myapps.microsoft.com/ em uma nova sessão privada do navegador.

Para testar o cenário em que o usuário não tem que inserir o nome de usuário ou a senha, siga uma destas etapas: 
   - Entre no https://myapps.microsoft.com/contoso.onmicrosoft.com em uma nova sessão privada do navegador. Substitua *contoso* pelo nome do seu locatário.
   - Entre no https://myapps.microsoft.com/contoso.com em uma nova sessão privada do navegador. Substitua *contoso.com* por um domínio verificado (não um domínio federado) em seu locatário.

## <a name="step-5-roll-over-keys"></a>Etapa 5: Sobrepor chaves

Na etapa 2, o Azure AD Connect cria contas de computador (representando o Azure Active Directory) em todas as florestas do AD no qual você habilitou o SSO contínuo. Para saber mais, veja [Logon Único Contínuo do Azure Active Directory: aprofundamento técnico](active-directory-aadconnect-sso-how-it-works.md).

>[!IMPORTANT]
>A chave de descriptografia do Kerberos em uma conta de computador, se vazada, poderá ser usada para gerar tíquetes Kerberos para qualquer usuário em sua floresta do AD. Indivíduos mal-intencionados então poderão representar logons do Azure AD para usuários comprometidos. É altamente recomendável renovar periodicamente essas chaves de descriptografia do Kerberos – pelo menos uma vez a cada 30 dias.

Para obter instruções sobre como implantar as chaves, veja [Logon Único Contínuo do Azure Active Directory: perguntas frequentes](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account). Estamos trabalhando em uma funcionalidade para introduzir renovação automatizada das chaves.

>[!IMPORTANT]
>Você não precisa executar essa etapa _imediatamente_ depois de habilitar o recurso. Sobrepor as chaves de descriptografia Kerberos pelo menos uma vez a cada 30 dias.

## <a name="next-steps"></a>Próximas etapas

- [Análise técnica aprofundada](active-directory-aadconnect-sso-how-it-works.md): entender como funciona o recurso de logon único contínuo.
- [Perguntas frequentes](active-directory-aadconnect-sso-faq.md): respostas às perguntas frequentes sobre o Logon Único Contínuo.
- [Solução de problemas](active-directory-aadconnect-troubleshoot-sso.md): saiba como resolver problemas comuns com o recurso de Logon Único Contínuo.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): use o Fórum do Azure Active Directory para arquivar novas solicitações.
