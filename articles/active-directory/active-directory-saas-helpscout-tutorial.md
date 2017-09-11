---
title: "Tutorial: Integração do Azure Active Directory com o Help Scout | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Help Scout."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 84cee39c28a0f7e6b9878441e504131795673020
ms.contentlocale: pt-br
ms.lasthandoff: 08/28/2017

---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Tutorial: integração do Azure Active Directory com o Help Scout

Neste tutorial, você aprenderá a integrar o Help Scout ao Azure AD (Azure Active Directory).

Você obtém os seguintes benefícios ao integrar o Help Scout ao Azure AD:

- No Azure AD, é possível controlar quem tem acesso ao Help Scout.
- Você pode conectar automaticamente seus usuários ao Help Scout usando o logon único e a conta do Azure AD de um usuário.
- É possível gerenciar suas contas em uma, um local central e no portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Help Scout, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Help Scout, com o logon único ativado 

> [!NOTE]
> Em caso de testar as etapas deste tutorial, recomendamos não testá-las em um ambiente de produção.

Recomendações para testar as etapas deste tutorial:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Help Scout da galeria.
2. Configurar e testar o logon único do Azure AD.

## <a name="add-help-scout-from-the-gallery"></a>Adicionar o Help Scout da galeria
Para configurar a integração do Help Scout ao Azure AD, na galeria, adicione o Help Scout à sua lista de aplicativos SaaS gerenciados.

Para adicionar o Help Scout da galeria:

1. No [portal do Azure](https://portal.azure.com), no menu esquerdo, selecione **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.

    ![A página de aplicativos empresariais][2]
    
3. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, insira **Help Scout**. Nos resultados da pesquisa, selecione **Help Scout** e **Adicionar**.

    ![Help Scout na lista de resultados](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o Help Scout, com base em uma usuária de teste chamada *Brenda Fernandes*.

Para que o logon único funcione, o Azure AD precisa conhecer o usuário equivalente do Azure AD no Help Scout. É necessário estabelecer uma relação de vinculação entre um usuário do Azure AD e o usuário relacionado no Help Scout.

Para estabelecer a relação de vinculação, no Help Scout, para **Nome de usuário**, atribua o valor de **nome de usuário** no Azure AD.

Para configurar e testar o logon único do Azure AD com o Help Scout, conclua as seguintes tarefas:

1. [Configurar o logon único do Azure AD](#set-up-azure-ad-single-sign-on). Configura um usuário para usar esse recurso.
2. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user). Testa o logon único do Azure AD com a usuária Brenda Fernandes.
3. [Criar um usuário de teste do Help Scout](#create-a-help-scout-test-user). Cria um equivalente de Brenda Fernandes no Help Scout que é vinculado à representação do usuário no Azure AD.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user). Configura Brenda Fernandes para usar o logon único do Azure AD.
5. [Testar o logon único](#test-single-sign-on). Verifica se a configuração funciona.

### <a name="set-up-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você configura o logon único do Azure AD no portal do Azure. Em seguida, define o logon único no aplicativo Help Scout.

Para configurar o logon único do Azure AD com o Help Scout:

1. No portal do Azure, na página de integração do aplicativo **Help Scout**, selecione **Logon único**.
 
    ![Link Configurar logon único][4]

2. Na página **Logon único**, para **Modo**, selecione **Logon com base em SAML**.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. Em **Domínio e URLs do Help Scout**, se quiser configurar o aplicativo no modo iniciado por IDP, concluas as seguintes etapas:

    1. Na caixa **Identificador**, digite uma URL com o seguinte padrão: `urn:auth0:helpscout:<instancename>`

    2. Na caixa **URL de Resposta**, digite uma URL com o seguinte padrão: `https://helpscout.auth0.com/login/callback?connection=<instancename>`

    ![Informações de logon único de Domínio e URLs do Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

4. Se você quiser configurar o aplicativo no modo iniciado pelo SP, marque a caixa de seleção **Mostrar configurações avançadas de URL** e siga este procedimento:

    * Na caixa **URL de Entrada**, digite uma URL com o seguinte formato: `https://secure.helpscout.net/members/login/`

    ![Informações de logon único de Domínio e URLs do Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)
 
    > [!NOTE] 
    > Os valores nessas URLs são apenas para demonstração. Atualize os valores com as verdadeiras URL de identificador e URL de resposta. Para obter esses valores, entre em contato com a [equipe de suporte do Help Scout](mailto:help@helpscout.com). 

5. Em **Certificado de Autenticação SAML**, selecione **XML de Metadados** e salve o arquivo de metadados no computador.

    ![O link de download do Certificado](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Selecione **Salvar**.

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)
    
7. Para configurar o logon único no lado do Help Scout, envie o arquivo XML de metadados baixado para a [equipe de suporte do Help Scout](mailto:help@helpscout.com). A equipe de suporte do Help Scout aplica essa configuração para que a conexão de logon único do SAML seja definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo! Depois de adicionar o aplicativo selecionando **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único**. Você pode acessar a documentação inserida na seção **Configuração**, na parte inferior da página. Para saber mais, confira a [documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, no portal do Azure, você criará uma usuária de teste chamada Brenda Fernandes.

![Criar um usuário de teste do Azure AD][100]

Para criar um usuário de teste no Azure AD:

1. No portal do Azure, no menu esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, selecione **Usuários e grupos** e, em seguida, selecione **Todos os usuários**.

    ![Selecionar Usuários e grupos e depois Todos os usuários](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, na parte superior da página **Todos os Usuários**, selecione **Adicionar**.

    ![O botão Adicionar](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, conclua as seguintes etapas:

    1. Na caixa **Nome**, insira **BrendaFernandes**.

    2. Na caixa **Nome de usuário**, insira o endereço de email da usuária Brenda Fernandes.

    3. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    4. Selecione **Criar**.

        ![A caixa de diálogo Usuário](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

 
### <a name="create-a-help-scout-test-user"></a>Criar um usuário de teste do Help Scout

O objeto desta seção é criar um usuário chamado Brenda Fernandes no Help Scout. O Help Scout dá suporte ao provisionamento JIT (Just-In-Time), que é ativado por padrão.

Nesta seção, não há ação nem tarefa a ser executada. Se um usuário ainda não existir no Help Scout, um novo será criado quando você tentar acessar o Help Scout.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que a usuária Brenda Fernandes use o logon único do Azure AD concedendo acesso de conta de usuário ao Help Scout.

![Atribuir a função de usuário][200] 

Para atribuir Brenda Fernandes ao Help Scout:

1. No portal do Azure, abra a exibição de aplicativos e vá para a exibição de diretório. Selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Help Scout**.

    ![O link do Help Scout na lista de Aplicativos](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. No menu esquerdo, selecione **Usuários e grupos**.

    ![O link Usuários e grupos][202]

4. Selecione **Adicionar**. Na página **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

5. Na página **Usuários e grupos**, na lista de usuários, selecione **Brenda Fernandes**.

6. Na página **Usuários e grupos**, selecione **Selecionar**.

7. Na página **Adicionar Atribuição**, selecione **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao selecionar o bloco Help Scout no painel de acesso, você deverá ser conectado automaticamente ao aplicativo Help Scout.

Para saber mais sobre o painel de acesso, veja [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png


