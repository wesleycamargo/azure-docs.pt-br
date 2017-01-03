---
title: Autenticar com o Data Lake Store usando o Active Directory | Microsoft Docs
description: Saiba como autenticar com o Data Lake Store usando o Active Directory
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 820b7c5d-4863-4225-9bd1-df4d8f515537
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 35cde786bbc091c58f4dcb341cd47ce4c4f4b46c
ms.openlocfilehash: 02e52c3aba82ab8e3a8b1dc921731c29e505e23e


---
# <a name="service-to-serivce-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticação de serviço a serviço com o Data Lake Store usando o Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticação serviço a serviço](data-lake-store-authenticate-using-active-directory.md)
> * [Autenticação do usuário final](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

O Azure Data Lake Store usa o Azure Active Directory para autenticação. Antes de criar um aplicativo que funciona com o Azure Data Lake Store ou com o Azure Data Lake Analytics, primeiro você deve decidir como deseja autenticar seu aplicativo no Azure Active Directory (Azure AD). As duas principais opções disponíveis são:

* Autenticação de usuário final e 
* Autenticação serviço a serviço. 

As duas opções resultam no fornecimento de um token OAuth 2.0 a seu aplicativo, que é anexado a cada solicitação feita ao Azure Data Lake Store ou ao Azure Data Lake Analytics.

Este artigo fala sobre como criar um aplicativo Web do Azure AD para autenticação de serviço a serviço. Para obter instruções sobre a configuração de aplicativo do Azure AD para autenticação de usuário final, consulte [Autenticação de usuário final com o Data Lake Store usando o Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* A ID de sua assinatura. Você pode habilitá-la no Portal do Azure. Por exemplo, está disponível na folha da conta do Data Lake Store.
  
    ![Obter ID da assinatura](./media/data-lake-store-authenticate-using-active-directory/get-subscription-id.png)
* O nome de domínio do Azure AD. Você pode recuperá-lo passando o mouse sobre o canto superior direito do Portal do Azure. Na captura de tela abaixo, o nome de domínio é **contoso.microsoft.com** e o GUID entre colchetes é a ID de locatário. 
  
    ![Obter domínio do AAD](./media/data-lake-store-authenticate-using-active-directory/get-aad-domain.png)

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço
Essa é a abordagem recomendada se você deseja que seu aplicativo autentique automaticamente no Azure AD, sem a necessidade de um usuário final fornecendo suas credenciais. Seu aplicativo será capaz de se autenticar desde que suas credenciais sejam válidas, que podem ser personalizadas para durar anos.

### <a name="what-do-i-need-to-use-this-approach"></a>O que é necessário para usar essa abordagem?
* Nome de domínio do Azure AD. Já está listado no pré-requisito deste artigo.
* **Aplicativo web**do Azure AD.
* ID do cliente para o aplicativo Web do Azure AD.
* Segredo do cliente para o aplicativo Web do Azure AD.
* Ponto de extremidade do token para o aplicativo Web do Azure AD.
* Habilite o acesso para o aplicativo Web do Azure AD no arquivo/pasta do Data Lake Store ou na conta do Data Lake Analytics em que você deseja trabalhar.

Para obter instruções sobre como criar um aplicativo Web do Azure AD configurá-lo para os requisitos listados acima, consulte a seção [Criar um aplicativo do Active Directory](#create-an-active-directory-application) abaixo.

> [!NOTE]
> Por padrão, o aplicativo do Azure AD é configurado para usar o segredo do cliente, que você pode recuperar do aplicativo do Azure AD. No entanto, se desejar que o aplicativo do Azure AD use um certificado em vez disso, crie o aplicativo Web do Azure AD usando o Azure PowerShell, conforme descrito em [Criar uma entidade de serviço com certificado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).
> 
> 

## <a name="create-an-active-directory-application"></a>Criar um aplicativo do Active Directory
Nesta seção aprendemos como criar e configurar um aplicativo Web do Azure AD para autenticação de serviço a serviço com o Azure Data Lake Store usando o Azure Active Directory. 

### <a name="step-1-create-an-azure-active-directory-application"></a>Etapa 1: Criar um aplicativo do Azure Active Directory
> [!NOTE]
> As etapas abaixo utilizam o Portal do Azure. Você também pode criar um aplicativo do Azure AD usando o [Azure PowerShell](../resource-group-authenticate-service-principal.md) ou o [CLI do Azure](../resource-group-authenticate-service-principal-cli.md).
> 
> 

1. Faça logon na sua conta do Azure por meio do [Portal Clássico](https://manage.windowsazure.com/).
2. Selecione **Active Directory** no painel à esquerda.
   
     ![selecionar Active Directory](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
3. Selecione o Active Directory que você deseja usar para criar o novo aplicativo. Se tiver mais de um Active Directory, você geralmente deseja criar o aplicativo no diretório em que está a sua assinatura. Você só pode conceder acesso a recurso em sua assinatura para aplicativos no mesmo diretório que sua assinatura.  
   
     ![escolher o diretório](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
4. Para exibir os aplicativos em seu diretório, clique em **Aplicativos**.
   
     ![exibir aplicativos](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)
5. Se você não criou um aplicativo nesse diretório antes de você verá algo semelhante à imagem a seguir. Clique em **ADICIONAR UM APLICATIVO**
   
     ![Adicionar aplicativo](./media/data-lake-store-authenticate-using-active-directory/create-application.png)
   
     Ou então, clique em **Adicionar** no painel inferior.
   
     ![adicionar](./media/data-lake-store-authenticate-using-active-directory/add-icon.png)
6. Forneça um nome para o aplicativo e selecione o tipo de aplicativo que você deseja criar. Para este tutorial, crie um **APLICATIVO WEB E/OU API WEB** e clique no botão Avançar.
   
     ![nomear aplicativo](./media/data-lake-store-authenticate-using-active-directory/tell-us-about-your-application.png)

    > [!TIP]
    > Forneça um nome que facilite a busca pelo aplicativo posteriormente. Mais adiante neste tutorial, você vai procurar esse aplicativo e atribui-lo a uma conta do Data Lake Store.
    > 
    > 

7. Preencha as propriedades de seu aplicativo. Para a **URL DE ENTRADA**, forneça o URI para um site da Web que descreve seu aplicativo. A existência do site Web não é validada. 
   Para **URI DE ID DO APLICATIVO**, forneça o URI que identifica seu aplicativo.
   
     ![propriedades do aplicativo](./media/data-lake-store-authenticate-using-active-directory/app-properties.png)
   
    Clique na marca de seleção para concluir o assistente e criar o aplicativo.

### <a name="step-2-get-client-id-client-secret-and-token-endpoint"></a>Etapa 2: Obter a id do cliente, o segredo do cliente e o ponto de extremidade do token
Ao fazer logon por meio de programação, você precisa da id para seu aplicativo. Se o aplicativo for executado com suas próprias credenciais, você também precisará de uma chave de autenticação.

1. Clique na guia **Configurar** para definir a senha do aplicativo.
   
     ![configurar o aplicativo](./media/data-lake-store-authenticate-using-active-directory/application-configure.png)
2. Copie a **ID do CLIENTE**.
   
     ![ID do CLIENTE](./media/data-lake-store-authenticate-using-active-directory/client-id.png)
3. Se o aplicativo for executado com suas próprias credenciais, role para baixo até a seção **Chaves** e selecione por quanto tempo você deseja que a senha seja válida.
   
     ![chaves](./media/data-lake-store-authenticate-using-active-directory/create-key.png)
4. Selecione **Salvar** para criar a chave.
   
    ![Salvar](./media/data-lake-store-authenticate-using-active-directory/save-icon.png)
   
    A chave salva é exibida e você pode copiá-la. Você não poderá recuperar a chave mais tarde; portanto, copie-a agora.
   
    ![chave salva](./media/data-lake-store-authenticate-using-active-directory/save-key.png)
5. Recupere o ponto de extremidade do token selecionando **Exibir pontos de extremidade** na parte inferior da tela e recuperando o valor para **Ponto de Extremidade do Token OAuth 2.0**, como mostrado abaixo.  
   
    ![ID do locatário](./media/data-lake-store-authenticate-using-active-directory/save-tenant.png)

### <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>Etapa 3: Atribuir o aplicativo do Azure AD ao arquivo ou pasta da conta do Azure Data Lake Store (apenas para autenticação de serviços)
1. Faça logon no novo [Portal do Azure](https://portal.azure.com) e abra a conta do Azure Data Lake Store que você deseja associar ao aplicativo do Azure Active Directory criado anteriormente.
2. Na folha de sua conta do Repositório Data Lake, clique em **Gerenciador de Dados**.
   
    ![Criar diretórios na conta do repositório Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Create directories in Data Lake account")
3. Na folha **Data Explorer**, clique no arquivo ou pasta para o qual você deseja fornecer acesso ao aplicativo do Azure AD e, em seguida, clique em **Acessar**. Para configurar o acesso a um arquivo, você deverá clicar em **Acessar** da folha **Visualização do Arquivo**.
   
    ![Definir ACLs no sistema de arquivos do Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Set ACLs on Data Lake file system")
4. A folha **Acesso** lista o acesso padrão e o acesso personalizado já atribuídos à raiz. Clique no ícone **Adicionar** para adicionar as ACLs de nível personalizado.
   
    ![Listar acesso padrão e personalizado](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "List standard and custom access")
5. Clique no ícone **Adicionar** para abrir a folha **Adicionar Acesso Personalizado**. Nessa folha, clique em **Selecionar Usuário ou Grupo** e, na folha **Selecionar Usuário ou Grupo**, procure o aplicativo do Azure Active Directory que você criou anteriormente. Se houver muitos grupos para sua pesquisa, use a caixa de texto na parte superior para filtrar pelo nome do grupo. Clique no grupo que você deseja adicionar e clique em **Selecionar**.
   
    ![Adicionar um grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Add a group")
6. Clique em **Selecionar Permissões**, selecione as permissões e se você desejar atribuir as permissões como uma ACL padrão, acessar a ACL ou ambos. Clique em **OK**.
   
    ![Atribuir permissões ao grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Assign permissions to group")
   
    Para obter mais informações sobre permissões no Data Lake Store e ACLs de acesso/padrão, consulte [Controle de acesso no Data Lake Store](data-lake-store-access-control.md).
7. Na folha **Adicionar Acesso Personalizado**, clique em **OK**. O grupo recém-adicionado, com as permissões associadas, estará listado na folha **Acesso** .
   
    ![Atribuir permissões ao grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Assign permissions to group")    

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um aplicativo Web do Azure AD e reuniu as informações necessárias em seus aplicativos cliente, que você cria usando o SDK do .NET, SDK do Java, etc. Agora você pode prosseguir para os artigos seguintes, que falam sobre como usar o aplicativo Web do Azure AD para primeiro se autenticar no Data Lake Store e, em seguida, executar outras operações no repositório.

* [Introdução ao Repositório Azure Data Lake usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
* [Introdução ao Azure Data Lake Store usando o SDK do Java](data-lake-store-get-started-java-sdk.md)




<!--HONumber=Nov16_HO5-->


