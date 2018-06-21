---
title: 'Tutorial: Integração do Azure Active Directory com o SAP Cloud Platform Identity Authentication | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Cloud Platform Identity Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: dc62d7a24a53ffa04026c0f1a40b05b18e6db71d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231382"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Tutorial: Integração do Azure Active Directory com o SAP Cloud Platform Identity Authentication

Neste tutorial, você aprenderá a integrar o SAP Cloud Platform Identity Authentication ao Azure Active Directory (Azure AD). O SAP Cloud Platform Identity Authentication é usado como um proxy IdP para acessar aplicativos SAP que usam o Azure AD como o IdP principal.

Quando você integra a Autenticação de Identidade da SAP Cloud Platform ao Azure AD recebe os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso aos aplicativos SAP.
- Você pode permitir que seus usuários entrem automaticamente em aplicativos SAP com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local: o novo Portal do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS ao Azure AD, consulte o artigo [O que é o acesso de aplicativos e o logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com SAP Cloud Platform Identity Authentication, você precisa dos seguintes itens:

- Uma assinatura do Azure AD.
- Uma assinatura habilitada para logon único para SAP Cloud Platform Identity Authentication.

> [!NOTE]
> Para testar as etapas deste tutorial, não recomendamos o uso de um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção a menos que seja necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, [receba uma versão de avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar SAP Cloud Platform Identity Authentication a partir da galeria
2. Configurar e testar o logon único do AD do Azure

Antes de se aprofundar nos detalhes técnicos, é essencial entender os conceitos que serão examinados. Os AD FS (Serviços de Federação do Active Directory) e a Autenticação de Identidade da SAP Cloud Platform permitem que você implemente o SSO em aplicativos ou serviços protegidos pelo Azure AD (como um IdP) com aplicativos SAP e serviços protegidos pela Autenticação de Identidade da SAP Cloud Platform.

Atualmente, o SAP Cloud Platform Identity Authentication atua como um provedor de identidade de proxy para aplicativos SAP. O Azure Active Directory por sua vez atua como o principal provedor de identidade nessa configuração. 

O diagrama a seguir ilustra esse relacionamento:

![Criação de um usuário de teste do AD do Azure](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Com essa configuração, seu locatário de SAP Cloud Platform Identity Authentication é configurado como um aplicativo confiável no Azure Active Directory. 

Todos os serviços e aplicativos SAP que você deseja proteger dessa maneira são posteriormente configurados no console de gerenciamento de Autenticação de Identidade da SAP Cloud Platform.

Portanto, a autorização para conceder acesso aos serviços e aplicativos SAP precisa ocorrer na Autenticação de Identidade da SAP Cloud Platform (diferentemente do Azure Active Directory).

Ao configurar a autenticação de identidade da SAP Cloud Platform como um aplicativo por meio do Azure Active Directory Marketplace, você não precisa configurar declarações individuais ou declarações SAML.

>[!NOTE] 
>Atualmente, somente o SSO da Web foi testado por ambas as partes. Os fluxos necessários para a comunicação de aplicativo a API ou de API para API devem funcionar, mas ainda não foram testados. Eles serão testados durante as atividades subsequentes.
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adicionar Autenticação de Identidade da SAP Cloud Platform por meio da galeria
Para configurar a integração da Autenticação de Identidade da SAP Cloud Platform no Azure AD, você precisa adicionar a Autenticação de Identidade da SAP Cloud Platform da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar a Autenticação de Identidade da SAP Cloud Platform da galeria, execute as seguintes etapas:**

1. No [Portal do Azure](https://portal.azure.com), no painel navegação à esquerda, selecione o ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Executar **Aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **SAP Cloud Platform Identity Authentication**. 

5. Selecione **SAP Cloud Platform Identity Authentication** do painel de resultados e, em seguida, selecione o botão **Adicionar**.

    ![Adicionar SAP Cloud Platform Identity Authentication na lista de resultados](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SAP Cloud Platform Identity Authentication. Configure e teste-o com um usuário chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber quem é o usuário da Autenticação de Identidade da SAP Cloud Platform equivalente. Em outras palavras, é necessário estabelecer um vínculo entre um usuário do Azure AD e o usuário relacionado da Autenticação de Identidade da SAP Cloud Platform.

Na autenticação de identidade da SAP Cloud Platform, atribua ao valor **Username** o mesmo valor de **nome de usuário** no Azure AD. Agora, você estabeleceu o vínculo entre os dois usuários.

Para configurar e testar o logon único do Azure AD com o SAP Cloud Platform Identity Authentication, conclua os seguintes blocos de construção:

1. [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
3. [Criar um usuário de teste do SAP Cloud Platform Identity Authentication](#create-an-sap-cloud-platform-identity-authentication-test-user) para ter um equivalente de Brenda Fernandes no SAP Cloud Platform Identity Authentication que esteja vinculado à representação dela no Azure AD.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Teste o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo do SAP Cloud Platform Identity Authentication.

**Para configurar o logon único do Azure AD com SAP Cloud Platform Identity Authentication, realize as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos de **SAP Cloud Platform Identity Authentication**, selecione **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, em **Logon baseado em SAML** selecione **Modo** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_samlbase.png)

3. Se desejar configurar o aplicativo no modo iniciado pelo **IDP**, na seção **Domínio e URLs do SAP Cloud Platform Identity Authentication e URLS**, realize as etapas a seguir:  

    ![Informações sobre logon único de Domínio e URLs do SAP Cloud Platform Identity Authentication](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url.png)

    a. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Na caixa de **URL de Resposta**, digite uma URL com o seguinte padrão: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte do Cliente de SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) para obter esses valores. Se você não entender esse valor de identificador, leia a documentação do SAP Cloud Platform Identity Authentication sobre [Configuração do SAML 2.0 do Locatário](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

4. Se deseja configurar o aplicativo no modo iniciado em **SP**, selecione **Mostrar configurações avançadas de URL**.

    ![Informações sobre logon único de Domínio e URLs do SAP Cloud Platform Identity Authentication](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url1.png)

    Na caixa **URL de Entrada**, digite um URL usando o seguinte padrão: `{YOUR BUSINESS APPLICATION URL}`.

    > [!NOTE]
    > Esse valor não é real. Atualize esse valor com o URL de Logon real. Use a URL de logon do aplicativo comercial específica. Entre em contato com a [equipe de suporte do Cliente de SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) se você tiver qualquer dúvida.

5. Na seção **Certificado de Autenticação SAML**, selecione **XML de Metadados**. Em seguida, salve o arquivo de metadados no computador.

    ![O link de download do Certificado](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_certificate.png)

6. O aplicativo de SAP Cloud Platform Identity Authentication espera as asserções SAML em um formato específico. Gerencie os valores desses atributos da seção **Atributos de Usuário** na página de integração do aplicativo. As capturas de tela a seguir mostram um exemplo do formato. 

    ![Configurar o logon único](./media/sap-hana-cloud-platform-identity-authentication-tutorial/attribute.png)

7. Se seu aplicativo SAP espera um atributo, como **firstName**, adicione o atributo **firstName** na seção **Atributos de Usuário**. Essa opção está disponível na caixa de diálogo **Logon único** da caixa de diálogo **Atributos de tokens SAML**.

    a. Para abrir a caixa de diálogo **Adicionar Atributo**, selecione **Adicionar Atributo**. 
    
    ![Configurar o logon único](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_04.png)
    
    ![Configurar o logon único](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_05.png)
    
    b. Na caixa **Nome**, digite o nome do atributo **firstName**.
    
    c. Na lista **Valor**, selecione o valor do atributo **user.givenname**.
    
    d. Selecione **Ok**.

8. Selecione o botão **Salvar**.

    ![Configurar botão Salvar no logon único](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_400.png)

9. Na seção **Configuração do SAP Cloud Platform Identity Authentication**, selecione **Configurar SAP Cloud Platform Identity Authentication** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do SAP Cloud Platform Identity Authentication](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_configure.png) 

10. Para configurar o SSO para o seu aplicativo, vá para o Console de administração do SAP Cloud Platform Identity Authentication. A URL tem o seguinte padrão: `https://<tenant-id>.accounts.ondemand.com/admin`. Em seguida, leia a documentação sobre SAP Cloud Platform Identity Authentication em [Integração com o Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

11. No portal do Azure, selecione o botão **Salvar**.

12. Continue com o seguinte somente se quiser adicionar e habilitar o SSO para outro aplicativo SAP. Repita as etapas na seção **Adicionar SAP Cloud Platform Identity Authentication por meio da galeria**.

13. No portal do Azure, na página de integração de aplicativos de **SAP Cloud Platform Identity Authentication**, selecione **Logon vinculado**.

    ![Configurar o logon vinculado](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

14. Salve a configuração.

>[!NOTE] 
>O novo aplicativo aproveita a configuração de logon único do aplicativo SAP anterior. Certifique-se de usar os mesmos Provedores de Identidade Corporativa no Console de Administração de SAP Cloud Platform Identity Authentication.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único** e acesse a documentação inserida na seção **Configuração**, na parte inferior. Você pode ler mais sobre o recurso de documentação inserida na [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No Portal do Azure, no painel esquerdo, selecione o botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, em seguida, selecione **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar** na parte superior da caixa de diálogo **Todos os usuários**.

    ![O botão Adicionar](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>Criar um usuário de teste do SAP Cloud Platform Identity Authentication

Você não precisa criar um usuário no SAP Cloud Platform Identity Authentication. Os usuários que estão no repositório de usuários do Azure AD podem usar a funcionalidade de SSO.

O SAP Cloud Platform Identity Authentication é compatível com a opção de Federação de Identidades. Essa opção permite que o aplicativo verifique se os usuários autenticados pelo provedor de identidade corporativa existem no repositório de usuários do SAP Cloud Platform Identity Authentication. 

A opção de Federação de Identidades é desabilitada por padrão. Se a Federação de Identidades estiver habilitada, somente os usuários que são importados na Autenticação de Identidade da SAP Cloud Platform poderão acessar o aplicativo. 

Para obter mais informações sobre como habilitar ou desabilitar a Federação de Identidades com o SAP Cloud Platform Identity Authentication, consulte Habilitar a Federação de Identidades com o SAP Cloud Platform Identity Authentication em [Configurar a Federação de Identidades com o repositório de usuários do SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso ao SAP Cloud Platform Identity Authentication.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao SAP Cloud Platform Identity Authentication, execute as seguintes etapas:**

1. No portal do Azure, abra a exibição de aplicativos e vá para a exibição de diretório. Em seguida, vá para **Aplicativos empresariais**, em seguida, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **SAP Cloud Platform Identity Authentication**.

    ![Link do SAP Cloud Platform Identity Authentication na lista de Aplicativos](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_app.png)  

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Selecione o botão **Adicionar**. Em seguida, selecione **Usuários e grupos**, na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários.

6. Clique no botão **Selecionar** na caixa de diálogo **Usuários e grupos**.

7. Selecione o botão **Atribuir** na caixa de diálogo **Adicionar atribuição**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Quando você seleciona o bloco de SAP Cloud Platform Identity Authentication no painel de acesso, seu logon deve ser realizado automaticamente no seu aplicativo de SAP Cloud Platform Identity Authentication.

Para saber mais sobre o painel de acesso, veja [Introdução ao painel de acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/sapcloudauth-tutorial/tutorial_general_203.png
