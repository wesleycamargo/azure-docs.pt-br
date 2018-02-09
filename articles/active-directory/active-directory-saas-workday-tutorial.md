---
title: "Tutorial: integração do Azure Active Directory com o Workday | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Workday."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: jeedes
ms.openlocfilehash: 1dfe319e708e6a4e815413da1a7bf635f4d0a53d
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2018
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Tutorial: Integração do Active Directory do Azure com o Workday

Neste tutorial, você aprenderá como integrar o Workday ao Azure AD (Azure Active Directory).

A integração do Workday com o Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Workday.
- É possível permitir que os usuários se conectem automaticamente ao Workday (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD ao Workday, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Workday habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Workday da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-workday-from-the-gallery"></a>Adicionar o Workday da galeria
Para configurar a integração do Workday com o Azure AD, é necessário adicionar o Workday da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Workday da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Workday**, selecione **Workday** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Workday na lista de resultados](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Workday, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Workday é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Workday.

No Workday, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Workday, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criação de um usuário de teste do Workday](#create-a-workday-test-user)** – para ter um equivalente de Brenda Fernandes no Workday que esteja vinculado à representação de usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Workday.

**Para configurar o logon único do Azure AD com o Workday, siga as etapas abaixo:**

1. No Portal do Azure, na página de integração de aplicativos do **Workday**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. Na seção **URLs e Domínio do Workday**, siga as etapas abaixo:

    ![Informações de logon único de Domínio e URLs do Workday](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml2.htmld`

    b. Na caixa de texto **Identificador**, digite uma URL: `http://www.workday.com`

4. Marque a opção **Mostrar configurações de URL avançadas** e execute a seguinte etapa:

    ![Informações de logon único de Domínio e URLs do Workday](./media/active-directory-saas-workday-tutorial/tutorial_workday_url1.png)

    Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://impl.workday.com/<tenant>/login-saml.htmld`
     
    > [!NOTE] 
    > Esses não são os valores reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Sua URL de resposta deve ter um subdomínio, por exemplo: www, wd2, wd3, wd3-impl, wd5, wd5-impl. O uso de algo como "*http://www.myworkday.com*" funciona, mas "*http://myworkday.com*", não. Contate a [equipe de suporte ao cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter esses valores.  

5. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)
    
7. Na seção **Configuração do Workday**, clique em **Configurar o Workday** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Workday](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 

8. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Workday como administrador.

9. Na **Caixa de pesquisa**, procure com o nome **Editar Configuração de Locatário – Segurança** no lado superior esquerdo da home page.
   
    ![Editar segurança de locatário](./media/active-directory-saas-workday-tutorial/IC782925.png "Editar segurança de locatário")

10. Na seção **URLs de Redirecionamento** , execute as seguintes etapas:
   
    ![URLs de redirecionamento](./media/active-directory-saas-workday-tutorial/IC7829581.png "URLs de redirecionamento")
   
    a. Clique em **Adicionar Linha**.
   
    b. Nas caixas de texto **URL de Redirecionamento de Logon** e **URL de Redirecionamento Móvel**, digite a **URL de Entrada** inserida na página **URLs e Domínio do Workday** do Portal do Azure.
   
    c. No Portal do Azure, na janela **Configurar logon**, copie a **URL de Saída** e, em seguida, cole-a na caixa de texto **URL de Redirecionamento de Logoff**.

    d. Na caixa de texto **Usado para Ambientes**, selecione o nome do ambiente.  

    >[!NOTE]
    > O valor do atributo Ambiente é vinculado ao valor da URL do locatário:  
    >–Se o nome de domínio da URL do locatário do Workday começar com impl, por exemplo, *https://impl.workday.com/\<tenant\>/login-saml2.htmld*, o atributo **Ambiente** deverá ser definido como Implementação.  
    >–Se o nome de domínio começar de outra forma, será necessário contatar a [equipe de suporte ao cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter o valor de **Ambiente** correspondente.

11. Na seção **Configuração do SAML** , execute as seguintes etapas:
   
    ![Instalação do SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "Instalação do SAML")
   
    a.  Selecione **Habilitar Autenticação SAML**.
   
    b.  Clique em **Adicionar Linha**.

12. Na seção **Provedores de Identidade do SAML**, execute as seguintes etapas:
   
    ![Provedores de Identidade SAML](./media/active-directory-saas-workday-tutorial/IC7829271.png "Provedores de Identidade SAML")
   
    a. Na caixa de texto **Nome do Provedor de Identidade**, digite um nome de provedor (por exemplo: *SPInitiatedSSO*).
   
    b. No Portal do Azure, na janela **Configurar logon**, copie o valor da **ID da Entidade SAML** e, em seguida, cole-o na caixa de texto **Emissor do Certificado**.

    ![Provedores de Identidade SAML](./media/active-directory-saas-workday-tutorial/IC7829271(1).png "Provedores de Identidade SAML")
   
    c. Selecione **Habilitar Logoff Iniciado pelo Workday**.
   
    d. No portal do Azure, na janela **Configurar logon**, copie o valor da **URL de Saída** e, em seguida, cole-o na caixa de texto **URL de Resposta de Logoff**.

    e. No Portal do Azure, na janela **Configurar logon**, copie o valor da **URL do Serviço de Logon Único SAML** e, em seguida, cole-o na caixa de texto **URL do Serviço de SSO IdP**.

    f. Na caixa de texto **Usado para Ambientes**, selecione o nome do ambiente.

    g. Clique em **Certificado de Chave Pública do Provedor de Identidade** e em **Criar**. 

    ![Criar](./media/active-directory-saas-workday-tutorial/IC782928.png "Criar")

    h. Clique em **Criar Chave Pública x509**. 

    ![Criar](./media/active-directory-saas-workday-tutorial/IC782929.png "Criar")

13. Na seção **Exibir Chave Pública x509** , realize as seguintes etapas: 
   
    ![Exibir chave pública x509](./media/active-directory-saas-workday-tutorial/IC782930.png "Exibir chave pública x509") 
   
    a. Na caixa de texto **Nome**, digite um nome para o seu certificado (por exemplo: *PPE\_SP*).
   
    b. Na caixa de texto **Válido de** , digite o valor do atributo “válido de” do seu certificado.
   
    c.  Na caixa de texto **Válido até** , digite o valor do atributo “válido até” do seu certificado.
   
    > [!NOTE]
    > Você pode obter as datas de “válido a partir de” e “válido até” do certificado baixado clicando duas vezes nele.  As datas são listadas na guia **Detalhes** .
    > 
    >
   
    d.  Abra seu certificado codificado em base-64 no bloco de notas e copie o conteúdo dele.
   
    e.  Na caixa de texto **Certificado** , cole o conteúdo da área de transferência.
   
    f.  Clique em **OK**.

14. Execute as seguintes etapas: 
   
    ![Configuração de SSO](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "Configuração de SSO")
   
    a.  Na caixa de texto **ID do provedor de serviço**, digite **http://www.workday.com**.
   
    b. Selecione **Não Esvazie a Solicitação de Autenticação iniciada por SP**.
   
    c. Para **Solicitação de Método de Autenticação de Assinatura** , selecione **SHA256**. 
   
    ![Método de assinatura da solicitação de autenticação](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "Método de assinatura da solicitação de autenticação") 
   
    d. Clique em **OK**. 
   
    ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-workday-test-user"></a>Criar um usuário de teste do Workday

Nesta seção, você cria um usuário chamado Brenda Fernandes no Workday. Trabalhe com [a equipe de suporte do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para adicionar os usuários à plataforma Workday. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Workday.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Workday, siga as etapas abaixo:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Workday**.

    ![O link do Workday na lista de Aplicativos](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Workday no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Workday.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workday-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workday-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workday-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workday-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workday-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workday-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workday-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workday-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workday-tutorial/tutorial_general_203.png

