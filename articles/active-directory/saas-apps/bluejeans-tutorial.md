---
title: 'Tutorial: integração do Azure Active Directory com o BlueJeans | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 2ec94217a8df2efaa23eb3cc2c9d5a80e8037615
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425980"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Tutorial: Integração do Azure Active Directory ao BlueJeans

Neste tutorial, você aprende a integrar o BlueJeans ao Azure AD (Azure Active Directory).

A integração do BlueJeans ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao BlueJeans
- É possível permitir que os usuários se conectem automaticamente ao BlueJeans (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao BlueJeans, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do BlueJeans

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o BlueJeans por meio da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-bluejeans-from-the-gallery"></a>Adicionando o BlueJeans por meio da galeria
Para configurar a integração do BlueJeans ao Azure AD, você precisa adicionar o BlueJeans à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o BlueJeans por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]

1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **BlueJeans**.

    ![Criação de um usuário de teste do AD do Azure](./media/bluejeans-tutorial/tutorial_bluejeans_search.png)

1. No painel de resultados, selecione **BlueJeans** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o BlueJeans, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do BlueJeans é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do BlueJeans.

No BlueJeans, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o BlueJeans, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Criando um usuário de teste do BlueJeans](#creating-a-bluejeans-test-user)** – para ter um equivalente de Brenda Fernandes no BlueJeans que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo BlueJeans.

**Para configurar o logon único do Azure AD com o BlueJeans, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **BlueJeans**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/bluejeans-tutorial/tutorial_bluejeans_samlbase.png)

1. Na seção **Domínio e URLs do BlueJeans**, realize as seguintes etapas:

    ![Configurar o logon único](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.BlueJeans.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do BlueJeans](https://support.bluejeans.com/contact) para obter esses valores.

1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/bluejeans-tutorial/tutorial_bluejeans_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/bluejeans-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do BlueJeans**, clique em **Configurar o BlueJeans** para abrir a janela **Configurar logon**. Copie a **URL de Saída, a URL de Alteração de Senha e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida.**

    ![Configurar o logon único](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do **BlueJeans** como administrador.

1. Vá para **ADMINISTRADOR \> Configurações de Grupo \> Segurança**.

   ![Admin](./media/bluejeans-tutorial/IC785868.png "Admin")

1. Na seção **Segurança** , realize as seguintes etapas:

   ![Logon Único do SAML](./media/bluejeans-tutorial/IC785869.png "Logon Único do SAML")

   a. Selecione **Logon Único do SAML**.

   b. Selecione **Habilitar provisionamento automático**.

1. Siga em frente com as seguintes etapas:

    ![Caminho de Certificado](./media/bluejeans-tutorial/IC785870.png "Caminho de Certificado")

    a. Clique em **Escolher Arquivo**e carregue o certificado baixado.

    b. Cole a **URL do Serviço de Logon Único SAML** na caixa de texto **URL de Logon**.

    c. Cole a **URL de Alteração de Senha** na caixa de texto **URL de Alteração de Senha**.

    d. Cole a **URL de Saída** na caixa de texto **URL de Logoff**.

1. Siga em frente com as seguintes etapas:

    ![Salvar Alterações](./media/bluejeans-tutorial/IC785874.png "Salvar Alterações")

    a. Na caixa de texto **ID de usuário**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Na caixa de texto **Email**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Clique em **Salvar Alterações**.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/bluejeans-tutorial/create_aaduser_01.png)

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.

    ![Criação de um usuário de teste do AD do Azure](./media/bluejeans-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.

    ![Criação de um usuário de teste do AD do Azure](./media/bluejeans-tutorial/create_aaduser_03.png)

1. Na página do diálogo **Usuário**, execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/bluejeans-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.

### <a name="creating-a-bluejeans-test-user"></a>Criando um usuário de teste do BlueJeans

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no BlueJeans. O BlueJeans dá suporte ao provisionamento automático de usuário, que é habilitado por padrão. Você pode encontrar [aqui](bluejeans-provisioning-tutorial.md) mais detalhes de como configurar o provisionamento automático de usuário.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **BlueJeans** como administrador.

1. Vá para **ADMINISTRADOR \> Gerenciar Usuários \> Adicionar Usuário**.

   ![Admin](./media/bluejeans-tutorial/IC785877.png "Admin")

   >[!IMPORTANT]
   >A guia **Adicionar Usuário** só estará disponível se, na **guia Segurança**, a opção **Habilitar provisionamento automático** estiver desmarcada. 

1. Na seção **Adicionar Usuário** , realize as seguintes etapas:

    ![Adicionar Usuário](./media/bluejeans-tutorial/IC785886.png "Adicionar Usuário")

    a. Digite um **Nome de Usuário do BlueJeans**, **Endereço de email**, **ID de Reunião do BlueJeans**, **Senha de Moderador**, **Nome Completo** e a **Empresa** de uma conta válida do AAD que você deseja provisionar nas caixas de texto relacionadas.

    b. Clique em **Adicionar Usuário**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do BlueJeans ou as APIs fornecidas pelo BlueJeans para provisionar as contas de usuário do AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao BlueJeans.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao BlueJeans, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

1. Na lista de aplicativos, selecione **BlueJeans**.

    ![Configurar o logon único](./media/bluejeans-tutorial/tutorial_bluejeans_app.png)

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco BlueJeans no Painel de Acesso, deverá acessar a página de logon do aplicativo BlueJeans.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configurar Provisionamento de Usuário](bluejeans-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/bluejeans-tutorial/tutorial_general_203.png
