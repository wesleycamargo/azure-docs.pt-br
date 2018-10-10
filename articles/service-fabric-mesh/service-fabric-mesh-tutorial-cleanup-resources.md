---
title: Tutorial – Limpeza de recursos da Malha do Azure Service Fabric | Microsoft Docs
description: Saiba como remover recursos da Malha do Azure Service Fabric para não ser cobrado por recursos que não estiver mais usando.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: fb7a444c54a57e7f2c38d941eb99f2fea7eebcef
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993350"
---
# <a name="tutorial-remove-azure-resources"></a>Tutorial: remover recursos do Azure

Este tutorial é a parte cinco de uma série, e mostra como excluir o aplicativo e seus recursos, para que você não seja cobrado por eles.

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Limpe os recursos usados pelo aplicativo, de modo que você não seja cobrado por eles.

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Criar um aplicativo da Malha do Service Fabric no Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Depure um aplicativo Service Fabric Mesh em execução no cluster de desenvolvimento local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Implantar um aplicativo da Malha do Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Atualizar um aplicativo da Malha do Service Fabric](service-fabric-mesh-tutorial-upgrade.md)
> * Limpar os recursos da Malha do Service Fabric

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tiver implantado o aplicativo de tarefas, siga as instruções em [Publicar um aplicativo Web da Malha do Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="clean-up-resources"></a>Limpar recursos

Este é o fim do tutorial. Quando você terminar de usar os recursos que criou, exclua-os para não ser cobrado pelos recursos que não estiver mais usando. Isso é especialmente importante porque a Malha é um serviço sem servidor que cobra por segundo. Para saber mais sobre os preços da Malha, confira https://aka.ms/sfmeshpricing.

Uma das conveniências que o Azure oferece é que quando você cria recursos que estão associados a um grupo de recursos específico, a exclusão do grupo de recursos exclui todos os recursos associados. Dessa forma, você não precisa exclui-los individualmente.

Como você criou um novo grupo de recursos para criar o aplicativo de tarefas pendentes, você pode excluir com segurança esse grupo de recursos, o que excluirá todos os recursos associados.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Como alternativa, é possível excluir o grupo de recursos **sfmeshTutorial1RG** [no portal](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources). 

## <a name="next-steps"></a>Próximas etapas

Agora que você concluiu a publicação de um aplicativo de Malha do Service Fabric no Azure, tente o seguinte:

* Para ver outro exemplo de comunicação de serviço a serviço, explore o [exemplo de aplicativo de votação](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp)
* Para saber mais sobre o modelo de recursos do Service Fabric, consulte [modelo de recursos de Malha do Service Fabric](service-fabric-mesh-service-fabric-resources.md).
* Para saber mais sobre Malha do Service Fabric, leia [visão geral sobre Malha do Service Fabric](service-fabric-mesh-overview.md).
* Leia sobre o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)