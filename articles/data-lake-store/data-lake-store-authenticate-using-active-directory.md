---
title: "Autenticação de serviço a serviço: Data Lake Store com o Azure Active Directory | Microsoft Docs"
description: "Saiba como obter a autenticação de serviço a serviço com o Data Lake Store usando o Azure Active Directory"
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
ms.date: 01/10/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9019a4115e81a7d8f1960098b1138cd437a0460b
ms.openlocfilehash: dac6c9f3be7b4535f8cb30a9ec0c1e398ca5ff28


---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticação de serviço a serviço com o Data Lake Store usando o Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticação serviço a serviço](data-lake-store-authenticate-using-active-directory.md)
> * [Autenticação do usuário final](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

O Azure Data Lake Store usa o Azure Active Directory para autenticação. Antes de criar um aplicativo que funciona com o Azure Data Lake Store ou com o Azure Data Lake Analytics, primeiro você deve decidir como deseja autenticar seu aplicativo no Azure Active Directory (Azure AD). As duas principais opções disponíveis são:

* Autenticação do usuário final 
* Autenticação serviço a serviço (este artigo) 

As duas opções resultam no fornecimento de um token OAuth 2.0 a seu aplicativo, que é anexado a cada solicitação feita ao Azure Data Lake Store ou ao Azure Data Lake Analytics.

Este artigo fala sobre como criar um aplicativo Web do Azure AD para autenticação de serviço a serviço. Para obter instruções sobre a configuração de aplicativo do Azure AD para autenticação de usuário final, consulte [Autenticação de usuário final com o Data Lake Store usando o Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-active-directory-application"></a>Criar um aplicativo do Active Directory
Nesta seção aprendemos como criar e configurar um aplicativo Web do Azure AD para autenticação de serviço a serviço com o Azure Data Lake Store usando o Azure Active Directory. Observe que criar um "aplicativo do Active Directory" cria uma entidade de serviço para você, ele não cria realmente um aplicativo ou qualquer código.

### <a name="step-1-create-an-azure-active-directory-application"></a>Etapa 1: Criar um aplicativo do Azure Active Directory
1. Faça logon na sua conta do Azure por meio do [Portal Clássico](https://manage.windowsazure.com/).
2. Selecione **Active Directory** no painel à esquerda.
   
     ![selecionar Active Directory](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
3. Selecione o Active Directory que você deseja usar para criar o novo aplicativo. Se tiver mais de um Active Directory, você geralmente deseja criar o aplicativo no diretório em que está a sua assinatura. Você só pode conceder acesso a recurso em sua assinatura para aplicativos no mesmo diretório que sua assinatura.  
   
     ![escolher o diretório](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
4. Para exibir os aplicativos em seu diretório, clique em **Aplicativos**.
   
     ![exibir aplicativos](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)
5. Se você não criou um aplicativo nesse diretório antes, você verá algo semelhante à imagem a seguir. Clique em **ADICIONAR UM APLICATIVO**
   
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

#### <a name="note-down-the-following-properties-that-you-will-need-for-the-next-steps"></a>Anote as seguintes propriedades que você precisará para as próximas etapas:
1. Nome da ID do aplicativo Web criado na etapa 1.6 acima
2. A ID do cliente recuperado na etapa 2.2 acima
3. A chave criada na etapa 2.4 acima
4. A ID do locatário recuperado na etapa 2.5 acima

### <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>Etapa 3: Atribuir o aplicativo do Azure AD ao arquivo ou pasta da conta do Azure Data Lake Store (apenas para autenticação de serviços)
1. Faça logon no novo [portal do Azure](https://portal.azure.com) e abra a conta do Azure Data Lake Store que você deseja associar ao aplicativo do Azure Active Directory criado anteriormente.
2. Na folha de sua conta do Repositório Data Lake, clique em **Gerenciador de Dados**.
   
    ![Crie diretórios na conta do Data Lake Store](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "criar diretórios na conta Data Lake")
3. Na folha **Data Explorer**, clique no arquivo ou pasta para o qual você deseja fornecer acesso ao aplicativo do Azure AD e, em seguida, clique em **Acessar**. Para configurar o acesso a um arquivo, você deverá clicar em **Acessar** da folha **Visualização do Arquivo**.
   
    ![Configurar ACLs no sistema de arquivos do Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "definir ACLs no sistema de arquivos do Data Lake")
4. A folha **Acesso** lista o acesso padrão e o acesso personalizado já atribuídos à raiz. Clique no ícone **Adicionar** para adicionar as ACLs de nível personalizado.
   
    ![Lista de acesso padrão e personalizado](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "lista de acesso padrão e personalizado")
5. Clique no ícone **Adicionar** para abrir a folha **Adicionar Acesso Personalizado**. Nessa folha, clique em **Selecionar Usuário ou Grupo** e, na folha **Selecionar Usuário ou Grupo**, procure o aplicativo do Azure Active Directory que você criou anteriormente. Se houver muitos grupos para sua pesquisa, use a caixa de texto na parte superior para filtrar pelo nome do grupo. Clique no grupo que você deseja adicionar e clique em **Selecionar**.
   
    ![Adicionar um grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Adicionar um grupo")
6. Clique em **Selecionar Permissões**, selecione as permissões e se você desejar atribuir as permissões como uma ACL padrão, acessar a ACL ou ambos. Clique em **OK**.
   
    ![Atribuir permissões ao grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Atribuir permissões ao grupo")
   
    Para obter mais informações sobre permissões no Data Lake Store e ACLs de acesso/padrão, consulte [Controle de acesso no Data Lake Store](data-lake-store-access-control.md).
7. Na folha **Adicionar Acesso Personalizado**, clique em **OK**. O grupo recém-adicionado, com as permissões associadas, estará listado na folha **Acesso** .
   
    ![Atribuir permissões ao grupo](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Atribuir permissões ao grupo")    

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um aplicativo Web do Azure AD e reuniu as informações necessárias em seus aplicativos cliente, que você cria usando o SDK do .NET, SDK do Java, etc. Agora você pode prosseguir para os artigos seguintes, que falam sobre como usar o aplicativo Web do Azure AD para primeiro se autenticar no Data Lake Store e, em seguida, executar outras operações no repositório.

* [Introdução ao Repositório Azure Data Lake usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
* [Introdução ao Azure Data Lake Store usando o SDK do Java](data-lake-store-get-started-java-sdk.md)

Este artigo ensinou em detalhes as etapas básicas necessárias para obter um usuário principal para cima e em execução para o seu aplicativo. Você pode ver os seguintes artigos para obter mais informações:
* [Usar o PowerShell para criar entidade de serviço](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Usar autenticação de certificado para autenticação de entidade de serviço](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal#create-service-principal-with-certificate)
* [Outros métodos para autenticar no AD do Azure](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-authentication-scenarios)





<!--HONumber=Jan17_HO4-->


