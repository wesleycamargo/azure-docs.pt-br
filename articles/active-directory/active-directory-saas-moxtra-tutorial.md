---
title: "Tutorial: Integração do Azure Active Directory com o Moxtra | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Active Directory do Azure e o Moxtra."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b35d87a10e216c07d01ceb838253f75efa8f59a8


---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Tutorial: integração do Active Directory do Azure ao Moxtra
O objetivo deste tutorial é mostrar como integrar o Moxtra ao Azure AD (Azure Active Directory).  
A integração do Moxtra ao Azure AD oferece os seguintes benefícios: 

* No Azure AD, você pode controlar quem tem acesso ao Moxtra 
* Você pode permitir que os usuários façam logon automaticamente no Moxtra (Logon único) com suas contas do Azure AD
* Gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos
Para configurar a integração do Azure AD ao Moxtra, você precisará dos seguintes itens:

* Uma assinatura do AD do Azure
* Uma assinatura habilitada para logon único do Moxtra

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.
> 
> 

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

* Não use o ambiente de produção, a menos que seja necessário.
* Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descrição do cenário
O objetivo deste tutorial é permitir que você teste o logon único do Azure AD em um ambiente de teste.  
O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Moxtra da galeria 
2. configurar e testar o logon único do AD do Azure

## <a name="adding-moxtra-from-the-gallery"></a>Adicionando Moxtra da galeria
Para configurar a integração do Moxtra ao Azure AD, você precisará adicionar o Moxtra da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Moxtra a partir da galeria, realize as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 
   
    ![Active Directory][1]
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos][2]
4. Clique em **Adicionar** na parte inferior da página.
   
    ![Aplicativos][3]
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Aplicativos][4]
6. Na caixa de pesquisa, digite **Moxtra**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_01.png)
7. No painel de resultados, selecione **Moxtra** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure
O objetivo desta seção é mostrar como configurar e testar o logon único do Azure AD com o Moxtra, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Moxtra é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Moxtra.  
Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Moxtra.

Para configurar e testar o logon único do Azure AD com o Moxtra, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste Moxtra](#creating-a-moxtra-test-user)** - para ter um equivalente de Brenda Fernandes no Moxtra que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do AD do Azure
O objetivo desta seção é habilitar o logon único do Azure AD no portal clássico do Azure e configurar o logon único em seu aplicativo do Moxtra. 

Seu aplicativo do Moxtra espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra um exemplo disso.

![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_09.png) 

**Para configurar o logon único do Azure AD com o Moxtra, realize as seguintes etapas:**

1. No portal clássico, na página de integração de aplicativos do **Moxtra**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único][6] 
2. Na página **Como você deseja que os usuários façam logon no Moxtra**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_03.png) 
3. Na página do diálogo **Definir Configurações do Aplicativo** , realize as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_04.png) 
   
    a. Na caixa de texto **URL de Logon**, digite a seguinte URL: **https://www.moxtra.com/service/#login**.
   
    b. Clique em **Avançar**.
4. Na página **Configurar logon único no Moxtra** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_05.png) 
   
    a. Clique em **Baixar certificado**e salve o arquivo em seu computador.
   
    b. Clique em **Avançar**.
5. Em outra janela do navegador, entre em seu site de empresa do Moxtra como administrador.
6. Na barra de ferramentas à esquerda, clique em **Console do Administrador > Logon Único do SAML** e em **Novo**.
   
    ![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_06.png) 
7. Na página **SAML** , execute as seguintes etapas:
   
    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_08.png) 
   
    a. Na caixa de texto **Nome** , digite um nome para a sua configuração (por exemplo: *SAML*). 
   
    b. No portal clássico do Azure, na página de diálogo **Configurar logon único no Moxtra**, copie o valor da **ID de Entidade** e cole-o na caixa de texto **ID de Entidade do IDP**. 
   
    c. No portal clássico do Azure, na página de diálogo **Configurar logon único no Moxtra**, copie o valor de **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**. 
   
    d. Na caixa de texto **AuthnContextClassRef**, digite **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.
   
    e. No Portal Clássico do Azure, na página de diálogo **Configurar logon único no Moxtra**, copie o valor de **Formato do Identificador de Nome** e cole-o na caixa de texto **Formato da ID de Nome**. 
   
    f. Abra seu certificado baixado no Bloco de Notas, copie o conteúdo e cole-o na caixa de texto **Certificado** .    
   
    g. Na caixa de texto de domínio de email SAML email, digite seu domínio de email SAML.    
   
   > [!NOTE]
   > Para ver as etapas de verificação do domínio, clique no “**i**” abaixo.
   > 
   > 

    h. Clique em **Atualizar**.


1. No Portal Clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Avançar**. 
   
    ![Logon Único do AD do Azure][10]
2. Na página **Confirmação de logon único**, clique em **Concluir**.  
   
    ![Logon Único do AD do Azure][11]
3. Para adicionar mapeamentos de atributo personalizados para a sua configuração de atributos de token SAML, na parte superior do menu, clique em **Atributos** para abrir o diálogo **Atributos de Token SAML**. 
   
    ![Configurar o logon único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_80.png) 
4. Para cada linha de dados na tabela abaixo, realize as seguintes etapas:
   
   | Nome do atributo | Valor do atributo |
   | --- | --- |
   | nome |givenname |
   | sobrenome |sobrenome |
   | idpid |*\<o valor da **ID da Entidade** do diálogo **Configurar logon único no Moxtra** no Portal Clássico do Azure\>* |

    a. Clique em Adicionar atributo de usuário 

    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_81.png) 

    b. Na caixa de diálogo **Adicionar Atributo de Usuário** , digite o nome do atributo e o valor do atributo mostrado para aquela linha na tabela. 

    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_82.png) 

    c. Clique em **Concluído**.



1. Clique em **Aplicar alterações**. 
   
    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_general_84.png) 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.  

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_09.png)  
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_03.png) 
4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**. 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_04.png) 
5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas: 
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_05.png)  
   
    a. Em Tipo de Usuário, selecione Novo usuário na organização.
   
    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.
   
    c. Clique em **Próximo**.
6. Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas: 
   
   ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_06.png) 
   
   a. Na caixa de texto **Nome**, digite **Brenda**.  
   
   b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
   c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.
   
   d. Na lista **Função**, selecione **Usuário**.
   
   e. Clique em **Próximo**.
7. Na página de diálogo **Obter senha temporária**, clique em **criar**.
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_07.png) 
8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:
   
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-moxtra-tutorial/create_aaduser_08.png) 
   
    a. Anote o valor da **Nova Senha**.
   
    b. Clique em **Concluído**.   

### <a name="creating-a-moxtra-test-user"></a>Criar um usuário de teste Moxtra
O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Moxtra.

**Para criar um usuário chamado Brenda Fernandes no Moxtra, realize as seguintes etapas:**

1. Faça logon em seu site de empresa do Moxtra como administrador.
2. Na barra de ferramentas à esquerda, clique em **Console do Administrador > Gerenciamento de Usuário** e em **Adicionar Usuário**.
   
    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_10.png) 
3. No diálogo **Adicionar Usuário** , realize as seguintes etapas:
   
    a. Na caixa de texto **Nome**, digite **Brenda**.
   
    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.
   
    c. Na caixa de texto **Email** , digite o endereço de email de Brenda Fernandes no Portal clássico do Azure.
   
    d. Na caixa de texto **Divisão**, digite **Dev**.
   
    e. Na caixa de texto **Departamento**, digite **TI**.
   
    f. Selecione **Administrador**.
   
    g. Clique em **Adicionar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure
O objetivo desta seção é habilitar Brenda Fernandes a usar o logon único do Azure, concedendo a ela acesso ao Moxtra.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Moxtra, realize as seguintes etapas:**

1. No portal clássico do Azure, para abrir a exibição de aplicativos, na exibição de diretório, clique em **Aplicativos** no menu superior.
   
    ![Atribuir usuário][201] 
2. Na lista de aplicativos, selecione **Moxtra**.
   
    ![Configurar Logon Único](./media/active-directory-saas-moxtra-tutorial/tutorial_moxtra_50.png) 
3. No menu na parte superior, clique em **Usuários**.
   
    ![Atribuir usuário][203] 
4. Na lista de usuários, selecione **Brenda Fernandes**.
5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
   
    ![Atribuir usuário][205]

### <a name="testing-single-sign-on"></a>Teste do logon único
O objetivo desta seção é testar sua configuração de logon único do Azure AD usando o Painel de Acesso.  
Ao clicar no bloco do Moxtra no Painel de Acesso, você deverá ser conectado automaticamente ao seu aplicativo do Moxtra.

## <a name="additional-resources"></a>Recursos adicionais
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moxtra-tutorial/tutorial_general_205.png









<!--HONumber=Nov16_HO3-->


