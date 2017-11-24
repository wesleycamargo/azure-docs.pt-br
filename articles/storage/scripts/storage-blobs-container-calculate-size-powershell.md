---
title: "Amostra de script do Azure PowerShell – calcular o tamanho do contêiner de blob | Microsoft Docs"
description: "Calcule o tamanho de um contêiner no Armazenamento de Blobs do Azure, totalizando o tamanho de cada um dos seus blobs."
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: f6f421e780bfbb7922a4b11f758330f2a9a0b84b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calcular o tamanho de um contêiner de Armazenamento de Blobs

Este script calcula o tamanho de um contêiner no Armazenamento de Blobs do Azure, totalizando o tamanho dos blobs no contêiner.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Este script do PowerShell fornece um tamanho estimado para o contêiner e não deve ser usado para cálculos de cobrança. Para obter um script que calcula o tamanho do contêiner para fins de cobrança, consulte [Calcular o tamanho de um contêiner do armazenamento de Blobs para fins de cobrança](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Execute o comando a seguir para remover o grupo de recursos, o contêiner e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para calcular o tamanho do contêiner de Armazenamento de Blobs. Cada item em que a tabela contém links para a documentação específica do comando.

| Command | Observações |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Obtém uma conta de armazenamento especificada ou todas as contas de armazenamento em um grupo de recursos ou na assinatura. |
| [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) | Lista os blobs em um contêiner. ||

## <a name="next-steps"></a>Próximas etapas

Para obter um script que calcula o tamanho do contêiner para fins de cobrança, consulte [Calcular o tamanho de um contêiner do armazenamento de Blobs para fins de cobrança](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras adicionais de script do PowerShell de armazenamento podem ser encontradas em [Amostras do PowerShell para Armazenamento do Azure](../blobs/storage-samples-blobs-powershell.md).
