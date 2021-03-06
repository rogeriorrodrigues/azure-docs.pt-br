---
title: 'Tutorial: Integração do Azure Active Directory ao Halogen Software | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Halogen Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: a7be918118d86da7e1134f5ce46e5f163ba62601
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432840"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Tutorial: Integração do Active Directory do Azure com o Halogen Software

Neste tutorial, você aprenderá a integrar o Halogen Software ao Azure AD (Azure Active Directory).

A integração do Halogen Software ao Azure AD oferece os seguintes benefícios:

- Você pode controlar, no Azure AD, quem tem acesso ao Halogen Software
- Você pode habilitar seus usuários a fazerem logon automaticamente no Halogen Software (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Halogen Software, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Halogen Software com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Halogen Software por meio da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-halogen-software-from-the-gallery"></a>Adicionar o Halogen Software por meio da galeria

Para configurar a integração do Halogen Software com o Azure AD, você precisa adicionar o Halogen Software, por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o Halogen Software por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

1. Na caixa de pesquisa, digite **Halogen Software**.

    ![Criação de um usuário de teste do AD do Azure](./media/halogen-software-tutorial/tutorial_halogensoftware_search.png)

1. No painel de resultados, selecione **Halogen Software** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/halogen-software-tutorial/tutorial_halogensoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Nesta seção, você configura e testa o logon único do Azure AD com o Halogen Software com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Halogen Software é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Halogen Software.

No Halogen Software, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Halogen Software, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
1. **[Como criar de um usuário de teste do Halogen Software](#creating-a-halogen-software-test-user)** – para ter um equivalente de Brenda Fernandes no Halogen Software que esteja vinculado à representação do usuário no Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único em seu aplicativo do Halogen Software.

**Para configurar o logon único do Azure AD com o Halogen Software, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Halogen Software**, clique em **Logon único**.

    ![Configurar o logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar o logon único](./media/halogen-software-tutorial/tutorial_halogensoftware_samlbase.png)

1. Na seção **URLs e Domínio do Halogen Software**, execute as seguintes etapas:

    ![Configurar o logon único](./media/halogen-software-tutorial/tutorial_halogensoftware_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://global.hgncloud.com/<companyname>`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:`https://global.halogensoftware.com/<companyname>`, `https://global.hgncloud.com/<companyname>`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte ao Cliente do Halogen Software](https://support.halogensoftware.com/) para obter esses valores. 
 


1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![Configurar o logon único](./media/halogen-software-tutorial/tutorial_halogensoftware_certificate.png) 

1. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/halogen-software-tutorial/tutorial_general_400.png)

1. Em uma janela diferente do navegador, faça logon no aplicativo **Halogen Software** como administrador.

1. Clique na guia **Opções** . 
   
    ![O que é o Azure AD Connect][12]

1. No painel de navegação esquerdo, clique em **Configuração do SAML**. 
   
    ![O que é o Azure AD Connect][13]

1. Na página **Configuração do SAML** , realize as seguintes etapas: 

    ![O que é o Azure AD Connect][14]

     a. Como **Identificador exclusivo**, selecione **NameID**.

     b. Em **Identificador exclusivo mapeia para**, selecione **Nome de usuário**.
  
     c. Para carregar o arquivo de metadados baixado, clique em **Procurar** para selecionar o arquivo e clique em **Carregar arquivo**.
 
     d. Para testar a configuração, clique em **Executar Teste**. 
    
    >[!NOTE]
    >Você precisa esperar pela mensagem "*O teste de SAML foi concluído. Feche esta janela*". Feche a janela do navegador aberta. A caixa de seleção **Habilitar SAML** só será habilitada se o teste for concluído. 
     
     e. Selecione **Habilitar SAML**.
    
     f. Clique em **Salvar Alterações**. 

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/halogen-software-tutorial/create_aaduser_01.png) 

1. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/halogen-software-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/halogen-software-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/halogen-software-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **BrendaFernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-halogen-software-test-user"></a>Criação de um usuário de teste do Halogen Software

O objetivo desta seção é criar um usuário chamado Britta Simon no Halogen Software.

**Para criar um usuário chamado Britta Simon no Halogen Software, execute as seguintes etapas:**

1. Faça logon no aplicativo **Halogen Software** como administrador.

1. Clique na guia **Central do Usuário** e clique em **Criar Usuário**.
   
    ![O que é o Azure AD Connect][300]  

1. Na página do diálogo **Novo Usuário** , realize as seguintes etapas:
   
    ![O que é o Azure AD Connect][301]

    a. Na caixa de texto **Nome**, digite o nome do usuário, como **Brenda**.
    
    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como **Fernandes**. 

    c. Na caixa de texto **Nome de Usuário**, digite **Brenda Fernandes**, o nome de usuário como no portal do Azure.

    d. Na caixa de texto **Senha** , digite uma senha para Britta.
    
    e. Clique em **Salvar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você habilita Brenda Fernandes a usar o logon único do Azure concedendo acesso ao Halogen Software.

![Atribuir usuário][200] 

**Para atribuir Britta Simon ao Halogen Software, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **Halogen Software**.

    ![Configurar o logon único](./media/halogen-software-tutorial/tutorial_halogensoftware_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Quando clica no bloco Halogen Software no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo Halogen Software.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/halogen-software-tutorial/tutorial_general_01.png
[2]: ./media/halogen-software-tutorial/tutorial_general_02.png
[3]: ./media/halogen-software-tutorial/tutorial_general_03.png
[4]: ./media/halogen-software-tutorial/tutorial_general_04.png

[12]: ./media/halogen-software-tutorial/tutorial_halogen_12.png

[13]: ./media/halogen-software-tutorial/tutorial_halogen_13.png

[14]: ./media/halogen-software-tutorial/tutorial_halogen_14.png

[100]: ./media/halogen-software-tutorial/tutorial_general_100.png

[200]: ./media/halogen-software-tutorial/tutorial_general_200.png
[201]: ./media/halogen-software-tutorial/tutorial_general_201.png
[202]: ./media/halogen-software-tutorial/tutorial_general_202.png
[203]: ./media/halogen-software-tutorial/tutorial_general_203.png

[300]: ./media/halogen-software-tutorial/tutorial_halogen_300.png

[301]: ./media/halogen-software-tutorial/tutorial_halogen_301.png
