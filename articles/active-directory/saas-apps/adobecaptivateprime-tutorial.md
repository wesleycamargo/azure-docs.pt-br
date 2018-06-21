---
title: 'Tutorial: integração do Azure Active Directory com a Adobe Captivate Prime | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e a Adobe Captivate Prime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f95b226-1465-47f4-b8b7-de4b0772abbc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: jeedes
ms.openlocfilehash: 8b8d678f9125e6051dad1b41eba5dec3ec1ac747
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224106"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-captivate-prime"></a>Tutorial: integração do Azure Active Directory com a Adobe Captivate Prime

Neste tutorial, você aprenderá a integrar a Adobe Captivate Prime ao Azure AD (Azure Active Directory).

A integração da Adobe Captivate Prime ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD que tenha acesso ao Adobe Captivate Prime.
- Você pode habilitar seus usuários para fazerem logon automaticamente no Adobe Captivate Prime (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD com Adobe Captivate Prime, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura de logon único habilitado do Adobe Captivate Prime

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Adobe Captivate Prime da Galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-adobe-captivate-prime-from-the-gallery"></a>Adicionando Adobe Captivate Prime da Galeria
Para configurar a integração do Adobe Captivate Prime no Azure AD, você precisa adicionar Adobe Captivate Prime da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Adobe Captivate Prime da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Adobe Captivate Prime**, selecione **Adobe Captivate Prime** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Adobe Captivate Prime na lista de resultados](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Adobe Captivate Prime com base em um usuário de teste chamado "Brenda Fernandes".

Para logon único funcione, o Azure AD precisa saber qual é o usuário correspondente no Adobe Captivate Prime a um usuário no Azure AD. Em outras palavras, uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Adobe Captivate Prime precisa ser estabelecida.

Para configurar e testar o logon único do Azure AD com Adobe Captivate Prime, você precisa concluir os blocos de construção a seguir:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Adobe Captivate Prime](#create-an-adobe-captivate-prime-test-user)** - ter um equivalente de Brenda Fernandes no Adobe Captivate Prime que está vinculada à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, habilitar o logon único do Azure AD no portal do Azure e configurar o logon único no aplicativo Adobe Captivate Prime.

**Para configurar o logon único do Azure AD com Adobe Captivate Prime, execute as seguintes etapas:**

1. No portal do Azure, na página de interação de aplicativos do **Adobe Captivate Prime**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_samlbase.png)

3. Na seção **domínio e URLs do Adobe Captivate Prime**, execute as seguintes etapas:

    ![Informações de logon único da URL e domínio do Adobe Captivate Prime](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL: `https://captivateprime.adobe.com`

    b. Na caixa de texto **URL de resposta**, insira uma URL: `https://captivateprime.adobe.com/saml/SSO`

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/adobecaptivateprime-tutorial/tutorial_general_400.png)

6. Vá para guia **Propriedades**, copie a **URL de acesso de usuário** e cole-a no Bloco de Notas.

    ![O link de acesso do usuário](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_appprop.png)

7. Para configurar o logon único em **Adobe Captivate primo** lado, você precisa enviar baixado **Metadata XML** e copiados **URL de acesso do usuário** para [a equipe de suporte do Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/adobecaptivateprime-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/adobecaptivateprime-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/adobecaptivateprime-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/adobecaptivateprime-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-an-adobe-captivate-prime-test-user"></a>Criar um usuário de teste do Adobe Captivate Prime

Nesta seção, você deve criar um usuário chamado Brenda Fernandes no Adobe Captivate Prime. Trabalhar com [equipe de suporte do Adobe Captivate Prime](mailto:captivateprimesupport@adobe.com) para adicionar os usuários na plataforma Adobe Captivate Prime. Os usuários devem ser criados e ativados antes de você usar o logon único

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você deve habilitar Brenda Fernandes para usar logon único do Azure, concedendo acesso ao Adobe Captivate Prime.

![Atribuir a função de usuário][200] 

**Para atribuir Britta Simon ao Adobe Captivate Prime, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Adobe Captivate Prime**.

    ![O link do Adobe Captivate Prime na lista de aplicativos](./media/adobecaptivateprime-tutorial/tutorial_adobecaptivateprime_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do Adobe Captivate Prime no painel de acesso, você deve conectar-se automaticamente no seu aplicativo Adobe Captivate Prime.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adobecaptivateprime-tutorial/tutorial_general_01.png
[2]: ./media/adobecaptivateprime-tutorial/tutorial_general_02.png
[3]: ./media/adobecaptivateprime-tutorial/tutorial_general_03.png
[4]: ./media/adobecaptivateprime-tutorial/tutorial_general_04.png

[100]: ./media/adobecaptivateprime-tutorial/tutorial_general_100.png

[200]: ./media/adobecaptivateprime-tutorial/tutorial_general_200.png
[201]: ./media/adobecaptivateprime-tutorial/tutorial_general_201.png
[202]: ./media/adobecaptivateprime-tutorial/tutorial_general_202.png
[203]: ./media/adobecaptivateprime-tutorial/tutorial_general_203.png

