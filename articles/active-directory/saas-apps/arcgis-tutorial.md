---
title: 'Tutorial: Integração do Azure Active Directory ao ArcGIS Online | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: 24a82bbaf47153791da2f21a0b68c2f81c0670e7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446349"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Tutorial: Integração do Azure Active Directory ao ArcGIS Online

Neste tutorial, você aprende a integrar o ArcGIS Online ao Azure AD (Azure Active Directory).

A integração do ArcGIS Online ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao ArcGIS Online.
- É possível permitir que os usuários se conectem automaticamente ao ArcGIS Online (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao ArcGIS Online, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do ArcGIS Online

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o ArcGIS Online por meio da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-arcgis-online-from-the-gallery"></a>Adicionando o ArcGIS Online por meio da galeria
Para configurar a integração do ArcGIS Online ao Azure AD, você precisa adicionar o ArcGIS Online à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o ArcGIS Online por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **ArcGIS Online**, selecione **ArcGIS Online** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![ArcGIS Online na lista de resultados](./media/arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o ArcGIS Online, com base em um usuário de teste chamado “Britta Simon”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do ArcGIS Online é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ArcGIS Online.

No ArcGIS Online, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o ArcGIS Online, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do ArcGIS Online](#create-a-arcgis-online-test-user)** – para ter um equivalente de Brenda Fernandes no ArcGIS Online que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo ArcGIS Online.

**Para configurar o logon único do Azure AD com o ArcGIS Online, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo do **ArcGIS Online**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

1. Na seção **Domínio e URLs do ArcGIS Online**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do ArcGIS Online](./media/arcgis-tutorial/tutorial_arcgisonline_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.maps.arcgis.com`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `<companyname>.maps.arcgis.com`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do ArcGIS Online](http://support.esri.com/en/) para obter esses valores. 
 


1. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/arcgis-tutorial/tutorial_general_400.png)

1. Em outra janela do navegador da Web, faça logon em seu site de empresa ArcGIS como um administrador.

1. Clique em **EDITAR CONFIGURAÇÕES**.

    ![Editar Configurações](./media/arcgis-tutorial/ic784742.png "Editar Configurações")

1. Clique em **Segurança**.

    ![Segurança](./media/arcgis-tutorial/ic784743.png "Segurança")

1. Em **Logons Corporativos**, clique em **DEFINIR PROVEDOR DE IDENTIDADE**.

    ![Logons Corporativos](./media/arcgis-tutorial/ic784744.png "Logons Corporativos")

1. Na página de configuração **Definir Provedor de Identidade** , realize as seguintes etapas:
   
    ![Definir Provedor de Identidade](./media/arcgis-tutorial/ic784745.png "Definir Provedor de Identidade")
   
    a. Na caixa de texto **Nome**, digite o nome de sua organização.

    b. Para **Os metadados do Provedor de Identidade Corporativo serão fornecidos usando**, selecione **Um Arquivo**.

    c. Para carregar seu arquivo de metadados baixado, clique em **Escolher arquivo**.

    d. Clique em **DEFINIR PROVEDOR DE IDENTIDADE**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/arcgis-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/arcgis-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/arcgis-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/arcgis-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-arcgis-online-test-user"></a>Criar um usuário de teste do ArcGIS Online

Para permitir que os usuários do Azure AD façam logon no ArcGIS Online, eles devem ser provisionados no ArcGIS Online.  
No caso do ArcGIS Online, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **ArcGIS** .

1. Clique em **CONVIDAR MEMBROS**.
   
    ![Convidar Membros](./media/arcgis-tutorial/ic784747.png "Convidar Membros")

1. Selecione **Adicionar membros automaticamente sem enviar um email** e, depois, clique em **AVANÇAR**.
   
    ![Adicionar Membros Automaticamente](./media/arcgis-tutorial/ic784748.png "Adicionar Membros Automaticamente")

1. Na página do diálogo **Membros** , realize as seguintes etapas:
   
     ![Adicionar e examinar](./media/arcgis-tutorial/ic784749.png "Adicionar e examinar")
    
     a. Insira o **Email**, o **Nome** e o **Sobrenome** de uma conta válida do AAD que deseja provisionar.
  
     b. Clique em **ADICIONAR E EXAMINAR**.
1. Examine os dados inseridos e, depois, clique em **ADICIONAR MEMBROS**.
   
    ![Adicionar membro](./media/arcgis-tutorial/ic784750.png "Adicionar membro")
        
    > [!NOTE]
    > O titular da conta do Active Directory do Azure receberá um email e seguirá um link para confirmar a conta antes que ela se torne ativa.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao ArcGIS Online.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao ArcGIS Online, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **ArcGIS Online**.

    ![O link do ArcGIS Online na lista de Aplicativos](./media/arcgis-tutorial/tutorial_arcgisonline_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco ArcGIS Online no Painel de Acesso, deverá ser automaticamente conectado ao aplicativo ArcGIS Online.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/arcgis-tutorial/tutorial_general_01.png
[2]: ./media/arcgis-tutorial/tutorial_general_02.png
[3]: ./media/arcgis-tutorial/tutorial_general_03.png
[4]: ./media/arcgis-tutorial/tutorial_general_04.png

[100]: ./media/arcgis-tutorial/tutorial_general_100.png

[200]: ./media/arcgis-tutorial/tutorial_general_200.png
[201]: ./media/arcgis-tutorial/tutorial_general_201.png
[202]: ./media/arcgis-tutorial/tutorial_general_202.png
[203]: ./media/arcgis-tutorial/tutorial_general_203.png

