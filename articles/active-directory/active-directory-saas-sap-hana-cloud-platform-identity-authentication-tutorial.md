---
title: "Tutorial: Integração do Azure Active Directory com a SAP HANA Cloud Platform | Autenticação de Identidade | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e a Autenticação de Identidade da SAP HANA Cloud Platform."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 9b3f305e71502119e5dee6592347cf30bafef157
ms.contentlocale: pt-br
ms.lasthandoff: 04/03/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform-identity-authentication"></a>Tutorial: Integração do Azure Active Directory com a SAP HANA Cloud Platform | Autenticação de Identidade

Neste tutorial, você aprenderá a integrar a Autenticação de Identidade da SAP HANA Cloud Platform ao Azure AD (Azure Active Directory). A Autenticação de Identidade da SAP HANA Cloud Platform é usada como um proxy IdP para acessar aplicativos SAP usando o Azure AD como o IdP principal.

Integrar a Autenticação de Identidade da SAP HANA Cloud Platform ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD que tem acesso ao aplicativo SAP
- Você pode permitir que os usuários se conectem automaticamente ao SSO (logon único) de aplicativos SAP com suas contas do Azure AD
- Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Autenticação de Identidade da SAP HANA Cloud Platform, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura da **Autenticação de Identidade da SAP HANA Cloud Platform** habilitada para SSO


>[!NOTE] 
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
>

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando autenticação de identidade de plataforma de nuvem do SAP HANA da galeria
2. Configurar e testar o SSO do Azure AD

Antes de nos aprofundarmos nos detalhes técnicos, é essencial entender os conceitos que serão examinados. A federação da Autenticação de Identidade da SAP HANA Cloud Platform e do Azure Active Directory permite que você implemente o SSO em aplicativos ou serviços protegidos pelo AAD (como um IdP) com aplicativos SAP e serviços protegidos pela Autenticação de Identidade da SAP HANA Cloud Platform.

Atualmente, a Autenticação de Identidade da SAP HANA Cloud Platform atua como um provedor de identidade de proxy para aplicativos SAP. O Azure Active Directory por sua vez atua como o principal provedor de identidade nessa configuração. 

O seguinte diagrama ilustra isso:    

![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Com essa configuração, seu locatário de Autenticação de Identidade da SAP HANA Cloud Platform será configurado como um aplicativo confiável no Azure Active Directory. 

Todos os serviços e aplicativos SAP que você deseja proteger dessa maneira são posteriormente configurados no console de gerenciamento de Autenticação de Identidade da SAP HANA Cloud Platform!

Isso significa que a autorização para conceder acesso aos serviços e aplicativos SAP precisa ocorrer na Autenticação de Identidade da SAP HANA Cloud Platform para uma configuração como essa (em vez de configurar a autorização no Azure Active Directory).

Configurando a Autenticação de Identidade da SAP HANA Cloud Platform como um aplicativo por meio do Azure Active Directory Marketplace, você não precisa cuidar da configuração de declarações individuais/transformações e asserções SAML necessárias para gerar um token de autenticação válido para aplicativos SAP.

>[!NOTE] 
>Atualmente, o SSO da Web foi testado somente por ambas as partes. Fluxos necessários para a comunicação de aplicativo a API ou de API para API devem funcionar, mas ainda não foram testados. Eles serão testados como parte das atividades subsequentes.
>

## <a name="add-sap-hana-cloud-platform-identity-authentication-from-the-gallery"></a>Adicionar a Autenticação de Identidade da SAP HANA Cloud Platform por meio da galeria
Para configurar a integração da Autenticação de Identidade da SAP HANA Cloud Platform no Azure AD, você precisa adicionar a Autenticação de Identidade da SAP HANA Cloud Platform da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a Autenticação de Identidade da SAP HANA Cloud Platform da galeria, execute as seguintes etapas:**

1. No [**Portal de Gerenciamento do Azure**](https://portal.azure.com), no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Autenticação de Identidade da SAP HANA Cloud Platform**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_01.png)

5. No painel resultados, selecione **Autenticação de Identidade da SAP HANA Cloud Platform** e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_02.png)


##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configura e testa o SSO do Azure AD com a Autenticação de Identidade da SAP HANA Cloud Platform com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o SSO funcione, o Azure AD precisa saber qual usuário da Autenticação de Identidade da SAP HANA Cloud Platform é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado da Autenticação de Identidade da SAP HANA Cloud Platform.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** na Autenticação de Identidade da SAP HANA Cloud Platform.

Para configurar e testar o SSO do Azure AD com a Autenticação de Identidade da SAP HANA Cloud Platform, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste de Autenticação de Identidade da SAP HANA Cloud Platform](#creating-a-sap-hana-cloud-platform-identity-authentication-test-user)** – para ter um equivalente de Brenda Fernandes na Autenticação de Identidade da SAP HANA Cloud Platform que é vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilita o SSO do Azure AD no portal de Gerenciamento do Azure e configura o logon único no aplicativo da Autenticação de Identidade da SAP HANA Cloud Platform.

O aplicativo de Autenticação de Identidade da SAP HANA Cloud Platform espera as asserções SAML em um formato específico. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo disso.

![Configurar o logon único](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_03.png)

**Para configurar o SSO do Azure AD com a Autenticação de Identidade da SAP HANA Cloud Platform, realize as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração de aplicativos **Autenticação de Identidade da SAP HANA Cloud Platform**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único][5]

3. Na seção **Atributos de Usuário** da caixa de diálogo **Logon único**, se seu aplicativo SAP espera um atributo, por exemplo "firstName". No diálogo de atributos de token SAML, adicione o atributo "firstName".
 1. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.
 
    ![Configurar o logon único][6]

    ![Configurar Logon Único](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_05.png)
 2. Na caixa de texto **Nome do Atributo**, digite o nome do atributo "firstName".
 3. Na lista **Valor do Atributo**, selecione o valor do atributo "user.givenname".
 4. Clique em **OK**.

4. Na seção **Domínio e URLs da Autenticação de Identidade da SAP HANA Cloud Platform**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_06.png)
 1. Na caixa de texto **URL de logon**, digite a URL de logon do aplicativo SAP.
 2. Na caixa de texto **Identificador**, digite o valor no seguinte padrão: `<entity-id>.accounts.ondemand.com` 
    * Se você não souber esse valor, siga a documentação da Autenticação de Identidade da SAP HANA Cloud Platform em [Configuração do SAML 2.0 do Locatário](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Na seção **Configuração da Autenticação de Identidade da SAP HANA Cloud Platform**, clique em **Configurar Autenticação de Identidade da SAP HANA Cloud Platform** para abrir a caixa de diálogo **Configurar logon**. Em seguida, clique em **Metadados XML SAML** e salve o arquivo no computador.

    ![Configurar o logon único](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_07.png) 

    ![Configurar Logon Único](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_08.png)

6. Para configurar o SSO para o seu aplicativo, vá para o Console de administração da Autenticação de Identidade da SAP HANA Cloud Platform. A URL tem o seguinte padrão: `https://<tenant-id>.accounts.ondemand.com/admin`
 * Depois, siga a documentação sobre a Autenticação de Identidade da SAP HANA Cloud Platform para [Configurar o Microsoft Azure AD como provedor de identidade corporativa na Autenticação de Identidade da SAP HANA Cloud Platform](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

7. No Portal de Gerenciamento do Azure, clique no botão **Salvar**.
8. Continue as etapas a seguir somente se você quiser adicionar e habilitar o SSO para outro aplicativo SAP. Repita as etapas na seção "Adicionar autenticação de identidade de plataforma de nuvem do SAP HANA da galeria" para adicionar outra instância de Autenticação de identidade de plataforma de nuvem do SAP HANA.
9. No portal de Gerenciamento do Azure, na página de integração de aplicativos **Autenticação de Identidade da SAP HANA Cloud Platform**, clique em **Logon Vinculado**.

    ![Configurar o logon vinculado](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)
10. Depois, salve a configuração.

>[!NOTE] 
>O novo aplicativo aproveitará a configuração de SSO para o aplicativo SAP anterior. Use os mesmos Provedores de identidade corporativa no Console de Administração de autenticação de identidade de plataforma de nuvem do SAP HANA.
>

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no novo portal chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_04.png) 
  1. Na caixa de texto **Nome**, digite **Brenda Fernandes**.
  2. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.
  3. Selecione **Mostrar senha** e anote o valor de **senha**.
  4. Clique em **Criar**. 

### <a name="create-a-sap-hana-cloud-platform-identity-authentication-test-user"></a>Criar um usuário de teste da Autenticação de Identidade da SAP HANA Cloud Platform

Você não precisa criar um usuário na Autenticação de Identidade da SAP HANA Cloud Platform. Os usuários que estão no repositório de usuários do Azure AD podem usar a funcionalidade de SSO.

A Autenticação de Identidade da SAP HANA Cloud Platform dá suporte à opção de Federação de Identidades. Essa opção permite que o aplicativo verifique se os usuários autenticados pelo provedor de identidade corporativa existem no repositório de usuários da Autenticação de Identidade da SAP HANA Cloud Platform. 

Na configuração padrão, a opção de Federação de Identidades é desabilitada. Se a Federação de Identidades é habilitada, somente os usuários que são importados na Autenticação de Identidade da SAP HANA Cloud Platform são capazes de acessar o aplicativo. 

Para obter mais informações sobre como habilitar ou desabilitar a Federação de Identidades com a Autenticação de Identidade da SAP HANA Cloud Platform, consulte Habilitar a Federação de Identidades com a Autenticação de Identidade da SAP HANA Cloud Platform em [Configurar a Federação de Identidades com o repositório de usuários da Autenticação de Identidade da SAP HANA Cloud Platform](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso à Autenticação de Identidade da SAP HANA Cloud Platform.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes à Autenticação de Identidade da SAP HANA Cloud Platform, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **Aplicativos empresariais**, depois clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Autenticação de Identidade da SAP HANA Cloud Platform**.

    ![Configurar o logon único](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_09.png)

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Quando você clica no bloco de Autenticação de Identidade da SAP HANA Cloud Platform no Painel de Acesso, seu logon deve ser realizado automaticamente no seu aplicativo de Autenticação de Identidade da SAP HANA Cloud Platform.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_06.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_203.png
