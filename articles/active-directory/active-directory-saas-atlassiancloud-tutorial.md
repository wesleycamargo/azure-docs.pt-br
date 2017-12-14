---
title: "Tutorial: integração do Azure Active Directory ao Atlassian Cloud | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Atlassian Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: jeedes
ms.openlocfilehash: fff906913b637c47d394f9127983a08f96d568e4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Tutorial: Integração do Azure Active Directory com o Atlassian Cloud

Neste tutorial, você aprenderá a integrar o Atlassian Cloud ao Azure AD (Azure Active Directory).

A integração do Atlassian Cloud ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Atlassian Cloud.
- Você pode habilitar os usuários a fazer logon automaticamente no Atlassian Cloud (Logon Único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Atlassian Cloud, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Para habilitar o logon único SAML para produtos Atlassian Cloud, você precisará configurar o Identity Manager. Saiba mais sobre o [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager)

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição do Atlassian Cloud da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Adição do Atlassian Cloud da galeria
Para configurar a integração do Atlassian Cloud ao Azure AD, você precisará adicionar o Atlassian Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Atlassian Cloud da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Atlassian Cloud**, selecione **Atlassian Cloud** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Atlassian Cloud na lista de resultados](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Atlassian Cloud, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Atlassian Cloud é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Atlassian Cloud.

No Atlassian Cloud, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Atlassian Cloud, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Atlassian Cloud](#create-an-atlassian-cloud-test-user)** – para ter um equivalente de Brenda Fernandes no Atlassian Cloud que esteja vinculado à sua representação no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Atlassian Cloud.

**Para configurar o logon único do Azure AD com o Atlassian Cloud, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativo do **Atlassian Cloud**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Na seção **URLs e Domínio do Atlassian Cloud**, se desejar configurar o aplicativo em modo iniciado pelo **IDP**, siga as etapas abaixo:

    ![Informações de logon único em Domínio e URLs do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url_new.png)
    
    a. Na caixa de texto **Identificador**, digite a URL: `https://id.atlassian.com/login`
    
    b. Na caixa de texto **URL de Resposta**, digite a URL: `https://id.atlassian.com/login/saml/acs`

    c. Na caixa de texto **Estado de Retransmissão**, digite uma URL usando o seguinte padrão: `https://<instancename>.atlassian.net`

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de logon único em Domínio e URLs do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<instancename>.atlassian.net`

    > [!NOTE] 
    > Esses valores não são reais. Você pode obter esses valores da tela Configuração SAML do Atlassian Cloud, que é explicada posteriormente no tutorial.

5. Na seção **Certificado de Autenticação do SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

7. Na seção **Configuração do Atlassian Cloud**, clique em **Configurar Atlassian Cloud** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção de Referência Rápida.**

    ![Configuração do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

8. Para configurar o SSO para seu aplicativo, faça logon no Portal do Atlassian usando os direitos de administrador.

9. Navegue para **Administração de Site da Atlassian** > **Organizações e Segurança**. Se você ainda não fez isso, crie e nomeie sua organização. No painel de navegação esquerdo, clique em **Domínios**.

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

10. Selecione o modo em que você deseja verificar seu domínio – **DNS** ou **HTTPS**.

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

11. Para verificação de DNS, selecione a guia **DNS** na página **Domínios** e realize as etapas a seguir:

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. Clique em **Copiar** para copiar um valor para o registro TXT.

    b. Do DNS, localize a página de configurações para adicionar um novo registro

    c. Selecione a opção de adicionar um novo registro e cole o valor que você copiou da página **Domínios** para o campo **Valor**. Seu DNS também pode referir-se a ela como **Resposta** ou **Descrição**.

    d. O registro DNS também pode incluir os seguintes campos:
    
    * **Tipo do registro**: insira **TXT**
    * **Nome/Host/Alias**: mantenha o padrão (@ ou em branco)
    * **TTL (vida útil)**: insira **86400**
    
    e.  Salve o registro.

12. Retorne para a **página Domínios** na administração da organização e clique no botão **Verificar domínio**. Insira seu nome de domínio no pop-up e clique no botão **Verificar domínio**.

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)
    
    > [!NOTE]
    > Pode levar até 72h para que as alterações ao registro TXT entrem em vigor, você não saberá imediatamente se sua verificação de domínio foi bem-sucedida. Verifique sua página **Domínios** logo após você realizar essas etapas para ver o status da sua verificação. Você vê a tela a seguir com o status atualizado como **VERIFICADO**.

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)

13. Para verificação de HTTPS, selecione a guia **HTTPS** na página **Domínios** e realize as etapas a seguir:

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a.  Clique em **Baixar arquivo** para baixar o arquivo HTML.

    b.  Carregue o arquivo HTML no diretório raiz do seu domínio.

14. Retorne para a página **Domínios** na administração da organização e clique no botão **Verificar domínio**. Insira seu **nome de domínio** no pop-up e clique no botão **Verificar domínio**.

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

15. Se o processo de verificação puder localizar o arquivo que você carregou no diretório raiz, o status do domínio será atualizado para **Verificado**.

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > Para obter mais informações, consulte a [documentação de verificação de domínio da Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)

16. Na barra de navegação à esquerda, clique em **Logon único SAML**. Se você ainda não fez isso, assine o Identity Manager da Atlassian.

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

17. Na caixa de diálogo **Adicionar configuração SAML**, adicione as configurações do provedor de identidade conforme demonstrado a seguir:

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Na caixa de texto **ID da Entidade do Provedor de Identidade**, cole o valor da **ID da Entidade SAML** copiado do portal do Azure.

    b. Na caixa de texto **URL de SSO do Provedor de Identidade**, cole o valor da **URL de Serviço de Logon Único SAML** que você copiou do portal do Azure.

    c. Abra o certificado baixado em um bloco de notas e copie os valores sem as linhas Begin e End e cole-os na caixa **Certificado X509 público**.
    
    d. Clique em **Salvar Configuração** para salvar as configurações.
     
18. Atualize as configurações do Azure AD para garantir que você configurou as URLs corretas.
  
    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Copie o valor da **ID de Entidade do SP** da tela do SAML e cole o valor na caixa **Identificador** no Portal do Azure, na seção **Domínio e URLs** do Atlassian Cloud.
    
    b. Copie o valor da **URL do Serviço do Consumidor de Declaração do SP** da tela do SAML e cole o valor na caixa **URL de Resposta** no Portal do Azure, na seção **Domínio e URLs** do Atlassian Cloud.
    
    c. A URL de Entrada é a URL do locatário do seu Atlassian Cloud. 
    
19. No portal do Azure, clique no botão **Salvar**.

    ![Configurar Logon Único](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-an-atlassian-cloud-test-user"></a>Criar um usuário de teste do Atlassian Cloud

Para que os usuários do Azure AD possam fazer logon no Atlassian Cloud, eles devem ser provisionados no Atlassian Cloud. No caso do Atlassian Cloud, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Na seção de administração do site do Portal Atlassian, clique no botão **Usuários**

    ![Criar usuário do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Clique no botão **Convidar usuário** para criar um usuário no Atlassian Cloud.

    ![Criar usuário do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Insira o **Endereço de email** e atribua o acesso ao aplicativo. 

    ![Criar usuário do Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Clique no botão **Convidar usuários**, ele enviará o convite por email ao usuário que, após aceitá-lo, ficará ativo no sistema. 

>[!NOTE] 
>Você também pode criar os usuários em massa clicando no botão **Criar em Massa** na seção Usuários.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela acesso ao Atlassian Cloud.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Atlassian Cloud, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Atlassian Cloud**.

    ![O link para o Atlassian Cloud na lista de aplicativos](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Atlassian Cloud no Painel de Acesso, você deve entrar automaticamente no aplicativo Atlassian Cloud.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png

