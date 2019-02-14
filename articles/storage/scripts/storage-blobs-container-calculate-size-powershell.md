---
title: Amostra de script do Azure PowerShell – calcular o tamanho do contêiner de blob | Microsoft Docs
description: Calcule o tamanho de um contêiner no Armazenamento de Blobs do Azure, totalizando o tamanho de cada um dos seus blobs.
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: ec8254478786825d0333e8be24b0dfbea8c18e60
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245137"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calcular o tamanho de um contêiner de Armazenamento de Blobs

Este script calcula o tamanho de um contêiner no Armazenamento de Blobs do Azure, totalizando o tamanho dos blobs no contêiner.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Este script do PowerShell fornece um tamanho estimado para o contêiner e não deve ser usado para cálculos de cobrança. Para obter um script que calcula o tamanho do contêiner para fins de cobrança, consulte [Calcular o tamanho de um contêiner do armazenamento de Blobs para fins de cobrança](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, o contêiner e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para calcular o tamanho do contêiner de Armazenamento de Blobs. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Obtém uma conta de armazenamento especificada ou todas as contas de armazenamento em um grupo de recursos ou na assinatura. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Lista os blobs em um contêiner. ||

## <a name="next-steps"></a>Próximas etapas

Para obter um script que calcula o tamanho do contêiner para fins de cobrança, consulte [Calcular o tamanho de um contêiner do armazenamento de Blobs para fins de cobrança](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras adicionais de script do PowerShell de armazenamento podem ser encontradas em [Amostras do PowerShell para Armazenamento do Azure](../blobs/storage-samples-blobs-powershell.md).
