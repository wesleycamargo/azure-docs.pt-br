---
title: "Tutorial: Integração do Azure Active Directory ao Boomi | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Boomi."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a60db808388bf1fdf9441518920f2eb4e5fcc4d
ms.openlocfilehash: bafbfb710d8cdb370d21d9299c447a0dfed3c468


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Tutorial: Integração do Active Directory do Azure ao Boomi

Neste tutorial, você aprenderá a integrar o Boomi ao Azure AD (Azure Active Directory).

A integração do Boomi ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Boomi
- Você pode permitir que seus usuários faça logon automaticamente no Boomi (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local: o Portal clássico do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Boomi, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Boomi habilitada para logon único


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Boomi da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-boomi-from-the-gallery"></a>Adicionar Boomi da galeria
Para configurar a integração do Boomi ao Azure AD, você precisa adicionar o Boomi por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Boomi da galeria, execute as seguintes etapas:**

1. No **Portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**. 

    ![Active Directory][1]

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicativos][3]

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Aplicativos][4]

6. Na caixa de pesquisa, digite **Boomi**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_01.png)

7. No painel de resultados, selecione **Boomi** e clique em **Concluir** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Boomi, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Boomi é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Boomi.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** no Azure AD como o valor de **Nome de usuário** no Boomi.

Para configurar e testar o logon único do Azure AD com o Boomi, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** - para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criar um usuário de teste do Boomi](#creating-a-boomi-test-user)** - para ter um equivalente de Brenda Fernandes no Boomi que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

O objetivo desta seção é habilitar o logon único do Azure AD no Portal clássico do Azure e configurar o logon único em seu aplicativo Boomi.

O aplicativo Boomi espera a asserção do SAML em um formato específico, o que exige a definição do valor do atributo NameIdentifier com a ID de Federação do usuário. Por padrão o Azure AD usa o UserPrincipalName para o atributo NameIdentifier. Mas para uma integração bem-sucedida, você precisa ajustar esse valor a fim de coincidir com a ID de Federação do usuário no Boomi. Altere isso na guia "**Atributo**", conforme mostra a captura de tela abaixo. A integração funcionará apenas depois de concluir o mapeamento correto.

![Configurar o logon único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

**Para configurar o logon único do Azure AD com o Boomi, execute as seguintes etapas:**

1. No portal clássico, na página de integração do aplicativo **Boomi**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar Logon Único][6] 

2. Na página **Como você deseja que os usuários façam logon no Boomi**, selecione **Logon Único do Azure AD** e clique em **Avançar**.
 
    ![Configurar o logon único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_03.png)

3. Na página de diálogo **Definir Configurações de Aplicativo** , execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_04.png)

    a. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://platform.boomi.com/sso/<account name>/saml`

    b. Clique em **Próximo**.

    > [!NOTE] 
    > Observe que esse não é o valor real. Você precisa atualizar esse valor com a URL de Resposta real. Para obter esse valor, entre em contato com a equipe de suporte do Boomi.

4. Na página **Configurar logon único no Boomi**, clique em **Baixar certificado** e salve o arquivo no computador:

    ![Configurar o logon único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_05.png) 

    > [!NOTE]
    > Observe que o valor da declaração NameID na Resposta deve corresponder à ID de Federação configurada no sistema Boomi. Portanto, trabalhe com a equipe de suporte do Boomi para mapear o identificador de usuário apropriado em sua organização como a ID de Federação. Por padrão, o Azure AD definirá o NameIdentifier como o valor de UPN. Você pode alterar isso na guia Atributo, conforme mostrado na captura de tela abaixo. A integração funcionará apenas depois de concluir o mapeamento correto. 
    
    ![Configurar Logon Único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

5. Em outra janela do navegador da Web, faça logon em seu site de empresa Boomi como um administrador. 

6. Navegue até **Nome da Empresa** e vá para **Configurar**.

7. Clique na guia **Opções de SSO** e execute etapas a seguir.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_06.png)

    a. Marque a caixa de seleção **Habilitar Logon Único do SAML**.

    b. Clique em **Importar** para carregar o certificado baixado do Azure AD no **Certificado do Provedor de Identidade**.
    
    c. Na caixa de texto **URL de Logon do Provedor de Identidade**, insira o valor de **URL de Logon Remoto** do assistente de configuração de aplicativo do Azure AD.

    d. Para **Local do ID de Federação**, selecione o botão de opção **A ID de Federação está contida no elemento NameID da Entidade**. 

    e. Clique no botão **Salvar** .

8. No portal clássico, selecione a confirmação da configuração de logon único e clique em **Avançar**.

    ![Logon Único do AD do Azure][10]

9. Na página **Confirmação de logon único**, clique em **Concluir**.  
  
    ![Logon Único do AD do Azure][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal Clássico do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][20]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal clássico do Azure**, no painel de navegação à esquerda, clique em **Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/create_aaduser_09.png) 

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para exibir a lista de usuários, no menu na parte superior, clique em **Usuários**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar Usuário**, na barra de ferramentas na parte inferior, clique em **Adicionar Usuário**.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

5. Na página do diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/create_aaduser_05.png) 

    a. Em Tipo de Usuário, selecione Novo usuário na organização.

    b. Na **caixa de texto** Nome do Usuário, digite **BrendaFernandes**.

    c. Clique em **Próximo**.

6.  Na página do diálogo **Perfil do Usuário** , realize as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/create_aaduser_06.png) 

    a. Na caixa de texto **Nome**, digite **Brenda**.  

    b. Na caixa de texto **Sobrenome**, digite **Fernandes**.

    c. Na caixa de texto **Nome de Exibição**, digite **Brenda Fernandes**.

    d. Na lista **Função**, selecione **Usuário**.

    e. Clique em **Próximo**.

7. Na página de diálogo **Obter senha temporária**, clique em **criar**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/create_aaduser_07.png) 

8. Na página de caixa de diálogo **Obter senha temporária** , execute as seguintes etapas:

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/create_aaduser_08.png) 

    a. Anote o valor da **Nova Senha**.

    b. Clique em **Concluído**.   



### <a name="creating-a-boomi-test-user"></a>Criar um usuário de teste do Boomi

Para permitir que os usuários do AD do Azure façam logon no Boomi, eles deverão ser provisionados no Boomi. No caso do Boomi, o provisionamento é uma tarefa manual.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Faça logon em seu site de empresa Boomi como um administrador.

2. Depois de fazer logon, navegue até **Gerenciamento de Usuário** e vá até **Usuários**.

    ![Usuários](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "Users")

3. Clique no ícone **+** e o diálogo **Adicionar/Manter Funções de Usuário** será aberto.

    ![Usuários](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "Users")

    ![Usuários](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "Users")

4. Insira o **Endereço de email de usuário** do usuário.

5. Insira o **Nome** e **Sobrenome** do usuário.

6. Insira a **ID de Federação** do usuário. Cada usuário deve ter uma ID de Federação que o identifique exclusivamente na conta. 

7. Atribua a função **Usuário Padrão** ao usuário. Não atribua a função Administrador, pois isso daria acesso normal ao Atmosphere, bem como acesso de logon único.

8. Clique em **OK**.

    > [!NOTE]
    > O usuário não receberá um email de notificação de boas-vindas contendo uma senha que pode ser usada para fazer logon na conta do AtomSphere, pois sua senha será gerenciada por meio do provedor de identidade. É possível usar qualquer outra ferramenta de criação da conta de usuário do Boomi ou as APIs fornecidas pelo Boomi para provisionar as contas de usuário do AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Boomi.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Boomi, execute as seguintes etapas:**

1. No portal clássico, para abrir o modo de exibição de aplicativos, no modo de exibição de diretório, clique em **Aplicativos** no menu superior.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Boomi**.

    ![Configurar o logon único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_50.png) 

3. No menu na parte superior, clique em **Usuários**.

    ![Atribuir usuário][203] 

4. Na lista de usuários, selecione **Brenda Fernandes**.

5. Na barra de ferramentas na parte inferior, clique em **Atribuir**.
    
    ![Atribuir usuário][205]



### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Boomi no Painel de Acesso, você deverá ser conectado automaticamente a seu aplicativo Boomi.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


