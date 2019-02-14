---
title: 'Tutorial: Integração do Azure Active Directory com o Procore SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d72f4ac39ac992d0386b3971cd4d3bc6616f0a28
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186447"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Tutorial: Integração do Azure Active Directory com o Procore SSO

Neste tutorial, você aprenderá a integrar o Procore SSO ao Azure AD (Azure Active Directory).

A integração do Procore SSO com o Azure AD oferece os seguintes benefícios:

- É possível controlar, no Azure AD, quem tem acesso ao Procore SSO.
- É possível permitir que seus usuários façam logon automaticamente no Procore SSO (Logon Único) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Procore SSO, são necessários os seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura ativada de logon único do Procore SSO

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Procore SSO da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-procore-sso-from-the-gallery"></a>Adicionando o Procore SSO da galeria

Para configurar a integração do Procore SSO com o Azure AD, é necessário adicionar o Procore SSO da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Procore SSO da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Procore SSO**, selecione **Procore SSO** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Procore SSO na lista de resultados](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Procore SSO, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Procore SSO é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Procore SSO.

Para configurar e testar o logon único do Azure AD com o Procore SSO, é necessário concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste Procore SSO](#creating-a-procore-sso-test-user)** - para ter um equivalente de Britta Simon no Procore SSO vinculado à representação de usuário do Microsoft Azure Active Directory.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Microsoft Azure Active Directory no portal do Azure e configurará o logon único em seu aplicativo Procore SSO.

**Para configurar o logon único do Azure AD com o Procore SSO, siga as etapas abaixo:**

1. No portal do Azure, na página de integração do aplicativo **Procore SSO**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

    ![Informações do domínio Procore SSO Domain e URLs single sign-on](./media/procoresso-tutorial/tutorial_procoresso_url.png)

5. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para baixar **Metadados de federação XML** e salve o arquivo de metadados no seu computador.

    ![O link de download do Certificado](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

6. Na seção **Configurar o Procore SSO**, copie o URL apropriado de acordo com sua exigência.

     a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração de procore SSO](common/configuresection.png)

7. Para configurar o logon único no lado do **Procore SSO**, faça logon no site da sua empresa Procore como administrador.

8. Na lista suspensa da caixa de ferramentas, clique em **Admin** para abrir a página de configurações de SSO.

    ![Configurar o logon único](./media/procoresso-tutorial/procore_tool_admin.png)

9. Cole os valores nas caixas conforme descrito abaixo-

    ![Configurar o logon único](./media/procoresso-tutorial/procore_setting_admin.png)  

     a. Na caixa de texto **Single Sign On Issuer URL**, cole o valor de **Identificador do Microsoft Azure Active Directory** que você copiou do portal do Azure.

    b. Na caixa **URL de destino do logon SAML**, cole o valor do **URL de login** que você copiou do portal do Microsoft Azure.

    c. Agora, abra o **XML de metadados de federação** baixado acima do portal do Azure e copie o certificado na tag denominada **X509Certificate**. Cole o valor copiado para a caixa **Certificado x509 de logon único**.

10. Clique em **Salvar alterações**.

11. Após essas configurações, é necessário enviar o **nome de domínio** (por exemplo, **contoso.com**) por meio do qual você está fazendo logon no Procore para a [equipe de suporte Procore](https://support.procore.com/) e eles ativarão o SSO federado para esse domínio.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Criar um usuário do AD do Azure][100]

2. Selecione **Novo usuário** na parte superior da tela.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_01.png) 

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![Criação de um usuário de teste do AD do Azure](common/create_aaduser_02.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No **nome de usuário** , digite **brittasimon@yourcompanydomain.extension**  
       Por exemplo, BrittaSimon@contoso.com

    c. Selecione **Propriedades**, marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Selecione **Criar**.

### <a name="creating-a-procore-sso-test-user"></a>Criando um usuário de teste do Procore SSO

Por favor, siga os passos abaixo para criar um usuário de teste Procore no lado Procore SSOc.

1. Faça logon no site da sua empresa do Procore como administrador.  

2. Na lista suspensa da caixa de ferramentas, clique em **Diretório** para abrir a página do diretório da empresa.

    ![Configurar o logon único](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Clique na opção **Adicionar uma pessoa** para abrir o formulário e insira as seguintes opções –

    ![Configurar o logon único](./media/procoresso-tutorial/Procore_user_add.png)

     a. Na caixa de texto **Nome**, digite o nome do usuário como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como **Fernandes**.

    c. Na caixa de texto **Endereço de email**, digite o endereço de email do usuário como **BrittaSimon@contoso.com**.

    d. Selecione **Modelo de permissão** como **Aplicar o modelo de permissão mais tarde**.

    e. Clique em **Criar**.

4. Verifique e atualize os detalhes do contato recém-adicionado.

    ![Configurar o logon único](./media/procoresso-tutorial/Procore_user_check.png)

5. Clique em **Salvar e enviar convite** (se for necessário enviar um convite por email) ou **Salvar** (Salvar diretamente) para concluir o registro do usuário.
    
    ![Configurar o logon único](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que o Brenda Fernandes use o logon único do Azure, concedendo acesso ao Procore SSO.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Procore SSO**.

    ![Configurar o logon único](./media/procoresso-tutorial/tutorial_procoresso_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Procore SSO no Painel de Acesso, seu logon deverá ser feito automaticamente no aplicativo Procore SSO.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
