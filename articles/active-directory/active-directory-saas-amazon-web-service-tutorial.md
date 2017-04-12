---
title: "Tutorial: Integração do Azure Active Directory com o AWS (Amazon Web Services) | Microsoft Docs"
description: "Saiba como usar o AWS (Amazon Web Service) com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 2cbd477776329ef84ef615b6c28c56cfee12fc99
ms.lasthandoff: 03/31/2017


---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Tutorial: Integração do Azure Active Directory com o AWS (Amazon Web Services)
O objetivo deste tutorial é mostrar como integrar o AWS (Amazon Web Services) ao Azure Active Directory (Azure AD).  

A integração do AWS (Amazon Web Services) ao Azure AD oferece os seguintes benefícios: 

* Você pode controlar no Azure AD quem tem acesso ao AWS (Amazon Web Services) 
* Você pode habilitar seus usuários a fazerem logon automaticamente no AWS (Amazon Web Services) usando SSO (logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD com o AWS (Amazon Web Services), você precisa dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura do AWS (Amazon Web Services) habilitada para SSO (logon único)

>[!NOTE]
>Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção. 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o SSO do Azure AD em um ambiente de teste.  

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o AWS (Amazon Web Services) da galeria 
2. Configurar e testar o SSO do Azure AD

## <a name="add-amazon-web-services-aws-from-the-gallery"></a>Adicionar o AWS (Amazon Web Services) da galeria
Para configurar a integração do AWS (Amazon Web Services) com o Azure AD, você precisa adicionar o AWS (Amazon Web Services), por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o AWS (Amazon Web Services) da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1] 

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal. 
   
    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página. 
   
    ![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**. 
   
    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **serviço AWS (Amazon Web Services)**.
   
    ![Aplicativos][5]

7. No painel de resultados, selecione **AWS (Amazon Web Services)** e, em seguida, clique em **Concluir** para adicionar o aplicativo.
   
    ![Aplicativos][6]

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD
O objetivo desta seção é mostrar como configurar e testar o SSO (logon único) do Azure AD com o AWS (Amazon Web Services), com base em um usuário de teste chamado "Brenda Fernandes".

Para que o SSO funcione, o Azure AD precisa saber qual usuário do AWS (Amazon Web Services) é equivalente a um usuário do Azure AD. Em outras palavras, uma relação de link entre um usuário do Azure AD e o usuário relacionado no serviço AWS (Amazon Web Services) deve ser estabelecida.  

Essa relação de vínculo é estabelecida, atribuindo-se o valor do **nome de usuário** no Azure AD como o valor da **nome de usuário** no AWS (Amazon Web Services).

Para configurar e testar o SSO do Azure AD com o AWS (Amazon Web Services), você precisa concluir os seguintes blocos de construção:

1. **[Configurar logon único do Azure AD](#configuring-azure-ad-single-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do AWS (Amazon Web Services)](#creating-a-halogen-software-test-user)** - para ter um equivalente de Brenda Fernandes no AWS (Amazon Web Services) que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD
O objetivo desta seção é habilitar o SSO do Azure AD no Portal Clássico do Azure e configurar o logon único em seu aplicativo AWS (Amazon Web Services).  

Seu aplicativo AWS (Amazon Web Services) espera as declarações SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizado à sua configuração **atributos de token saml** . 

A captura de tela a seguir mostra um exemplo disso.

![Configurar Logon Único][27]

**Para configurar o logon único do Azure AD com o AWS (Amazon Web Services), execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração do aplicativo **AWS (Amazon Web Services)**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][7]

2. Na página **Como você gostaria que os usuários fizessem logon na página do AWS (Amazon Web Services)**, selecione **Logon Único do Azure AD**, e, em seguida, clique em **Avançar**.
   
    ![Configurar Logon Único][8]

3. Na caixa de diálogo **Definir Configurações do Aplicativo**, clique em **Próximo**. 
   
    ![Definir configurações de aplicativo][9]

4. Na página **Configurar logon único no AWS (Amazon Web Services)** clique em **Baixar metadados**, e, em seguida, salve o arquivo de metadados localmente em seu computador.
   
    ![Configurar Logon Único][10]

5. Em uma janela de navegador diferente, entre no site de sua empresa do AWS (Amazon Web Services) como administrador.

6. Clique em **página inicial do Console**.
   
    ![Configurar Logon Único][11]

7. Clique em **Gerenciamento de identidades e acesso**. 
   
    ![Configurar Logon Único][12]

8. Clique em **Provedores de identidade**, e, em seguida, clique em **Criar provedor**. 
   
    ![Configurar Logon Único][13]

9. Na página da caixa de diálogo **Configurar provedor** , execute as seguintes etapas: 
   
    ![Configurar Logon Único][14]   
  1. Como **Tipo de provedor**, selecione **SAML**.
  2. Na caixa de texto **Nome do provedor**, digite um nome de provedor (p. ex.: *WAAD*).
  3. Para carregar o arquivo de metadados baixado, clique em **Escolher arquivo**.
  4. Clique em **Próxima etapa**.

10. Na página de diálogo **Verificar informações do provedor**, clique em **Criar**. 
    
    ![Configurar Logon Único][15]

11. Clique em **Funções** e, em seguida, clique em **Criar Nova Função**. 
    
    ![Configurar o logon único][16]

12. Na caixa de diálogo **Definir Nome de Função** , execute as seguintes etapas: 
    
    ![Configurar o logon único][17] 
  1. Na caixa de texto **Nome da função** , digite um nome de função (por exemplo: *TestUser*). 
  2. Clique em **Próxima etapa**.

13. Na caixa de diálogo **Selecionar Tipo de Função** , execute as seguintes etapas: 
    
    ![Configurar Logon Único][18] 
  1. Selecione **Função de acesso do provedor de identidade**. 
  2. Na seção **Conceder acesso de logon único da Web (WebSSO) a provedores SAML**, clique em **Selecionar**.

14. Na caixa de diálogo **Estabelecer Confiança** , execute as seguintes etapas:  
    
    ![Configurar Logon Único][19] 
  1. Como provedor SAML, selecione o provedor SAML criado previamente (p. ex.: *WAAD*)   
  2. Clique em **Próxima etapa**.

15. Na caixa de diálogo **Verificar Confiança na Função**, clique em **Próxima Etapa**. 
    
    ![Configurar o logon único][32]

16. Na caixa de diálogo **Anexar Política**, clique em **Próxima Etapa**.  
    
    ![Configurar Logon Único][33]

17. Na caixa de diálogo **Examinar** , execute as seguintes etapas:   
    
    ![Configurar Logon Único][34] 
  1. Cópia do valor **Função ARN** .  
  2. Cópia do valor ARN de **Entidades confiáveis** . 
  3. Clique em **Criar função**. 

18. No portal clássico do Azure, selecione a confirmação de configuração de logon único e clique em **Avançar**.
    
    ![O que é o Azure AD Connect][20]

19. Na página **Confirmação de logon único**, clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![O que é o Azure AD Connect][22]

20. Na parte superior do menu, clique em **Atributos** to open the **SAML Token Atributos** . 
    
    ![Configurar Logon Único][21]

21. Clique em **adicionar atributo de usuário**. 
    
    ![Configurar Logon Único][23]

22. Na caixa de diálogo Adicionar Atributo de Usuário, execute as seguintes etapas. 
    
    ![Configurar Logon Único][24]  
  1. Na caixa de texto **Nome do Atributo**, digite **https://aws.amazon.com/SAML/Attributes/Role**.  
  2. Na caixa de texto **Valor do Atributo**, digite **[o valor da função ARN], [o valor ARN de entidade confiável]**.
    
     >[!TIP]
     >Estes são os valores que você copiou da caixa de diálogo Revisão quando criou sua função. 
     > 
     
  3. Clique em **Concluir** para fechar a caixa de diálogo **Adicionar Atributo de Usuário**.

23. Clique em **adicionar atributo de usuário**. 
    
    ![Configurar o logon único][23]

24. Na caixa de diálogo Adicionar Atributo de Usuário, execute as seguintes etapas e clique em **Aplicar Alterações**. 
    
    ![Configurar o logon único][25]
 1. Na caixa de texto **Nome do Atributo**, digite **https://aws.amazon.com/SAML/Attributes/RoleSessionName**.
 2. Na caixa de texto **Valor do Atributo**, digite ou selecione **user.userprincipalname** na lista suspensa.

     ![Configurar o logon único][35]
 3. Clique em **Concluir** para fechar a caixa de diálogo **Adicionar Atributo de Usuário**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas: 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_05.png) 
 1. Em Tipo de Usuário, selecione Novo usuário na organização.
 2. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
 3. Clique em Próximo.

6. Na página de caixa de diálogo **Perfil do Usuário** , realize as seguintes etapas: 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_06.png)  
 1. Na caixa de texto **Nome**, digite **Brenda**.   
 2. Na caixa de texto **Sobrenome**, digite **Fernandes**. 
 3. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**. 
 4. Na lista **Função**, selecione **Usuário**. 
 5. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_07.png) 

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_08.png)  
 1. Anote o valor da **Nova Senha**.  
 2. Clique em **Concluído**.   

### <a name="create-a-amazon-web-services-aws-test-user"></a>Criar um usuário de teste do AWS (Amazon Web Services)
O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no AWS (Amazon Web Services).

**Para criar uma usuária chamada Brenda Fernandes no AWS (Amazon Web Services), execute as seguintes etapas:**

1. Faça logon no site da sua empresa **AWS (Amazon Web Services)** como administrador.

2. Clique no ícone **Página Inicial do Console** . 
   
    ![Configurar Logon Único][11]

3. Clique em Gerenciamento de identidades e acesso. 
   
    ![Configurar Logon Único][28]

4. No Painel, clique em Usuários e, em seguida, clique em Criar Novos Usuários. 
   
    ![Configurar Logon Único][29]

5. No diálogo Criar Usuário, realize as seguintes etapas: 
   
    ![Configurar Logon Único][30]   
 1. Na caixa de texto **Inserir Nomes de Usuário** , digite o nome de usuário (userprincipalname) de Brenda Fernandes no AD do Azure. 
 2. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD
O objetivo desta seção é permitir que Brenda Fernandes use o SSO do Azure, concedendo o acesso ao serviço AWS (Amazon Web Services).

![Atribuir usuário][31]

**Para atribuir Brenda Fernandes ao AWS (Amazon Web Services), execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][26]

2. Na lista de aplicativos, selecione **AWS (Amazon Web Services)**.
   
    ![Atribuir usuário][27]

3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][25]

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][29]

### <a name="test-single-sign-on"></a>Testar logon único
O objetivo desta seção é testar sua configuração de SSO do Azure AD usando o Painel de Acesso.  

Ao clicar no bloco AWS (Amazon Web Services) no Painel de Acesso, você deve fazer logon automaticamente em seu aplicativo AWS (Amazon Web Services).

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service-tutorial/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service-tutorial/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/user_attributes_01.png























