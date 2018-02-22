---
title: "Tutorial: Integração do Azure Active Directory ao BambooHR | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o BambooHR."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 081144a645683d4d00ed0d464e23558378dc1b38
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Tutorial: Integração do Azure Active Directory ao BambooHR

Neste tutorial, você aprende a integrar o BambooHR ao Azure AD (Azure Active Directory).

A integração do BambooHR ao Microsoft Azure Active Directory oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao BambooHR.
- Você pode permitir que seus usuários façam logon automaticamente no BambooHR usando SSO (logon único) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local, o portal clássico do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS ao Azure AD, consulte [O que é o acesso de aplicativos e o logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD ao BambooHR, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada pelo SSO do BambooHR

> [!NOTE]
> Ao testar as etapas deste tutorial, recomendamos que você não use um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Microsoft Azure Active Directory, você pode [obter uma versão de avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o BambooHR por meio da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="add-bamboohr-from-the-gallery"></a>Adicionar o BambooHR por meio da galeria
Para configurar a integração do BambooHR ao Azure AD, adicione o BambooHR à lista de aplicativos SaaS gerenciados por meio da galeria da seguinte maneira:

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![O painel Aplicativos Empresariais][2]
    
3. Para adicionar um aplicativo, selecione **Novo aplicativo**.

    ![O botão “Novo aplicativo”][3]

4. Na caixa de pesquisa, digite **BambooHR**. Na lista de resultados, selecione **BambooHR**e, em seguida, selecione **adicionar**.

    ![BambooHR na lista de resultados](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você irá configurar e testar o SSO do Azure AD com o BambooHR usando o usuário de teste “Brenda Fernandes”.

Para o SSO funcionar, o Microsoft Azure Active Directory precisa saber qual é o seu usuário correspondente no BambooHR. Em outras palavras, você precisa estabelecer uma relação de vínculo entre um usuário do Microsoft Azure Active Directory e o usuário relacionado do BambooHR.

Para estabelecer a relação de vínculo no BambooHR, atribua o valor **nome de usuário** do Microsoft Azure Active Directory como o valor de **Nome do usuário** do BambooHR.

Para configurar e testar o logon único do SSO do Microsoft Azure Active Directory com o BambooHR, complete os blocos de construção nas próximas cinco seções.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o SSO do Azure AD no portal do Azure e configura o SSO em seu aplicativo BambooHR fazendo seguinte:

1. No portal do Azure, na página de integração do aplicativo do **BambooHR**, selecione **Logon único**.

    ![Link Configurar logon único][4]

2. Na janela **Logon Único**, na lista suspensa **Modo**, selecione **Logon baseado em SAML**.
 
    ![Janela de Logon único](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. Em **Domínio e URLs do BambooHR**, faça o seguinte:

    ![Seção Domínio e URLs do BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. Na caixa **URL de Entrada**, digite um URL usando o seguinte formato: `https://<company>.bamboohr.com`.

    b. Na caixa **Identificador**, digite um valor: `BambooHR-SAML`.

    > [!NOTE] 
    > O valor da **URL de logon** não é real. Atualize-o com a URL de logon real. Para obter o valor, contate a [equipe de suporte ao cliente do BambooHR](https://www.bamboohr.com/contact.php). 
 
4. Na seção **Certificado de Autenticação do SAML**, selecione **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Selecione **Salvar**.

    ![O botão Salvar](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_400.png)

6. Em **Configuração do BambooHR**, selecione **Configurar o BambooHR** para abrir a janela **Configurar logon**. Na seção **Referência Rápida**, copie a **URL do Serviço de Logon Único SAML** para usar mais tarde.

    ![Configuração do BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

7. Em uma nova janela, entre no site da empresa do BambooHR como administrador.

8. Na página inicial, faça o seguinte:
   
    ![A página de logon único do BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/ic796691.png "Logon único")   

    a. Selecione **Aplicativos**.
   
    b. No painel **Aplicativos**, selecione **Logon único**.
   
    c. Selecione **Logon Único do SAML**.

9. No painel **Logon único do SAML**, faça o seguinte:
   
    ![O painel de Logon Único do SAML](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "Logon Único do SAML")
   
    a. Na caixa **URL de logon SSO**, cole a **URL de Serviço de Logon Único SAML** que você copiou do Portal do Azure na etapa 6.
      
    b. No Bloco de Notas, abra o certificado codificado em Base 64 baixado no Portal do Azure, copie o conteúdo e cole-o na caixa **Certificado X.509**.
   
    c. Selecione **Salvar**.

> [!TIP]
> Enquanto você estiver configurando o aplicativo, é possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com). Depois de adicionar o aplicativo da seção **Active Directory** > **Aplicativos Empresariais**, basta selecionar a guia **Logon Único** e, em seguida, acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Para saber mais, confira a [documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar usuário de teste do Microsoft Azure Active Directory, Brenda Fernandes][100]

Para criar um usuário de teste no Azure AD, faça o seguinte:

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, em seguida, selecione **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_02.png)

3. Na parte superior do painel **Todos os usuários**, selecione **Adicionar**.

    ![O botão Adicionar](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_03.png)

4. Na janela **Usuário**, faça o seguinte:

    ![A janela de usuário](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.
 
### <a name="create-a-bamboohr-test-user"></a>Crie um usuário de teste do BambooHR

Para permitir que os usuários do Microsoft Azure Active Directory façam logon no BambooHR, configure-os manualmente no BambooHR da seguinte maneira:

1. Entre em seu site de empresa do **BambooHR** como administrador.

2. Na barra de ferramentas na parte superior, selecione **Configurações**.
   
    ![O botão Configurações](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Configuração")

3. Selecione **Visão geral**.

4. No painel esquerdo, selecione **Segurança** > **Usuários**.

5. Digite o nome de usuário, a senha e o endereço de email da conta válida do Microsoft Azure Active Directory que você deseja configurar.

6. Selecione **Salvar**.
        
>[!NOTE]
>Para configurar contas de usuários do Microsoft Azure Active Directory, você também pode usar a ferramenta de criação de contas de usuários do BambooHR ou APIs.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Permita que o usuário Brenda Fernandes use o SSO do Azure concedendo-lhe acesso ao BambooHR.

![Atribuir a função de usuário][200] 

Para atribuir o usuário Brenda Fernandes ao BambooHR, faça o seguinte:

1. No Portal do Azure, abra a exibição de aplicativos, acesse a exibição de diretório e, em seguida, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de **Aplicativos empresariais**, selecione **BambooHR**.

    ![O link do BambooHR na lista de Aplicativos empresariais](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. No painel esquerdo, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Selecione o botão **Adicionar** e, em seguida, no painel **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

5. Na janela **Usuários e grupos**, na lista **Usuários**, selecione **Britta Simon**.

6. Escolha o botão **Selecionar**.

7. Na janela **Adicionar atribuição**, selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Teste sua configuração de SSO do Microsoft Azure Active Directory usando o Painel de Acesso.

Quando você selecionar o bloco **BambooHR** no Painel de Acesso, deverá ser automaticamente conectado ao aplicativo BambooHR.

Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_203.png

