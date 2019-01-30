---
title: 'Tutorial: Integração do Azure Active Directory à AWS (Amazon Web Services) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AWS (Amazon Web Services).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: d5633648ee94c4db20f095619871ac5cd9cec7da
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54825163"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Tutorial: Integração do Azure Active Directory à AWS (Amazon Web Services)

Neste tutorial, você aprenderá a integrar o AWS (Amazon Web Services) ao Azure AD (Active Directory).
A integração do AWS (Amazon Web Services) ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Microsoft Azure AD quem tem acesso ao AWS (Amazon Web Services).
* Você pode habilitar seus usuários a fazerem logon automaticamente na AWS (Amazon Web Services) (logon único) com suas contas do Microsoft Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

![AWS (Amazon Web Services)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Você pode configurar vários identificadores para várias instâncias, conforme abaixo.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Com esses valores, o Azure AD removerá o valor de **#** e enviará o valor correto `https://signin.aws.amazon.com/saml` como o URL de público no token SAML.

**É recomendável usar essa abordagem pelos seguintes motivos:**

 a. Cada aplicativo fornecerá o certificado exclusivo X509 e, portanto, cada instância poderá ter uma data de expiração de certificado diferente, e você poderá gerenciá-la com base na conta individual da AWS. A rolagem geral do certificado será fácil nesse caso.

b. Você pode ativar o aprovisionamento de usuário com o aplicativo da AWS no Azure AD e, em seguida, nosso serviço buscará todas as funções dessa conta da AWS. Você não precisa adicionar ou atualizar manualmente as funções da AWS no aplicativo.

c. Você pode atribuir o proprietário do aplicativo individualmente para o aplicativo que pode gerenciar o aplicativo diretamente no Azure AD.

> [!Note]
> Verifique se que você usar apenas o aplicativo da Galeria

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o AWS (Amazon Web Services), você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura habilitada para logon único da AWS (Amazon Web Services)

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

> [!Note]
> Se você deseja integrar múltiplas contas AWS para uma conta do Azure para logon único, consulte [este](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) artigo.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* A AWS (Amazon Web Services) dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adicionar o AWS (Amazon Web Services) da galeria

Para configurar a integração do AWS (Amazon Web Services) com o Azure AD, você precisa adicionar o AWS (Amazon Web Services), por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o AWS (Amazon Web Services) da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **AWS (Amazon Web Services)**, selecione **AWS (Amazon Web Services)** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![AWS (Amazon Web Services) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com a AWS (Amazon Web Services), com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na AWS (Amazon Web Services).

Para configurar e testar o logon único do Azure AD com o AWS (Amazon Web Services), você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único da AWS (Amazon Web Services)](#configure-amazon-web-services-aws-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste da AWS (Amazon Web Services)](#create-amazon-web-services-aws-test-user)** para ter um equivalente de Brenda Fernandes na AWS (Amazon Web Services) que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com a AWS (Amazon Web Services), execute as etapas a seguir:

1. 758405No [Portal do Azure](https://portal.azure.com/), na página de integração de aplicativos **Amazon Web Services (AWS)**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. Quando você estiver configurando mais de uma instância, forneça o valor do Identificador. De segunda instância em diante, forneça o valor do Identificador no seguinte formato. Use uma **#** entrar para especificar um valor exclusivo de SPN.

    `https://signin.aws.amazon.com/saml#2`

    ![Informações de logon único de Domínio e URLs do AWS (Amazon Web Services)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_identifier.png)

6. Aplicativo Amazon Web Services (AWS) espera as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

7. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    | NOME  | Atributo de Origem  | Namespace |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Função            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "fornecer um valor entre 900 segundos (15 minutos) para segundos 43200 (12 horas)" |  https://aws.amazon.com/SAML/Attributes |

     a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na caixa de texto **Namespace**, digite o valor de namespace mostrado para essa linha.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Salvar**.

8. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

9. Na seção **Configurar a AWS (Amazon Web Services)**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

     a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Configurar logon único da AWS (Amazon Web Services)

1. Em uma janela de navegador diferente, entre no site de sua empresa do AWS (Amazon Web Services) como administrador.

2. Clique na **Página inicial do AWS**.

    ![Configurar página inicial de logon único][11]

3. Clique em **Gerenciamento de identidades e acesso**.

    ![Configurar identidade de logon único][12]

4. Clique em **Provedores de identidade**, e, em seguida, clique em **Criar provedor**.

    ![Configurar provedor de logon único][13]

5. Na página da caixa de diálogo **Configurar provedor** , execute as seguintes etapas:

    ![Configurar diálogo logon único][14]

     a. Como **Tipo de provedor**, selecione **SAML**.

    b. Na caixa de texto **Nome do provedor**, digite um nome de provedor (por exemplo: *WAAD*).

    c. Para carregar seu **arquivo de metadados** baixado do Portal do Azure, clique em **Escolher Arquivo**.

    d. Clique em **Próxima etapa**.

6. Na página de diálogo **Verificar informações do provedor**, clique em **Criar**.

    ![Configurar verificação de logon único][15]

7. Clique em **Funções** e, em seguida, clique em **Criar função**.

    ![Configurar funções de logon único][16]

8. Na página **Criar função**, realize as seguintes etapas:  

    ![Configurar confiança de logon único][19]

     a. Selecione **Federação do SAML 2.0** em **Selecionar tipo de entidade confiável**.

    b. Em **Escolher uma seção do provedor do SAML 2.0**, selecione o **provedor do SAML** criado anteriormente (por exemplo: *WAAD*)

    c. Selecione **Permitir acesso do Console de Gerenciamento do AWS e programação**.
  
    d. Clique em **Avançar: Permissões.**

9. Na caixa de diálogo **Anexar políticas de permissão**, anexe a política adequada conforme sua organização. Clique em **Avançar: Análise**.  

    ![Configurar política de logon único][33]

10. Na caixa de diálogo **Examinar** , execute as seguintes etapas:

    ![Configurar revisão de logon único][34]

     a. Na caixa de texto **Nome da função**, insira o nome da função.

    b. Na caixa de texto **Descrição da função**, insira a descrição.

    c. Clique em **Criar função**.

    d. Crie quantas funções forem necessárias e mapeie-as para o Provedor de Identidade.

11. Use as credenciais de conta de serviço AWS para buscar as funções da conta AWS no Provisionamento de Usuário do Microsoft Azure AD. Para isso, abra a página inicial do console AWS.

12. Clique em **Serviços** -> **Segurança, Identidade e Conformidade** -> **IAM**.

    ![buscando as funções da conta AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Selecione a guia **Políticas** na seção IAM.

    ![buscando as funções da conta AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Crie uma nova política, clicando em **Criar política** para efetuar fetch das funções da conta do AWS no Provisionamento de Usuário do Microsoft Azure AD.

    ![Criar nova política](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Crie sua própria política para buscar todas as funções de contas AWS, executando as seguintes etapas:

    ![Criar nova política](./media/amazon-web-service-tutorial/policy1.png)

     a. Na seção **"Criar a política"** clique na guia **"JSON"**.

    b. No documento de política, adicione o JSON abaixo.

    ```

    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",

    "Action": [

    "iam:ListRoles"

    ],

    "Resource": "*"

    }

    ]

    }

    ```

    c. Clique no **botão Examinar política** para validar a política.

    ![Definir a nova política](./media/amazon-web-service-tutorial/policy5.png)

16. Defina a **nova política** executando as etapas a seguir:

    ![Definir a nova política](./media/amazon-web-service-tutorial/policy2.png)

     a. Forneça o **Nome da política** como **AzureAD_SSOUserRole_Policy**.

    b. Você pode fornecer **Descrição** para a política como **Essa política permitirá buscar as funções de contas AWS**.

    c. Clique no botão **"Criar política"**.

17. Crie uma nova conta de usuário no serviço de IAM do AWS executando as etapas a seguir:

     a. Clique na navegação **Usuários** no console IAM do AWS.

    ![Definir a nova política](./media/amazon-web-service-tutorial/policy3.png)

    b. Clique no botão **Adicionar usuário** para criar um novo usuário.

    ![Adicionar usuário](./media/amazon-web-service-tutorial/policy4.png)

    c. Na seção **Adicionar usuário**, execute as etapas a seguir:

    ![Adicionar usuário](./media/amazon-web-service-tutorial/adduser1.png)

    * Digite o nome de usuário como **AzureADRoleManager**.

    * No tipo de acesso, selecione a opção **Acesso programático**. Dessa forma, o usuário pode invocar as APIs e buscar as funções da conta AWS.

    * Clique no botão **Próximas permissões** no canto inferior direito.

18. Agora, crie uma nova política para esse usuário executando as etapas a seguir:

    ![Adicionar usuário](./media/amazon-web-service-tutorial/adduser2.png)

     a. Clique no botão **Anexar políticas existentes diretamente**.

    b. Pesquise a política recém-criada na seção filtro **AzureAD_SSOUserRole_Policy**.

    c. Selecione a **política** e, em seguida, clique no botão **Próximo: Análise**.

19. Examine a política para o usuário conectado executando as etapas a seguir:

    ![Adicionar usuário](./media/amazon-web-service-tutorial/adduser3.png)

     a. Examine o nome de usuário, tipo de acesso e política mapeados para o usuário.

    b. Clique no botão **Criar usuário** no canto inferior direito para criar o usuário.

20. Faça o download das credenciais do usuário de um usuário executando as etapas a seguir:

    ![Adicionar usuário](./media/amazon-web-service-tutorial/adduser4.png)

     a. Copie a **ID de chave de acesso** do usuário e a **Chave de acesso secreta**.

    b. Insira essas credenciais na seção de provisionamento de usuário do Microsoft Azure AD para buscar as funções do console AWS.

    c. Clique no botão **Editar** na parte inferior.

21. Navegue até a seção **Provisionamento de usuário** do aplicativo do Amazon Web Services no Portal de Gerenciamento do Microsoft Azure AD.

    ![Adicionar usuário](./media/amazon-web-service-tutorial/provisioning.png)

22. Insira a **Chave de Acesso** e o **Segredo** nos campos **Segredo do Cliente** e **Token do Segredo** respectivamente.

    ![Adicionar usuário](./media/amazon-web-service-tutorial/provisioning1.png)

     a. Insira a chave de acesso do usuário do AWS no campo **Segredo do cliente**.

    b. Insira o segredo do usuário do AWS no campo **Segredo do Token**.

    c. Clique no botão **Testar Conexão** e você deverá poder testar com êxito essa conexão.

    d. Salve a configuração clicando no botão **Salvar** na parte superior.

23. Agora, certifique-se de habilitar o Status de Provisionamento **Ativo** na seção Configurações ativando o comutador e, em seguida, clicando no botão **Salvar** botão na parte superior.

    ![Adicionar usuário](./media/amazon-web-service-tutorial/provisioning2.png)

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

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao AWS (Amazon Web Services).

1. No portal do Azure, selecione **Aplicativos Empresariais**, selecione **Todos os aplicativos** e, em seguida, selecione **AWS (Amazon Web Services)**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **AWS (Amazon Web Services)**.

    ![O link do AWS (Amazon Web Services) na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-amazon-web-services-aws-test-user"></a>Criar usuário de teste da AWS (Amazon Web Services)

O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no AWS (Amazon Web Services). O AWS (Amazon Web Services) não precisa que um usuário seja criado em seu sistema para o SSO, portanto você não precisa realizar nenhuma ação aqui.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco AWS (Amazon Web Services) no Painel de Acesso, você deve fazer logon automaticamente na AWS (Amazon Web Services) para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
