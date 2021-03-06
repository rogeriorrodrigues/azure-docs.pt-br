---
title: Todos os artigos sobre gerenciamento de aplicativos no Active Directory do Azure | Microsoft Azure
description: Saiba como personalizar a data de vencimento para seus certificados de federação e como renovar certificados que vencerão em breve.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: cde70518164c386697127b079979b5b3b76ae088
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42145674"
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Índice de artigos sobre gerenciamento de aplicativos no Active Directory do Azure
Esta página fornece uma lista abrangente com todos os documentos escritos sobre os vários recursos relacionados ao aplicativo no Active Directory do Azure (AD do Azure).

Há uma introdução rápida para cada recurso importante, além de uma orientação sobre quais artigos ler dependendo das informações que você está procurando.

## <a name="overview-articles"></a>Artigos de visão geral
Os artigos a seguir são bons pontos de partida para quem deseja apenas uma breve explicação dos recursos de gerenciamento de aplicativo do AD do Azure.

| Guia de artigos |  |
|:---:| --- |
| Uma introdução aos problemas de gerenciamento de aplicativo solucionados pelo AD do Azure |[Gerenciando aplicativos com o AD do Azure (Active Directory do Azure)](manage-apps/what-is-application-management.md) |
| Uma visão geral dos vários recursos no AD do Azure relacionados à habilitação do logon único, definição de quem tem acesso aos aplicativos e como os usuários iniciam os aplicativos |[Acesso aos aplicativos e logon único no Active Directory do Azure](manage-apps/what-is-single-sign-on.md) |
| Um exame das diferentes etapas envolvidas na integração de aplicativos ao AD do Azure |[Integrando aplicativos com o Azure Active Directory](manage-apps/plan-an-application-integration.md)<br /><br />[Habilitar logon único para aplicativos SaaS](manage-apps/configure-single-sign-on-portal.md)<br /><br />[Gerenciamento do acesso aos aplicativos](manage-apps/what-is-access-management.md) |
| Uma explicação técnica de como os aplicativos são representados no AD do Azure |[Como e por que os aplicativos são adicionados ao AD do Azure](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>Artigos de solução de problemas
Esta seção fornece acesso rápido às guias de solução de problemas relevantes. Para saber mais sobre cada área de recurso, veja o resto da página.

| Área de recurso |  |
|:---:| --- |
| Logon único federado |[Solução de problemas de logon único baseado em SAML](develop/howto-v1-debug-saml-sso-issues.md) |
| Logon único baseado em senha |[Solucionando problemas da extensão do painel de acesso para o Internet Explorer](active-directory-saas-ie-troubleshooting.md) |
| Proxy de Aplicativo |[Guia de solução de problemas de Proxy de Aplicativo](manage-apps/application-proxy-troubleshoot.md) |
| Logon único entre o AD local e o AD do Azure |[Solução de Problemas de Sincronização de Senha](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md#troubleshoot-password-hash-synchronization)<br /><br />[Solucionar problemas de write-back de senha](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Associações de grupo dinâmico |[Solucionar problemas de associações a grupos dinâmicos](users-groups-roles/groups-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>SSO (Logon único)
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>Logon único federado: logon em muitos aplicativos usando uma identidade
O logon único permite aos usuários acessar vários aplicativos e serviços usando apenas um conjunto de credenciais. Federação é um método por meio do qual você pode habilitar o logon único. Quando os usuários tentam entrar em aplicativos federados, eles são redirecionados à página oficial de suas organizações, processada pelo Active Directory do Azure, e após a autenticação bem-sucedida são redirecionados de volta ao aplicativo.

| Guia de artigos |  |
|:---:| --- |
| Uma introdução à federação e a outros tipos de logon |[Logon único com o AD do Azure](manage-apps/what-is-single-sign-on.md) |
| Milhares de aplicativos SaaS pré-integrados com o AD do Azure com etapas de configuração de logon único simplificadas |[Introdução à galeria de aplicativos do Azure AD](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Lista completa de aplicativos pré-integrados que dão suporte à federação](saas-apps/tutorial-list.md)<br /><br />[Como adicionar seu aplicativo à galeria de aplicativos do AD do Azure](develop/howto-app-gallery-listing.md) |
| Mais de 150 tutoriais de aplicativo sobre como configurar o logon único para aplicativos como [Salesforce](saas-apps/salesforce-tutorial.md), [ServiceNow](saas-apps/servicenow-tutorial.md), [Google Apps](saas-apps/google-apps-tutorial.md), [Workday](saas-apps/workday-tutorial.md) e muito mais |[Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](saas-apps/tutorial-list.md) |
| Como definir e personalizar manualmente a configuração do logon único |[Como configurar logon único federado para aplicativos que não estão na galeria de aplicativos do Azure Active Directory](application-config-sso-how-to-configure-federated-sso-non-gallery.md)<br /><br />[Como personalizar declarações emitidas no token SAML para aplicativos pré-integrados](active-directory-saml-claims-customization.md) |
| Guia de solução de problemas para aplicativos federados que usam o protocolo SAML |[Solução de problemas de logon único baseado em SAML](develop/howto-v1-debug-saml-sso-issues.md) |
| Como configurar a data de validade do certificado de seu aplicativo e como renovar os seus certificados |[Gerenciamento de certificados para logon único federado no Active Directory do Azure](manage-apps/manage-certificates-for-federated-single-sign-on.md) |

O logon único federado está disponível para todas as edições do AD do Azure para até dez aplicativos por usuário. [AD do Azure Premium](https://azure.microsoft.com/pricing/details/active-directory/) oferece suporte a uma quantidade ilimitada de aplicativos. Se sua organização tiver o [Azure AD Básico](https://azure.microsoft.com/pricing/details/active-directory/) ou o [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), você poderá [usar grupos para atribuir acesso a aplicativos federados](#managing-access-to-applications).

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>Logon único com base em senha: compartilhamento de conta e SSO para aplicativos não federados
Para habilitar o logon único em aplicativos que não oferecem suporte à federação, o AD do Azure oferece recursos de gerenciamento de senha que podem armazenar com segurança as senhas dos aplicativos SaaS e fazer o logon automático dos usuários nesses aplicativos. Você pode distribuir facilmente as credenciais para contas recém-criadas e compartilhar contas de equipe com várias pessoas. Os usuários não precisam saber as credenciais das contas para as quais recebem acesso.

| Guia de artigos |  |
|:---:| --- |
| Uma introdução ao funcionamento do SSO baseado em senha e uma breve visão geral técnica |[Logon único com base em senha com o AD do Azure](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on) |
| Um resumo dos cenários relacionados ao compartilhamento de conta e como esses problemas são solucionados pelo AD do Azure |[Compartilhar contas com o AD do Azure](active-directory-sharing-accounts.md) |
| Alterar automaticamente a senha para determinados aplicativos em intervalos regulares |[Substituição de senha automática (visualização)](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Guias de implantação e de solução de problemas para a versão do Internet Explorer da extensão de gerenciamento de senha do AD do Azure |[Como implantar a extensão do painel de acesso para o Internet Explorer usando a Política de Grupo](active-directory-saas-ie-group-policy.md)<br /><br />[Solucionando problemas da extensão do painel de acesso para o Internet Explorer](active-directory-saas-ie-troubleshooting.md) |

O logon único baseado em senha está disponível para todas as edições do AD do Azure para até dez aplicativos por usuário. [AD do Azure Premium](https://azure.microsoft.com/pricing/details/active-directory/) oferece suporte a uma quantidade ilimitada de aplicativos. Se sua organização tiver o [Azure AD Básico](https://azure.microsoft.com/pricing/details/active-directory/) ou o [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), você poderá [usar grupos para atribuir acesso a aplicativos](#managing-access-to-applications). A substituição de senha automática é um recurso do [AD do Azure Premium](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>Proxy de aplicativo: logon único e acesso remoto aos aplicativos locais
Se você tiver aplicativos em sua rede privada que precisam ser acessados por usuários e dispositivos fora da rede, use o Proxy de Aplicativo do AD do Azure para habilitar o acesso remoto e seguro a esses aplicativos.

| Guia de artigos |  |
|:---:| --- |
| Visão geral do Proxy de Aplicativo do AD do Azure e como ele funciona |[Fornecendo acesso remoto seguro a aplicativos locais](manage-apps/application-proxy.md) |
| Tutoriais sobre como configurar o Proxy de Aplicativo e como publicar seu primeiro aplicativo |[Como configurar o Proxy de aplicativo do Azure AD](manage-apps/application-proxy-enable.md)<br /><br />[Como instalar silenciosamente o conector de proxy de aplicativo](manage-apps/application-proxy-register-connector-powershell.md)<br /><br />[Como publicar aplicativos usando o proxy de aplicativo](manage-apps/application-proxy-publish-azure-portal.md)<br /><br />[Como usar o seu próprio nome de domínio](manage-apps/application-proxy-configure-custom-domain.md) |
| Como habilitar o logon único e o acesso condicional para aplicativos publicados com o Proxy de Aplicativo |[Logon único com Proxy de Aplicativo](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)<br /><br />[Acesso condicional e Proxy de Aplicativo](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| Orientação sobre como usar o Proxy de Aplicativo para os seguintes cenários |[Como dar suporte a aplicativos cliente nativos](manage-apps/application-proxy-configure-native-client-application.md)<br /><br />[Como dar suporte a aplicativos com reconhecimento de declarações](manage-apps/application-proxy-configure-for-claims-aware-applications.md)<br /><br />[Como dar suporte a aplicativos publicados em redes e locais separados](manage-apps/application-proxy-connector-groups.md) |
| Guia de solução de problemas de Proxy de Aplicativo |[Guia de solução de problemas de Proxy de Aplicativo](manage-apps/application-proxy-troubleshoot.md) |

O Proxy de Aplicativo está disponível para todas as edições do AD do Azure para até dez aplicativos por usuário. [AD do Azure Premium](https://azure.microsoft.com/pricing/details/active-directory/) oferece suporte a uma quantidade ilimitada de aplicativos. Se sua organização tiver o [Azure AD Básico](https://azure.microsoft.com/pricing/details/active-directory/) ou o [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), você poderá [usar grupos para atribuir acesso a aplicativos](#managing-access-to-applications).

Você também pode se interessar pelos [Serviços de domínio do Azure AD](../active-directory-domain-services/active-directory-ds-overview.md), que permitem a migração de seus aplicativos locais para o Azure, satisfazendo ainda as necessidades de identidade desses aplicativos.

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Habilitação do logon único entre o AD do Azure e o AD local
Se sua organização mantém um Active Directory do Windows Server no local junto com o Active Directory do Azure na nuvem, provavelmente você vai querer habilitar o logon único entre esses dois sistemas. O Azure AD Connect (a ferramenta que integra esses dois sistemas) fornece várias opções de configuração do logon único: estabelecer a federação com o ADFS, ou outro provedor de federação, ou habilitar a sincronização de senha.

| Guia de artigos |  |
|:---:| --- |
| Uma visão geral sobre as opções de logon único oferecidas no Azure AD Connect, bem como informações sobre o gerenciamento de ambientes híbridos |[Opções de logon único do usuário no Azure AD Connect](active-directory-aadconnect-user-signin.md) |
| Diretrizes gerais para o gerenciamento de ambientes com o Active Directory local e o Active Directory do Azure |[Considerações de design da Identidade Híbrida Azure AD](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[Integração de suas identidades locais com o Azure Active Directory](active-directory-aadconnect.md) |
| Orientação sobre como usar a Sincronização de Senha para habilitar SSO |[Implementar a sincronização de senha com o Azure AD Connect](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)<br /><br />[Solucionar problemas de sincronização de senha](https://support.microsoft.com/kb/2855271) |
| Orientação sobre como usar o Write-back de Senha para habilitar SSO |[Introdução ao Gerenciamento de Senhas no Azure AD](authentication/quickstart-sspr.md)<br /><br />[Solucionar problemas de Write-back de Senha](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Orientação sobre como usar provedores de identidade de terceiros para habilitar SSO |[Lista de provedores de identidade de terceiros compatíveis que podem ser usados para habilitar o logon único](https://aka.ms/ssoproviders) |
| Como os usuários do Windows 10 podem aproveitar os benefícios do logon único por meio do ingresso no AD do Azure |[Estendendo os recursos de nuvem para dispositivos com Windows 10 por meio do ingresso no Active Directory do Azure](active-directory-azureadjoin-overview.md) |

O Azure AD Connect está disponível para [todas as edições do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). O Autoatendimento de Redefinição de Senha do Azure AD está disponível para o [Azure AD Básico](https://azure.microsoft.com/pricing/details/active-directory/) e o [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). O Write-back de senha no AD local é um recurso do [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>Acesso condicional: imponha requisitos adicionais de segurança a aplicativos de alto risco
Depois de configurar o logon único em seus aplicativos e recursos, aplique uma proteção adicional aos aplicativos confidenciais com requisitos de segurança específicos em cada entrada nesse aplicativo. Por exemplo, você pode usar o AD do Azure para exigir que todo o acesso a um aplicativo específico sempre exija a autenticação multifator, independentemente desse aplicativo oferecer suporte nativo a essa funcionalidade. Outro exemplo comum de acesso condicional é exigir que os usuários estejam conectados à rede confiável da organização a fim de acessar um aplicativo particularmente confidencial.

| Guia de artigos |  |
|:---:| --- |
| Uma introdução aos recursos de acesso condicional oferecidos no AD do Azure, no Office365 e no Intune |[Gerenciamento de riscos com acesso condicional](active-directory-conditional-access-azure-portal.md) |
| Como habilitar o acesso condicional aos seguintes tipos de recursos |[Acesso condicional para aplicativos SaaS](conditional-access/app-based-conditional-access.md)<br /><br />[Acesso condicional para serviços do Office 365](active-directory-conditional-access-device-policies.md)<br /><br />[Acesso condicional para aplicativos locais](active-directory-conditional-access-azure-portal.md)<br /><br />[Acesso condicional para aplicativos locais publicados por meio do Proxy de Aplicativo do Azure AD](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| Como registrar dispositivos com o Active Directory do Azure a fim de habilitar políticas de acesso condicional baseadas no dispositivo |[Visão geral do registro de dispositivos do Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Como habilitar o registro de dispositivo automático para dispositivos Windows ingressados no domínio](active-directory-conditional-access-automatic-device-registration.md)<br />— [Etapas para dispositivos com Windows 8.1](active-directory-conditional-access-automatic-device-registration-setup.md)<br />— [Etapas para dispositivos com Windows 7](active-directory-conditional-access-automatic-device-registration-setup.md) |

| Como usar o aplicativo Microsoft Authenticator para verificação em duas etapas |[Microsoft Authenticator](user-help/microsoft-authenticator-app-how-to.md) |

O Acesso Condicional é um recurso do [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) .

## <a name="apps--azure-ad"></a>Aplicativos e AD do Azure
### <a name="cloud-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>Cloud Discovery: localize quais aplicativos SaaS estão sendo usados em sua organização
O Cloud Discovery analisa os logs de tráfego no catálogo de aplicativos de nuvem do Microsoft Cloud App Security de mais de 16.000 nuvem aplicativos que são classificados e pontuados com base nos fatores de risco mais de 70, para fornecer visibilidade contínua do nuvem uso, TI sombra e o risco Sombra poses IT em sua organização.

| Guia de artigos |  |
|:---:| --- |
| Uma visão geral de como isso funciona |[Configurar Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) |


### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>Provisionar e desprovisionar automaticamente as contas de usuário em aplicativos SaaS
Automatize a criação, a manutenção e a remoção de identidades de usuário em aplicativos SaaS como o Dropbox, Salesforce, ServiceNow e outros. Corresponda e sincronize as identidades existentes entre o AD do Azure e seus aplicativos SaaS e controle o acesso desabilitando automaticamente as contas quando os usuários saírem da organização.

| Guia de artigos |  |
|:---:| --- |
| Saiba mais sobre como isso funciona e encontre respostas para perguntas comuns |[Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS](active-directory-saas-app-provisioning.md) |
| Configurar como as informações são mapeadas entre o AD do Azure e seu aplicativo SaaS |[Personalizando mapeamentos de atributo](active-directory-saas-customizing-attribute-mappings.md)<br><br>[Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| Como habilitar o provisionamento automatizado para qualquer aplicativo que ofereça suporte ao protocolo SCIM |[Configurar o provisionamento automatizado de usuário para qualquer aplicativo habilitado para SCIM](manage-apps/use-scim-to-provision-users-and-groups.md) |
| Como relatar e solucionar problemas de provisionamento do usuário |[Relatórios sobre o provisionamento automático de usuário](active-directory-saas-provisioning-reporting.md)<br><br>[Solução de problemas de provisionamento do usuário](active-directory-application-provisioning-content-map.md) |
| Limitar quem recebe provisionamento para um aplicativo com base em seus valores de atributo |[Filtro de escopo](active-directory-saas-scoping-filters.md) |

O provisionamento automatizado de usuário está disponível para todas as edições do AD do Azure para até dez aplicativos por usuário. [AD do Azure Premium](https://azure.microsoft.com/pricing/details/active-directory/) oferece suporte a uma quantidade ilimitada de aplicativos. Se sua organização tiver o [Azure AD Básico](https://azure.microsoft.com/pricing/details/active-directory/) ou o [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), você poderá [usar grupos para gerenciar quais usuários recebem o provisionamento](#managing-access-to-applications).

### <a name="building-applications-that-integrate-with-azure-ad"></a>Compilação de aplicativos integrados ao AD do Azure
Se sua organização estiver desenvolvendo ou mantendo aplicativos LoB (linha de negócios), ou se você for um desenvolvedor de aplicativos com os clientes que usam o Active Directory do Azure, os tutoriais a seguir ajudarão você a integrar seus aplicativos ao AD do Azure.

| Guia de artigos |  |
|:---:| --- |
| Orientação para profissionais de TI e desenvolvedores de aplicativos sobre a integração de aplicativos ao AD do Azure |[O guia do profissional de TI para desenvolver aplicativos para o Azure AD](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[O guia do desenvolvedor para o Azure Active Directory](develop/azure-ad-developers-guide.md) |
| Como os fornecedores de aplicativos podem adicionar seus aplicativos à Galeria de aplicativos do AD do Azure |[Listando seu aplicativo na galeria de aplicativos do Azure Active Directory](develop/howto-app-gallery-listing.md) |
| Como gerenciar o acesso a aplicativos desenvolvidos usando o Active Directory do Azure |[Como habilitar a atribuição de usuário para aplicativos desenvolvidos](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Atribuindo usuários a seu aplicativo](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Atribuição de grupo para o seu aplicativo](active-directory-applications-guiding-developers-assigning-groups.md) |

Se você estiver desenvolvendo aplicativos voltados para o consumidor, talvez se interesse em usar o [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) para que você não precise desenvolver seu próprio sistema de identidade para gerenciamento dos usuários. [Saiba mais](../active-directory-b2c/active-directory-b2c-overview.md).

## <a name="managing-access-to-applications"></a>Gerenciamento do acesso a aplicativos
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>Como usar grupos e autoatendimento para gerenciar quem tem acesso a quais aplicativos
Para ajudar você a gerenciar quem deve ter acesso a quais recursos, o Active Directory do Azure permite que você defina atribuições e permissões em larga escala usando grupos. O departamento de TI pode optar por habilitar recursos de autoatendimento para que os usuários possam simplesmente solicitar a permissão quando for necessário.

| Guia de artigos |  |
|:---:| --- |
| Uma visão geral dos recursos de gerenciamento de acesso do AD do Azure |[Introdução ao gerenciamento do acesso a aplicativos](manage-apps/what-is-access-management.md)<br /><br />[Como funciona o gerenciamento de acesso no Azure AD](fundamentals/active-directory-manage-groups.md)<br /><br />[Como usar grupos para gerenciar o acesso a aplicativos SaaS](users-groups-roles/groups-saasapps.md) |
| Habilitar o gerenciamento por autoatendimento de aplicativos e grupos |[Gerenciamento de aplicativos de autoatendimento](active-directory-self-service-application-access.md)<br /><br />[Gerenciamento de grupo de autoatendimento](users-groups-roles/groups-self-service-management.md) |
| Instruções para configuração de seus grupos no AD do Azure |[Como criar grupos de segurança](fundamentals/active-directory-groups-create-azure-portal.md)<br /><br />[Como designar proprietários de um grupo](fundamentals/active-directory-accessmanagement-managing-group-owners.md)<br /><br />[Como usar o grupo "Todos os Usuários"](active-directory-accessmanagement-dedicated-groups.md) |
| Usar grupos dinâmicos para preencher automaticamente a associação de grupo usando regras de associação baseadas em atributo |[Associação de grupo dinâmico: regras avançadas](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[Solucionar problemas de associações a grupos dinâmicos](users-groups-roles/groups-troubleshooting.md) |

O gerenciamento de acesso de aplicativo baseado em grupo está disponível para o [Azure AD Básico](https://azure.microsoft.com/pricing/details/active-directory/) e o [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). O gerenciamento de grupo de autoatendimento, o gerenciamento de aplicativos de autoatendimento e os grupos dinâmicos são recursos do [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>Colaboração B2B: habilitar acesso de parceiros a aplicativos
Se sua empresa tiver parcerias com outras empresas, provavelmente você precise gerenciar o acesso dos parceiros aos seus aplicativos corporativos. A Colaboração B2B do Active Directory do Azure fornece uma maneira fácil e segura de compartilhar seus aplicativos com parceiros.

| Guia de artigos |  |
|:---:| --- |
| Uma visão geral de diferentes recursos do AD do Azure que podem ajudar você a gerenciar usuários externos, como parceiros, clientes etc. |[Comparando recursos de gerenciamento de identidades externas no AD do Azure](active-directory-b2b-compare-external-identities.md) |
| Uma introdução à Colaboração B2B e como começar a usá-la |[Integração de parceiros de nuvem simples e segura com o Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Colaboração B2B do Azure Active Directory](active-directory-b2b-collaboration-overview.md) |
| Um aprofundamento na Colaboração B2B do AD do Azure e como usá-la |[Colaboração B2B: como funciona](active-directory-b2b-how-it-works.md)<br /><br />[Limitações atuais da Colaboração B2B do Azure AD](active-directory-b2b-current-limitations.md)<br /><br />[Passo a passo detalhado do uso da Colaboração B2B do Azure AD](active-directory-b2b-detailed-walkthrough.md) |
| Artigos de referência com detalhes técnicos sobre o funcionamento da Colaboração B2B do AD do Azure |[Formato de arquivo CSV para adicionar usuários do parceiro](active-directory-b2b-references-csv-file-format.md)<br /><br />[Atributos de usuário afetados pela colaboração B2B do Azure AD](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[Formato de token de usuário para usuários parceiros](active-directory-b2b-references-external-user-token-format.md) |

A Colaboração B2B está disponível atualmente para [todas as edições do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>Painel de acesso: um portal para acessar aplicativos e recursos de autoatendimento
O Painel de acesso do AD do Azure é o local onde os usuários finais podem iniciar seus aplicativos e acessar os recursos de autoatendimento que permitem o gerenciamento de seus aplicativos e associações de grupo. Além do Painel de acesso, há outras opções para acessar aplicativos habilitados para SSO incluídas na lista abaixo.

| Guia de artigos |  |
|:---:| --- |
| Uma comparação das opções diferentes disponíveis para a implantação de aplicativos de logon único aos usuários |[Implantando aplicativos integrados do AD do Azure para usuários](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users) |
| Uma visão geral do Painel de acesso e seu MyApps equivalentes móveis |[Introdução ao Painel de Acesso e aos MyApps](user-help/active-directory-saas-access-panel-introduction.md)<br />— [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Como acessar os aplicativos do AD do Azure no site do Office 365 |[Usando o Iniciador de Aplicativos do Office 365](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Como acessar aplicativos do AD do Azure no aplicativo móvel Navegador Gerenciado do Intune |[Navegador gerenciado do Intune](https://technet.microsoft.com/library/dn878029.aspx)<br />— [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Como acessar aplicativos do AD do Azure usando links profundos para iniciar o logon único |[Como obter links de logon direto para seus aplicativos](manage-apps/what-is-single-sign-on.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

O Painel de Acesso está disponível para [todas as edições do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>Relatórios: audite com facilidade as alterações de acesso ao aplicativo e monitore as entradas nos aplicativos
O Active Directory do Azure fornece vários relatórios e alertas para ajudar você a monitorar o acesso a aplicativos de sua organização. Você pode receber alertas de entradas irregulares em seus aplicativos e controlar quando e por que o acesso dos usuários a um aplicativo foi alterado.

| Guia de artigos |  |
|:---:| --- |
| Uma visão geral dos recursos de relatório no Active Directory do Azure |[Introdução aos relatórios do AD do Azure](active-directory-reporting-getting-started.md) |
| Como monitorar as entradas e o uso do aplicativo por seus usuários |[Exibir relatórios de acesso e de uso](active-directory-view-access-usage-reports.md) |
| Controlar as alterações feitas com relação a quem pode acessar um aplicativo específico |[Eventos de relatório de auditoria do Active Directory do Azure](active-directory-reporting-audit-events.md) |
| Exportar os dados desses relatórios para suas ferramentas preferenciais usando a API de Relatório |[Introdução à API de Relatório do AD do Azure](active-directory-reporting-api-getting-started.md) |

Para ver quais relatórios estão incluídos em edições diferentes do Azure Active Directory, [clique aqui](active-directory-view-access-usage-reports.md).

## <a name="see-also"></a>Consulte também
[O que é o Active Directory do Azure?](fundamentals/active-directory-whatis.md)

[Active Directory B2C do Azure](https://azure.microsoft.com/services/active-directory-b2c/)

[Serviços de Domínio do Active Directory do Azure](https://azure.microsoft.com/services/active-directory-ds/)

[Autenticação Multifator do Azure](https://azure.microsoft.com/services/multi-factor-authentication/)
