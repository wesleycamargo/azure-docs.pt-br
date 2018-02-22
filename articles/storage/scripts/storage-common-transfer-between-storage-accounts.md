---
title: "Exemplo de script do Azure PowerShell – Migrar blobs entre contas de armazenamento usando o AzCopy no Windows | Microsoft Docs"
description: "Usando o AzCopy, copia o conteúdo de Blob de uma Conta de Armazenamento do Azure para outra."
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: rogarana
ms.openlocfilehash: 970315c5d597d691454f9dea0a76f2c0dc4a40ec
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Migrar blobs entre contas de armazenamento usando o AzCopy no Windows

Este exemplo copia todos os objetos de blob de uma conta de armazenamento de origem fornecida pelo usuário para uma conta de armazenamento de destino fornecida pelo usuário. 

Isso é feito usando o comando `Get-AzureStorageContainer`, que lista todos os contêineres em uma conta de armazenamento. O exemplo, em seguida, emite comandos do AzCopy, copiando cada contêiner da conta de armazenamento de origem para a conta de armazenamento de destino. Se ocorrerem falhas, o exemplo repetirá $retryTimes (o padrão é 3 e pode ser modificado com o parâmetro `-RetryTimes`). Se houver falha em cada tentativa, o usuário poderá executar o script novamente fornecendo o exemplo com o último contêiner copiado com êxito usando o parâmetro `-LastSuccessContainerName`. O exemplo, em seguida, continua copiando contêineres desse ponto.

Este exemplo exige o módulo do Armazenamento do Azure PowerShell versão **4.0.2** ou posterior. Você pode verificar a versão instalada usando `Get-Module -ListAvailable Azure.storage`. Se você precisa instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Esse exemplo também requer a versão mais recente do [AzCopy no Windows](http://aka.ms/downloadazcopy). O diretório de instalação padrão é `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`

Este exemplo usa um nome e chave de conta de armazenamento de origem, um nome e chave de conta de armazenamento de destino e o caminho de arquivo completo do AzCopy.exe (caso não esteja instalado no diretório padrão).

A seguir estão exemplos de entrada para este exemplo:

Se o AzCopy estiver instalado no diretório padrão:
```PowerShell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

Se o AzCopy não estiver instalado no diretório padrão:

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

Se ocorrer falha e o exemplo precisar ser executado novamente de um contêiner específico: 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para copiar dados de uma conta de armazenamento para outra. Cada item em que a tabela contém links para a documentação específica do comando.

| Get-Help | Observações |
|---|---|
| [Get-AzureStorageContainer](/powershell/module/azure.storage/Get-AzureStorageContainer) | Retorna os contêineres associados a essa conta de armazenamento. |
| [New-AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Cria um contexto do Armazenamento do Azure. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras adicionais de script do PowerShell de armazenamento podem ser encontradas em [Amostras do PowerShell para Armazenamento de Blobs do Azure](../blobs/storage-samples-blobs-powershell.md).
