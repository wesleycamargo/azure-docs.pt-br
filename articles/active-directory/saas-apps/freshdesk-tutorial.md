---
title: 'Tutorial: integração do Azure Active Directory ao FreshDesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: ce302db74f831e67b576e4c0001f21473fd7f2e0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037517"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: integração do Azure Active Directory ao FreshDesk

Neste tutorial, você aprenderá a integrar o FreshDesk ao Azure AD (Azure Active Directory).

A integração do FreshDesk ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao FreshDesk
- Você pode habilitar seus usuários, com as respectivas contas do Azure AD, a fazer logon automaticamente no FreshDesk (logon único)
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao FreshDesk, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do FreshDesk com logon único habilitado

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o FreshDesk da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-freshdesk-from-the-gallery"></a>Adicionar o FreshDesk da galeria

Para configurar a integração do FreshDesk ao Azure AD, você precisa adicionar o FreshDesk da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o FreshDesk por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]

3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![APLICATIVOS][3]

4. Na caixa de pesquisa, digite **FreshDesk**. Selecione **FreshDesk** do painel de resultados e, em seguida, selecione o botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure

Nesta seção, você configurará e testará o logon único do Azure AD com o FreshDesk, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do FreshDesk é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do FreshDesk.

Essa relação de vínculo é estabelecida atribuindo o valor de **Nome de Usuário** ao Azure AD como sendo o valor do **endereço de email** no FreshDesk.

Para configurar e testar o logon único do Azure AD com o FreshDesk, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do FreshDesk](#creating-a-freshdesk-test-user)** – para ter um equivalente de Brenda Fernandes no FreshDesk que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único em seu aplicativo FreshDesk.

**Para configurar o logon único do Azure AD com o FreshDesk, realize as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **FreshDesk**, clique em **Logon único**.

    ![Configurar o logon único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. Na seção **Domínio e URLs do FreshDesk**, execute as seguintes etapas:

    ![Configurar o logon único](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. Na caixa de texto **URL de Entrada**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.freshdesk.com` ou qualquer outro valor sugerido pelo FreshDesk.

    > [!NOTE]
    > Observe que esse não é o valor real. Você precisa atualizar o valor com a URL de Entrada real. Para obter esse valor, entre em contato com a [equipe de suporte do cliente FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg).

4. Seu aplicativo espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração dos atributos do token SAML. A captura de tela a seguir mostra um exemplo disso. O valor padrão do **Identificador de Usuário** é **user.userprincipalname**, mas o **FreshDesk** espera que isso seja mapeado com o endereço de email do usuário. Para que você possa usar o atributo **user. mail** na lista ou usar o valor do atributo apropriado com base na configuração da sua organização.

    ![Configurar o logon único](./media/freshdesk-tutorial/tutorial_attribute.png)

5. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

    > [!NOTE]
    > Se você tiver problemas, consulte este [link](https://support.freshdesk.com/support/discussions/topics/317543).

6. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/freshdesk-tutorial/tutorial_general_400.png)

7. Instale o **OpenSSL** no seu sistema, se você não tiver instalado.

8. Abra o **Prompt de Comando** e execute os seguintes comandos:

    a. Insira o valor `openssl x509 -inform DER -in FreshDesk.cer -out certificate.crt` no prompt de comando.

    > [!NOTE]
    > Aqui, **FreshDesk.cer** é o certificado que você baixou do portal do Azure.

    b. Insira o valor `openssl x509 -noout -fingerprint -sha256 -inform pem -in certificate.crt` no prompt de comando. 
    
    > [!NOTE]
    > Aqui **certificate.crt** é o certificado de saída que é gerado na etapa anterior.

    c. Copie o valor de **Impressão Digital** e cole-o no Bloco de Notas. Remova os dois-pontos da Impressão Digital e obtenha seu valor final.

9. Na seção **Configuração do FreshDesk**, clique em **Configurar o FreshDesk** para abrir a janela Configurar logon. Copie a URL do serviço de logon único do SAML e a URL de logoff da seção de **Referência Rápida**.

    ![Configurar o logon único](./media/freshdesk-tutorial/tutorial_freshdesk_configure.png)

10. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Freshdesk como administrador.

11. Selecione o **Ícone de Configurações** e, na seção **Segurança**, realize as seguintes etapas:

    ![Logon Único](./media/freshdesk-tutorial/IC776770.png "Logon Único")
  
    a. Para **SSO (Logon Único)**, selecione **Ativado**.

    b. Selecione **SSO do SAML**.

    c. Na caixa de texto **URL de Logon do SAML**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure.

    d. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Saída** copiado do portal do Azure.

    e. Na caixa de texto **Impressão Digital do Certificado de Segurança**, cole o valor de **Impressão Digital** obtido anteriormente após remover os dois-pontos.
  
    f. Clique em **Salvar**.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/freshdesk-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.

    ![Criação de um usuário de teste do AD do Azure](./media/freshdesk-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.

    ![Criação de um usuário de teste do AD do Azure](./media/freshdesk-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/freshdesk-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.

### <a name="creating-a-freshdesk-test-user"></a>Criar um usuário de teste FreshDesk

Para permitir que os usuários do Azure AD façam logon no FreshDesk, eles devem ser provisionados no FreshDesk.  
No caso do FreshDesk, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Freshdesk** .

2. No menu na parte superior, clique em **Administrador**.

   ![Admin](./media/freshdesk-tutorial/IC776772.png "Admin")

3. Na guia **Configurações Gerais**, clique em **Agentes**.
  
   ![Agentes](./media/freshdesk-tutorial/IC776773.png "Agentes")

4. Clique em **Novo Agente**.

    ![Novo Agente](./media/freshdesk-tutorial/IC776774.png "Novo Agente")

5. Na caixa de diálogo Informações sobre o Agente, execute as seguintes etapas:

   ![Informações de Agente](./media/freshdesk-tutorial/IC776775.png "Informações de Agente")

   a. Na caixa de texto **Email** , digite o endereço de email do Azure AD da conta do Azure AD que você deseja provisionar.

   b. Na caixa de texto **Nome Completo** , digite o nome da conta do Azure AD que você deseja provisionar.

   c. Na caixa de texto **Título** , digite a conta do Azure AD que você deseja provisionar.

   d. Clique em **Salvar**.

    >[!NOTE]
    >O titular da conta do Azure AD receberá um email que inclui um link para confirmar a conta antes que ela se torne ativa.
    >
    >[!NOTE]
    >É possível usar qualquer outra ferramenta de criação da conta de usuário do Freshdesk ou APIs fornecidas pelo Freshdesk para provisionar as contas de usuário do AAD para o FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Box.

![Atribuir usuário][200]

**Para atribuir Brenda Fernandes ao FreshDesk, realize as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **FreshDesk**.

    ![Configurar o logon único](./media/freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco FreshDesk no Painel de Acesso, você deverá ser conduzido à página de logon para ser conectado automaticamente ao seu aplicativo FreshDesk.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/freshdesk-tutorial/tutorial_general_203.png