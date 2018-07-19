---
title: 'Tutorial: Integração do Azure Active Directory com o Shmoop for Schools | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Shmoop for Schools.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: b5826fd3067ac337808b9e27040dee808cd6a01c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045950"
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Tutorial: Integração do Azure Active Directory com o Shmoop for Schools

Neste tutorial, você aprenderá a integrar o Shmoop for Schools ao Azure Active Directory (Azure AD).

A integração do Shmoop for Schools ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Shmoop for Schools.
- Você pode permitir que os usuários se conectem automaticamente no Shmoop For Schools com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local: o novo Portal do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS ao Azure AD, consulte [O que é o acesso de aplicativos e o logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Shmoop for Schools, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Shmoop for Schools

> [!NOTE]
> Para testar as etapas deste tutorial, não recomendamos o uso de um ambiente de produção.

Para testar as etapas neste tutorial, é recomendável:

- Usar seu ambiente de produção apenas se for necessário.
- Se ainda não tiver um ambiente de avaliação do Azure AD, você poderá [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Shmoop for Schools a partir da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="add-shmoop-for-schools-from-the-gallery"></a>Adicionar Shmoop for Schools da galeria
Para configurar a integração do Shmoop for Schools ao Azure AD, você precisará adicionar o Shmoop for Schools a partir da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Shmoop for Schools da galeria, execute as seguintes etapas:**

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone do **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Executar **Aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Shmoop For Schools**. Nos resultados, selecione **Shmoop For Schools** e selecione o botão **Adicionar** para adicionar o aplicativo.

    ![Shmoop for Schools na lista de resultados](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Shmoop for Schools, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Shmoop for Schools é equivalente a um usuário do Azure AD. Em outras palavras, você precisa estabelecer um vínculo entre um usuário do Azure AD e o usuário relacionado no Shmoop For Schools.

Para configurar e testar o logon único do Azure AD com o Shmoop for Schools, conclua os seguintes blocos de construção:

1. [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
3. [Criar um usuário de teste do Shmoop for Schools](#create-a-shmoop-for-schools-test-user) para ter um equivalente de Brenda Fernandes no Shmoop for Schools vinculado à representação do usuário no Azure AD.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Teste o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo Shmoop for Schools.

**Para configurar o logon único do Azure AD com o Shmoop for Schools, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Shmoop for Schools**, selecione **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, no menu suspenso abaixo de **Modo de logon único**, selecione **Logon baseado em SAML**.
 
    ![Caixa de diálogo Logon único](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. Na seção **URLs e Domínio do Shmoop for Schools**, execute as seguintes etapas:

    ![Configurar o logon único](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. Na caixa **URL de Entrada**, digite uma URL usando o seguinte padrão: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do Shmoop For Schools](mailto:support@shmoop.com) para obter esses valores. 
 
4. O aplicativo Shmoop For Schools espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração do aplicativo. A captura de tela a seguir mostra como configurar as declarações:

    ![Configurar o logon único](./media/shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > O Shmoop for Schools dá suporte a duas funções para os usuários: **Professor** e **Aluno**. Configure essas funções no Azure AD para que os usuários possam ser atribuídos às funções apropriadas. Para entender como configurar funções no Azure AD, confira [Gerenciar o acesso usando RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).
    
5. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostrado na imagem anterior.  Em seguida, execute as etapas a seguir:

    | Nome do atributo | Valor do atributo |
    | -------------- | --------------- |
    | função           | user.assignedroles |

    a. Para abrir a caixa de diálogo **Adicionar Atributo**, selecione **Adicionar Atributo**.
    
    ![Configurar o logon único ](./media/shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Configurar o logon único](./media/shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. Na caixa **Nome**, digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, selecione o valor de atributo mostrado para essa linha.

    d. Deixe a caixa **Namespace** em branco.
    
    e. Selecione **Ok**.

6. Selecione o botão **Salvar**.

    ![Configurar o logon único](./media/shmoopforschools-tutorial/tutorial_general_400.png)

7. Na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar a **URL de metadados de federação do aplicativo** e cole-a no bloco de notas.

    ![O link de download do Certificado](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_certificate.png)

8. Para configurar o logon único no lado do **Shmoop For Schools**, é necessário enviar a **URL de Metadados da Federação de Aplicativo** para a [equipe de suporte do Shmoop For Schools](mailto:support@shmoop.com).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No Portal do Azure, no painel esquerdo, selecione o botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/shmoopforschools-tutorial/create_aaduser_01.png)

2. Para exibir uma lista de usuários, vá para **Usuários e grupos**. Selecione **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/shmoopforschools-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar** na parte superior da caixa de diálogo **Todos os usuários**.

    ![O botão Adicionar](./media/shmoopforschools-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/shmoopforschools-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Criar um usuário de teste de Shmoop for Schools

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Shmoop for Schools. O Shmoop For Schools dá suporte ao provisionamento Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um novo usuário ainda não existir, ele será criado durante a tentativa de acessar o Shmoop For Schools.

>[!NOTE]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Shmoop For Schools](mailto:support@shmoop.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure ao conceder acesso ao Shmoop for Schools.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Shmoop For Schools, execute as seguintes etapas:**

1. No Portal do Azure, abra o modo de exibição de aplicativos. Em seguida, vá para **Aplicativos empresariais** na exibição de diretório.  Em seguida, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Shmoop for Schools**.

    ![O link Shmoop For Schools na lista de aplicativos](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Selecione o botão **Adicionar**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários.

6. Na caixa de diálogo **Usuários e grupos**, clique no botão **Selecionar**. 

7. Na caixa de diálogo **Adicionar Atribuição**, selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você seleciona o bloco **Shmoop For Schools** no Painel de Acesso, você deve ser conectado automaticamente ao seu aplicativo Shmoop For Schools.

Para saber mais sobre o painel de acesso, veja [Introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Azure Active Directory](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/shmoopforschools-tutorial/tutorial_general_203.png

