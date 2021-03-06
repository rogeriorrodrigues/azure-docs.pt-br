---
title: 'Tutorial: Integração do Microsoft Azure Active Directory ao SafeConnect | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SafeConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f9aaac2e-cdba-4f01-a57f-2c5c26287085
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: f011b9ef7229ba1e588e488be8b4fc5b098ee5ac
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40024507"
---
# <a name="tutorial-azure-active-directory-integration-with-safeconnect"></a>Tutorial: Integração do Active Directory do Azure com o SafeConnect

Neste tutorial, você aprenderá a integrar o v ao Microsoft Azure Active Directory.

A integração do SafeConnect ao Microsoft Azure Active Directory oferece os seguintes benefícios:

- No Microsoft Azure Active Directory, você poderá controlar quem tem acesso ao SafeConnect.
- Você pode permitir que seus usuários façam logon automaticamente no SafeConnect (logon único) com as contas do Microsoft Azure Active Directory deles.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory com o SafeConnect, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do SafeConnect

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando SafeConnect da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-safeconnect-from-the-gallery"></a>Adicionando SafeConnect da Galeria
Para configurar a integração do TigerText ao Microsoft Azure Active Directory, você precisa adicionar o TigerText por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SafeConnect da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **SafeConnect**, selecione **SafeConnect** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SafeConnect na lista de resultados](./media/safeconnect-tutorial/tutorial_safeconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Microsoft Azure Active Directory com o SafeConnect, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Microsoft Azure Active Directory precisa saber qual usuário do SafeConnect é equivalente a um usuário do Microsoft Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o usuário relacionado no SafeConnect.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o SafeConnect, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do SafeConnect](#create-a-safeconnect-test-user)** : para ter um equivalente de Brenda Fernandes no SafeConnect que esteja vinculado à representação de usuário no Microsoft Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure Active Directory no portal do Azure e configurará o logon único no aplicativo SafeConnect.

**Para configurar o logon único do Microsoft Azure Active Directory com o SafeConnect, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **SafeConnect**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/safeconnect-tutorial/tutorial_safeconnect_samlbase.png)

3. Na seção **Domínio e URLs do SafeConnect**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do SafeConnect](./media/safeconnect-tutorial/tutorial_safeconnect_url.png)

     Na caixa de texto **URL de Logon**, digite uma URL: `https://portal.myweblogon.com:8443/saml/login`

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/safeconnect-tutorial/tutorial_safeconnect_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/safeconnect-tutorial/tutorial_general_400.png)

6. Para configurar o logon único no lado do **SafeConnect**, é necessário enviar o **XML de Metadados** baixado para a [equipe de suporte do SafeConnect](mailto:support@impulse.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/safeconnect-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/safeconnect-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/safeconnect-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/safeconnect-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-safeconnect-test-user"></a>Criar um usuário de teste SafeConnect

Nesta seção, você criará uma usuária chamada Brenda Fernandes no SafeConnect. Trabalhar com [equipe de suporte do SafeConnect](mailto:support@impulse.com) para adicionar os usuários na plataforma do SafeConnect. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao SafeConnect.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao SafeConnect, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **SafeConnect**.

    ![O link do SafeConnect na lista de Aplicativos](./media/safeconnect-tutorial/tutorial_safeconnect_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na peça do SafeConnect no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo SafeConnect.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/safeconnect-tutorial/tutorial_general_01.png
[2]: ./media/safeconnect-tutorial/tutorial_general_02.png
[3]: ./media/safeconnect-tutorial/tutorial_general_03.png
[4]: ./media/safeconnect-tutorial/tutorial_general_04.png

[100]: ./media/safeconnect-tutorial/tutorial_general_100.png

[200]: ./media/safeconnect-tutorial/tutorial_general_200.png
[201]: ./media/safeconnect-tutorial/tutorial_general_201.png
[202]: ./media/safeconnect-tutorial/tutorial_general_202.png
[203]: ./media/safeconnect-tutorial/tutorial_general_203.png

