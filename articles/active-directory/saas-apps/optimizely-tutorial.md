---
title: 'Tutorial: Integração do Azure Active Directory com o Optimizely | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Optimizely.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 534a373024556db86d0553b68ce3f847ff085486
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210706"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Tutorial: Integração do Azure Active Directory com o Optimizely

Neste tutorial, você aprenderá a integrar o Optimizely ao Azure AD (Azure Active Directory).

A integração do Optimizely ao Azure AD proporciona os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Optimizely
- Você pode permitir que os usuários façam logon automaticamente no Optimizely (Logon Único) com as contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Optimizely, você precisará dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do Optimizely

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do AD do Azure, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
 O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Optimizely da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-optimizely-from-the-gallery"></a>Adicionando o Optimizely da galeria

Para configurar a integração do Optimizely ao Azure AD, você precisa adicionar o Optimizely da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Optimizely da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![APLICATIVOS][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![APLICATIVOS][3]

4. Na caixa de pesquisa, digite **Optimizely**.

    ![Criação de um usuário de teste do AD do Azure](./media/optimizely-tutorial/tutorial_optimizely_search.png)

5. No painel de resultados, selecione **Optimizely** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do AD do Azure

Nesta seção, você vai configurar e testar o logon único do Azure AD com o Optimizely, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Optimizely é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Optimizely.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no Optimizely.

Para configurar e testar o logon único do Azure AD com o Optimizely, você precisa concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** – para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Optimizely](#creating-an-optimizely-test-user)** – para ter um equivalente de Brenda Fernandes no Optimizely que esteja vinculado à representação do usuário no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Teste do logon único](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Optimizely.

**Para configurar o logon único do Azure AD com o Optimizely, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **Optimizely**, clique em **Logon único**.

    ![Configurar o logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Configurar o logon único](./media/optimizely-tutorial/tutorial_optimizely_samlbase.png)

3. Na seção **Domínio e URLs do Optimizely**, execute as seguintes etapas:

    ![Configurar o logon único](./media/optimizely-tutorial/tutorial_optimizely_url.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://app.optimizely.net/<instance name>`

    b. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão: `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Esses não são os valores reais. Você atualizará o valor com a URL de logon real e o identificador, o que é explicado no tutorial posteriormente.

4. Aplicativo otimizado espera as declarações SAML em um formato específico. Configure as seguintes declarações para o aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar o logon único](./media/optimizely-tutorial/tutorial_optimizely_attribute.png)
    
5. Clique na caixa de seleção **Exibir e editar todos os outros atributos de usuário** na seção **Atributos de Usuário** para expandir os atributos. Realize as seguintes etapas em cada um dos atributos exibidos:

    | Nome do atributo | Valor do atributo |
    | ---------------| --------------- |
    | email | user.mail |

     a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/optimizely-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/optimizely-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome**, digite o **nome do atributo** mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Clique em **OK**.

6. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![Configurar o logon único](./media/optimizely-tutorial/tutorial_optimizely_certificate.png)

7. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/optimizely-tutorial/tutorial_general_400.png)

8. Na seção **Configuração do Optimizely**, clique em **Configurar Optimizely** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configurar o logon único](./media/optimizely-tutorial/tutorial_optimizely_configure.png)

9. Para configurar o logon único no lado do **Optimizely**, entre em contato com seu gerente de conta do Optimizely e forneça o **Certificado (Base64)** baixado e a **URL do Serviço de Logon Único do SAML**.

10. Em resposta ao seu email, o Optimizely fornece a URL de Entrada (SSO iniciado pelo SP) e os valores do Identificador (ID de Entidade de Provedor de Serviços).

     a. Copie a **URL de SSO iniciado pelo SP** fornecida pelo Optimizely e cole na caixa de texto **URL de Logon** na seção **URLs e Domínio do Optimizely** no portal do Azure.

    b. Copie a **ID da Entidade do Provedor de Serviços** fornecida pelo Optimizely e cole na caixa de texto **Identificador** na seção **URLs e Domínio do Optimizely** no portal do Azure.

11. Em uma janela diferente do navegador, faça logon no aplicativo Optimizely.

12. Clique no nome da sua conta no canto superior direito e em **Configurações da Conta**.

    ![Logon Único do AD do Azure](./media/optimizely-tutorial/tutorial_optimizely_09.png)

13. Na guia Conta, marque a caixa **Habilitar SSO** em Logon Único na seção **Visão geral**.
  
    ![Logon Único do AD do Azure](./media/optimizely-tutorial/tutorial_optimizely_10.png)

14. Clique em **Salvar**

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **Portal do Azure**, no painel de navegação esquerdo, clique no ícone **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/optimizely-tutorial/create_aaduser_01.png) 

2. Vá para **Usuários e grupos** e clique em **Todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/optimizely-tutorial/create_aaduser_02.png) 

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/optimizely-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/optimizely-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.

### <a name="creating-an-optimizely-test-user"></a>Criando um usuário de teste do Optimizely

Nesta seção, você cria um usuário chamado Brenda Fernandes no Optimizely.

1. Na página inicial, escolha a guia **Colaboradores**.

2. Clique em **Novo Colaborador** para adicionar um novo colaborador ao projeto.
   
    ![Criação de um usuário de teste do AD do Azure](./media/optimizely-tutorial/create_aaduser_10.png)

3. Preencha o endereço de email e atribua a ele uma função. Clique em **Convidar**.

    ![Criação de um usuário de teste do AD do Azure](./media/optimizely-tutorial/create_aaduser_11.png)

4. Eles receberão um convite por email. Usando o endereço de email, eles precisarão fazer logon no Optimizely.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo a ela acesso ao Optimizely.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Optimizely, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, escolha **Optimizely**.

    ![Configurar o logon único](./media/optimizely-tutorial/tutorial_optimizely_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Optimizely no Painel de Acesso, você deverá ser automaticamente conectado ao seu aplicativo Optimizely.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/optimizely-tutorial/tutorial_general_01.png
[2]: ./media/optimizely-tutorial/tutorial_general_02.png
[3]: ./media/optimizely-tutorial/tutorial_general_03.png
[4]: ./media/optimizely-tutorial/tutorial_general_04.png

[100]: ./media/optimizely-tutorial/tutorial_general_100.png

[200]: ./media/optimizely-tutorial/tutorial_general_200.png
[201]: ./media/optimizely-tutorial/tutorial_general_201.png
[202]: ./media/optimizely-tutorial/tutorial_general_202.png
[203]: ./media/optimizely-tutorial/tutorial_general_203.png
