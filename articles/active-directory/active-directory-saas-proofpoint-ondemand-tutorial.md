---
title: "Tutorial: integração do Azure Active Directory com o Proofpoint on Demand | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e Proofpoint on Demand."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 773e7f7d-ec31-411b-860d-6a6633335d43
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 2ea87726b06a0e8ab20515031424bf4e93f45a80
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Tutorial: integração do Azure Active Directory ao Proofpoint on Demand
Neste tutorial, você aprenderá a integrar o Proofpoint on Demand ao Azure AD (Azure Active Directory).

Integrar o Proofpoint on Demand ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Proofpoint on Demand.
* Você pode habilitar os usuários para serem conectados automaticamente no Proofpoint on Demand (logon único ou SSO) com suas contas do Azure AD.
* Gerenciar suas contas em um único local: o Portal clássico do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Proofpoint on Demand, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura de SSO (logon único) do Proofpoint on Demand

Para testar as etapas neste tutorial, siga estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

* Adicione Proofpoint on Demand da galeria.
* Configurar e testar logon único do Azure AD.

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>Adicionar Proofpoint on Demand da galeria
Para configurar a integração do Proofpoint on Demand ao Azure AD, você precisa adicionar o Proofpoint on Demand da galeria à sua lista de aplicativos SaaS gerenciados.

1. No portal clássico do Azure, no painel de navegação esquerdo, clique em **Active Directory**.
   
    ![Ícone do Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **APLICATIVOS** no menu na parte superior.
   
    ![Item de menu APLICATIVOS][2]
4. Clique em **ADICIONAR** na parte inferior da página.
   
    ![Botão ADICIONAR][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Opção para adicionar um aplicativo da galeria][4]
6. Na caixa de pesquisa, digite **Proofpoint on Demand**.
   
    ![Caixa em que você digita "Proofpoint on Demand"](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_01.png)
7. No painel de resultados, selecione **Proofpoint on Demand** e clique em **Concluir** para adicionar o aplicativo.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
Nesta seção, você configura e testa o logon único do Azure AD com o Proofpoint on Demand com base em uma usuária chamada Brenda Fernandes.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Proofpoint on Demand é equivalente a um usuário do Azure AD. Em outras palavras, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Proofpoint on Demand.

Você estabelece essa relação de vínculo atribuindo o valor de **nome de usuário** no Azure AD como o valor de **nome de usuário** no Proofpoint on Demand.

Para configurar e testar o logon único do Azure AD com o Proofpoint on Demand, conclua os seguintes procedimentos:

1. [Configure o logon único do Azure AD](#configuring-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Crie um usuário de teste do Azure AD](#creating-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
3. [Criar um usuário de teste do Proofpoint on Demand](#creating-a-proofpoint-ondemand-test-user), para ter um equivalente de Brenda Fernandes no Proofpoint on Demand que seja vinculado à representação no Azure AD.
4. [Atribua o usuário de teste do Azure AD](#assigning-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Testar logon único](#testing-single-sign-on), para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo do Proofpoint on Demand.

1. No portal clássico, na página de integração do aplicativo **Proofpoint on Demand**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Botão "Configurar logon único"][6]
2. Na página **Como você deseja que os usuários façam logon no Proofpoint on Demand**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Botão de opção "Logon Único do Microsoft Azure AD "](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_03.png)
3. Na página **Definir Configurações do Aplicativo** , execute as seguintes etapas:
   
    ![Página "Definir configurações do aplicativo" com caixas preenchidas](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_04.png)
   1. Na **URL DE LOGON**, digite a URL em que os usuários entram em seu aplicativo Proofpoint on Demand. Use o seguinte padrão: **https://\<hostname\>.pphosted.com/ppssamlsp_hostname**
   2. Na caixa **IDENTIFICADOR**, digite a URL usando o seguinte padrão: **https://\<hostname/>.pphosted.com/ppssamlsp**
   3. Na caixa **URL de RESPOSTA**, digite a URL usando o seguinte padrão: **https://\<hostname/>.pphosted.com:portnumber/v1/samlauth/samlconsumer**  
   4. Clique em **Próximo**.
4. Na página **Configurar logon único no Proofpoint on Demand**, execute as seguintes etapas:
   
    ![Página "Configurar logon único no Proofpoint on Demand" com botão "Baixar certificado"](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_05.png)
   1. Clique em **Baixar certificado**e salve o arquivo em seu computador.   
   2. Clique em **Próximo**.
5. Para que o SSO seja configurado para o aplicativo, entre em contato com a equipe de suporte do Proofpoint on Demand e forneça o seguinte:
   * O certificado baixado
   * A ID da entidade
   * A URL de SSO do SAML
6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Caixa de seleção que confirma que você configurou o logon único][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Página de confirmação][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

![O usuário de teste na lista de usuários][20]

1. No portal clássico do Azure, no painel de navegação esquerdo, clique em **Active Directory**.
   
    ![Ícone do Active Directory](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_09.png)
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **USUÁRIOS**.
   
    ![Item de menu USUÁRIOS](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_03.png)
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **ADICIONAR USUÁRIO**.
   
    ![Botão ADICIONAR USUÁRIO](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_04.png)
5. Na página **Conte-nos sobre este usuário**, realize as seguintes etapas:

    ![Página "Conte-nos sobre este usuário" com as caixas preenchidas](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_05.png)   
   1. Na caixa **TIPO DE USUÁRIO**, selecione **Novo usuário na sua organização**.
   2. Na caixa **NOME de USUÁRIO**, digite **BrendaFernandes**.
   3. Clique em **Próximo**.
6. Na página **perfil do usuário**, realize as seguintes etapas:

  ![A página "perfil do usuário" com as caixas preenchidas](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_06.png)   
   1. No **NOME**, digite **Brenda**.  
   2. No **SOBRENOME**, digite **Fernandes**.
   3. Na caixa **NOME de EXIBIÇÃO**, digite **Brenda Fernandes**.
   4. Na lista **FUNÇÃO**, selecione **Usuário**.
   5. Clique em **Próximo**.
7. Na página **Obter senha temporária**, clique em **Criar**.
   
   ![Botão para criar uma senha temporária](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_07.png)
8. Na página **Obter senha temporária** , execute as seguintes etapas:
   
   ![Página "Obter senha temporária" com informações de senha](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_08.png)  
   1. Anote o valor da caixa **NOVA SENHA**.
   2. Clique em **Concluído**.   

### <a name="create-a-proofpoint-on-demand-test-user"></a>Criar um usuário de teste do Proofpoint on Demand
Nesta seção, você cria uma usuária chamada Brenda Fernandes no Proofpoint on Demand. Trabalhe com a equipe de suporte do Proofpoint on Demand para adicionar usuários na plataforma Proofpoint on Demand.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Proofpoint on Demand.

![Informações do usuário, mostrando o acesso habilitado por meio do método direto][200]

1. No portal clássico, na exibição de diretório, clique em **APLICATIVOS** no menu na parte superior para abrir a exibição de aplicativos.
   
    ![Item de menu APLICATIVOS][201]
2. Na lista de aplicativos, selecione **Proofpoint on Demand**.
   
    ![Lista de aplicativos com o Proofpoint on Demand selecionado](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_50.png)
3. No menu na parte superior, clique em **USUÁRIOS**.
   
    ![Item de menu USUÁRIOS][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **ATRIBUIR**.
   
    ![Botão atribuir][205]

### <a name="test-single-sign-on"></a>Testar logon único
Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do **Proofpoint on Demand** no painel de acesso, você deve entrar automaticamente em seu aplicativo do Proofpoint on Demand.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_205.png

