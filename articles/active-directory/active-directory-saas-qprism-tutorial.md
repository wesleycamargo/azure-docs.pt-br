---
title: "Tutorial: Integração do Azure Active Directory com o QPrism | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o QPrism."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: jeedes
ms.openlocfilehash: 1f697b95074e0fc9dbb3e8c7800e69f8ece9e0b3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Tutorial: Integração do Azure Active Directory ao QPrism

Neste tutorial, você aprende a integrar o QPrism ao Azure AD (Azure Active Directory).

A integração de QPrism ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem terá acesso ao QPrism.
- Você pode permitir que usuários façam logon automaticamente no QPrism (logon único) com as respectivas contas do Azure AD.
- Gerencie suas contas em um único local: o portal do Azure.

Para obter mais detalhes sobre a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o QPrism, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do QPrism

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do QPrism da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="add-qprism-from-the-gallery"></a>Adicionar o QPrism da galeria
Para configurar a integração do QPrism com o Azure AD, você precisará adicionar o QPrism à sua lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o QPrism da galeria:**

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **Aplicativos empresariais** > **Todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Para adicionar um novo aplicativo, na parte superior da caixa de diálogo **Novo aplicativo**.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **QPrism** e selecione **QPrism** no painel de resultados. Em seguida, clique em **Adicionar** para adicionar o aplicativo.

    ![QPrism na lista de resultados](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o QPrism com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do QPrism é equivalente a um usuário do Azure AD. Em outras palavras, deve haver uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no QPrism.

Para estabelecer esse relacionamento, no QPrism, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário**.

Para configurar e testar o logon único do Azure AD com o QPrism, você precisará concluir os seguintes blocos de construção:

1. [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
3. [Criar um usuário de teste do QPrism](#create-a-qprism-test-user) para ter um equivalente de Brenda Fernandes no QPrism vinculado à representação do usuário no Azure AD.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Testar o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal do Azure e configura o logon único no aplicativo QPrism.

1. No Portal do Azure, na página de integração de aplicativos do **QPrism**, selecione **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_samlbase.png)

3. Na seção **Domínio e URLs do QPrism**, faça o seguinte:

    ![Informações de logon único de Domínio e URLs do QPrism](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_url.png)

    a. Na caixa de texto **URL de Entrada**, digite uma URL que usa o seguinte padrão: `https://<customer domain>.qmyzone.com/login`

    b. Na caixa de texto **Identificador**, digite uma URL que usa o seguinte padrão: `https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de logon e o identificador reais. Contate a [equipe de suporte ao Cliente do QPrism](mailto:qsupport-ce@quatrro.com) para obter esses valores. 

4. Para gerar a URL de **Metadados**, faça o seguinte:

    a. Selecione **Registros do Aplicativo**.
    
    ![Configure registros do aplicativo de logon único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appregistrations.png)
   
    b. Selecione **Pontos de Extremidade** para abrir a caixa de diálogo **Pontos de Extremidade**.  
    
    ![Configurar ponto de extremidade de logon único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpointicon.png)

    c. Clique no botão copiar para copiar a URL **DOCUMENTO DE METADADOS DE FEDERAÇÃO** e cole-la no Bloco de Notas.
    
    ![Configurar ponto de extremidade de logon único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpoint.png)
     
    d. Agora, vá para a página de propriedades do **QPrism** e copie a **ID do Aplicativo** usando **Copiar**. Em seguida, cole no Bloco de Notas.
 
    ![Configurar a ID do aplicativo de logon único](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appid.png)

    e. Gere a **URL de Metadados** usando o padrão a seguir: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>` 

5. Selecione **Salvar**.

    ![Configurar botão Salvar no logon único](./media/active-directory-saas-qprism-tutorial/tutorial_general_400.png)
    
6. Para configurar o logon único no lado do **QPrism**, envie a **URL de Metadados** à [Equipe de suporte do QPrism](mailto:qsupport-ce@quatrro.com). Ela garante que a conexão de logon único do SAML seja definida corretamente em ambos os lados.

> [!TIP]
> Agora, é possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo. Depois de adicionar este aplicativo da seção **Active Directory** > **Aplicativos Empresariais**, simplesmente selecione a guia **Logon Único** e acesse a documentação inserida na seção **Configuração** na parte inferior. Você pode ler mais sobre o recurso de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no Azure AD:**

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-qprism-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-qprism-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, na parte superior da caixa de diálogo **Todos os Usuários**, selecione **Adicionar**.

    ![O botão Adicionar](./media/active-directory-saas-qprism-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, faça o seguinte:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-qprism-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.
 
### <a name="create-a-qprism-test-user"></a>Criar um usuário de teste do QPrism

Nesta seção, você criará uma usuária chamada Brenda Fernandes no QPrism. Trabalhe com a [equipe de suporte do QPrism](mailto:qsupport-ce@quatrro.com) para adicionar os usuários na plataforma do QPrism. Os usuários devem ser criados e ativados antes de usar o logon único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao QPrism.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao QPrism:**

1. No portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório. Vá para **Aplicativos empresariais** e selecione **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **QPrism**.

    ![O link do QPrism na lista de aplicativos](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_app.png)  

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Selecione **Adicionar**. Em seguida, em **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de **Usuários**.

6. Na caixa de diálogo **Usuários e grupos**, escolha **Selecionar**.

7. Em **Adicionar Atribuição**, selecione **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

No Painel de Acesso, ao selecionar o bloco QPrism, você deverá ser conectado automaticamente ao seu aplicativo QPrism.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_203.png

