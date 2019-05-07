---
title: Executar comandos da CLI do Azure ou do PowerShell com credenciais do Azure AD para acessar dados de blob ou fila | Microsoft Docs
description: PowerShell e CLI do Azure dão suporte a entrar com credenciais do Azure AD para executar comandos em dados de blob e filas do armazenamento do Azure. Um token de acesso é fornecido para a sessão e usado para autorizar operações de chamada. As permissões dependem da função RBAC atribuída à entidade de segurança do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 41ca1c5f413e5e15691f336d203edb918f21dc1a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147304"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Executar comandos da CLI do Azure ou do PowerShell com credenciais do Azure AD para acessar dados de blob ou fila

O armazenamento do Azure fornece extensões para a CLI do Azure e do PowerShell que permitem que você entrar e executar comandos de script com credenciais do Active Directory do Azure (AD Azure). Quando você entrar CLI do Azure ou o PowerShell com credenciais do Azure AD, é retornado um token de acesso OAuth 2.0. Esse token é usado automaticamente pela CLI ou o PowerShell para autorizar operações de dados subsequentes em relação ao armazenamento de Blob ou fila. Para operações com suporte, você não precisa passar uma chave de conta ou token SAS com o comando.

Você pode atribuir permissões aos dados de blob e fila para uma entidade de segurança do Azure AD por meio do controle de acesso baseado em função (RBAC). Para obter mais informações sobre as funções RBAC no armazenamento do Azure, consulte [gerenciar direitos de acesso a dados do armazenamento do Azure com o RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Operações com suporte

As extensões são suportadas para operações em contêineres e filas. Quais operações você pode chamar depende de permissões concedidas à entidade de segurança do Azure AD com o qual você entrar CLI do Azure ou PowerShell. Permissões para contêineres de Armazenamento do Microsoft Azure ou filas são atribuídas por meio do controle de acesso baseado em função (RBAC). Por exemplo, se você for atribuído a **leitor de dados de Blob** função, em seguida, você pode executar comandos de script que leiam dados de uma fila ou um contêiner. Se você for atribuído a **Colaborador de dados de Blob** função, em seguida, você pode executar comandos de script que ler, gravarem ou excluir um contêiner ou fila ou os dados que eles contêm. 

Para obter detalhes sobre as permissões para cada operação de Armazenamento do Microsoft Azure em um contêiner ou fila, veja [Permissões para chamar operações REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Chamar comandos da CLI usando credenciais do Azure AD

CLI do Azure é compatível com o `--auth-mode` parâmetro para operações de dados de blob e fila:

- Defina as `--auth-mode` parâmetro para `login` entrar usando uma entidade de segurança do Azure AD.
- Defina o `--auth-mode` parâmetro o valor `key` herdado para tentar consultar uma chave de conta, se nenhum parâmetro de autenticação para a conta for fornecido. 

O exemplo a seguir mostra como criar um contêiner em uma nova conta de armazenamento da CLI do Azure usando suas credenciais do Azure AD. Lembre-se de substituir os valores de espaço reservado entre colchetes angulares com seus próprios valores: 

1. Certifique-se de que você tenha instalado a versão da CLI do Azure 2.0.46 ou posterior. Execute `az --version` para verificar sua versão instalada.

1. Executar `az login` e autenticar na janela do navegador: 

    ```azurecli
    az login
    ```
    
1. Especifique a assinatura desejada. Criar um grupo de recursos usando [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Criar uma conta de armazenamento dentro desse grupo de recursos usando [criar conta de armazenamento az](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create): 

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```
    
1. Antes de criar o contêiner, atribua o [Colaborador de dados de Blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) função para si mesmo. Mesmo que você o proprietário da conta, você precisa de permissões explícitas para executar operações de dados na conta de armazenamento. Para obter mais informações sobre como atribuir funções RBAC, consulte [conceder acesso a dados do Azure blob e fila com o RBAC no portal do Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > As atribuições de função RBAC podem levar alguns minutos para propagar.
    
1. Chame o [criar contêiner de armazenamento az](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) com o `--auth-mode` parâmetro definido como `login` para criar o contêiner usando suas credenciais do Azure AD:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

A variável de ambiente associada ao `--auth-mode` parâmetro é `AZURE_STORAGE_AUTH_MODE`. Você pode especificar o valor apropriado na variável de ambiente para evitar incluí-lo em cada chamada de uma operação de dados do armazenamento do Azure.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Chamar comandos do PowerShell usando as credenciais do Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para usar o PowerShell do Azure para entrar e executar as operações subsequentes no armazenamento do Azure usando as credenciais do Azure AD, crie um contexto de armazenamento para fazer referência a conta de armazenamento e incluindo o `-UseConnectedAccount` parâmetro.

O exemplo a seguir mostra como criar um contêiner em uma nova conta de armazenamento do Azure PowerShell usando suas credenciais do Azure AD. Lembre-se de substituir os valores de espaço reservado entre colchetes angulares com seus próprios valores:

1. Entrar em sua assinatura do Azure com o `Connect-AzAccount` de comando e siga na tela instruções para inserir suas credenciais do Azure AD: 

    ```powershell
    Connect-AzAccount
    ```
    
1. Criar um grupo de recursos do Azure chamando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Criar uma conta de armazenamento chamando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Obter o contexto da conta de armazenamento que especifica a nova conta de armazenamento chamando [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Ao agir em uma conta de armazenamento, você pode referenciar o contexto em vez de passar repetidamente as credenciais. Incluir o `-UseConnectedAccount` parâmetro ao chamar qualquer operação de dados subsequentes usando suas credenciais do AD do Azure:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Antes de criar o contêiner, atribua o [Colaborador de dados de Blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) função para si mesmo. Mesmo que você o proprietário da conta, você precisa de permissões explícitas para executar operações de dados na conta de armazenamento. Para obter mais informações sobre como atribuir funções RBAC, consulte [conceder acesso a dados do Azure blob e fila com o RBAC no portal do Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > As atribuições de função RBAC podem levar alguns minutos para propagar.

1. Criar um contêiner chamando [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Como essa chamada usa o contexto criado nas etapas anteriores, o contêiner é criado usando suas credenciais do Azure AD. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as funções RBAC para o armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados do armazenamento com RBAC](storage-auth-aad-rbac.md).
- Para saber mais sobre como usar identidades gerenciadas de recursos do Azure com o armazenamento do Azure, consulte [autenticar o acesso a blobs e filas com o Azure Active Directory e identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md).
- Para saber como autorizar o acesso aos contêineres e filas de seus aplicativos de armazenamento, consulte [Use o Microsoft Azure Active Directory com aplicativos de armazenamento](storage-auth-aad-app.md).
