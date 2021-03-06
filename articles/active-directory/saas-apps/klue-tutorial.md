---
title: 'Tutorial: Integração do Azure Active Directory ao Klue | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Klue.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 08341008-980b-4111-adb2-97bbabbf1e47
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.openlocfilehash: 4afe11d6d241e86b57ebb40d54e4c2dceb63a46c
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123049"
---
# <a name="tutorial-azure-active-directory-integration-with-klue"></a>Tutorial: Integração do Azure Active Directory ao Klue

Neste tutorial, você aprende a integrar o Klue ao Azure AD (Azure Active Directory).

A integração do Klue ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Klue
- É possível permitir que os usuários se conectem automaticamente ao Klue (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Klue, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Klue

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Klue por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-klue-from-the-gallery"></a>Adicionando o Klue por meio da galeria

Para configurar a integração do Klue ao Azure AD, é necessário adicionar o Klue à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Klue por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

4. Na caixa de pesquisa, digite **Klue**.

    ![Criação de um usuário de teste do AD do Azure](./media/klue-tutorial/tutorial_klue_search.png)

5. No painel de resultados, selecione **Klue** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/klue-tutorial/tutorial_klue_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure

Nesta seção, você configura e testa o logon único do Azure AD com o Klue, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Klue é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Klue.

No Klue, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Klue, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do Klue](#creating-a-klue-test-user)** – para ter um equivalente de Brenda Fernandes no Klue que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Klue.

**Para configurar o logon único do Azure AD com o Klue, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Klue**, clique em **Logon único**.

    ![Configurar o logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/klue-tutorial/tutorial_klue_samlbase.png)

3. Na seção **Domínio e URLs do Klue**, se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Configurar o logon único](./media/klue-tutorial/tutorial_klue_url1.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `urn:klue:<Customer ID>`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://app.klue.com/account/auth/saml/<Customer UUID>/callback`

4. Marque **Mostrar configurações de URL avançadas**. Se quiser configurar o aplicativo no modo iniciado em **SP**:

    ![Configurar o logon único](./media/klue-tutorial/tutorial_klue_url2.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://app.klue.com/account/auth/saml/<Customer UUID>/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon reais. Contate a [equipe de suporte ao Cliente do Klue](mailto:support@klue.com) para obter esses valores.

5. O aplicativo Klue espera que as declarações SAML estejam em um formato específico, o que exige a adição de mapeamentos de atributo personalizados para a configuração de atributos do token SAML. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo.

    ![Configurar o logon único](./media/klue-tutorial/attribute.png)

6. Na seção **Atributos de Usuário** da caixa de diálogo **Logon único**, configure o atributo do token SAML, conforme mostrado na imagem anterior e realize as seguintes etapas:

    | Nome do atributo      | Valor do atributo      |
    | ------------------- | -------------------- |
    | first_name          | user.givenname |
    | last_name           | user.surname |
    | email               | user.userprincipalname|

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/klue-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/klue-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Clique em **OK**.

    > [!NOTE]
    > Deixe o valor de **NAMESPACE** em branco.

7. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/klue-tutorial/tutorial_klue_certificate.png) 

8. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/klue-tutorial/tutorial_general_400.png)

9. Na seção **Configuração do Klue**, clique em **Configurar o Klue** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/klue-tutorial/tutorial_klue_configure.png) 

10. Para configurar o logon único no lado do **Klue**, é necessário enviar o **Certificado (Base64) baixado, a URL do Serviço de Logon Único SAML e a ID da Entidade SAML** para a [equipe de suporte do Klue](mailto:support@klue.com).

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/klue-tutorial/create_aaduser_01.png)

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.

    ![Criação de um usuário de teste do AD do Azure](./media/klue-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.

    ![Criação de um usuário de teste do AD do Azure](./media/klue-tutorial/create_aaduser_03.png)

4. Na página do diálogo **Usuário**, execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/klue-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.

### <a name="creating-a-klue-test-user"></a>Criando um usuário de teste do Klue

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Klue. O Klue dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Klue, caso ele ainda não exista.

> [!Note]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Klue](mailto:support@klue.com).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Klue.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Klue, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Klue**.

    ![Configurar o logon único](./media/klue-tutorial/tutorial_klue_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do Klue no Painel de Acesso, deverá ser conectado automaticamente ao aplicativo Klue.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/klue-tutorial/tutorial_general_01.png
[2]: ./media/klue-tutorial/tutorial_general_02.png
[3]: ./media/klue-tutorial/tutorial_general_03.png
[4]: ./media/klue-tutorial/tutorial_general_04.png

[100]: ./media/klue-tutorial/tutorial_general_100.png

[200]: ./media/klue-tutorial/tutorial_general_200.png
[201]: ./media/klue-tutorial/tutorial_general_201.png
[202]: ./media/klue-tutorial/tutorial_general_202.png
[203]: ./media/klue-tutorial/tutorial_general_203.png