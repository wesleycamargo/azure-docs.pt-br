---
title: 'Tutorial: integração do Azure Active Directory ao SuccessFactors | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 89224b32efaecdf7a2797b034b1beac7ad191ee5
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685218"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Tutorial: Integração do Azure Active Directory com o SuccessFactors

Neste tutorial, você aprenderá a integrar o SuccessFactors ao Azure AD (Azure Active Directory).

A integração do SuccessFactors ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao SuccessFactors.
- Você pode habilitar seus usuários a fazerem logon automaticamente no SuccessFactors (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SuccessFactors, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do SuccessFactors

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do SuccessFactors da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-successfactors-from-the-gallery"></a>Adição do SuccessFactors da galeria

Para configurar a integração do SuccessFactors ao Azure AD, você precisará adicionar o SuccessFactors da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SuccessFactors da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **SuccessFactors**, selecione **SucessFactors** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SuccessFactors na lista de resultados](./media/successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SuccessFactors, com base em um usuário de teste chamado “Britta Simon”.

Para que o logon único funcione, o Azure AD precisa saber qual é usuário correspondente no SuccessFactors para um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SuccessFactors.

Para configurar e testar o logon único do Azure AD com o SuccessFactors, é preciso concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do SuccessFactors](#creating-a-successfactors-test-user)** - para ter um equivalente de Brenda Fernandes no SuccessFactors que esteja vinculado à representação do usuário do Microsoft Azure Active Directory.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo SuccessFactors.

**Para configurar o logon único do Azure AD com o SuccessFactors, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **SuccessFactors**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do SuccessFactors](./media/successfactors-tutorial/tutorial_successfactors_url.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. Na caixa de texto **URL de resposta** , digite uma URL no seguinte padrão:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte do cliente do SuccessFactors](https://www.successfactors.com/support.html) para obter esses valores. 

5. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para fazer o download do **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/successfactors-tutorial/tutorial_successfactors_certificate.png) 

6. Na seção **Configurar SuccessFactors**, copie o URL apropriado de acordo com sua exigência.

     a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração do SuccessFactors](common/configuresection.png)

7. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do **portal de administração do SuccessFactors** como administrador.
    
8. Visite **Segurança de Aplicativo** e nativo do **Recurso Logon Único**. 

9. Coloque qualquer valor em **Redefinir Token** e clique em **Salvar Token** para habilitar SSO do SAML.
   
    ![Configurar o logon único no lado do aplicativo][11]

    > [!NOTE] 
    > Esse valor é usado como chave liga/desliga. Se nenhum valor for salvo, o SSO do SAML será ATIVADO. Se um valor em branco for salvo, o SSO do SAML será DESATIVADO.

10. Nativo da captura de tela abaixo e execute as ações a seguir:
   
    ![Configurar o logon único no lado do aplicativo][12]
   
     a. Selecione o botão de opção **SSO do SAML v2**
   
    b. Defina o **Nome da Parte de Declaração SAML** (por exemplo, emissor SAML + nome da empresa).
   
    c. Na caixa de texto **Issuer URL**, cole o valor **Identificador do Microsoft Azure Active Directory** que você copiou do portal do Azure.
   
    d. Selecione **Asserção** como **Exigir Assinatura Obrigatória**.
   
    e. Selecione **Habilitado** como **Habilitar Sinalizador SAML**.
   
    f. Selecione **Não** como **Assinatura da Solicitação de Logon (Gerado por SF/SP/RP)**.
   
    g. Selecione **Perfil de Navegador/Postagem** como **Perfil SAML**.
   
    h. Selecione **Não** como **Impor Período de Certificado Válido**.
   
    i. Copie o conteúdo do arquivo do certificado baixado do portal do Azure e, então, cole-o na caixa de texto **Certificado de Verificação SAML**.

    > [!NOTE] 
    > O conteúdo do certificado deve ter começar marcas de certificado do certificado e de fim.

11. Navegue até SAML V2 e então execute as seguintes etapas:
   
    ![Configurar o logon único no lado do aplicativo][13]
   
     a. Selecione **Sim** como **Logoff Global iniciado por SP de suporte**.
   
    b. Na caixa de texto **URL do Global Logout Service (destino LogoutRequest)**, cole o valor **URL de logout** que você copiou do portal do Azure.
   
    c. Selecione **Não** como **Exigir que sp criptografe todos os elementos NameID**.
   
    d. Selecione **não especificado** como **Formato de NameID**.
   
    e. Selecione **Sim** como **Habilitar logon iniciado por sp (AuthnRequest)**.
   
    f. Na caixa de texto **Enviar solicitação como emissor de toda a empresa**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure.

12. Execute estas etapas se quiser fazer com os nomes de usuário de logon diferenciem maiúsculas de minúsculas.
   
    ![Configurar o logon único][29]
    
     a. Visite **Configurações da Empresa**(próximo à parte inferior).
   
    b. marque a caixa de seleção ao lado de **Habilitar Nome de Usuário que Não Diferencia Maiúsculas de Minúsculas**.
   
    c.Clique em **Salvar**.
   
    > [!NOTE] 
    > Se você tentar habilitar essa opção, o sistema verifica se ele cria um nome de logon SAML duplicado. Por exemplo, se o cliente tiver os nomes de usuário Usuário1 e usuário1. Parar de diferenciar maiúsculas e minúsculas cria essas duplicatas. O sistema fornece a você uma mensagem de erro e não habilita o recurso. O cliente precisa alterar um dos nomes de usuário para que ele seja digitado diferente.

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

### <a name="creating-a-successfactors-test-user"></a>Criação de um usuário de teste do SuccessFactors

Para permitir que os usuários do Azure AD façam logon no SuccessFactors, eles deverão ser provisionados no SuccessFactors.  
No caso do SuccessFactors, o provisionamento será uma tarefa manual.

Para obter os usuários criados no SuccessFactors, você precisará entrar em contato com a [equipe de suporte do SuccessFactors](https://www.successfactors.com/support.html).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Britta Simon use o logon único do Azure concedendo acesso ao SuccessFactors.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **SuccessFactors**.

    ![Configurar o logon único](./media/successfactors-tutorial/tutorial_successfactors_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco SuccessFactors no Painel de Acesso, deverá fazer logon automaticamente no seu aplicativo SuccessFactors.
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
[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
