---
title: "Tutorial: Integração do Azure Active Directory com o AWS (Amazon Web Services) | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o AWS (Amazon Web Services)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/3/2017
ms.author: jeedes
ms.openlocfilehash: b4d96df72fd7f8f817140e7599e22a63ddd79910
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Tutorial: Integração do Azure Active Directory com o AWS (Amazon Web Services)

Neste tutorial, você aprenderá a integrar o AWS (Amazon Web Services) ao Azure AD (Active Directory).

A integração do AWS (Amazon Web Services) ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure AD quem tem acesso ao AWS (Amazon Web Services).
- Você pode habilitar seus usuários a fazerem logon automaticamente no AWS (Amazon Web Services) (logon único) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o AWS (Amazon Web Services), você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada de logon único do AWS (Amazon Web Services)

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o AWS (Amazon Web Services) da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adicionar o AWS (Amazon Web Services) da galeria
Para configurar a integração do AWS (Amazon Web Services) com o Azure AD, você precisa adicionar o AWS (Amazon Web Services), por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o AWS (Amazon Web Services) da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **AWS (Amazon Web Services)**, selecione **AWS (Amazon Web Services)** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![AWS (Amazon Web Services) na lista de resultados](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o AWS (Amazon Web Services), com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do AWS (Amazon Web Services) é equivalente a um usuário do Azure AD. Em outras palavras, uma relação de link entre um usuário do Azure AD e o usuário relacionado no serviço AWS (Amazon Web Services) deve ser estabelecida.

No Amazon Web Services (AWS), atribua o valor do **nome de usuário** no Microsoft Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o AWS (Amazon Web Services), você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do AWS (Amazon Web Services)](#create-an-amazon-web-services-aws-test-user)** – para ter um equivalente de Brenda Fernandes no AWS (Amazon Web Services) que esteja vinculado à representação do usuário no Microsoft Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você vai habilitar o logon único do Azure AD no Portal do Azure e configurar o logon único em seu aplicativo AWS (Amazon Web Services).

**Para configurar o logon único do Azure AD com o AWS (Amazon Web Services), execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **AWS (Amazon Web Services)**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Na seção **Domínio e URLs do AWS (Amazon Web Services)**, o usuário não precisa seguir as etapas, uma vez que o aplicativo já está pré-integrado ao Azure.

    ![Informações de logon único de Domínio e URLs do AWS (Amazon Web Services)](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. O aplicativo AWS (Amazon Web Services) espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo disso.

    ![Configurar logon único attb](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)   

5. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:
    
    | Nome do atributo  | Valor do atributo | Namespace |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Função            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Você precisa configurar o provisionamento do usuário no Azure AD para buscar todas as funções no Console do AWS. Veja as etapas de provisionamento abaixo.

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar logon único Add](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Configurar logon único Addattb](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.
    
    d. Clique em **OK**.

6. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. Em uma janela de navegador diferente, entre no site de sua empresa do AWS (Amazon Web Services) como administrador.

9. Clique em **página inicial do Console**.
   
    ![Configurar página inicial de logon único][11]

10. Clique em **Gerenciamento de identidades e acesso**. 
   
    ![Configurar identidade de logon único][12]

11. Clique em **Provedores de identidade**, e, em seguida, clique em **Criar provedor**. 
   
    ![Configurar provedor de logon único][13]

12. Na página da caixa de diálogo **Configurar provedor** , execute as seguintes etapas: 
   
    ![Configurar diálogo logon único][14]
 
    a. Como **Tipo de provedor**, selecione **SAML**.

    b. Na caixa de texto **Nome do provedor**, digite um nome de provedor (por exemplo: *WAAD*).

    c. Para carregar seu **arquivo de metadados** baixado do Portal do Azure, clique em **Escolher Arquivo**.

    d. Clique em **Próxima etapa**.

13. Na página de diálogo **Verificar informações do provedor**, clique em **Criar**. 
    
    ![Configurar verificação de logon único][15]

14. Clique em **Funções** e, em seguida, clique em **Criar Nova Função**. 
    
    ![Configurar funções de logon único][16]

15. Na caixa de diálogo **Definir Nome de Função** , execute as seguintes etapas: 
    
    ![Configurar nome de logon único][17] 

    a. Na caixa de texto **Nome da função**, digite um nome de função (por exemplo: *TestUser*). 

    b. Clique em **Próxima etapa**.

16. Na caixa de diálogo **Selecionar Tipo de Função** , execute as seguintes etapas: 
    
    ![Configurar tipo de função de logon único][18] 

    a. Selecione **Função de acesso do provedor de identidade**. 

    b. Na seção **Conceder acesso de logon único da Web (WebSSO) a provedores SAML**, clique em **Selecionar**.

17. Na caixa de diálogo **Estabelecer Confiança** , execute as seguintes etapas:  
    
    ![Configurar confiança de logon único][19] 

    a. Como provedor SAML, selecione o provedor SAML criado anteriormente (por exemplo: *WAAD*) 
  
    b. Clique em **Próxima etapa**.

18. Na caixa de diálogo **Verificar Confiança na Função**, clique em **Próxima Etapa**. 
    
    ![Configurar confiança de função de logon único][32]

19. Na caixa de diálogo **Anexar Política**, clique em **Próxima Etapa**.  
    
    ![Configurar política de logon único][33]

20. Na caixa de diálogo **Examinar** , execute as seguintes etapas:   
    
    ![Configurar revisão de logon único][34] 

    a. Clique em **Criar função**.

    b. Crie quantas funções forem necessárias e mapeie-as para o Provedor de Identidade.

21. Use as credenciais de conta de serviço AWS para buscar as funções da conta AWS no Provisionamento de Usuário do Microsoft Azure AD. Para isso, abra a página inicial do console AWS.

22. Clique em **Serviços** -> **Segurança, identidade e conformidade** -> **IAM**.

    ![buscando as funções da conta AWS](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. Selecione a guia **Políticas** na seção IAM.

    ![buscando as funções da conta AWS](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. Crie uma nova política clicando em **Criar política**.

    ![Criar nova política](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. Crie sua própria política para buscar todas as funções de contas AWS. Na seção **Criar sua própria política** clique no botão **Selecionar**.
    
    ![Criar nova política](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

26. Defina a nova política executando as etapas a seguir:

    ![Definir a nova política](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. Forneça o **Nome da política** como **AzureAD_SSOUserRole_Policy**.

    b. Você pode fornecer **Descrição** para a política como **Essa política permitirá buscar as funções de contas AWS**.
    
    c. No documento de política, adicione o JSON abaixo.
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam: ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```
    
    d. Certifique-se de verificar em **Usar formatação automática para edição de política**.
    
    e. Clique no botão **Validar política** na parte inferior.
    
    f. Depois que a política for validada corretamente você pode clicar no botão **Criar política**.
    
27. Crie uma nova conta de usuário no serviço de IAM do AWS executando as etapas a seguir:

    a. Clique na navegação **Usuários** no console IAM do AWS.

    ![Definir a nova política](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b. Clique no botão **Adicionar usuário** para criar um novo usuário.

    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. Na seção **Adicionar usuário**, execute as etapas a seguir:
    
    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * Digite o nome de usuário como **AzureADRoleManager**.
    
    * No tipo de acesso, selecione a opção **Acesso programático**. Dessa forma, o usuário pode invocar as APIs e buscar as funções da conta AWS.
    
    * Clique no botão **Próximas permissões** no canto inferior direito.

28. Agora, crie uma nova política para esse usuário executando as etapas a seguir:

    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. Clique no botão **Anexar políticas existentes diretamente**.

    b. Pesquise a política recém-criada na seção filtro **AzureAD_SSOUserRole_Policy**.
    
    c. Selecione a **política** e, em seguida, clique no botão **Próxima: Revisão**.

29. Examine a política para o usuário conectado executando as etapas a seguir:

    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. Examine o nome de usuário, tipo de acesso e política mapeados para o usuário.
    
    b. Clique no botão **Criar usuário** no canto inferior direito para criar o usuário.

30. Faça o download das credenciais do usuário de um usuário executando as etapas a seguir:

    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. Copie a **ID de chave de acesso** do usuário e a **Chave de acesso secreta**.
    
    b. Insira essas credenciais na seção de provisionamento de usuário do Microsoft Azure AD para buscar as funções do console AWS.
    
    c. Clique no botão **Editar** na parte inferior.

31. Navegue até a seção **Provisionamento de usuário** do aplicativo do Amazon Web Services no Portal de Gerenciamento do Microsoft Azure AD.

    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. Insira a **Chave de Acesso** e o **Segredo** nos campos **Segredo do Cliente** e **Token do Segredo** respectivamente.

    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. Insira a chave de acesso do usuário do AWS no campo **Segredo do cliente**.
    
    b. Insira o segredo do usuário do AWS no campo **Segredo do Token**.
    
    c. Clique no botão **Testar Conexão** e você deverá poder testar com êxito essa conexão.

    d. Salve a configuração clicando no botão **Salvar** na parte superior.
 
33. Agora, certifique-se de habilitar o Status de Provisionamento **Ativo** na seção Configurações ativando o comutador e, em seguida, clicando no botão **Salvar** botão na parte superior.

    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com), enquanto você estiver configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Criar um usuário de teste do AWS (Amazon Web Services)

O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no AWS (Amazon Web Services). O AWS (Amazon Web Services) não precisa que um usuário seja criado em seu sistema para o SSO, portanto você não precisa realizar nenhuma ação aqui.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao AWS (Amazon Web Services).

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao AWS (Amazon Web Services), execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **AWS (Amazon Web Services)**.

    ![O link do AWS (Amazon Web Services) na lista Aplicativos](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco AWS (Amazon Web Services) no Painel de Acesso, você deve fazer logon automaticamente em seu aplicativo AWS (Amazon Web Services).
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

