---
title: Criar uma imagem da VM de uma VM do Azure | Microsoft Docs
description: "Saiba como criar uma imagem generalizada da VM de uma VM existente do Azure criada no modelo de implantação do Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b6dffec166ffe8e04c5d7b701aef009bf7b72d45


---
# <a name="download-the-template-for-a-vm"></a>Baixar o modelo para uma VM
Quando você cria uma VM no Azure usando o portal ou o PowerShell, um modelo do Resource Manager é criado automaticamente para você. Você pode usar este modelo para duplicar rapidamente uma implantação. O modelo contém informações sobre todos os recursos em um grupo de recursos. Para uma máquina virtual, isso significa que os modelo contém tudo o que é criado para dar suporte à VM desse grupo de recursos, incluindo os recursos de rede.

## <a name="download-the-template-using-the-portal"></a>Baixar o modelo usando o portal
1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. No menu do hub, selecione **Máquinas Virtuais**.
3. Selecione a máquina virtual na lista.
4. Selecione **Script de automação**.
5. Selecione **Baixar** e salve o arquivo zip em seu computador local.
6. Abra o arquivo zip e extraia os arquivos para uma pasta. O arquivo zip conterá:
   
   * deploy.ps1
   * deploy.sh 
   * deployer.rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

O arquivo .json é o modelo.

## <a name="download-the-template-using-powershell"></a>Baixar o modelo usando o PowerShell
Você também pode baixar o arquivo de modelo .json usando o cmdlet [Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx). Você pode usar o parâmetro `-path` para fornecer o nome de arquivo e o caminho para o arquivo .json. Este exemplo mostra como baixar o modelo para o grupo de recursos denominado **myResourceGroup** para a pasta **C:\users\public\downloads** no computador local.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como implantar recursos usando modelos, veja o [passo a passo do modelo do Resource Manager](../resource-manager-template-walkthrough.md).




<!--HONumber=Nov16_HO3-->


