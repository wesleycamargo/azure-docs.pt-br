<properties
    pageTitle="Pré-requisitos para acessar a API de relatório do Azure AD. | Microsoft Azure"
    description="Aprenda sobre os pré-requisitos para acessar a API de relatório do Azure AD"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>


# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Pré-requisitos para acessar a API de relatório do Azure AD 

As [APIs de relatório do Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação.

A API de relatório usa [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar o acesso às APIs da Web. 

Para preparar seu acesso à API de relatório, faça o seguinte:

1. Crie um aplicativo em seu locatário do Azure AD 

2. Conceda as permissões apropriadas do aplicativo para acessar os dados do Azure AD

3. Reúna as definições de configuração de seu diretório

Para dúvidas, problemas ou comentários, entre em contato com a [Ajuda de relatório do AAD](mailto:aadreportinghelp@microsoft.com).


## <a name="create-an-azure-ad-application"></a>Criar um aplicativo Azure AD

Para configurar seu diretório a fim de acessar a API de relatório do Azure AD, você deve entrar Portal Clássico do Azure com uma conta de administrador de assinatura do Azure que também é membro da função de diretório de Administrador Global em seu locatário do Azure AD.

> [AZURE.IMPORTANT] Aplicativos em execução com credenciais com privilégios de "admin" como esse podem ser muito poderosos, portanto certifique-se de proteger as credenciais de ID/segredo do aplicativo.


1. No [portal clássico do Azure](https://manage.windowsazure.com), no painel de navegação à esquerda, clique em **Active Directory**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Na lista de **diretórios ativos** , selecione o diretório.

3. No menu superior, clique em **Aplicativos**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Na barra de ferramentas inferior, clique em **Adicionar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/03.png) 

5. Na caixa de diálogo **O que você deseja fazer?**, clique em **Adicionar um aplicativo que minha organização esteja desenvolvendo**. 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/04.png) 


6. Na página de diálogo **Conte-nos sobre este usuário** , realize as seguintes etapas: 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/05.png) 

    a. Na caixa de texto **Nome** , digite um nome (por exemplo: Aplicativo de API de Relatório).

    b. Selecione **Aplicativo Web e/ou API Web**.

    c. Clique em **Próximo**.


7. Na caixa de diálogo **Propriedades de aplicativo** , execute as seguintes etapas: 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/06.png) 

    a. Na caixa de texto **URL de Entrada**, digite `https://localhost`.

    b. Na caixa de texto **URI da ID do Aplicativo**, digite ```https://localhost/ReportingApiApp```.

    c. Clique em **Concluído**.



## <a name="grant-your-application-permission-to-use-the-api"></a>Conceda permissão ao aplicativo para usar a API

1. No [portal clássico do Azure](https://manage.windowsazure.com/), no painel de navegação à esquerda, clique em **Active Directory**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Na lista de **diretórios ativos** , selecione o diretório.

3. No menu superior, clique em **Aplicativos**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/02.png)


3. Na lista de aplicativos, selecione o aplicativo recém-criado.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/07.png)

4. No menu na parte superior, clique em **Configurar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/08.png)


5. Na seção **Permissões para outros aplicativos**, para o recurso **Azure Active Directory**, clique na lista suspensa **Permissões de Aplicativo** e selecione **Ler dados de diretório**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/09.png)


5. Na barra inferior, clique em **Salvar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/10.png)


## <a name="gather-configuration-settings-from-your-directory"></a>Reúna as definições de configuração de seu diretório

Esta seção mostra como obter as seguintes configurações de seu diretório:

- Nome de domínio
- ID do cliente
- Segredo do cliente

Você precisa desses valores ao configurar chamadas para a API de relatórios. 


### <a name="get-your-domain-name"></a>Obter seu nome de domínio

1. No [portal clássico do Azure](https://manage.windowsazure.com), no painel de navegação à esquerda, clique em **Active Directory**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Na lista de **diretórios ativos** , selecione o diretório.

3. No menu na parte superior, clique em **Domínios**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/11.png) 

4. Na coluna **Nome de Domínio** copie seu nome de domínio no campo.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/12.png) 


### <a name="get-the-application's-client-id"></a>Obter a ID do cliente do aplicativo

1. No [portal clássico do Azure](https://manage.windowsazure.com), no painel de navegação à esquerda, clique em **Active Directory**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Na lista de **diretórios ativos** , selecione o diretório.

3. No menu superior, clique em **Aplicativos**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Na lista de aplicativos, selecione o aplicativo recém-criado.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/07.png)

4. No menu na parte superior, clique em **Configurar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/08.png)

4. Copie a **ID do Cliente**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/13.png)


### <a name="get-the-application's-client-secret"></a>Obter o segredo do cliente do aplicativo

Para obter o segredo do cliente do aplicativo, você precisa criar uma nova chave e salvar seu valor ao salvar a nova chave, pois não é possível recuperar este valor posteriormente.

1. No [portal clássico do Azure](https://manage.windowsazure.com), no painel de navegação à esquerda, clique em **Active Directory**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Na lista de **diretórios ativos** , selecione o diretório.

3. No menu superior, clique em **Aplicativos**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Na lista de aplicativos, selecione o aplicativo recém-criado.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/07.png)

4. No menu na parte superior, clique em **Configurar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/08.png)

5. Na seção **Chaves** , execute as seguintes etapas: 

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/14.png)

    a. Na lista duração, selecione uma duração

    b. Na barra inferior, clique em **Salvar**.

    ![Registrar aplicativo](./media/active-directory-reporting-api-prerequisites/10.png)

    c. Copie o valor da chave.

## <a name="next-steps"></a>Próximas etapas

- Você gostaria de acessar os dados da API de relatório do Azure AD de uma maneira programática? Confira [Introdução à API de Relatório do Azure Active Directory](active-directory-reporting-api-getting-started.md).

- Se você quiser saber mais sobre os relatórios do Azure Active Directory, confira o [Guia de relatórios do Azure Active Directory](active-directory-reporting-guide.md).  



<!--HONumber=Oct16_HO2-->


