---
title: Políticas internas no Azure Active Directory B2C | Microsoft Docs
description: Um tópico sobre a estrutura da política extensível do Azure Active Directory B2C e sobre como criar vários tipos de política.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f26db8bcb50fa09a8d2829d477f90cac8c52533f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337567"
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: políticas internas


A estrutura de política extensível do Azure AD (Azure Active Directory) B2C é o principal ponto forte do serviço. As políticas descrevem totalmente as experiências de identidade do consumidor, como inscrição, entrada ou edição de perfil. Por exemplo, uma política de inscrição permite controlar comportamentos definindo as seguintes configurações:

* Tipos de conta (contas sociais, como o Facebook ou contas locais, como um endereço de email) que os consumidores podem usar para se inscrever no aplicativo
* Atributos (como nome, código postal e tamanho do calçado, por exemplo) que serão coletados junto ao consumidor durante a inscrição
* Uso da Autenticação Multifator do Microsoft Azure
* A aparência e funcionalidade de todas as páginas de inscrição
* Informações (que se manifestam como declarações em um token) que o aplicativo recebe quando a execução da política é concluída

Você pode criar várias políticas de tipos diferentes em seu locatário e usá-las em seus aplicativos, conforme necessário. As políticas podem ser reutilizadas entre os aplicativos. Essa flexibilidade permite aos desenvolvedores definir e modificar experiências de identidade do consumidor com pouca ou nenhuma alteração no código.

As políticas estão disponíveis para uso por meio de uma interface simples do desenvolvedor. O aplicativo dispara uma política usando uma solicitação de autenticação HTTP padrão (passando um parâmetro de política na solicitação) e recebe um token personalizado como resposta. Por exemplo, a única diferença entre solicitações que invocam uma política de inscrição e aquelas que invocam uma política de entrada é o nome da política usado no parâmetro de cadeia de caracteres de consulta "p":

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

## <a name="create-a-sign-up-or-sign-in-policy"></a>Criar uma política de inscrição ou credenciais

Esta política controla as duas experiências de inscrição e credenciais do consumidor com uma única configuração. Os consumidores são conduzidos para o caminho certo (inscrição ou credenciais), dependendo do contexto. Ele também descreve o conteúdo de tokens que o aplicativo receberá mediante inscrições ou entradas bem-sucedidas.  Há um exemplo de código para a política de **inscrição ou entrada**, [disponível aqui](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  É recomendável usar essa política em uma política de **inscrição** ou uma política de **entrada**.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>Criar uma política de inscrição

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>Usar uma política de entrada

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>Criar uma política de edição de perfil

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>Criar uma política de redefinição de senha

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-policies"></a>Visualizar políticas

Como podemos lançar novos recursos, alguns deles podem não estar disponíveis em políticas existentes.  Planejamos substituir as versões anteriores com a versão mais recente do mesmo tipo depois que essas políticas entrarem no GA.  As políticas existentes não serão alterado e para aproveitar esses novos recursos você precisa criar novas políticas.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Como fazer para vincular uma política de inscrição ou entrada a uma política de redefinição de senha?
Ao criar uma política de **inscrição ou entrada** (com contas locais), você verá um link **Esqueceu a senha?** na primeira página da experiência. Clicar nesse link não dispara automaticamente uma política de redefinição de senha. 

Em vez disso, o código de erro **`AADB2C90118`** é retornado para seu aplicativo. Seu aplicativo precisa lidar com esse código de erro invocando uma política de redefinição de senha específica. Para obter mais informações, consulte um [exemplo que demonstra a abordagem de vinculação de políticas](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>Eu devo usar uma política de inscrição ou entrada ou uma política de inscrição e uma política de entrada?
É recomendável que você use uma política de **inscrição ou entrada** em uma política de **inscrição** e uma política de **entrada**.  

A política de **inscrição ou entrada** tem mais recursos que a política de **entrada**. Ela também permite que você use a personalização da interface do usuário da página e tem melhor suporte para localização. 

A política de **entrada** será recomendável, se você não precisar localizar suas políticas e precisar apenas de pequenos recursos de personalização para identidade visual e quiser redefinir a senha interna.

## <a name="next-steps"></a>Próximas etapas
* [Configuração de token, de sessão e de logon único](active-directory-b2c-token-session-sso.md)
* [Desabilitar a verificação de email durante a inscrição do consumidor](active-directory-b2c-reference-disable-ev.md)

