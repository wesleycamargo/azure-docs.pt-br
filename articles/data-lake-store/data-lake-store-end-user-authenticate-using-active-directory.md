---
title: "Autenticação do usuário final: Data Lake Store com o Azure Active Directory | Microsoft Docs"
description: "Saiba como obter a autenticação do usuário final com o Data Lake Store usando o Azure Active Directory"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ec586ecd-1b42-459e-b600-fadbb7b80a9b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9019a4115e81a7d8f1960098b1138cd437a0460b
ms.openlocfilehash: a50fc687a1738a55c3d22eb3e12060397c162e06


---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticação do usuário final com o Data Lake Store usando o Azure Active Directory
> [!div class="op_single_selector"]
> * [Autenticação serviço a serviço](data-lake-store-authenticate-using-active-directory.md)
> * [Autenticação do usuário final](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

O Azure Data Lake Store usa o Azure Active Directory para autenticação. Antes de criar um aplicativo que funciona com o Azure Data Lake Store ou com o Azure Data Lake Analytics, primeiro você deve decidir como deseja autenticar seu aplicativo no Azure Active Directory (Azure AD). As duas principais opções disponíveis são:

* Autenticação do usuário final (este artigo)
* Autenticação serviço a serviço

As duas opções resultam no fornecimento de um token OAuth 2.0 a seu aplicativo, que é anexado a cada solicitação feita ao Azure Data Lake Store ou ao Azure Data Lake Analytics.

Este artigo fala sobre como criar um aplicativo Web do Azure AD para autenticação do usuário final. Para obter instruções sobre a configuração de aplicativo do Azure AD para autenticação serviço a serviço, consulte [Autenticação serviço a serviço com o Data Lake Store usando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* A ID de sua assinatura. Você pode habilitá-la no Portal do Azure. Por exemplo, está disponível na folha da conta do Data Lake Store.
  
    ![Obter ID da assinatura](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)
* O nome de domínio do Azure AD. Você pode recuperá-lo passando o mouse sobre o canto superior direito do Portal do Azure. Na captura de tela abaixo, o nome de domínio é **contoso.microsoft.com** e o GUID entre colchetes é a ID de locatário. 
  
    ![Obter domínio do AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>Autenticação do usuário final
Essa é a abordagem recomendada se você quer que um usuário final entre em seu aplicativo por meio do Azure AD. Seu aplicativo será capaz de acessar recursos do Azure com o mesmo nível de acesso do usuário final que fez logon. O usuário final precisará fornecer suas credenciais periodicamente para manter o acesso de seu aplicativo.

O resultado de fazer o usuário final entrar é que seu aplicativo recebe um token de acesso e um token de atualização. O token de acesso é anexado a cada solicitação feita ao Data Lake Store ou ao Data Lake Analytics e é válido por uma hora por padrão. O token de atualização pode ser usado para obter um novo token de acesso e é válido por até duas semanas, por padrão, se usado regularmente. Você pode usar duas abordagens diferentes para o logon do usuário final.

### <a name="using-the-oauth-20-pop-up"></a>Usando o pop-up OAuth 2.0
Seu aplicativo pode disparar um pop-up de autorização OAuth 2.0 no qual o usuário final pode inserir suas credenciais. Essa janela pop-up também funciona com o processo de autenticação do Azure AD de dois fatores (2FA), se necessário. 

> [!NOTE]
> Esse método ainda não tem suporte na ADAL (biblioteca de autenticação do Azure AD) para Python ou Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Transmitindo credenciais do usuário diretamente
Seu aplicativo pode fornecer credenciais de usuário ao Azure AD diretamente. Esse método só funciona com contas de usuário de IDs organizacionais. Ele não é compatível com contas de usuário "live ID"/pessoais, incluindo as terminadas em @outlook.com ou @live.com. Além disso, esse método não é compatível com contas de usuário que requerem autenticação do Azure AD de dois fatores (2FA).

### <a name="what-do-i-need-to-use-this-approach"></a>O que é necessário para usar essa abordagem?
* Nome de domínio do Azure AD. Já está listado no pré-requisito deste artigo.
* **Aplicativo Web**do Azure AD
* ID do cliente para o aplicativo Web do Azure AD
* URI de resposta para o aplicativo Web do Azure AD
* Definir permissões delegadas

Para obter instruções sobre como criar um aplicativo Web do Azure AD configurá-lo para os requisitos listados acima, consulte a seção [Criar um aplicativo do Active Directory](#create-an-active-directory-application) abaixo. 

## <a name="create-an-active-directory-application"></a>Criar um aplicativo do Active Directory
Nesta seção aprendemos como criar e configurar um aplicativo Web do Azure AD para autenticação do usuário final com o Azure Data Lake Store usando o Azure Active Directory.

### <a name="step-1-create-an-azure-active-directory-application"></a>Etapa 1: Criar um aplicativo do Azure Active Directory
> [!NOTE]
> As etapas abaixo utilizam o Portal do Azure. Você também pode criar um aplicativo do Azure AD usando o [Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md) ou o [CLI do Azure](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md).
> 
> 

1. Faça logon na sua conta do Azure por meio do [Portal Clássico](https://manage.windowsazure.com/).
2. Selecione **Active Directory** no painel à esquerda.
   
     ![selecionar Active Directory](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory.png)
3. Selecione o Active Directory que você deseja usar para criar o novo aplicativo. Se tiver mais de um Active Directory, você geralmente deseja criar o aplicativo no diretório em que está a sua assinatura. Você só pode conceder acesso a recurso em sua assinatura para aplicativos no mesmo diretório que sua assinatura.  
   
     ![escolher o diretório](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory-details.png)
4. Para exibir os aplicativos em seu diretório, clique em **Aplicativos**.
   
     ![exibir aplicativos](./media/data-lake-store-end-user-authenticate-using-active-directory/view-applications.png)
5. Se você não criou um aplicativo nesse diretório antes de você verá algo semelhante à imagem a seguir. Clique em **ADICIONAR UM APLICATIVO**
   
     ![Adicionar aplicativo](./media/data-lake-store-end-user-authenticate-using-active-directory/create-application.png)
   
     Ou então, clique em **Adicionar** no painel inferior.
   
     ![adicionar](./media/data-lake-store-end-user-authenticate-using-active-directory/add-icon.png)
6. Forneça um nome para o aplicativo e selecione o tipo de aplicativo que você deseja criar. Para este tutorial, crie um **APLICATIVO WEB E/OU API WEB** e clique no botão Avançar.
   
     ![nomear aplicativo](./media/data-lake-store-end-user-authenticate-using-active-directory/tell-us-about-your-application.png)
7. Preencha as propriedades de seu aplicativo. Para a **URL DE ENTRADA**, forneça o URI para um site da Web que descreve seu aplicativo. A existência do site Web não é validada. 
   Para **URI DE ID DO APLICATIVO**, forneça o URI que identifica seu aplicativo.
   
     ![propriedades do aplicativo](./media/data-lake-store-end-user-authenticate-using-active-directory/app-properties.png)
   
    Clique na marca de seleção para concluir o assistente e criar o aplicativo.

### <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>Etapa 2: Obter ID do cliente, URI de resposta e definir permissões delegadas
1. Clique na guia **Configurar** para definir a senha do aplicativo.
   
     ![configurar o aplicativo](./media/data-lake-store-end-user-authenticate-using-active-directory/application-configure.png)
2. Copie a **ID do CLIENTE**.
   
     ![ID do CLIENTE](./media/data-lake-store-end-user-authenticate-using-active-directory/client-id.png)
3. Sob a seção **Logon único**, copie a **URI de resposta**.
   
    ![ID do CLIENTE](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-get-reply-uri.png)
4. Sob **Permissões para outros aplicativos**, clique em **Adicionar aplicativo**
   
    ![ID do CLIENTE](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)
5. No assistente **Permissões para outros aplicativos**, selecione **Azure Data Lake** e **Windows** **API de Gerenciamento de Serviços do Azure** e clique na marca de seleção.
6. Por padrão o **Permissões delegadas** para os serviços adicionados recentemente é definido como zero. Clique no menu suspenso **Permissões delegadas** do Azure Data Lake e do Serviço de gerenciamento do Microsoft Azure e selecione as caixas de seleção disponíveis para definir os valores como 1. O resultado deve ter essa aparência.
   
     ![ID do CLIENTE](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
7. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você criou um aplicativo Web do Azure AD e reuniu as informações necessárias em seus aplicativos cliente, que você cria usando o SDK do .NET, SDK do Java, etc. Agora você pode prosseguir para os artigos seguintes, que falam sobre como usar o aplicativo Web do Azure AD para primeiro se autenticar no Data Lake Store e, em seguida, executar outras operações no repositório.

* [Introdução ao Repositório Azure Data Lake usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
* [Introdução ao Azure Data Lake Store usando o SDK do Java](data-lake-store-get-started-java-sdk.md)




<!--HONumber=Jan17_HO4-->


