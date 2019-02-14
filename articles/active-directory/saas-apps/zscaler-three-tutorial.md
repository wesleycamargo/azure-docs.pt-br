---
title: 'Tutorial: Integração do Azure Active Directory com Zscaler Three | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zscaler Three.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5739c878812c09a8f2646320df4b1069dfa8810e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189031"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>Tutorial: Integração do Azure Active Directory com Zscaler Three

Neste tutorial, você aprenderá como integrar o Zscaler Three ao Azure AD (Azure Active Directory).

A integração do Zscaler Three com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Zscaler Three.
- Você pode permitir que os usuários façam logon automaticamente no Zscaler Three (Logon Único) com as contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é acesso de aplicativos e logon único com o Active Directory do Azure](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zscaler Three, são necessários os seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do Zscaler Three habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Zscaler Três da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-zscaler-three-from-the-gallery"></a>Adicionando Zscaler Três da Galeria

Para configurar a integração do Zscaler Three ao Azure AD, é necessário adicionar o Zscaler Three da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Zscaler Three da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Zscaler Three**, selecione **Zscaler Three** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Zscaler Three na lista de resultados](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Zscaler Three com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Zscaler Three é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Zscaler Three.

Para configurar e testar o logon único do Azure AD com o Zscaler Three, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar Logon Único do Zscaler Three](#configure-zscaler-three-single-sign-on)** - para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Criar um usuário de teste do Zscaler Three](#create-zscaler-three-test-user)** – para ter um equivalente de Brenda Fernandes no Zscaler Three vinculado à representação do usuário no Azure AD.
5. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único em seu aplicativo Zscaler Three.

**Para configurar o logon único do Azure AD com o Zscaler Three, siga as etapas abaixo:**

1. No Portal do Azure, na página de integração de aplicativos do **Zscaler Three**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Selecionar um método de logon único**, clique em **Selecionar** do modo **SAML** para habilitar o logon único.

    ![Configurar o logon único](common/tutorial_general_301.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Configurar o logon único](common/editconfigure.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de Logon Único de Domínio e URLs do Zscaler Three](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://login.zscalerthree.net/sfc_sso`

5. O aplicativo Zscaler Three espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **Atributos e reivindicações do usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos e Declarações do Usuário**.

    ![O link do Atributo](./media/zscaler-three-tutorial/tutorial_zscalerthree_attribute.png)

6. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    | NOME  | Atributo de Origem  |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

     a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](./common/new_save_attribute.png)
    
    ![image](./common/new_attribute_details.png)

    b. Da Lista **Atributo de origem**, selecione o valor do atributo.

    c. Clique em **OK**.

    d. Clique em **Salvar**.

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) para saber como configurar a Função no Azure AD

7. Na página **Certificado de assinatura SAML**, na seção **Certificado de assinatura SAML**, clique em **Download** para baixar o **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png) 

8. Na seção **Configurar Zscaler Three**, copie a URL apropriada de acordo com suas necessidades.

     a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

    ![Configuração do Zscaler Three](common/configuresection.png)

### <a name="configure-zscaler-three-single-sign-on"></a>Configurar Logon Único do Zscaler Three

9. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Zscaler Three como administrador.

10. Vá até **Administração > Autenticação > Configurações de Autenticação** e execute as seguintes etapas:
   
    ![Administração](./media/zscaler-three-tutorial/ic800206.png "Administração")

     a. Em Tipo de Autenticação, escolha **SAML**.

    b. Clique em **Configurar SAML**.

11. Na janela **Editar SAML**, execute as seguintes etapas: e clique em Salvar.  
            
    ![Gerenciar usuários e autenticação](./media/zscaler-three-tutorial/ic800208.png "Gerenciar usuários e autenticação")
    
     a. Na caixa de texto **URL do Portal SAML**, cole o a **URL de Logon** copiada do portal do Azure.

    b. Na caixa de texto **Atributo de Nome de Logon**, insira **NameID**.

    c. Clique em **Upload** para fazer upload do certificado de assinatura SAML do Azure que você baixou do portal do Azure para o **Certificado SSL Público**.

    d. Acione **Habilitar Provisionamento Automático do SAML**.

    e. Na caixa de texto **Atributo de Nome de Exibição do Usuário**, insira **displayName** se você quiser habilitar o provisionamento automático do SAML para atributos de displayName.

    f. Na caixa de texto **Atributo de Nome do Grupo**, insira **memberOf** se você quiser habilitar o provisionamento automático do SAML para atributos de memberOf.

    g. Em **Atributo de Nome do Departamento**, insira **departamento** se você quiser habilitar o provisionamento automático do SAML para atributos de departamento.

    i. Clique em **Salvar**.

12. Na página de caixa de diálogo **Configurar Autenticação de Usuário** , execute as seguintes etapas:

    ![Administração](./media/zscaler-three-tutorial/ic800207.png)

     a. Passe o mouse sobre o menu **Ativação** na parte inferior esquerda.

    b. Clique em **Ativar**.

## <a name="configuring-proxy-settings"></a>Definindo as configurações de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir as configurações de proxy no Internet Explorer

1. Inicie o **Internet Explorer**.

1. Selecione **Opções da Internet** no menu **Ferramentas** para abrir a caixa de diálogo **Opções da Internet**.   
    
     ![Opções da Internet](./media/zscaler-three-tutorial/ic769492.png "Opções da Internet")

1. Clique na guia **Conexões** .   
  
     ![Conexões](./media/zscaler-three-tutorial/ic769493.png "Conexões")

1. Clique em **Configurações da LAN** para abrir a caixa de diálogo **Configurações da LAN**.

1. Na seção Servidor de proxy, execute as seguintes etapas:   
   
    ![Servidor proxy](./media/zscaler-three-tutorial/ic769494.png "Servidor proxy")

     a. Selecione **Usar um servidor proxy para LAN**.

    b. Na caixa de texto Endereço, digite **gateway.Zscaler Three.net**.

    c. Na caixa de texto Porta, digite **80**.

    d. Selecione **Ignorar servidor proxy para endereços locais**.

    e. Clique em **OK** para fechar a caixa de diálogo **Configurações da Rede Local (LAN)**.

1. Clique em **OK** para fechar a caixa de diálogo **Opções da Internet**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

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

### <a name="create-zscaler-three-test-user"></a>Criar um usuário de teste do Zscaler Three

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Zscaler Three. O Zscaler Three dá suporte ao provisionamento just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Zscaler Three, caso ainda não exista.
>[!Note]
>Se for necessário criar um usuário manualmente, contate a  [Equipe de suporte do Zscaler Three](https://www.zscaler.com/company/contact).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Zscaler Three.

1. No Portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Zscaler Three**.

    ![Configurar o logon único](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione o usuário como **Brenda Fernandes** na lista e, em seguida, clique no botão **Selecionar** na parte inferior da tela.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

6. Na caixa de diálogo **Selecionar Função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

7. Na caixa de diálogo **Adicionar Atribuição** selecione o botão **Atribuir**.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Zscaler Three no Painel de Acesso, você deverá entrar automaticamente no aplicativo Zscaler Three.
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
