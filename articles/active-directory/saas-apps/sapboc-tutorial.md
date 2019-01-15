---
title: 'Tutorial: Integração do Azure Active Directory com o SAP Business Object Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Business Object Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 18adc7728ffd0b4faf2e63e7c5d3be0da7dd651c
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065101"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Tutorial: Integração do Azure Active Directory com o SAP Business Object Cloud

Neste tutorial, você aprenderá a integrar o SAP Business Object Cloud ao Azure AD (Azure Active Directory).
A integração do SAP Business Object Cloud ao Azure AD oferece os seguintes benefícios:

* No Azure AD, você pode controlar quem tem acesso ao SAP Business Object Cloud.
* Você pode habilitar os usuários a fazer logon automaticamente no SAP Business Object Cloud (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SAP Business Object Cloud, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura do SAP Business Object Cloud habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SAP Business Object Cloud é compatível com o SSO iniciado por **SP**

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Adicionar o SAP Business Object Cloud da galeria

Para configurar a integração do SAP Business Object Cloud ao Azure AD, você precisará adicionar o SAP Business Object Cloud da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o SAP Business Object Cloud da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SAP Business Object Cloud**, selecione **SAP Business Object Cloud** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![SAP Business Object Cloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SAP Business Object Cloud, com base em uma usuária de teste chamada **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP Business Object Cloud.

Para configurar e testar o logon único do Azure AD com o SAP Business Object Cloud, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do SAP Business Object Cloud](#configure-sap-business-object-cloud-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do SAP Business Object Cloud](#create-sap-business-object-cloud-test-user)** – para ter um equivalente de Brenda Fernandes no SAP Business Object Cloud que esteja vinculado à representação dela no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SAP Business Object Cloud, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo do **SAP Business Object Cloud**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de Domínio e URLs do SAP Business Object Cloud para logon único](common/sp-identifier.png)

     a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. Na caixa de texto **Identificador (ID da Entidade)**, digite uma URL usando o seguinte padrão:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Os valores nessas URLs são apenas para demonstração. Atualize os valores com a verdadeira URL de entrada e a URL do identificador. Para obter a URL de entrada, entre em contato com a [equipe de Suporte ao cliente do SAP Business Object Cloud](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Você pode obter a URL do identificador ao baixar os metadados do SAP Business Object Cloud do console do administrador. Isso é explicado mais adiante no tutorial.

4. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

### <a name="configure-sap-business-object-cloud-single-sign-on"></a>Configurar o logon único do SAP Business Object Cloud

1. Em outra janela do navegador da Web, entre no site da empresa do seu SAP Business Object Cloud como um administrador.

2. Selecione **Menu** > **Sistema** > **Administração**.
    
    ![Selecionar Menu, Sistema e Administração](./media/sapboc-tutorial/config1.png)

3. Na guia **Segurança**, selecione o ícone **Editar** (caneta).
    
    ![Na guia Segurança, selecionar o ícone Editar](./media/sapboc-tutorial/config2.png)  

4. Para **Método de Autenticação**, selecione **SSO (Logon Único) do SAML**.

    ![Selecionar Logon Único do SAML para o método de autenticação](./media/sapboc-tutorial/config3.png)  

5. Para baixar os metadados do provedor de serviços (Etapa 1), selecione **Baixar**. No arquivo de metadados, localize e copie o valor **entityID**. No portal do Azure, na caixa de diálogo **Configuração Básica de SAML**, cole o valor na caixa **Identificador**.

    ![Copiar e colar o valor de entityID](./media/sapboc-tutorial/config4.png)  

6. Para fazer upload dos metadados do provedor de serviços (Etapa 2) no arquivo que você baixou do portal do Azure, em **Fazer upload dos metadados do Provedor de Identidade**, selecione **Fazer Upload**.  

    ![Em Fazer upload dos metadados do Provedor de Identidade, selecionar Fazer Upload](./media/sapboc-tutorial/config5.png)

7. Na lista **Atributo de Usuário**, selecione o atributo de usuário (Etapa 3) que você quer usar na sua implementação. Esse atributo de usuário é mapeado para o provedor de identidade. Para inserir um atributo personalizado na página do usuário, use a opção **Mapeamento de SAML Personalizado**. Ou você pode selecionar o **Email** ou a **ID DE USUÁRIO** como o atributo de usuário. Em nosso exemplo, selecionamos **Email** porque mapeamos a declaração do identificador de usuário com o atributo **userprincipalname** na seção **Atributos e Declarações do Usuário** do portal do Azure. Isso fornece um email de usuário exclusivo, que é enviado ao aplicativo SAP Business Object Cloud em cada resposta bem-sucedida de SAML.

    ![Selecionar Atributo de Usuário](./media/sapboc-tutorial/config6.png)

8. Para verificar a conta com o provedor de identidade (Etapa 4), na caixa **Credencial de Logon (Email)**, insira o endereço de email do usuário. Em seguida, selecione **Verificar Conta**. O sistema adiciona credenciais de entrada à conta de usuário.

    ![Inserir email e selecionar Verificar Conta](./media/sapboc-tutorial/config7.png)

9. Selecione o ícone **Salvar**.

    ![Ícone Salvar](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará Brenda Fernandes a usar o logon único do Azure concedendo-lhe acesso ao SAP Business Object Cloud.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **SAP Business Object Cloud**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **SAP Business Object Cloud**.

    ![Link do SAP Business Object Cloud na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-sap-business-object-cloud-test-user"></a>Criar um usuário de teste do SAP Business Object Cloud

Os usuários do Azure AD devem ser provisionados no SAP Business Object Cloud para que possam entrar no SAP Business Object Cloud. No SAP Business Object Cloud, o provisionamento é uma tarefa manual.

Para provisionar uma conta de usuário:

1. Entre no site da empresa do seu SAP Business Object Cloud como administrador.

2. Selecione **Menu** > **Segurança** > **Usuários**.

    ![Adicionar Funcionário](./media/sapboc-tutorial/user1.png)

3. Na página **Usuários**, para adicionar detalhes do novo usuário, selecione **+**. 

    ![Página Adicionar Usuários](./media/sapboc-tutorial/user4.png)

    Em seguida, conclua as seguintes etapas:

     a. Na caixa **ID DE USUÁRIO**, insira a ID do usuário, como **Brenda**.

    b. Na caixa **NOME**, insira o nome do usuário, como **Brenda**.

    c. Na caixa **SOBRENOME**, insira o sobrenome do usuário, como **Fernandes**.

    d. Na caixa **NOME DE EXIBIÇÃO**, insira o nome completo do usuário, como **Brenda Fernandes**.

    e. Na caixa **EMAIL**, insira o endereço de email do usuário, como **brittasimon@contoso.com**.

    f. Na página **Selecionar Funções**, selecione a função apropriada para o usuário e selecione **OK**.

      ![Escolher função](./media/sapboc-tutorial/user3.png)

    g. Selecione o ícone **Salvar**.    

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco do SAP Business Object Cloud no Painel de Acesso, você deverá ser conectado automaticamente ao aplicativo SAP Business Object Cloud para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

