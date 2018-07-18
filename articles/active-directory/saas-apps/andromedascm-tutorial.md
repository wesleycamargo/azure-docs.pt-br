---
title: 'Tutorial: Integração do Azure Active Directory ao Andromeda | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Andromeda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: jeedes
ms.openlocfilehash: 09f2f854e3b5a70938b6c47bb7dfb5fdf6d52df3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223204"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Tutorial: Integração do Azure Active Directory ao Andromeda

Neste tutorial, você aprende a integrar o Andromeda ao Azure AD (Azure Active Directory).

A integração do Andromeda ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Andromeda.
- Você pode permitir que os usuários sejam conectados automaticamente no Andromeda (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD ao Andromeda, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Andromeda

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Andromeda por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-andromeda-from-the-gallery"></a>Adicionando o Andromeda por meio da galeria
Para configurar a integração do Andromeda ao Azure AD, você precisa adicionar o Andromeda por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o Andromeda por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Andromeda**, selecione **Andromeda** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Andromeda na lista de resultados](./media/andromedascm-tutorial/tutorial_andromedascm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Andromeda, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Andromeda é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Andromeda.

Para configurar e testar o logon único do Azure AD com o Andromeda, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Andromeda](#create-an-andromeda-test-user)** – para ter um equivalente de Brenda Fernandes no Andromeda que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Andromeda.

**Para configurar o logon único do Azure AD com o Andromeda, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Andromeda**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/andromedascm-tutorial/tutorial_andromedascm_samlbase.png)

3. Na seção **Domínio e URLs do Andromeda**, execute as seguintes etapas se desejar configurar o aplicativo no modo iniciado pelo **IDP**:

    ![Informações de logon único de Domínio e URLs do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://<tenantURL>.ngcxpress.com/`

    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único de Domínio e URLs do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`
     
    > [!NOTE] 
    > O valor anterior não é um valor real. Você atualizará o valor com o identificador real, URL de resposta, e URL de logon que é explicado no tutorial posteriormente.

5. O aplicativo Andromeda espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar logon único attb](./media/andromedascm-tutorial/tutorial_andromedascm_attribute.png)

    > [!Important]
    > Desmarque out as definições do NameSpace ao configurá-los.
    
6. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | -------------- | -------------------- |    
    | função        | Função específica do aplicativo |
    | Tipo        | Tipo de Aplicativo |
    | company       | CompanyName    |

    > [!NOTE]
    > Não há valores reais. Esses valores são somente para finalidade de demonstração; use as funções de sua organização.

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar logon único Add](./media/andromedascm-tutorial/tutorial_attribute_04.png)

    ![Configurar logon único Addattb](./media/andromedascm-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    e. Clique em **OK**.

7. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/andromedascm-tutorial/tutorial_andromedascm_certificate.png) 

8. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/andromedascm-tutorial/tutorial_general_400.png)
    
9. Na seção **Configuração do Andromeda**, clique em **Configurar o Andromeda** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configuração do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_configure.png)

10. Faça logon no site de empresa do Andromeda como administrador.

11. Na parte superior da barra de menus, clique em **Admin** e navegue até **Administração**.

    ![Administrador do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

12. No lado esquerdo da barra de ferramentas na seção **Interfaces**, clique em **Configuração do SAML**.

    ![SAML do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

13. Na página **Configuração do SAML**, realize as seguintes etapas:

    ![Configuração do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Marque **Habilitar o SSO com SAML**.

    b. Na seção **Informações sobre o Andromeda**, copie o valor **Identidade do SP** e cole-o na caixa de texto **Identificador** da seção **Domínio e URL do Andromeda**.

    c. Copie o valor **URL do Consumidor** e cole-o na caixa de texto **URL de Resposta** da seção **Domínio e URLs do Andromeda**.

    d. Copie o valor **URL de Logon** e cole-o na caixa de texto **URL de Logon** da seção **Domínio e URLs do Andromeda**.

    e. Na seção **provedor de identidade SAML**, digite seu nome IDP.

    f. Na caixa de texto **Ponto de extremidade de logon único**, cole o valor da **URL de Serviço de Logon Único do SAML** que você copiou do portal do Azure.

    g. Abra o **certificado codificado com Base64** baixado do portal do Azure no bloco de notas, cole-o na caixa de texto **Certificado X 509**.
    
    h. Mapeie os seguintes atributos com o respectivo valor para facilitar o logon SSO do Azure AD. O atributo **ID de usuário** é necessário para fazer login. Para o provisionamento, **Email**, **Company**, **UserType** e **Role** são obrigatórios. Nesta seção, definimos o mapeamento de atributos (nome e valores) correlacionados àqueles definidos no portal do Azure

    ![attbmap do Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Clique em **Salvar**.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/andromedascm-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/andromedascm-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/andromedascm-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/andromedascm-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-andromeda-test-user"></a>Criar um usuário de teste do Andromeda

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Andromeda. O Andromeda dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Andromeda, caso ele ainda não exista.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao Cliente do Andromeda](https://www.ngcsoftware.com/support/).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Andromeda.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Andromeda, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Andromeda**.

    ![O link do Andromeda na lista de Aplicativos](./media/andromedascm-tutorial/tutorial_andromedascm_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Andromeda no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo Andromeda.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/andromedascm-tutorial/tutorial_general_01.png
[2]: ./media/andromedascm-tutorial/tutorial_general_02.png
[3]: ./media/andromedascm-tutorial/tutorial_general_03.png
[4]: ./media/andromedascm-tutorial/tutorial_general_04.png

[100]: ./media/andromedascm-tutorial/tutorial_general_100.png

[200]: ./media/andromedascm-tutorial/tutorial_general_200.png
[201]: ./media/andromedascm-tutorial/tutorial_general_201.png
[202]: ./media/andromedascm-tutorial/tutorial_general_202.png
[203]: ./media/andromedascm-tutorial/tutorial_general_203.png
