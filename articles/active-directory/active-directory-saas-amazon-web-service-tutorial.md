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
ms.date: 1/16/2017
ms.author: jeedes
ms.openlocfilehash: 0ff14365323d66a101e5847d7959045c3f20dea2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Tutorial: Integração do Azure Active Directory com o AWS (Amazon Web Services)

Neste tutorial, você aprenderá a integrar o AWS (Amazon Web Services) ao Azure AD (Active Directory).

A integração do AWS (Amazon Web Services) ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure AD quem tem acesso ao AWS (Amazon Web Services).
- Você pode habilitar seus usuários a fazerem logon automaticamente no AWS (Amazon Web Services) com suas contas do Microsoft Azure Active Directory.
- Você pode gerenciar suas contas em um único local: o novo Portal do Azure.

Para obter mais informações sobre a integração de aplicativos SaaS ao Azure AD, consulte [O que é o acesso de aplicativos e o logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD com o AWS (Amazon Web Services), você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada de logon único do AWS (Amazon Web Services)

> [!NOTE]
> Para testar as etapas deste tutorial, não recomendamos o uso de um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Microsoft Azure Active Directory, você pode [obter uma versão de avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o AWS (Amazon Web Services) da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="add-amazon-web-services-aws-from-the-gallery"></a>Adicionar o AWS (Amazon Web Services) da galeria
Para configurar a integração do AWS (Amazon Web Services) com o Azure AD, você precisa adicionar o AWS (Amazon Web Services), por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o AWS (Amazon Web Services) da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, selecione o ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Executar **Aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **serviço AWS (Amazon Web Services)**. Selecione **AWS (Amazon Web Services)** no painel de resultados e selecione o botão **Adicionar** para adicionar o aplicativo.

    ![AWS (Amazon Web Services) na lista de resultados](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Microsoft Azure Active Directory com o AWS (Amazon Web Services), com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Microsoft Azure Active Directory precisa saber qual usuário do AWS (Amazon Web Services) é equivalente a um usuário do Microsoft Azure Active Directory. Em outras palavras, você precisa estabelecer um vínculo entre um usuário do Azure Active Directory e um usuário relacionado no Amazon Web Services (AWS).

Para estabelecer o link, no Amazon Web Services (AWS), atribua ao valor **Nome de usuário** o mesmo valor de **nome de usuário** do Microsoft Azure Active Directory. 

Para configurar e testar o logon único do Microsoft Azure Active Directory com o AWS (Amazon Web Services), conclua os seguintes blocos de construção:

1. [Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on) para habilitar seus usuários a usar esse recurso.
2. [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com Brenda Fernandes.
3. [Criar um usuário de teste do AWS (Amazon Web Services)](#create-an-amazon-web-services-aws-test-user) para ter um equivalente de Brenda Fernandes no AWS (Amazon Web Services) que esteja vinculado à representação do usuário no Microsoft Azure Active Directory.
4. [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que Brenda Fernandes use o logon único do Azure AD.
5. [Teste o logon único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você vai habilitar o logon único do Azure AD no Portal do Azure e configurar o logon único em seu aplicativo AWS (Amazon Web Services).

**Para configurar o logon único do Microsoft Azure Active Directory com o AWS (Amazon Web Services), execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **AWS (Amazon Web Services)**, selecione **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, na caixa **Modo**, selecione **Logon com base em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Na seção **Domínio e URLs do AWS (Amazon Web Services)**, o usuário não precisa seguir as etapas, pois o aplicativo já está pré-integrado ao Azure.

    ![Informações de logon único de domínio e URLs do AWS (Amazon Web Services)](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. O aplicativo AWS (Amazon Web Services) espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo:

    ![Configurar o atributo logon único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. Na seção **Atributos do Usuário**, na caixa **Logon único**, configure o atributo do token SAML como mostrado na imagem anterior e execute as seguintes etapas:
    
    | Nome do atributo  | Valor do atributo | Namespace |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Função            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Configure o provisionamento do usuário no Microsoft Azure Active Directory para buscar todas as funções do console do Amazon Web Services (AWS). Consulte as seguintes etapas de provisionamento.

    a. Para abrir a caixa de diálogo **Adicionar Atributo**, selecione **Adicionar Atributo**.

    ![Configurar o atributo logon único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Configurar o atributo logon único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. Na caixa **Nome**, digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor de atributo mostrado para essa linha.

    d. Na caixa **Namespace**, digite o valor de namespace mostrado para essa linha.
    
    d. Selecione **Ok**.

6. Na seção **Certificado de Autenticação SAML**, selecione **XML de Metadados**. Em seguida, salve o arquivo de metadados no computador.

    ![O link de download do certificado](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Selecione **Salvar**.

    ![Configurar botão Salvar no logon único](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. Em uma janela de navegador diferente, entre no site de sua empresa do AWS (Amazon Web Services) como administrador.

9. Selecione **Página inicial do Console**.
   
    ![Configurar página inicial de logon único][11]

10. Selecione **Gerenciamento de acesso e identidade**. 
   
    ![Configurar identidade de logon único][12]

11. Selecionar **Provedores de identidade**. Em seguida, selecione **Criar Provedor**. 
   
    ![Configurar provedor de logon único][13]

12. Na caixa de diálogo **Configurar provedor**, execute as seguintes etapas: 
   
    ![Configurar diálogo logon único][14]
 
    a. Em **Tipo de provedor**, selecione **SAML**.

    b. Na caixa **Nome do provedor**, digite um nome de provedor (por exemplo: *WAAD*).

    c. Para carregar seu **arquivo de metadados** baixado do Portal do Azure, selecione **Escolher Arquivo**.

    d. Selecione **Próxima etapa**.

13. Na caixa de diálogo **Verificar informações do provedor**, selecione **Criar**. 
    
    ![Configurar verificação de logon único][15]

14. Selecione **funções**. Em seguida, selecione **Criar Nova Função**. 
    
    ![Configurar funções de logon único][16]

15. Na caixa de diálogo **Definir Nome de Função**, execute as seguintes etapas: 
    
    ![Configurar nome de logon único][17] 

    a. Na caixa **Nome da função**, digite um nome de função (por exemplo, *TestUser*). 

    b. Selecione **Próxima etapa**.

16. Na caixa de diálogo **Selecionar Tipo de Função**, execute as seguintes etapas: 
    
    ![Configurar tipo de função de logon único][18] 

    a. Selecione **Função de acesso do provedor de identidade**. 

    b. Na seção **Conceder acesso de logon único da Web (WebSSO) a provedores SAML**, clique em **Selecionar**.

17. Na caixa de diálogo **Estabelecer Confiança**, execute as seguintes etapas:  
    
    ![Configurar confiança de logon único][19] 

    a. Selecione o provedor SAML criado anteriormente (por exemplo, *WAAD*). 
  
    b. Selecione **Próxima etapa**.

18. Na caixa de diálogo **Verificar Confiança na Função**, selecione **Próxima Etapa**. 
    
    ![Configurar confiança de função de logon único][32]

19. Na caixa de diálogo **Anexar Política**, selecione **Próxima Etapa**.  
    
    ![Configurar política de logon único][33]

20. Na caixa de diálogo **Revisão**, execute as seguintes etapas:   
    
    ![Configurar revisão de logon único][34] 

    a. Selecione **Criar Função**.

    b. Crie quantas funções forem necessárias e mapeie-as para o Provedor de Identidade.

21. Use as credenciais de conta de serviço Amazon Web Services (AWS) para buscar as funções da conta Amazon Web Services (AWS) no provisionamento de usuário do Microsoft Azure Active Directory. Para iniciar essa tarefa, abra a página inicial do console do Amazon Web Services (AWS).

22. Selecione **Serviços** > **Segurança, Identidade e Conformidade** > **IAM**.

    ![Buscar as funções da conta do Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. Na seção IAM, selecione a guia **Políticas**.

    ![Buscar as funções da conta do Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. Para criar uma nova política, selecione **Criar política**.

    ![Criar uma nova política](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. Para criar sua própria política para buscar todas as funções de contas Amazon Web Services (AWS), execute as seguintes etapas:

    ![Criar uma nova política](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. Na seção **Criar a política**, selecione a guia **JSON**.

    b. No documento de política, adicione o seguinte JSON:
    
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

    c. Para validar a política, clique no **botão Examinar política**.

    ![Definir a nova política](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)

26. Defina a **nova política** executando as etapas a seguir:

    ![Definir a nova política](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. Forneça o **Nome da política** como **AzureAD_SSOUserRole_Policy**.

    b. Você pode fornecer a seguinte **Descrição** para a política: **Essa política permitirá buscar as funções de contas AWS**.
    
    c. Selecione o botão **Criar Política**.
        
27. Para criar uma nova conta de usuário no serviço de IAM do Amazon Web Services (AWS), execute as etapas a seguir:

    a. Selecione **Usuários** no console do IAM do Amazon Web Services (AWS).

    ![Definir a nova política](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b. Selecione o botão **Adicionar usuário** para criar um novo usuário.

    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. Na seção **Adicionar usuário**, realize as etapas a seguir:
    
    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * Digite **AzureADRoleManager** na caixa de nome de usuário.
    
    * Em tipo de acesso, selecione a opção **Acesso programático**. Dessa forma, o usuário pode invocar as APIs e buscar as funções da conta Amazon Web Services (AWS).
    
    * Selecione o botão **Próximas permissões** no canto inferior direito.

28. Crie uma nova política para esse usuário executando as etapas a seguir:

    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. Selecione o botão **Anexar políticas existentes diretamente**.

    b. Pesquise a política recém-criada na seção filtro **AzureAD_SSOUserRole_Policy**.
    
    c. Selecione o bloco **política**. Selecione o botão **Próximo: Revisão**.

29. Examine a política para o usuário conectado executando as etapas a seguir:

    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. Examine o nome de usuário, tipo de acesso e política mapeados para o usuário.
    
    b. Para criar o usuário, selecione o botão **Criar usuário** botão no canto inferior direito.

30. Faça o download das credenciais de um usuário executando as etapas a seguir:

    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. Copie a **ID de chave de acesso** do usuário e a **Chave de acesso secreta**.
    
    b. Insira essas credenciais na seção de provisionamento de usuário do Microsoft Azure Active Directory para buscar as funções do console Amazon Web Services (AWS).
    
    c. Selecione o botão **Fechar** no canto inferior direito.

31. Navegue até a seção **Provisionamento de usuário** do aplicativo do Amazon Web Services (AWS) no Portal de Gerenciamento do Microsoft Azure Active Directory.

    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. Insira a **Chave de Acesso** e o **Segredo** nos campos **Segredo do Cliente** e **Token do Segredo** respectivamente.

    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. Insira a chave de acesso do usuário do Amazon Web Services (AWS) no campo **Segredo do cliente**.
    
    b. Insira o segredo do usuário do Amazon Web Services (AWS) no campo **Token Secreto**.
    
    c. Selecione o botão **Conexão de Teste**. Você deve poder testar corretamente essa conexão.

    d. Salve a configuração selecionando o botão **Salvar** na parte superior.
 
33. Certifique-se de mudar o Status de Provisionamento para **Ativado** nas **Configurações**. Isso é feito selecionando **Ativado** e, em seguida, selecionando o botão **Salvar** na parte superior.

    ![Adicionar usuário](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> É possível ler uma versão concisa dessas instruções no [Portal do Azure](https://portal.azure.com) enquanto você estiver configurando o aplicativo. Depois de adicionar este aplicativo por meio da seção **Active Directory > Aplicativos Empresariais**, selecione a guia **Logon Único**. Em seguida, acesse a documentação inserida por meio da seção **Configuração** na parte inferior. Você pode ler mais sobre o recurso de documentação inserida na [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no Azure AD, execute as seguintes etapas:**

1. No Portal do Azure, no painel esquerdo, selecione o botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. Para exibir uma lista de usuários, vá para **Usuários e grupos**. Selecione **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, selecione **Adicionar** na parte superior da caixa de diálogo **Todos os usuários**.

    ![O botão Adicionar](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo usuário](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Criar um usuário de teste do AWS (Amazon Web Services)

O objetivo desta seção é criar uma usuária chamada Brenda Fernandes no AWS (Amazon Web Services). O AWS (Amazon Web Services) não precisa que um usuário seja criado em seu sistema para o logon único, portanto você não precisa realizar nenhuma ação aqui.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao AWS (Amazon Web Services) para ela.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao AWS (Amazon Web Services), execute as seguintes etapas:**

1. No Portal do Azure, abra o modo de exibição de aplicativos. Depois disso, vá para a exibição de diretório e selecione **Aplicativos empresariais**. Em seguida, selecione **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Amazon Web Services (Amazon Web Services (AWS)**.

    ![O link do Amazon Web Services (AWS) na lista de aplicativos](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Selecione o botão **Adicionar**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

5. Na caixa de diálogo **Usuários e grupos**, selecione **Brenda Fernandes** na lista de usuários.

6. Na caixa de diálogo **Usuários e grupos**, clique no botão **Selecionar**. 

7. Na caixa de diálogo **Adicionar Atribuição**, selecione o botão **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao selecionar o bloco Amazon Web Services (AWS) no painel de acesso, você deve fazer logon automaticamente em seu aplicativo Amazon Web Services (AWS). Para saber mais sobre o painel de acesso, veja [Introdução ao painel de acesso](active-directory-saas-access-panel-introduction.md). 

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

