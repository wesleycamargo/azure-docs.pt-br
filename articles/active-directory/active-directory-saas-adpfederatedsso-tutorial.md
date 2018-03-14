---
title: "Tutorial: integração do Azure Active Directory com o ADP | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o ADP."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: jeedes
ms.openlocfilehash: 1e0a35fd76f9eb6335685f05b8936b0b5105f6b2
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Tutorial: Integração do Active Directory do Azure ao ADP

Neste tutorial, você aprende a integrar o ADP ao Microsoft Azure Active Directory (Azure Active Directory).

A integração de ADP ao Microsoft Azure Active Directory oferece os seguintes benefícios:

- No Microsoft Azure Active Directory, é possível controlar quem tem acesso a ADP.
- Você pode permitir que seus usuários façam logon automaticamente em ADP (logon único) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory com ADP, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para ADP

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar ADP da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-adp-from-the-gallery"></a>Adicionar ADP da galeria
Para configurar a integração de ADP ao Microsoft Azure Active Directory, você precisa adicionar ADP por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ADP da galeria, execute as seguintes etapas:**

1.  Faça logon em seu ambiente de provedor de identidade do Microsoft Azure como administrador.

2. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

3. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
4. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

5. Na caixa de pesquisa, digite **ADP**, selecione **ADP** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![ADP na lista de resultados](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Microsoft Azure Active Directory com o ADP, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Microsoft Azure Active Directory precisa saber qual usuário do ADP é equivalente a um usuário do Microsoft Azure Active Directory. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o usuário relacionado em ADP.

No ADP, atribua o valor do **nome de usuário** no Microsoft Azure Active Directory como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o ADP, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do ADP](#create-an-adp-test-user)** – para ter um equivalente de Brenda Fernandes no ADP que esteja vinculado à representação de usuário do Microsoft Azure Active Directory.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Microsoft Azure Active Directory no portal do Azure e configura o logon único no aplicativo ADP.

**Para configurar o logon único do Microsoft Azure Active Directory com o ADP, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **ADP**, clique na **guia Propriedades** e execute as seguintes etapas: 

    ![Propriedades de logon único](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Defina o valor do campo **Habilitado para os usuários entrarem** como **Sim**.

    b. Copie a **URL de acesso do usuário** e cole-a na **seção Configurar URL de Entrada**, que é explicado mais adiante no tutorial.

    c. Defina o valor do campo **Atribuição de usuário necessária** como **Sim**.

    d. Defina o valor do campo **Visível para os usuários** como **Não**.

2. Clique em **Logon único** na página de integração de aplicativo **ADP**.

    ![Link Configurar logon único][4]

3. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_samlbase.png)

4. Na seção **Domínio e URLs do ADP**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do ADP](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_url.png)

    Na caixa de texto **Identificador**, digite uma URL: `https://fed.adp.com/` 
    
5. O ADP espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra um exemplo disso. O nome de declaração sempre será **"PersonImmutableID"** e o valor que mapeamos para **employeeid**. 

    Aqui, o mapeamento de usuário do Microsoft Azure Active Directory para o ADP será feito na **employeeid**, mas isso pode ser mapeado para um valor diferente baseado nas configurações de aplicativo. Portanto, trabalhe com a [equipe de suporte do ADP](https://www.adp.com/contact-us/overview.aspx) primeiro para usar o identificador correto de um usuário e mapear esse valor com a declaração **“PersonImmutableID”**.

    ![Configurar o logon único](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_attribute.png)

6. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | ------------------- | -------------------- |    
    | PersonImmutableID | user.employeeid |
    
    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**.

    > [!NOTE] 
    > Antes de configurar a declaração SAML, é necessário contatar a [equipe de suporte da ADP](https://www.adp.com/contact-us/overview.aspx) e solicitar o valor do atributo de identificador exclusivo para seu locatário. Você precisa desse valor para configurar a declaração personalizada para seu aplicativo. 

7. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_certificate.png) 

8. Para configurar o logon único do lado do **ADP**, você precisa carregar o **XML de Metadados** baixado no [site do ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Esse processo pode levar alguns dias. 

### <a name="configure-your-adp-services-for-federated-access"></a>Configurar os serviços do ADP para acesso federado

>[!Important]
> Os funcionários que precisam de acesso federado aos serviços do ADP precisam ser atribuídos ao aplicativo de serviço do ADP e posteriormente, os usuários precisam ser reatribuídos para o serviço do ADP específico.
Após o recebimento da confirmação de seu representante da ADP, configure os serviços do ADP e atribua/gerencie usuários para controlar o acesso do usuário ao serviço do ADP específico.

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **ADP**, selecione **ADP** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![ADP na lista de resultados](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_addservicegallery.png)

5. No portal do Azure, na página de integração de aplicativos do seu **ADP**, clique na **guia Propriedades** e execute as seguintes etapas:  

    ![Linkedproperties de logon único](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Defina o valor do campo **Habilitado para os usuários entrarem** como **Sim**.

    b.  Defina o valor do campo **Atribuição de usuário necessária** como **Sim**.

    c.  Defina o valor do campo **Visível para os usuários** como **Sim**.

6. Clique em **Logon único** na página de integração de aplicativo **ADP**.

    ![Link Configurar logon único][4]

7. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon vinculado**. para vincular seu aplicativo ao **ADP**.

    ![Logon único vinculado](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Navegue para a seção **Configurar URL de Entrada** e execute as seguintes etapas:

    ![Propriedades de logon único](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)
                                                              
    a. Cole a **URL de acesso do usuário**, copiada da **guia de propriedades** acima (do aplicativo principal do ADP).
                                                             
    b. Veja a seguir os cinco aplicativos que dão suporte a diferentes **URLs de Estado de Retransmissão**. Você precisa acrescentar o valor **URL do Estado de Retransmissão** apropriado de determinado aplicativo manualmente à **URL de acesso do usuário**.
    
    * **ADP Workforce Now**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?Relay State=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Salve** suas alterações.

10. Após o recebimento da confirmação de seu representante da ADP, inicie o teste com um ou dois usuários.

    a. Atribua alguns usuários ao Aplicativo do serviço ADP para testar o acesso federado.

    b. O teste é bem-sucedido quando os usuários acessam o aplicativo de serviço do ADP na galeria e podem acessar seus serviços do ADP.
 
11. Após a confirmação de um teste bem-sucedido, atribua o serviço ADP federado a usuários individuais ou grupos de usuários, que é explicado mais adiante no tutorial, e distribua-o a seus funcionários. 

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-adpfederatedsso-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-adp-test-user"></a>Criar um usuário de teste do ADP

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no ADP. Trabalhe com a [equipe de suporte da ADP](https://www.adp.com/contact-us/overview.aspx) para adicionar os usuários à conta do ADP.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao ADP.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao ADP, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **ADP**.

    ![O link do ADP na lista de Aplicativos](./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_adp_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco de ADP no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo de ADP.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adpfederatedsso-tutorial/tutorial_general_203.png

