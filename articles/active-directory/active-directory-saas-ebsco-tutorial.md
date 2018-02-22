---
title: "Tutorial: Integração do Active Directory do Azure com EBSCO | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e EBSCO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.openlocfilehash: ea7fe09c31d88cf2095b3a3777b6b1f9feb8df46
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Tutorial: Integração do Active Directory do Azure com EBSCO

Neste tutorial, você aprenderá como integrar EBSCO com o Azure Active Directory (Azure AD).

Integrar EBSCO com o Azure AD fornece os seguintes benefícios:

- Você pode controlar no Azure AD que tenha acesso ao EBSCO.
- Você pode habilitar os usuários para entrar automaticamente no EBSCO (logon único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD com EBSCO, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Um logon único EBSCO na assinatura habilitada

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando EBSCO da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-ebsco-from-the-gallery"></a>Adicionando EBSCO da galeria
Para configurar a integração de EBSCO no Azure AD, você precisa adicionar EBSCO da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar EBSCO da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **EBSCO**, selecione **EBSCO** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![EBSCO na lista de resultados](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com EBSCO com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário em EBSCO é equivalente a um usuário no Azure AD. Em outras palavras, uma relação de link entre um usuário do Azure AD e o usuário relacionado em EBSCO precisa ser estabelecida.

Para configurar e testar o logon único do Azure AD com EBSCO, você precisa concluir os blocos de construção a seguir:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste EBSCO](#create-an-ebsco-test-user)** – você pode automatizar o provisionamento/a personalização de usuário de EBSCOhost. EBSCO oferece suporte ao provisionamento just in time do usuário.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo EBSCO.

**Para configurar o logon único do Azure AD com EBSCO, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **EBSCO**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_samlbase.png)

3. Na seção **Domínio e URLs do EBSCO**, execute as etapas a seguir se deseja configurar o aplicativo em modo iniciado **IDP**:

    ![Informações de logon único de domínio e URLs do EBSCO](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_url.png)

    Na caixa de texto **Identificador**, digite uma URL: `pingsso.ebscohost.com`

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de domínio e URLs do EBSCO](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`
     
    > [!NOTE] 
    > O valor da URL de logon não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte do cliente EBSCO](mailto:sso@ebsco.com) para obter o valor. 

    o   **Elementos exclusivos:**  

    o   **Custid** = Inserir ID de cliente exclusivo do EBSCO 

    o   **Profile** = Clientes podem personalizar o link para direcionar usuários a um perfil específico (dependendo do que adquirirem do EBSCO). Eles podem digitar uma ID de perfil específica. As IDs principais são eds (Serviço de descoberta de EBSCO) e ehost (bancos de dados do EBSOCOhost). Instruções para isso são fornecidas [aqui](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

5. O aplicativo EBSCO espera as asserções SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar o logon único](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_attribute.png)

    > [!Note]
    > O atributo **nome** é obrigatório e está mapeado com **Identificador de usuário** no aplicativo EBSCO. Isso é adicionado por padrão, então você não precisa adicioná-lo manualmente.
    
6. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:
    
    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |    
    | Nome   | user.givenname |
    | Sobrenome   | user.surname |
    | Email   | user.mail |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-ebsco-tutorial/tutorial_officespace_04.png)

    ![Configurar o logon único](./media/active-directory-saas-ebsco-tutorial/tutorial_attribute_05.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **Ok**

7. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_certificate.png) 

8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-ebsco-tutorial/tutorial_general_400.png)
    
9. Para configurar o logon único no lado do **EBSCO**, você precisa enviar o **XML de metadados** baixado para a [equipe de suporte do EBSCO](mailto:sso@ebsco.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-ebsco-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-ebsco-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-ebsco-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-ebsco-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-ebsco-test-user"></a>Criar um usuário de teste EBSCO

No caso de EBSCO, o provisionamento de usuários é automático.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

O Azure AD passa os dados necessários para o aplicativo EBSCO. O provisionamento de usuário do EBSCO pode ser automático OU exige uma única forma. Depende de se o cliente possui muitas contas de EBSCOhost pré-existentes com configurações pessoais salvadas. O mesmo pode ser discutido com a [equipe de suporte EBSCO](mailto:sso@ebsco.com) durante a implementação. De qualquer forma, o cliente não precisa criar contas de EBSCOhost antes de testar.

   >[!Note]
   >Você pode automatizar o provisionamento/a personalização de usuário de EBSCOhost. Entre em contato com a [equipe de suporte EBSCO](mailto:sso@ebsco.com) sobre o provisionamento just in time do usuário. 
 
### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará Brenda Fernandes para usar logon único do Azure, concedendo acesso ao EBSCO.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao EBSCO, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **EBSCO**.

    ![O link EBSCO na Lista de aplicativos](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

1. Quando você clicar no bloco EBSCO no Painel de acesso, você deve ser conectado automaticamente ao seu aplicativo EBSCO.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

2. Depois que fizer logon no aplicativo, clique no botão **entrar** no canto superior direito.

    ![A entrada do EBSCO na Lista de aplicativos](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Você receberá uma solicitação única para emparelhar o logon institucional/SAML com um **Vincular sua conta existente do MyEBSCOhost à sua conta institucional agora** OU **Criar uma nova conta de MyEBSCOhost e vinculá-la à sua conta institucional**. A conta é usada para personalização no aplicativo EBSCOhost. Selecione a opção **Criar uma nova conta** e você verá que o formulário de personalização é previamente concluído com os valores da resposta saml como mostrado na captura de tela abaixo. Clique em **'Continuar'** para salvar essa seleção.
    
     ![O usuário EBSCO na Lista de aplicativos](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_user.png)

4. Depois de concluir a configuração acima, limpe cookies/cache e faça logon novamente. Você não precisará entrar manualmente novamente, e as configurações de personalização serão lembradas

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_203.png

