---
title: "Tutorial: Integração do Azure Active Directory ao HackerOne | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o HackerOne."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 229d1efb-b6a5-4df8-9839-5d551487db4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: ee734cd99ad6aca31ab9350a0c2702ed9cc956fb


---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Tutorial: Integração do Azure Active Directory ao HackerOne
Neste tutorial, você integrará o HackerOne ao Azure AD (Azure Active Directory).

A integração do HackerOne ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao HackerOne
* Você pode permitir que seus usuários façam logon automaticamente no HackerOne (logon único) com as contas do Azure AD deles
* Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao HackerOne, você precisa dos seguintes itens:

* Uma assinatura do Azure
* Uma assinatura habilitada para logon único do HackerOne

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o HackerOne da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-hackerone-from-the-gallery"></a>Adicionando o HackerOne da galeria
Para integrar o HackerOne ao Azure AD, você precisará adicionar o HackerOne à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o HackerOne da galeria, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

![Aplicativos][4]

1. Na caixa de pesquisa, digite **HackerOne**.

![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_01.png)

1. No painel de resultados, selecione **HackerOne** e clique em **Concluir** para adicionar o aplicativo.

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
Em seguida, você configurará e testará o logon único do Azure AD com o HackerOne com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do HackerOne é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do HackerOne.  
Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no HackerOne.

Para configurar e testar o logon único do Azure AD com o HackerOne, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Simon Britta.
3. **[Criação de um usuário de teste do HackerOne](#creating-a-hackerone-test-user)** : para ter um equivalente de Brenda Fernandes no Certify que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - habilitar Simon Britta a usar o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
Em seguida, você habilitará o logon único do Azure AD no portal clássico e configurará o logon único em seu aplicativo HackerOne.

Como parte deste procedimento, será necessário criar um arquivo de certificado codificado em base 64.  
Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Para configurar o logon único do Azure AD com o HackerOne, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **HackerOne**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários entrem no HackerOne**, escolha **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar o logon único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_03.png) 
3. Na página de diálogo **Definir Configurações do Aplicativo**, execute as seguintes etapas e, em seguida, clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_04.png) 

    a. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para fazer logon no seu aplicativo HackerOne usando o seguinte padrão: **“https://hackerone.com/\<nome da empresa\>/authentication”**. 

    b. Contate a equipe de suporte do HackerOne pelo email [support@hackerone.com](mailto:support@hackerone.com) para obter a URL do locatário, caso não saiba qual é.

    c. Na caixa de texto **Identificador** , digite a URL do locatário. 

    d. Clique em **Próximo**.


1. Na página **Configurar logon único no HackerOne**, execute as seguintes etapas e clique em **Avançar**:
   
    ![Configurar Logon Único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_05.png) 
   
    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   
    b. Clique em **Próximo**.
2. Faça logon no seu locatário HackerOne como administrador.
3. No menu na parte superior, clique em **Configurações**.
   
    ![Configurar o logon único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png) 
4. Navegue até "**Autenticação**" e clique em "**Adicionar configurações do SAML**".
   
    ![Configurar Logon Único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png) 
5. Na caixa de diálogo **Configurações de SAML** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png) 
   
    a. Na caixa de texto **Domínio de Email** , digite um domínio registrado.
   
    b. No portal clássico do Azure, copie a **URL do Serviço de Logon Único**e cole-a na caixa de texto URL de Logon Único.
   
    c. Crie um arquivo **codificado em base 64** usando o certificado baixado.  
   
       >[AZURE.TIP] Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
   
    d. Abra seu certificado codificado de base 64 no bloco de notas, copie o conteúdo dele para a área de transferência e cole-o na caixa de texto **Certificado X509** .
   
    e. Clique em **Salvar**
6. No diálogo Configurações de Autenticação, execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png) 
   
    a. Clique em **Executar teste**.
   
    b. Se o valor do campo **Status** for igual a **Status do último teste: criado**, contate sua equipe de suporte do HackerOne através de [support@hackerone.com](mailto:support@hackerone.com) para solicitar uma revisão da sua configuração.
7. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**.
   
    ![Logon Único do AD do Azure][10]
8. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
Em seguida, você criará um usuário de teste no portal clássico chamado Brenda Fernandes.  

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste do SECURE DELIVER no Azure AD, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hackerone-tutorial/create_aaduser_09.png) 
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png) 
5. Na página de diálogo **Conte-nos sobre este usuário** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hackerone-tutorial/create_aaduser_05.png) 
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hackerone-tutorial/create_aaduser_06.png) 
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hackerone-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-hackerone-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-hackerone-test-user"></a>Criação de um usuário de teste do HackerOne
Em seguida, você criará um usuário chamado Brenda Fernandes no HackerOne. O HackerOne dá suporte ao provisionamento just-in-time, que está habilitado por padrão.

Não há itens de ação para você nesta seção. Quando você acessar HackerOne, um novo usuário será criado caso ele ainda não exista. [Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on).

> [!NOTE]
> Se precisar criar um usuário manualmente, você precisará entrar em contato com a equipe de suporte do Certify.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
Em seguida, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao HackerOne.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao HackerOne, execute as seguintes etapas:**

1. No portal clássico do Azure, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **HackerOne**.
   
    ![Configurar Logon Único](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
Por fim, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Quando você clica no bloco HackerOne no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo HackerOne.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


