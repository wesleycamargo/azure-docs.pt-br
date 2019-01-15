---
title: 'Tutorial: Integração do Azure Active Directory com o ADP | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.openlocfilehash: 487c9cb145000b92a4aa664ea2bd159026104b6b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065147"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Tutorial: Integração do Azure Active Directory com o ADP

Neste tutorial, você aprende a integrar o ADP ao Microsoft Azure Active Directory (Azure Active Directory).
A integração de ADP ao Microsoft Azure Active Directory oferece os seguintes benefícios:

* No Microsoft Azure Active Directory, é possível controlar quem tem acesso a ADP.
* Você pode permitir que seus usuários entrem automaticamente no ADP (Logon Único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure Active Directory com ADP, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do ADP habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O ADP é compatível com SSO iniciado por **IDP**

## <a name="adding-adp-from-the-gallery"></a>Adicionar ADP da galeria

Para configurar a integração de ADP ao Microsoft Azure Active Directory, você precisa adicionar ADP por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o ADP da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ADP**, selecione **ADP** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![ADP na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o ADP com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ADP.

Para configurar e testar o logon único do Microsoft Azure Active Directory com o ADP, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do ADP](#configure-adp-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do ADP](#create-adp-test-user)** – para ter um equivalente de Brenda Fernandes no ADP que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o ADP, execute as seguintes etapas:

1. No portal do Azure, na página de integração de aplicativos do **ADP**, clique na **guia Propriedades** e execute as seguintes etapas: 

    ![Propriedades de logon único](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

     a. Defina o valor do campo **Habilitado para os usuários entrarem** como **Sim**.

    b. Copie a **URL de acesso do usuário** e cole-a na **seção Configurar URL de Entrada**, que é explicado mais adiante no tutorial.

    c. Defina o valor do campo **Atribuição de usuário necessária** como **Sim**.

    d. Defina o valor do campo **Visível para os usuários** como **Não**.

2. No [Portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ADP**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

3. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

4. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

5. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do ADP](common/idp-identifier.png)

    Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL: `https://fed.adp.com`

6. O aplicativo ADP espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**. O nome da declaração sempre será **"PersonImmutableID"** e o valor que mapeamos para **employeeid**. 

    O mapeamento de usuário do Azure AD para o ADP será feito na **employeeid**, mas isso pode ser mapeado para um valor diferente baseado nas configurações de aplicativo. Portanto, trabalhe com a [equipe de suporte do ADP](https://www.adp.com/contact-us/overview.aspx) primeiro para usar o identificador correto de um usuário e mapear esse valor com a declaração **“PersonImmutableID”**.

    ![image](common/edit-attribute.png)

7. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML conforme mostrado na imagem acima e executar as seguintes etapas:
    
    | NOME | Atributo de Origem | 
    | ---------------| --------------- |
    | PersonImmutableID  | user.employeeid |

     a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Salvar**.

    > [!NOTE] 
    > Antes de configurar a declaração SAML, é necessário contatar a [equipe de suporte da ADP](https://www.adp.com/contact-us/overview.aspx) e solicitar o valor do atributo de identificador exclusivo do usuário para seu locatário. Você precisa desse valor para configurar a declaração personalizada para seu aplicativo. 

8. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

### <a name="configure-adp-single-sign-on"></a>Configurar o logon único do ADP

Para configurar o logon único do lado do **ADP**, você precisa carregar o **XML de Metadados** baixado no [site do ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Esse processo pode levar alguns dias.

### <a name="configure-your-adp-services-for-federated-access"></a>Configurar os serviços do ADP para acesso federado

>[!Important]
> Os funcionários que precisam de acesso federado aos serviços do ADP precisam ser atribuídos ao aplicativo de serviço do ADP e posteriormente, os usuários precisam ser reatribuídos para o serviço do ADP específico.
Após o recebimento da confirmação de seu representante da ADP, configure os serviços do ADP e atribua/gerencie usuários para controlar o acesso do usuário ao serviço do ADP específico.

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ADP**, selecione **ADP** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![ADP na lista de resultados](common/search-new-app.png)

5. No portal do Azure, na página de integração de aplicativos do seu **ADP**, clique na **guia Propriedades** e execute as seguintes etapas:  

    ![Linkedproperties de logon único](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

     a.  Defina o valor do campo **Habilitado para os usuários entrarem** como **Sim**.

    b.  Defina o valor do campo **Atribuição de usuário necessária** como **Sim**.

    c.  Defina o valor do campo **Visível para os usuários** como **Sim**.

6. No [Portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ADP**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

7. Na caixa de diálogo **Selecione um método de logon único**, selecione **Modo** como **Vinculado**. para vincular seu aplicativo ao **ADP**.

    ![Logon único vinculado](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Navegue para a seção **Configurar URL de Entrada** e execute as seguintes etapas:

    ![Propriedades de logon único](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

     a. Cole a **URL de acesso do usuário**, copiada da **guia de propriedades** acima (do aplicativo principal do ADP).
                                                             
    b. Veja a seguir os cinco aplicativos que dão suporte a diferentes **URLs de Estado de Retransmissão**. Você precisa acrescentar o valor **URL do Estado de Retransmissão** apropriado de determinado aplicativo manualmente à **URL de acesso do usuário**.
    
    * **ADP Workforce Now**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Salve** suas alterações.

10. Após o recebimento da confirmação de seu representante da ADP, inicie o teste com um ou dois usuários.

     a. Atribua alguns usuários ao Aplicativo do serviço ADP para testar o acesso federado.

    b. O teste é bem-sucedido quando os usuários acessam o aplicativo de serviço do ADP na galeria e podem acessar seus serviços do ADP.
 
11. Após a confirmação de um teste bem-sucedido, atribua o serviço ADP federado a usuários individuais ou grupos de usuários, que é explicado mais adiante no tutorial, e distribua-o a seus funcionários.
 
### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao ADP.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **ADP**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **ADP**.

    ![O link do ADP na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-adp-test-user"></a>Criar um usuário de teste do ADP

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no ADP. Trabalhe com a [equipe de suporte da ADP](https://www.adp.com/contact-us/overview.aspx) para adicionar os usuários à conta do ADP. 

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ADP no Painel de Acesso, você deverá ser conectado automaticamente ao ADP no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

