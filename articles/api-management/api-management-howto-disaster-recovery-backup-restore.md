---
title: Implementar a recuperação de desastre usando backup e restauração no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como usar o backup e restauração para executar a recuperação de desastres no Gerenciamento de API no Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: apimpm
ms.openlocfilehash: 683efc6f772337754fc21a1e486d35b7f92e8f81
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49428242"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Como implementar a recuperação de desastre usando o backup de serviço e restaurar no Gerenciamento de API no Azure

Ao escolher publicar e gerenciar suas APIs através do Gerenciamento de API do Azure, você está tirando proveito de muitos recursos de tolerância e infraestrutura que você precisaria criar, implementar e gerenciar. A plataforma Azure atenua grande parte das falhas potenciais a uma fração do custo.

Para se recuperar de problemas de disponibilidade que afetam a região onde o serviço de Gerenciamento de API está hospedado, você deve estar pronto para reconstituir seu serviço em outra região a qualquer momento. Dependendo de suas metas de disponibilidade e do objetivo de tempo de recuperação, recomendamos reservar um serviço de backup em uma ou mais regiões e tentar manter sua configuração e seu conteúdo em sincronização com o serviço ativo. O recurso de “backup e restauração” do serviço fornece o bloco de construção necessário para implementar sua estratégia de recuperação de desastre.

Este guia mostra como autenticar solicitações do Azure Resource Manager e como fazer backup e restaurar suas instâncias de serviço do Gerenciamento de API.

> [!NOTE]
> O processo de backup e restauração de uma instância do serviço Gerenciamento de API para recuperação de desastres também pode ser usado para replicar as instâncias de serviço Gerenciamento de API para cenários como preparação.
>
> Cada backup expira após 30 dias. Se você tentar restaurar um backup após o período de expiração de 30 dias, a restauração falhará com uma mensagem `Cannot restore: backup expired`.
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Autenticação de solicitações do gerenciador de recursos do Azure

> [!IMPORTANT]
> A API REST para backup e restauração usa o Gerenciador de Recursos do Azure e tem um mecanismo de autenticação diferentes das APIs REST para gerenciar suas entidades de Gerenciamento de API. As etapas desta seção descrevem como autenticar solicitações do Gerenciador de Recursos do Azure. Para mais informações, consulte [Autenticação de solicitações do Gerenciador de Recursos do Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Todas as tarefas realizadas em recursos com o Azure Resource Manager precisam ser autenticadas com o Azure Active Directory usando as seguintes etapas:

* Adicione um aplicativo no locatário do Active Directory do Azure.
* Defina permissões para o aplicativo que você adicionou.
* Obtenha o token para autenticar solicitações ao Gerenciador de Recursos do Azure.

### <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo do Azure Active Directory

1. Entre no [Portal do Azure](https://portal.azure.com). 
2. Usando a assinatura que contém sua instância de serviço de gerenciamento de API, navegue até a guia **registros de Aplicativo** em **Azure Active Directory** (Azure Active Directory > registros de Aplicativo/Gerenciar).

    > [!NOTE]
    > Se o diretório padrão do Active Directory do Azure não é visível para sua conta, contate o administrador da assinatura do Azure para conceder as permissões necessárias para sua conta.
3. Clique em **Novo registro de aplicativo**.

    A janela **Criar** é exibida à direita. É nela em que você pode inserir as informações relevantes do aplicativo do AAD.
4. Insira um nome para o aplicativo.
5. Para o tipo de aplicativo, selecione **Nativo**.
6. Insira uma URL de espaço reservado como `http://resources` para o **URI de redirecionamento**, que é um campo obrigatório, mas o valor não é usado posteriormente. Clique na caixa de seleção para salvar o aplicativo.
7. Clique em **Criar**.

### <a name="add-an-application"></a>Adicionar um aplicativo

1. Depois que o aplicativo for criado, clique em **Configurações**.
2. Clique em **Permissões necessárias**.
3. Clique em **+Adicionar**.
4. Pressione **Selecionar uma API**.
5. Escolha **Windows** **API de Gerenciamento de Serviços do Azure**.
6. Pressione **Selecionar**. 

    ![Adicionar permissões](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Clique em **Permissões Delegadas** ao lado do aplicativo recém-adicionado e marque a caixa de **Acessar o Gerenciamento de Serviços do Azure (versão prévia)**.
8. Pressione **Selecionar**.
9. Clique em **Conceder Permissões**.

### <a name="configuring-your-app"></a>Configurando seu aplicativo

Antes de chamar as APIs que geram o backup e o restauram, é necessário obter um token. O exemplo a seguir usa o pacote NuGet [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) para recuperar o token.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Substitua `{tentand id}`, `{application id}` e `{redirect uri}` usando as seguintes instruções:

1. Substitua `{tenant id}` pela ID de locatário do aplicativo do Azure Active Directory criado. Acesse a ID clicando em **Registros do aplicativo** -> **Pontos de Extremidade**.

    ![Pontos de extremidade][api-management-endpoint]
2. Substitua `{application id}` pelo valor obtido navegando para a página **Configurações**.
3. Substitua a guia `{redirect uri}` com o valor das **URIs de Redirecionamento** pelo aplicativo do Azure Active Directory.

    Depois que os valores são especificados, o exemplo de código deve retornar um token semelhante ao seguinte exemplo:

    ![A criptografia do token][api-management-arm-token]

    > [!NOTE]
    > O token pode expirar após um período determinado. Execute o código de exemplo novamente para gerar um novo token.

## <a name="calling-the-backup-and-restore-operations"></a>Chamando as operações de backup e restauração

As APIs REST são [Serviço de Gerenciamento de Api - Backup](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/apimanagementservice_backup) e [Serviço de Gerenciamento de Api - Restauração](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/apimanagementservice_restore).

Antes de chamar as operações de “backup e restauração” descritas nas seções a seguir, defina o cabeçalho de solicitação de autorização para a chamada REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Fazer backup de um serviço de Gerenciamento de API
Para fazer backup de um serviço de Gerenciamento de API, execute a seguinte solicitação HTTP:

```
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

onde:

* `subscriptionId` – ID da assinatura que contém o serviço de Gerenciamento de API do qual você está tentando fazer backup
* `resourceGroupName` -nome do grupo de recursos do seu serviço de Gerenciamento de API do Azure
* `serviceName` - o nome do serviço de Gerenciamento de API que você está fazendo um backup em específico, no momento de sua criação
* `api-version` - substitua por `2018-06-01-preview`

No corpo da solicitação, especifique o nome da conta de armazenamento de destino do Azure, a chave de acesso, o nome do contêiner Blob e o nome de backup:


```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

Defina o valor do cabeçalho de solicitação `Content-Type` para `application/json`.

O backup é uma operação longa de execução que pode levar vários minutos para concluir.  Se a solicitação foi bem-sucedida e o processo de backup foi iniciado, você receberá um código de status de resposta `202 Accepted` com um cabeçalho `Location`.  Faça solicitações “GET” para a URL no cabeçalho do `Location` para descobrir o status da operação. Enquanto o backup está em andamento, você continua recebendo um código de status “202 Aceito”. Um Código de resposta `200 OK` indica a conclusão bem-sucedida da operação de backup.

Observe as restrições a seguir ao fazer uma solicitação de backup.

* O **contêiner** especificado no corpo solicitado **tem que existir**.
* Enquanto o backup está em andamento, você **não deve tentar quaisquer operações de gerenciamento de serviço** , como atualização ou downgrade de SKU, alteração do nome do domínio, etc.
* A restauração de um **backup é garantida somente por 30 dias** desde o momento de sua criação.
* Os **dados de uso** utilizados para a criação de relatórios de análise **não estão incluídos** no backup. Use o [API REST de Gerenciamento de API do Azure][Azure API Management REST API] para recuperar periodicamente os relatórios analíticos por questões de segurança.
* A frequência com que você executa backups de serviço afeta seu objetivo de ponto de recuperação. Para minimizá-la, a recomendação é implementar backups regulares, bem como executar backups sob demanda depois de fazer alterações importantes no serviço de Gerenciamento de API.
* As **alterações** feitas na configuração do serviço (por exemplo, APIs, políticas, aparência do portal do desenvolvedor) enquanto uma operação de backup está em andamento **podem não ser incluídas no backup e, portanto, serão perdidas**.

### <a name="step2"> </a>Restaurar um serviço de Gerenciamento de API
Para restaurar um serviço de Gerenciamento de API de um backup criado anteriormente faz a seguinte solicitação HTTP:

```
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

onde:

* `subscriptionId` - ID da assinatura contendo o serviço de Gerenciamento de API que você está restaurando um backup em
* `resourceGroupName` – nome do grupo de recursos contendo o serviço de Gerenciamento de API do Azure em que você está restaurando um backup
* `serviceName` - o nome do serviço de Gerenciamento de API para o qual está sendo realizada a restauração, especificado no momento de sua criação
* `api-version` - substitua por `2018-06-01-preview`

No corpo da solicitação, especifique o local do arquivo de backup, ou seja, o nome da conta de armazenamento do Azure, a chave de acesso, o nome do contêiner de blobs e o nome do backup:

```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

Defina o valor do cabeçalho de solicitação `Content-Type` para `application/json`.

Restaure uma operação longa de execução que pode levar até 30 minutos ou mais para concluir. Se a solicitação for bem-sucedida e o processo de restauração for iniciado, você receberá um código de status de resposta `202 Accepted` com um cabeçalho `Location`. Faça solicitações “GET” para a URL no cabeçalho do `Location` para descobrir o status da operação. Enquanto a restauração está em andamento, você continua recebendo o código de status “202 Aceito”. Um código de resposta `200 OK` indica a conclusão bem-sucedida da operação de restauração.

> [!IMPORTANT]
> **A SKU** do serviço que está sendo restaurada **precisa corresponder** à SKU do serviço copiado em backup que está sendo restaurada.
>
> As **alterações** feitas na configuração do serviço (por exemplo, APIs, políticas, aparência do portal do desenvolvedor) enquanto uma operação de restauração está em andamento **podem ser substituídas**.

> [!NOTE]
> Operações de backup e restauração também podem ser executadas com os comandos PowerShell *Backup-AzureRmApiManagement* e *Restore-AzureRmApiManagement*, respectivamente.

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes recursos para diferentes orientações do processo de backup/restauração.

* [Replicar contas de Gerenciamento de API do Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
* [Automatizar backup e restauração do Gerenciamento de API com Aplicativos Lógicos](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
* [Gerenciamento de API do Azure: Fazendo backup e restaurando a configuração](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  *A abordagem detalhada por Stuart não corresponde com as diretrizes oficiais, mas é interessante.*

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2

[Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
