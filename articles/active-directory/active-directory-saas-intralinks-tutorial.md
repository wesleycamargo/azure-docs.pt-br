---
title: "Tutorial: integração do Azure Active Directory com o Intralinks | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Intralinks."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 147f2bf9-166b-402e-adc4-4b19dd336883
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ed2fc2b34ff10acc806daec84986f8db58e713c3
ms.openlocfilehash: 2112f4b129ff8735955d440341174a44a6a1469c
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>Tutorial: Integração do Azure Active Directory com o Intralinks
Neste tutorial, você aprenderá como integrar o Intralinks ao Azure AD (Azure Active Directory).

A integração do Intralinks ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Intralinks
* Você pode permitir que seus usuários façam logon automaticamente no SSO (logon único) do Intralinks com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Intralinks, você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do Intralinks

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Intralinks da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="add-intralinks-from-the-gallery"></a>Adicionar o Intralinks da galeria
Para configurar a integração do Intralinks ao Azure AD, você precisará adicionar o Intralinks da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Intralinks da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Intralinks**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_01.png)
7. No painel de resultados, selecione **Intralinks**, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_02.png)

## <a name="configure-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD
Nesta seção, você configurará e testará o logon único do Azure AD com o Intralinks, com base em uma usuária de teste chamada "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do Intralinks é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Intralinks.

>[!NOTE]
>Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Username** no Intralinks.
>

Para configurar e testar o logon único do Azure AD com o Intralinks, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configuring-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#creating-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar de um usuário de teste do Intralinks](#creating-an-intralinks-test-user)** – para ter um equivalente de Brenda Fernandes no Intralinks, que esteja vinculado à representação dela no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Testar o logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
Nesta seção, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo Intralinks.

**Para configurar o logon único do Azure AD com o Intralinks, execute as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos **Intralinks**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você gostaria que os usuários fizessem logon no Intralinks**, selecione **Logon Único do Azure AD**, em seguida, clique em **Próximo**.
   
    ![Configurar Logon Único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_03.png) 
3. Na página do diálogo **Definir Configurações do Aplicativo** , realize as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_04.png) 
   
    1. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon no seu aplicativo Intralinks usando o seguinte padrão: **https://\<nome empresa\>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/\<ID de Locatário do Azure AD\>/**.
    2. Clique em **Próximo**.
4. Na página **Configurar logon único no Intralinks** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_05.png)
   
    1. Clique em **Baixar metadados**e salve o arquivo no computador.
    2. Clique em **Próximo**.
5. Para configurar o SSO para seu aplicativo, entre em contato com a equipe de suporte do Intralinks e envie o arquivo de metadados baixado anexado ao email.
6. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
7. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
Nesta seção, você criará uma usuária de teste no portal clássico chamada Brenda Fernandes.

* Na lista de usuários, selecione **Brenda Fernandes**.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-intralinks-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-intralinks-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-intralinks-tutorial/create_aaduser_04.png) 
5. Na página de diálogo **Conte-nos sobre este usuário**, realize as seguintes etapas:  ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-intralinks-tutorial/create_aaduser_05.png) 
   
    1. Em Tipo de Usuário, selecione Novo usuário na organização. 
    2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
    3. Clique em **Próximo**.
6. Na caixa de diálogo **perfil de usuário**, realize as etapas a seguir: ![criação de um usuário de teste do AD do Azure](./media/active-directory-saas-intralinks-tutorial/create_aaduser_06.png) 
   
   1. Na caixa de texto **Nome**, digite **Brenda**.  
   2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.   
   4. Na lista **Função**, selecione **Usuário**.
   5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-intralinks-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-intralinks-tutorial/create_aaduser_08.png) 
   
    1. Anote o valor da **Nova Senha**.
    2. Clique em **Concluído**.   

### <a name="create-an-intralinks-test-user"></a>Criar um usuário de teste do Intralinks
Nesta seção, você criará uma usuária chamada Brenda Fernandes no Intralinks. Trabalhe com a equipe de suporte do Intralinks para adicionar os usuários à plataforma do Intralinks.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso ao Intralinks.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Intralinks, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Intralinks**.
   
    ![Configurar Logon Único](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203]
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="add-intralinks-via-or-elite-application"></a>Adicionar o aplicativo Intralinks VIA ou Elite
O Intralinks usa a mesma plataforma de Identidade de SSO para todos os outros aplicativos Intralinks excluindo o aplicativo Deal Nexus. Portanto, se você planeja usar qualquer outro aplicativo Intralinks, primeiro você precisa configurar o SSO para um aplicativo Intralinks primário usando o procedimento descrito acima.

Depois disso, você pode seguir o procedimento abaixo para adicionar outro aplicativo Intralinks em seu locatário, que pode aproveitar esse aplicativo principal para o SSO. 

>[!NOTE]
>Esse recurso está disponível somente para clientes de SKU do Azure AD Premium e não está disponível para clientes de SKU Gratuito ou Básico.
> 

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na guia à esquerda, clique na guia **Personalizado**.
   
    ![Adicionar o aplicativo Intralinks VIA ou Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_51.png)
7. Forneça o nome apropriado para o aplicativo, por exemplo, **Intralinks Elite**, então clique no botão Concluir.
8. Clique no botão **Configurar o Logon Único**.
9. Selecione a opção **Logon Único Existente**.
   
    ![Adicionar o aplicativo Intralinks VIA ou Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_52.png)
10. Obtenha a URL de SSO Iniciada do SP da equipe do Intralinks para o outro aplicativo Intralinks e insira-a como mostrado abaixo. 
    
    ![Adicionar o aplicativo Intralinks VIA ou Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_53.png)
    
    * Na caixa de texto URL de Logon, digite a URL usada pelos usuários para fazer logon no seu aplicativo Intralinks usando o seguinte padrão: **https://\<NomeEmpresa\>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/\<IDLocatarioAzureAD\>/**
11. Clique em **Próximo**.
12. Atribua o aplicativo para usuários ou grupos, como mostrado na seção **[Atribuição do usuário de teste do Azure AD](#assigning-the-azure-ad-test-user)**.

### <a name="test-single-sign-on"></a>Testar logon único
Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Intralinks no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo Intralinks.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_205.png

