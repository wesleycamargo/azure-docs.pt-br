---
title: 'Tutorial: integração do Azure Active Directory com o BitaBIZ | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o BitaBIZ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 894363a48f0ba1f45664451d5508473f78aceb07
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162201"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Tutorial: integração do Azure Active Directory com o BitaBIZ

Neste tutorial, você aprenderá a integrar o BitaBIZ ao Azure Active Directory (Azure AD).

A integração do BitaBIZ ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao BitaBIZ.
- Você pode permitir que usuários façam logon automaticamente no BitaBIZ (logon único) com as respectivas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao BitaBIZ, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do BitaBIZ

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o BitaBIZ a partir da galeria
1. configurar e testar o logon único do AD do Azure

## <a name="adding-bitabiz-from-the-gallery"></a>Adicionar o BitaBIZ a partir da galeria
Para configurar a integração do BitaBIZ ao Azure AD, você precisará adicionar o Bynder a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o BitaBIZ a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

1. Na caixa de pesquisa, digite **BitaBIZ**, selecione **BitaBIZ** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![BitaBIZ na lista de resultados](./media/bitabiz-tutorial/tutorial_bitabiz_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o BitaBIZ, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do BitaBIZ é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do AD do Azure e o usuário relacionado no BitaBIZ.

No BitaBIZ, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o BitaBIZ, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
1. **[Criar um usuário de teste do BitaBIZ](#create-a-bitabiz-test-user)** – para ter um equivalente de Brenda Fernandes no BitaBIZ vinculado à representação do usuário no Azure AD.
1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo BitaBIZ.

**Para configurar o logon único do Azure AD com o BitaBIZ, realize as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **BitaBIZ**, clique em **Logon único**.

    ![Link Configurar logon único][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/bitabiz-tutorial/tutorial_bitabiz_samlbase.png)

1. Na seção **Domínio e URLs do BitaBIZ**, realize as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo IDP:

    ![Informações de logon único de Domínio e URLs do BitaBIZ](./media/bitabiz-tutorial/tutorial_bitabiz_url.png)

    Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://www.bitabiz.com/<instanceId>`

    > [!NOTE] 
    > O valor na URL anterior é apenas para demonstração. Você atualiza o valor com o identificador real, o que é explicado no tutorial posteriormente.

1. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do BitaBIZ](./media/bitabiz-tutorial/tutorial_bitabiz_url1.png)

    Na caixa de texto **URL de Logon**, digite a URL: `https://www.bitabiz.com/dashboard`

1. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/bitabiz-tutorial/tutorial_bitabiz_certificate.png) 

1. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/bitabiz-tutorial/tutorial_general_400.png)
    
1. Na seção **Configuração do BitaBIZ**, clique em **Configurar o BitaBIZ** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida.**

    ![Configuração do BitaBIZ](./media/bitabiz-tutorial/tutorial_bitabiz_configure.png) 

1. Em uma janela diferente do navegador da Web, faça logon em seu locatário do BitaBIZ como um administrador.

1. Clique em **CONFIGURAR ADMIN**.

    ![Configuração do BitaBIZ](./media/bitabiz-tutorial/settings1.png)

1. Clique em **Integrações Microsoft** na seção **Adicionar valor**.

    ![Configuração do BitaBIZ](./media/bitabiz-tutorial/settings2.png)

1. Role para baixo até a seção **Microsoft Azure AD (Habilitar logon único)** e execute as seguintes etapas:

    ![Configuração do BitaBIZ](./media/bitabiz-tutorial/settings3.png)

     a. Copie o valor da caixa de texto **ID da entidade ("Identificador" no Azure AD)** e cole-o na caixa e texto **Identificador** na seção **Domínio e URLs do BitaBIZ** no portal do Azure. 
    
    b. Cole a **URL de Serviço de Logon Único do SAML** que você copiou do portal do Azure na caixa de texto **URL de Serviço de Logon Único do Azure AD**.
    
    c. Na caixa de texto **ID da Entidade do SAML do Azure AD**, cole a **ID da Entidade do SAML** copiada do portal do Azure.

    d. Abra o arquivo de **Certificado (Base64)** baixado no bloco de notas, copie o conteúdo dele para a área de transferência e, em seguida, cole-o na caixa de texto **Certificado de autenticação do Azure AD (codificação Base64)**.

    e. Adicione seu nome de domínio de email corporativo, ou seja, minhaempresa.com na caixa de texto **Nome de domínio** para atribuir o SSO aos usuários em sua empresa com este domínio de email (OPCIONAL).
    
    f. Marque **Habilitado para SSO** para a conta do BitaBIZ.
    
    g. Clique em **Salvar a configuração do Azure AD** para salvar e ativar a configuração de SSO.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/bitabiz-tutorial/create_aaduser_01.png)

1. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/bitabiz-tutorial/create_aaduser_02.png)

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/bitabiz-tutorial/create_aaduser_03.png)

1. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/bitabiz-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-bitabiz-test-user"></a>Criar um usuário de teste BitaBIZ

Para permitir que os usuários do Azure AD façam logon no BitaBIZ, eles devem ser provisionados no BitaBIZ.  
No caso do BitaBIZ, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do BitaBIZ como administrador.

1. Clique em **CONFIGURAR ADMIN**.

    ![Adicionar usuário no BitaBIZ](./media/bitabiz-tutorial/settings1.png)

1. Clique em **Adicionar usuários** na seção **Organização**.

    ![Adicionar usuário no BitaBIZ](./media/bitabiz-tutorial/user1.png)

1. Clique em **Adicionar novo funcionário**.

    ![Adicionar usuário no BitaBIZ](./media/bitabiz-tutorial/user2.png)

1. Na página da caixa de diálogo **“Adicionar novo funcionário”**, execute as seguintes etapas:

    ![Adicionar usuário no BitaBIZ](./media/bitabiz-tutorial/user3.png)

     a. Na caixa de texto **Nome**, digite o nome do usuário, como Brenda.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário, como Fernandes.

    c. Na caixa de texto **Email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.

    d. Selecione uma data em **Data de admissão**.

    e. Existem outros atributos de usuário opcionais, que podem ser configurados para o usuário. Consulte o [Documento de configuração do usuário](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) para obter mais detalhes.    
    
    f. Clique em **Salvar funcionário**.
    
    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe um email e segue um link para confirmar sua conta antes que ela se torne ativa.
    
### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao BitaBIZ.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao BitaBIZ, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

1. Na lista de aplicativos, selecione **BitaBIZ**.

    ![O link do BitaBIZ na lista de Aplicativos](./media/bitabiz-tutorial/tutorial_bitabiz_app.png)  

1. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do BitaBIZ no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do BitaBIZ.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bitabiz-tutorial/tutorial_general_01.png
[2]: ./media/bitabiz-tutorial/tutorial_general_02.png
[3]: ./media/bitabiz-tutorial/tutorial_general_03.png
[4]: ./media/bitabiz-tutorial/tutorial_general_04.png

[100]: ./media/bitabiz-tutorial/tutorial_general_100.png

[200]: ./media/bitabiz-tutorial/tutorial_general_200.png
[201]: ./media/bitabiz-tutorial/tutorial_general_201.png
[202]: ./media/bitabiz-tutorial/tutorial_general_202.png
[203]: ./media/bitabiz-tutorial/tutorial_general_203.png

