---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/07/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f7539ed5083a386ef05134bea36426f4a360afad
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192701"
---
## <a name="shared-image-management"></a>Gerenciamento de imagem compartilhada 

Estes são alguns exemplos de tarefas comuns de gerenciamento e como para concluí-las usando o PowerShell.

Liste todas as galerias por nome.

```azurepowershell-interactive
$galleries = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

Liste todas as definições de imagem por nome.

```azurepowershell-interactive
$imageDefinitions = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

Liste todas as versões de imagem por nome.

```azurepowershell-interactive
$imageVersions = Get-AzureRMResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

Excluir uma versão de imagem. Este exemplo exclui a versão da imagem chamada *1.0.0*.

```azurepowershell-interactive
Remove-AzureRmGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```





