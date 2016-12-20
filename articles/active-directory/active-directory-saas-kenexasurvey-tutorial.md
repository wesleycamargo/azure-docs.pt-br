---
title: "Tutorial: Integração do Azure Active Directory com o IBM Kenexa Survey Enterprise | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o IBM Kenexa Survey Enterprise."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d6cee6b4d47264bb0b91d3169490af0b08c05711


---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Tutorial: Integração do Azure Active Directory com o IBM Kenexa Survey Enterprise
Neste tutorial, você aprenderá como integrar o IBM Kenexa Survey Enterprise ao Azure AD (Azure Active Directory).

A integração do IBM Kenexa Survey Enterprise ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao IBM Kenexa Survey Enterprise
* Você pode habilitar seus usuários a fazerem logon automaticamente no IBM Kenexa Survey Enterprise (logon único) com suas contas do Azure AD
* Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o IBM Kenexa Survey Enterprise, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do IBM Kenexa Survey Enterprise habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o IBM Kenexa Survey Enterprise da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Adicionando o IBM Kenexa Survey Enterprise da galeria
Para configurar a integração do IBM Kenexa Survey Enterprise com o Azure AD, você precisará adicionar o IBM Kenexa Survey Enterprise à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o IBM Kenexa Survey Enterprise por meio da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **IBM Kenexa Survey Enterprise**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_01.png)
7. No painel de resultados, selecione **IBM Kenexa Survey Enterprise** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o IBM Kenexa Survey Enterprise, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do IBM Kenexa Survey Enterprise é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do IBM Kenexa Survey Enterprise.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD ao valor de **Nome de usuário** no IBM Kenexa Survey Enterprise.

Para configurar e testar o logon único do Azure AD com o IBM Kenexa Survey Enterprise, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do IBM Kenexa Survey Enterprise](#creating-an-kenexasurvey-test-user)** : para ter um equivalente de Brenda Fernandes no IBM Kenexa Survey Enterprise que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo IBM Kenexa Survey Enterprise.

**Para configurar o logon único do Azure AD com o IBM Kenexa Survey Enterprise, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **IBM Kenexa Survey Enterprise**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no IBM Kenexa Survey Enterprise**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_03.png)
3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_04.png)
   
    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `https://surveys.kenexa.com/<company code>`
   
    b. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://surveys.kenexa.com/<company code>/tools/sso.asp`
   
    c. Clique em **Avançar**.
   
   > [!NOTE]
   > Observe que esses não são os valores reais. Você precisa atualizar esses valores com o Identificador e a URL de Resposta reais. Contate a equipe de suporte da IBM Kenexa Survey Enterprise para obter esses valores.
   > 
   > 
4. Na página **Configurar logon único no IBM Kenexa Survey Enterprise**, clique em **Baixar certificado** e salve o arquivo no computador:
   
    ![Configurar Logon Único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_05.png) 
5. Para que o SSO seja configurado para seu aplicativo, entre em contato com a equipe de suporte do IBM Kenexa e forneça o seguinte:
   
    •  O arquivo de certificado baixado
   
    • A **URL do Emissor**
   
    • A **URL de SSO do SAML**
   
    • A **URL do Serviço de Logoff Único**
   
   > [!NOTE]
   > Observe que o valor da declaração NameID na Resposta deve corresponder à ID de SSO configurada no sistema Kenexa. Portanto, trabalhe com a equipe de suporte do Kenexa para mapear o identificador de usuário apropriado em sua organização como a ID de SSO. Por padrão, o Azure AD definirá o NameIdentifier como o valor de UPN. Você pode alterar isso na guia Atributo, conforme mostrado na captura de tela abaixo. A integração funcionará apenas depois de concluir o mapeamento correto. 
   > 
   > 
   
    ![Configurar Logon Único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_51.png)
6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]
8. No portal clássico do Azure, na página de integração de aplicativos do **IBM Kenexa Survey Enterprise**, no menu superior, clique em **Atributos**.
   
    ![Configurar Logon Único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_06.png)
9. Na caixa de diálogo **Atributos de token SAML** , execute as seguintes etapas:
   
    a. Selecione o atributo de **NameIdentifier** e clique no ícone **Editar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_07.png)
   
    b. Da lista **Valor do Atributo**, digite o valor do atributo de identificação do SSO que está configurado no sistema Kenexa.
   
    c. Clique em **Concluído**

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_06.png) 
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-an-ibm-kenexa-survey-enterprise-test-user"></a>Criando um usuário de teste do IBM Kenexa Survey Enterprise
Nesta seção, você criará uma usuária chamada Brenda Fernandes no IBM Kenexa Survey Enterprise. Trabalhe com a equipe de suporte do IBM Kenexa para mapear a ID de SSO para todos os usuários. Além disso, esse valor de ID de SSO deve ser mapeado para o valor de NameIdentifier do Azure AD. Você pode alterar essas configurações padrão na guia Atributo.

> [!NOTE]
> Se precisar criar um usuário manualmente, entre em contato com a equipe de suporte do IBM Kenexa Survey Enterprise.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso ao IBM Kenexa Survey Enterprise.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao IBM Kenexa Survey Enterprise, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **IBM Kenexa Survey Enterprise**.
   
    ![Configurar Logon Único](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do IBM Kenexa Survey Enterprise no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo IBM Kenexa Survey Enterprise.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


