---
title: Autenticar com APIs REST do Mobile Engagement
description: Descreve como autenticar com APIs REST do Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 574e699a1cfca2caef0cf20872570bbb8650117b
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Autenticar com APIs REST do Mobile Engagement

## <a name="overview"></a>Visão geral

Este documento descreve como obter um token OAuth válido do Azure AD (Azure Active Directory) para se autenticar nas APIs REST do Mobile Engagement.

Esse procedimento pressupõe que você tenha uma assinatura válida do Azure e que criou um aplicativo do Mobile Engagement usando um dos [tutoriais do desenvolvedor](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Autenticação

Um token OAuth baseado no Microsoft Azure Active Directory é usado para autenticação. 

Para autenticar uma solicitação de API, um cabeçalho de autorização precisa ser adicionado a cada solicitação. O cabeçalho de autorização está no seguinte formato:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Tokens de Azure Active Directory expiram em uma hora.
> 
> 

Há várias maneiras de obter um token. Como as APIs são chamadas em um serviço de nuvem, recomendamos usar uma chave de API. Na terminologia do Azure, uma chave de API é chamada de uma senha da Entidade de Serviço. O procedimento a seguir descreve uma maneira de configurá-la manualmente.

### <a name="one-time-setup-using-a-script"></a>Configuração única (usando um script)

Para executar a configuração usando um script do PowerShell, execute as etapas nas instruções a seguir. Um script do PowerShell exige o mínimo de tempo para a instalação, mas usa os padrões mais permitidos. 

Opcionalmente, você também pode seguir as instruções da [configuração manual](mobile-engagement-api-authentication-manual.md) para fazer isso diretamente no portal do Azure. Ao configurar no portal do Azure, você pode fazer uma configuração mais detalhada.

1. Obtenha a última versão do Azure PowerShell [baixando-a](http://aka.ms/webpi-azps). Para obter mais informações sobre as instruções de download, consulte [esta visão geral](/powershell/azure/overview).

2. Depois de instalar o PowerShell, use os seguintes comandos para garantir que o **módulo do Azure** esteja instalado:

    a. Verifique se o módulo do Azure PowerShell está disponível na lista de módulos disponíveis.

        Get-Module –ListAvailable

    ![Módulos do Azure disponíveis][1]

    b. Se você não encontrar o módulo do Azure PowerShell na lista anterior, precisará executar:

        Import-Module Azure
3. Entre no Azure Resource Manager por meio do PowerShell executando o comando a seguir. Forneça o nome de usuário e a senha de sua conta do Azure: 

        Login-AzureRmAccount
4. Se você tem várias assinaturas, execute as seguintes etapas:

    a. Obtenha uma lista de todas as suas assinaturas. Em seguida, copie a **SubscriptionId** da assinatura que você deseja usar. Verifique se essa assinatura tem o aplicativo Mobile Engagement. Você usará esse aplicativo para interagir com as APIs. 

        Get-AzureRmSubscription

    b. Execute o comando a seguir. Forneça a **SubscriptionId** para configurar a assinatura que você pretende usar:

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Copie o texto do script [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) para o computador local. Em seguida, salve-o como um cmdlet do PowerShell (por exemplo, `APIAuth.ps1`) e executá-lo.

         `.\APIAuth.ps1`.

6. O script solicita que você forneça uma entrada para **principalName**. Forneça um nome adequado que você deseja usar para o aplicativo do Active Directory (por exemplo, APIAuth). 

7. Depois que o script conclui a execução, ele exibe os quatro valores a seguir. Lembre-se de copiá-los, porque você precisa deles para se autenticar de forma programática no Active Directory: 

   - **TenantId**
   - **SubscriptionId**
   - **ApplicationId**
   - **Segredo**

   Use TenantId como `{TENANT_ID}`, ApplicationId como `{CLIENT_ID}` e Secret como `{CLIENT_SECRET}`.

   > [!NOTE]
   > A política de segurança padrão pode impedir a execução de scripts do PowerShell. Nesse caso, use o seguinte comando para configurar temporariamente a política de execução para permitir a execução de script:
   > 
   > Set-ExecutionPolicy RemoteSigned
8. Esta é a aparência de um conjunto de cmdlets do PowerShell.
    ![Cmdlets do PowerShell][3]
9. No portal do Azure, acesse o Active Directory, selecione **Registros do aplicativo** e, em seguida, pesquise seu aplicativo para verificar se ele existe.
    ![Pesquisar o aplicativo][4]

### <a name="steps-to-get-a-valid-token"></a>Etapas para obter um token válido

1. Chame a API com os parâmetros a seguir. Lembre-se de substituir **TENANT\_ID**, **CLIENT\_ID** e **CLIENT\_SECRET**:
   
   * **URL de Solicitação** como `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`

   * **Cabeçalho HTTP Content-Type** como `application/x-www-form-urlencoded`
   
   * **Corpo da Solicitação HTTP** como `grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
    Segue um exemplo de solicitação:
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    Esta é uma resposta de exemplo:
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     Este exemplo inclui a codificação de URL dos parâmetros de POST, em que o valor `resource` é, na verdade, `https://management.core.windows.net/`. Tenha cuidado para também codificar a URL de `{CLIENT_SECRET}`, pois ela pode conter caracteres especiais.

     > [!NOTE]
     > Para o teste, use uma ferramenta de cliente HTTP, como o [Fiddler](http://www.telerik.com/fiddler) ou a [extensão Chrome Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop). 
     > 
     > 
2. Agora, em todas as chamadas à API, inclua o cabeçalho de solicitação de autorização:
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Se a solicitação retornar um código de status 401, verifique o corpo da resposta. Ele pode informá-lo de que o token expirou. Nesse caso, obtenha um novo token.

## <a name="use-the-apis"></a>Usar as APIs
Agora que você tem um token válido, você está pronto para fazer as chamadas à API.

1. Em cada solicitação de API, é necessário passar um token válido não expirado. Você obteve o token não expirado na seção anterior.

2. Conecte alguns parâmetros ao URI de solicitação que identifica seu aplicativo. A URI de solicitação é semelhante ao seguinte código:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Para obter os parâmetros, selecione o nome do aplicativo. Em seguida, selecione **Painel**. Você verá uma página com todos os três parâmetros, da seguinte maneira:
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** O nome do seu Grupo de recursos será **MobileEngagement**, a menos que você tenha criado um novo. 

> [!NOTE]
> Ignore o endereço raiz da API, pois ele servia apenas para as APIs anteriores.
> 
> Se você criou o aplicativo usando o portal do Azure, precisará usar o nome do Recurso do Aplicativo, que é diferente do Nome do Aplicativo em si. Se você criar o aplicativo no portal do Azure, deverá usar o Nome do Aplicativo. (Não há nenhuma diferenciação entre o Nome do Recurso do Aplicativo e o Nome do Aplicativo para aplicativos que são criados no novo portal.)
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



