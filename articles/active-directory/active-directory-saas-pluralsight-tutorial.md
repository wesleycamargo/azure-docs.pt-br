---
title: "Tutorial: integração do Azure Active Directory ao Pluralsight | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Pluralsight."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4c3f07d2-4e1f-4ea3-9025-c663f1f2b7b4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: f77f459219a5c9a0e218de924c0c7578647a3594
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-pluralsight"></a>Tutorial: Integração do Azure Active Directory com o Pluralsight
O objetivo desse tutorial é mostrar como integrar o Pluralsight ao Azure AD (Azure Active Directory).

A integração do Pluralsight ao Azure AD oferece os seguintes benefícios:

* No AD do Azure, você poderá controlar quem tem acesso ao Pluralsight
* Você pode permitir que seus usuários façam logon automaticamente no Pluralsight usando SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do AD do Azure com o Pluralsight, você precisa dos seguintes itens:

* Uma assinatura do Azure
* Uma assinatura habilitada para SSO do Pluralsight

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Como adicionar o Pluralsight da galeria
2. Configurar e testar o SSO do Azure AD

## <a name="add-pluralsight-from-the-gallery"></a>Adicionar o Pluralsight da galeria
Para configurar a integração do Pluralsight ao AD do Azure, você precisará adicionar o Pluralsight da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Pluralsight por meio da galeria, realize as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Pluralsight**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_01.png)
7. No painel de resultados, selecione **Pluralsight** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_06.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO do Azure AD com o Pluralsight com base em um usuário de teste chamado "Brenda Fernandes".

Para configurar e testar o SSO do Azure AD com o Pluralsight, você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar logon único do Azure AD com Britta Simon.
3. **[Criar um usuário de teste do Pluralsight](#creating-a-pluralsight-test-user)** - para ter um equivalente de Brenda Fernandes no Pluralsight que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testar o logon único](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD
O objetivo desta seção é habilitar o logon único do AD do Azure no Portal Clássico do Azure e configurar o SSO em seu aplicativo Pluralsight.

Seu aplicativo Pluralsight espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra um exemplo disso. 

![Configurar Logon Único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_02.png) 

Também é possível adicionar o atributo **“ID Exclusiva”** com o valor apropriado, como EmployeeID, ou algo que se adapte à sua organização. Observe também que esse não é o atributo obrigatório; no entanto, é possível adicioná-lo para identificar o usuário exclusivo. 

**Para configurar o logon único do AD do Azure com o Pluralsight, execute as seguintes etapas:**

1. No Portal clássico do Azure, na página de integração de aplicativos do **Pluralsight**, no menu superior, clique em **Atributos**.
   
    ![Configurar Logon Único](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_81.png) 
2. Para remover os **atributos de token SAML**redundantes, realize as seguintes etapas: 
   
    ![Configurar Logon Único](./media/active-directory-saas-pluralsight-tutorial/2829.png) 
  * Para cada atributo de usuário mostrado na caixa vermelha da tabela acima, focalize o atributo e clique em Excluir. 
3. Para adicionar os **atributos de token SAML**necessários, para cada linha mostrada na tabela abaixo, realize as seguintes etapas:
   
   | Nome do atributo | Valor do atributo |
   | --- | --- |
   | Nome |user.givenname |
   | Sobrenome |user.surname |
   | Email |user.mail |
4. Clique em **adicionar atributo de usuário** para abrir a caixa de diálogo **Adicionar Atributo de Usuário**.
   
    ![Configurar Logon Único](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_82.png)
  1. Na caixa de texto **Nome do Atributo** , digite o nome do atributo mostrado nessa linha.
  2. Na lista **Valor do Atributo** , selecione o valor do atributo mostrado nessa linha.
  3. Clique em **Concluído**.    
  4. Clique em **Aplicar alterações**.
 
   ![Configurar Logon Único](./media/active-directory-saas-pluralsight-tutorial/3232.png) 
    
5. No menu na parte superior, clique em **Início Rápido**.
   
    ![Configurar o logon único](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_83.png)  
6. No Portal clássico do Azure, na página de integração de aplicativos do **Pluralsight**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
7. Na página **Como você deseja que os usuários façam logon no Pluralsight**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_03.png) 
8. Na página do diálogo **Definir Configurações do Aplicativo** , realize as seguintes etapas:
   
    ![Configurar o logon único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_04.png) 
  1. Na caixa de texto URL de Entrada, digite a URL usada pelos usuários para entrar em seu aplicativo do Pluralsight usando o seguinte padrão: `https://<instance name>.pluralsight.com/sso/<comapny name>`
  2. Clique em **Próximo**.
9. Na página **Configurar logon único no Pluralsight**, execute as seguintes etapas:

  ![Configurar Logon Único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_05.png)   
  1. Clique em **Baixar metadados**e salve o arquivo no computador.
  2. Clique em **Próximo**.
10. Para configurar o SSO para seu aplicativo, entre em contato com a equipe de [Serviços Profissionais](mailTo:professionalservices@pluralsight.com) do Pluralsight e forneça o arquivo de metadados baixado.
11. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
12. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

* Na lista de usuários, selecione **Brenda Fernandes**.

  ![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_05.png)  
  1. Em Tipo de Usuário, selecione Novo usuário na organização.
  2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
  3. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_06.png) 
  1. Na caixa de texto **Nome**, digite **Brenda**.  
  2. Na caixa de texto **Sobrenome**, digite **Fernandes**.
  3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
  4. Na lista **Função**, selecione **Usuário**.
  5. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_08.png) 
  1. Anote o valor da **Nova Senha**.
  2. Clique em **Concluído**.   

### <a name="create-a-pluralsight-test-user"></a>Criar um usuário de teste do Pluralsight
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Pluralsight. Trabalhe com a equipe de suporte do Pluralsight para adicionar usuários à conta do Pluralsight. 

>[!NOTE]
>Se você precisar criar um usuário manualmente, entre em contato com a equipe de suporte do Pluralsight. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo a ela acesso ao Pluralsight.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Pluralsight, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Pluralsight**.
   
    ![Configurar Logon Único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Pluralsight no Painel de Acesso, você deve ser conectado automaticamente ao aplicativo Pluralsight.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_205.png

