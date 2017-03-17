---
title: "Tutorial: Integração do Azure Active Directory ao Boomi | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Boomi."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: e5d37e9e723d97d1e6844f0b89ad1c2aae258565
ms.openlocfilehash: 9ab3ac2cd6643b0e53f1f7853239c90e78f4bf77
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Tutorial: Integração do Active Directory do Azure ao Boomi

Neste tutorial, você aprenderá a integrar o Boomi ao Azure AD (Azure Active Directory).

A integração do Boomi ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao Boomi
- Você pode permitir que seus usuários faça logon automaticamente no Boomi (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Boomi, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Boomi habilitada para logon único


> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.


Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode obter uma avaliação de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Boomi da galeria
2. Configurar e testar o logon único do AD do Azure


## <a name="adding-boomi-from-the-gallery"></a>Adicionar Boomi da galeria
Para configurar a integração do Boomi ao Azure AD, você precisa adicionar o Boomi por meio da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Boomi da galeria, execute as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Boomi**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_000.png)

5. No painel de resultados, selecione **Boomi** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_0001.png)


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

Nesta seção, você habilita o logon único do Azure AD no Portal de Gerenciamento do Azure e configura o logon único em seu aplicativo Boomi.

**Para configurar o logon único do Azure AD com o Boomi, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração do aplicativo **Boomi**, clique em **Logon único**.

    ![Configurar Logon Único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_01.png)

3. Na seção **Domínio e URLs do Boomi**, na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://platform.boomi.com/sso/<account name>/saml`

    ![Configurar Logon Único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_02.png)

    > [!NOTE] 
    > Observe que esse não é o valor real. Você precisa atualizar esse valor com a URL de Resposta real. Para obter esse valor, entre em contato com a equipe de suporte do Boomi. 

4. O aplicativo Boomi espera que as declarações SAML estejam em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar Logon Único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_03.png)

5. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, para cada linha mostrada na tabela a seguir, execute as seguintes etapas:
    
    | Nome do atributo | Valor do atributo |
    | --- | --- |    
    | FEDERATION_ID | user.mail |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_04.png)

    ![Configurar Logon Único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_05.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**

6. Sobre o **certificado de autenticação SAML** seção, clique em **criar novo certificado**.

    ![Configurar o logon único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_06.png)     

7. Na caixa de diálogo **Criar um Novo Certificado**, clique no ícone de calendário e selecione uma **data de expiração**. Em seguida, clique no botão **Salvar**.

    ![Configurar Logon Único](./media/active-directory-saas-boomi-tutorial/tutorial_general_300.png)

8. Na seção **Certificado de Autenticação SAML**, selecione **Ativar o novo certificado** e clique no botão **Salvar**.

    ![Configurar o logon único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_07.png)

9. Na janela pop-up **Certificado de substituição**, clique em **OK**.

    ![Configurar o logon único](./media/active-directory-saas-boomi-tutorial/tutorial_general_400.png)

10. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_08.png) 

11. Na seção **Configuração do Boomi**, clique em **Configurar o Boomi** para abrir a janela **Configurar logon**.

    ![Configurar Logon Único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_09.png) 

    ![Configurar Logon Único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_10.png)

12. Em outra janela do navegador da Web, faça logon em seu site de empresa Boomi como um administrador. 

13. Navegue até **Nome da Empresa** e vá para **Configurar**.

14. Clique na guia **Opções de SSO** e execute etapas a seguir.

    ![Configurar o logon único no lado do aplicativo](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_11.png)

    a. Marque a caixa de seleção **Habilitar Logon Único do SAML**.

    b. Clique em **Importar** para carregar o certificado baixado do Azure AD no **Certificado do Provedor de Identidade**.
    
    c. Na caixa de texto **URL de Logon do Provedor de Identidade**, insira o valor da **URL de Serviço de Logon Único SAML** da janela de configuração de aplicativo do Azure AD.

    d. Para **Local do ID de Federação**, selecione o botão de opção **A Id de Federação está contida no elemento Atributo FEDERATION_ID**. 

    e. Clique no botão **Salvar** .
  

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal de Gerenciamento do Azure chamado Britta Simon.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo, clique em **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Britta Simon**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** de Britta Simon.

    c. Selecione **Mostrar Senha** e anote o valor de **Senha**.

    d. Clique em **Criar**. 



### <a name="creating-a-boomi-test-user"></a>Criar um usuário de teste do Boomi

Para permitir que os usuários do AD do Azure façam logon no Boomi, eles deverão ser provisionados no Boomi. No caso do Boomi, o provisionamento é uma tarefa manual.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Para provisionar uma conta de usuário, execute as seguintes etapas:

1. Faça logon em seu site de empresa Boomi como um administrador.

2. Depois de fazer logon, navegue até **Gerenciamento de Usuário** e vá até **Usuários**.

    ![Usuários](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "Usuários")

3. Clique no ícone **+** e o diálogo **Adicionar/Manter Funções de Usuário** será aberto.

    ![Usuários](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "Usuários")

    ![Usuários](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "Usuários")

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

1. No Portal de Gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **Aplicativos empresariais**, depois clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Boomi**.

    ![Configurar Logon Único](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_50.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Atribuir usuário][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Britta Simon** na lista de usuários.

6. Clique no botão **Selecionar** na caixa de diálogo **Usuários e grupos**.

7. Clique no botão **Atribuir** na caixa de diálogo **Adicionar Atribuição**.
    


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

[100]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png
