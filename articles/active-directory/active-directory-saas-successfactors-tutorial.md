---
title: "Tutorial: integração do Azure Active Directory ao SuccessFactors | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o SuccessFactors."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: b9545599b4ac02927c38931777a3cee623a4e940
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Tutorial: Integração do Azure Active Directory com o SuccessFactors

Neste tutorial, você aprenderá a integrar o SuccessFactors ao Azure AD (Azure Active Directory).

A integração do SuccessFactors ao Azure AD oferece os seguintes benefícios:

- No Azure AD, você pode controlar quem tem acesso ao SuccessFactors.
- Você pode habilitar seus usuários a fazerem logon automaticamente no SuccessFactors (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

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
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do SuccessFactors da galeria
2. Configurar e testar o logon único do AD do Azure

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

    ![SuccessFactors na lista de resultados](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SuccessFactors, com base em um usuário de teste chamado “Britta Simon”.

Para que o logon único funcione, o Azure AD precisa saber qual é usuário correspondente no SuccessFactors para um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SuccessFactors.

No SuccessFactors, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o SuccessFactors, é preciso concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do SuccessFactors](#create-a-successfactors-test-user)** : para ter um equivalente de Brenda Fernandes no SuccessFactors que esteja vinculado à representação dela no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo SuccessFactors.

**Para configurar o logon único do Azure AD com o SuccessFactors, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **SuccessFactors**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_samlbase.png)

3. Na seção **Domínio e URLs do SuccessFactors**, execute as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do SuccessFactors](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_url.png)

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
    > Esses valores não são reais. Atualize esses valores com o Identificador real, a URL de Resposta e a URL de Entrada. Entre em contato com a [equipe de suporte do cliente do SuccessFactors](https://www.successfactors.com/en_us/support.html) para obter esses valores. 

4. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-successfactors-tutorial/tutorial_general_400.png)
    
6. Na seção **Configuração do SuccessFactors**, clique em **Configurar SuccessFactors** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_configure.png) 

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
   
    c. Na caixa de texto **URL do Emissor**, cole o valor da **ID da Entidade SAML** copiada do Portal do Azure.
   
    d. Selecione **Resposta(Gerada pelo Cliente/IdP/AP)** como **Exigir Assinatura Obrigatória**.
   
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
   
    ![Configurar Logon Único][29]
    
    a. Visite **Configurações da Empresa**(próximo à parte inferior).
   
    b. marque a caixa de seleção ao lado de **Habilitar Nome de Usuário que Não Diferencia Maiúsculas de Minúsculas**.
   
    c.Clique em **Salvar**.
   
    > [!NOTE] 
    > Se você tentar habilitar essa opção, o sistema verifica se ele cria um nome de logon SAML duplicado. Por exemplo, se o cliente tiver os nomes de usuário Usuário1 e usuário1. Parar de diferenciar maiúsculas e minúsculas cria essas duplicatas. O sistema fornece a você uma mensagem de erro e não habilita o recurso. O cliente precisa alterar um dos nomes de usuário para que ele seja digitado diferente.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-successfactors-test-user"></a>Crie um usuário de teste do SuccessFactors

Para permitir que os usuários do Azure AD façam logon no SuccessFactors, eles deverão ser provisionados no SuccessFactors.  
No caso do SuccessFactors, o provisionamento será uma tarefa manual.

Para obter os usuários criados no SuccessFactors, você precisará entrar em contato com a [equipe de suporte do SuccessFactors](https://www.successfactors.com/en_us/support.html).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Britta Simon use o logon único do Azure concedendo acesso ao SuccessFactors.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao SuccessFactors, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **SuccessFactors**.

    ![O link do SuccessFactors na lista de aplicativos](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco SuccessFactors no Painel de Acesso, deverá fazer logon automaticamente no seu aplicativo SuccessFactors.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png

[100]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_203.png

