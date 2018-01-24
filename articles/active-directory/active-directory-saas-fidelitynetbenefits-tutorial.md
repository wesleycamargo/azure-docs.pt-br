---
title: "Tutorial: integração do Azure Active Directory ao Fidelity NetBenefits | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Fidelity NetBenefits."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2018
ms.author: jeedes
ms.openlocfilehash: 007d3c894731560423e2dde0572793a4282a4654
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2018
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Tutorial: integração do Azure Active Directory ao Fidelity NetBenefits

Neste tutorial, você aprenderá a integrar o Fidelity NetBenefits ao Azure AD (Azure Active Directory).

A integração do Fidelity NetBenefits ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Fidelity NetBenefits.
- Você pode permitir que os usuários façam logon automaticamente no Fidelity NetBenefits (Logon Único) com suas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Fidelity NetBenefits, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Fidelity NetBenefits

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Fidelity NetBenefits da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Adicionando o Fidelity NetBenefits da galeria
Para configurar a integração do Fidelity NetBenefits ao Azure AD, você precisará adicionar o Fidelity NetBenefits da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Fidelity NetBenefits da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Fidelity NetBenefits**, selecione **Fidelity NetBenefits** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Fidelity NetBenefits na lista de resultados](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Fidelity NetBenefits, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Fidelity NetBenefits é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Fidelity NetBenefits.

No Fidelity NetBenefits, o mapeamento de **usuário** deve ser feito com o **usuário do Azure AD** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Fidelity NetBenefits, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Fidelity NetBenefits](#create-a-fidelity-netbenefits-test-user)** – para ter um equivalente de Brenda Fernandes no Fidelity NetBenefits que esteja vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no Portal do Azure e configurará o logon único no aplicativo Fidelity NetBenefits.

**Para configurar o logon único do Azure AD com o Fidelity NetBenefits, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Fidelity NetBenefits**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_samlbase.png)

3. Na seção **URLs e Domínio do Fidelity NetBenefits**, siga as etapas abaixo:

    ![Informações de logon único de Domínio e URLs do Fidelity NetBenefits](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_url.png)

    a. Na caixa de texto **Identificador**, digite uma URL:

    Para o ambiente de teste: `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Para o ambiente de produção: `urn:sp:fidelity:geninbndnbparts20`

    b. Na caixa de texto **URL de Resposta**, digite uma URL:

    Para o ambiente de teste: `https://loginxq1.fidelity.com/ftgw/Fas/NBExternal/NBPartSSO/InboundSSO/consumer/sp/ACS.saml2`

    Para o ambiente de produção: `https://login.fidelity.com/ftgw/Fas/NBExternal/NBPartSSO/InboundSSO/consumer/sp/ACS.saml2`
 
4. O aplicativo Fidelity NetBenefits espera que as declarações SAML estejam em um formato específico. Mapeamos o **Identificador de usuário** com **user.principalname**. Você pode mapeá-lo com **employeeid** ou qualquer outra declaração aplicável à sua organização como um **Identificador de usuário**. A captura de tela a seguir mostra apenas um exemplo disso.

    ![Atributo do Fidelity NetBenefits](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_attribute.png)

    >[!Note]
    >O Fidelity NetBenefits dá suporte a federação estática e dinâmica. Estática significa que não usará o provisionamento de usuário just in time baseado em SAML, e dinâmico significa que há compatibilidade apenas com o provisionamento just in time. Para usar o provisionamento baseado JIT, os clientes precisam adicionar mais algumas declarações ao Azure AD, como a data de nascimento do usuário etc. Esses detalhes são fornecidos pela [Equipe de suporte do Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com) e eles precisam habilitar a federação dinâmica para sua instância.
    
4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_certificate.png) 

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Fidelity NetBenefits**, clique em **Configurar Fidelity NetBenefits** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Fidelity NetBenefits](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_configure.png) 

7. Para configurar o logon único no lado do **Fidelity NetBenefits**, você precisa enviar o **XML de Metadados**, **URL de Serviço de Logon Único do SAML** e **ID da Entidade SAML** para a [equipe de suporte do Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-fidelitynetbenefits-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-a-fidelity-netbenefits-test-user"></a>Criar um usuário de teste do Fidelity NetBenefits

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Fidelity NetBenefits. Se estiver criando a federação estática, trabalhe com a [Equipe de suporte do Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com) para criar usuários na plataforma Fidelity NetBenefits. Esses usuários devem ser criados e ativados antes de usar o logon único. 

Para a federação dinâmica, os usuários são criados usando apenas o provisionamento do usuário just in time. Para usar o provisionamento baseado JIT, os clientes precisam adicionar mais algumas declarações ao Azure AD, como a data de nascimento do usuário etc. Esses detalhes são fornecidos pela [Equipe de suporte do Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com) e eles precisam habilitar a federação dinâmica para sua instância.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure ao conceder acesso ao Fidelity NetBenefits.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Fidelity NetBenefits, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, escolha **Fidelity NetBenefits**.

    ![O link do Fidelity NetBenefits na lista de aplicativos](./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Fidelity NetBenefits no Painel de Acesso, deve fazer logon automaticamente no seu aplicativo Fidelity NetBenefits.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fidelitynetbenefits-tutorial/tutorial_general_203.png

