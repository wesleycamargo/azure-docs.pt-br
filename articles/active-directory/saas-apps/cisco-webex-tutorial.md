---
title: 'Tutorial: integração do Azure Active Directory com o Cisco Webex | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 73e20afdcacec76482f8ebf01bf2cef2105912a6
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005504"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Tutorial: integração do Azure Active Directory ao Cisco Webex

Neste tutorial, você aprenderá como integrar o Cisco Webex ao Azure AD (Azure Active Directory).

A integração do Cisco Webex ao Azure Active Directory oferece os seguintes benefícios:

- Você pode controlar no Azure Active Directory quem tem acesso ao Cisco Webex.
- Você pode permitir que os usuários façam logon automaticamente no Cisco Webex com as respectivas contas do Azure Active Directory.
- Você pode gerenciar suas contas em um único local: o novo Portal do Azure.

Para obter mais detalhes sobre a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure Active Directory ao Cisco Webex, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Cisco Webex

> [!NOTE]
> Para testar as etapas deste tutorial, não recomendamos o uso de um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Cisco Webex da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="add-cisco-webex-from-the-gallery"></a>Adicionar o Cisco Webex da galeria
Para configurar a integração do Cisco Webex ao Azure Active Directory, você precisa adicionar o Cisco Webex da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Cisco Webex da galeria, execute as seguintes etapas:**

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone do **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Executar **Aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Cisco Webex**. 

5. Selecione **Cisco Webex** no painel de resultados. Em seguida, selecione o botão **Adicionar** para adicionar o aplicativo.

    ![Cisco Webex na lista de resultados](./media/cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure Active Directory com o Cisco Webex com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure Active Directory precisa saber qual usuário do Cisco Webex é equivalente a um usuário do Azure Active Directory. Em outras palavras, você precisa estabelecer um vínculo entre um usuário do Azure Active Directory e um usuário relacionado no Cisco Webex.

No Cisco Webex, atribua ao **Username** o mesmo valor que o **nome de usuário** no Microsoft Azure AD. Agora, você estabeleceu o vínculo entre os dois usuários. 

Para configurar e testar o logon único do Azure Active Directory com o Cisco Webex, você precisará concluir os seguintes blocos de construção:

1. [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
3. [Criar um usuário de teste do Cisco Webex](#create-a-cisco-webex-test-user) para ter um equivalente de Brenda Fernandes no Cisco Webex que esteja vinculado à representação do usuário no Azure Active Directory.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Teste o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure Active Directory no Portal do Azure e configura o logon único em seu aplicativo Cisco Webex.

**Para configurar o logon único do Azure Active Directory com o Cisco Webex, execute as seguintes etapas:**

1. No Portal do Azure, na página de integração de aplicativos do **Cisco Webex**, selecione **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, na caixa **Modo**, selecione **Logon com base em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. Em uma janela diferente do navegador da Web, entre em seu site de empresa do Cisco Webex como administrador.

4. Clique em **Configurações** do lado esquerdo do menu.

    ![Configurar o logon único](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. Na página de configurações, na seção **Autenticação**, clique em **Modificar**.

    ![Configurar o logon único](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. Escolha **Integrar um provedor de identidade de terceiros. (Avançado)** e vá para a próxima tela.

    ![Configurar o logon único](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. Na página **Exportar Metadados de Diretório**, clique em **Baixar Arquivo de Metadados** para fazer o download o arquivo de metadados.

    ![Configurar o logon único](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. No portal do Azure, na seção**Domínio do Cisco Webex e URLs**, carregue o **arquivo de metadados baixado do provedor de serviços** e configure o aplicativo executando as seguintes etapas:

    a. Clique em **Carregar arquivo de metadados**.

    ![Informações de logon único em Domínio e URLs do Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_upload.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Informações de logon único em Domínio e URLs do Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_uploadconfig.png)

    c. Após a conclusão bem-sucedida do carregamento do **arquivo de metadados do provedor de serviços**, os valores **Identificador** e **URL de resposta** são preenchidos automaticamente na caixa de texto da seção **Domínio e URLs do Cisco Webex**, conforme mostrado abaixo:

    ![Informações de logon único em Domínio e URLs do Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    d. Na caixa **URL de Entrada**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.webex.com/`
     
    > [!NOTE] 
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada real. Contate a [equipe de suporte ao Cliente do Cisco Webex](https://www.webex.co.in/support/support-overview.html) para obter esses valores.

9. O aplicativo Cisco Webex espera que as asserções SAML contenham atributos específicos. Configure as atribuições a seguir para o aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. A captura de tela a seguir mostra um exemplo disso.
    
    ![Configurar o logon único](./media/cisco-webex-tutorial/tutorial_ciscowebex_07.png) 

10. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:
    
    |  Nome do atributo  | Valor do atributo |
    | --------------- | -------------------- |    
    |   nome    | user.givenname |
    |   sobrenome    | user.surname |
    |   uid    | user.mail |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar o logon único](./media/cisco-webex-tutorial/tutorial_attribute_04.png)

    ![Configurar o logon único](./media/cisco-webex-tutorial/tutorial_attribute_05.png)
    
    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.
    
    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**.

11. Na seção **Certificado de autenticação SAML**, selecione **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

12. Clique em **Salvar**.

    ![Botão Salvar em Configurar Logon Único](./media/cisco-webex-tutorial/tutorial_general_400.png)
    
13. Na página de administrador do site de empresa Cisco Webex, use a opção de navegador de arquivo para localizar e carregar o arquivo de metadados Microsoft Azure Active Directory. Em seguida, escolha **Exigir certificado assinado por uma autoridade de certificação em Metadados (mais seguro)** e vá para a próxima tela. 

    ![Configurar o logon único](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

14. Escolha **Testar Conexão SSO** e, quando uma nova guia do navegador for aberta, autentique-se com o Azure AD conectando-se.

15. Volte para a guia do navegador **Gerenciamento de Colaboração de Nuvem da Cisco**. Se o teste tiver sido bem-sucedido, escolha a opção **Este teste foi executado com êxito. Habilitar Logon Único** e clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No Portal do Azure, no painel esquerdo, selecione o botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/cisco-webex-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, em seguida, selecione **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/cisco-webex-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar** na parte superior da caixa de diálogo **Todos os usuários**.

    ![O botão Adicionar](./media/cisco-webex-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/cisco-webex-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.
 
### <a name="create-a-cisco-webex-test-user"></a>Criar um usuário de teste do Cisco Webex

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Cisco Webex. O Cisco Webex dá suporte ao provisionamento Just-In-Time e provisionamento automático de usuário, que é habilitado por padrão. Você pode encontrar mais detalhes [ aqui ](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial) sobre como configurar o provisionamento automático de usuários.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que o usuário Brenda Fernandes use o logon único do Azure, concedendo acesso ao Cisco Webex.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Cisco Webex, execute as seguintes etapas:**

1. No Portal do Azure, abra o modo de exibição de aplicativos. Em seguida, vá para a exibição de diretório e para **Aplicativos empresariais**.  

2. Selecione **Todos os aplicativos**.

    ![Atribuir usuário][201] 

3. Na lista de aplicativos, escolha **Cisco Webex**.

    ![O link do Cisco Webex na lista de Aplicativos](./media/cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Selecione o botão **Adicionar**. Em seguida, selecione **Usuários e grupos**, na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Britta Simon** na lista de **Usuários**.

6. Na caixa de diálogo **Usuários e grupos**, clique no botão **Selecionar**.

7. Selecione o botão **Atribuir** na caixa de diálogo **Adicionar atribuição**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Quando você seleciona o bloco Cisco Webex no painel de acesso, você entra automaticamente em seu aplicativo do Cisco Webex.

Para saber mais sobre o painel de acesso, veja [Introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/cisco-webex-tutorial/tutorial_general_203.png

