---
title: Tutorial de limpeza do cluster do Service Fabric autônomo - Azure Service Fabric | Microsoft Docs
description: Neste tutorial você aprenderá como limpar seu cluster autônomo
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: e3e618bfd141e5cf7159d8df5b2b59f0d17d000f
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372192"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Tutorial: Limpar seu cluster autônomo

Os clusters autônomos do Service Fabric oferecem a opção de escolher seu próprio ambiente e criar um cluster como parte da abordagem “qualquer sistema operacional, qualquer nuvem” que está sendo adotada pelo Service Fabric. Nesta série de tutoriais, você cria um cluster autônomo hospedado em AWS e instala um aplicativo nele.

Este tutorial é a parte quatro de uma série. Esta parte do tutorial mostra como limpar os recursos AWS que você criou para hospedar o cluster do Service Fabric.

Na primeira parte da série, você aprende a:

> [!div class="checklist"]
> * Limpar um cluster do Service Fabric
> * Limpar seus recursos do AWS

## <a name="clean-up-service-fabric-cluster"></a>Limpar um cluster do Service Fabric

* RDP para a instância de EC2 que você usou para instalar o Service Fabric
* Abrir o PowerShell
* Altere o diretório para a pasta extraída do segundo tutorial.
* Execute o seguinte comando para remover o cluster do Service Fabric:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

* `Y` quando solicitado, se tiver sido bem-sucedido a saída será semelhante a seguir, com seus próprios endereços IP substituídos em:

```powershell
Best Practices Analyzer completed successfully.
Removing configuration from machine 172.31.21.141
Removing configuration from machine 172.31.27.1
Removing configuration from machine 172.31.20.163
Configuration removed from machine 172.31.21.141
Configuration removed from machine 172.31.27.1
Configuration removed from machine 172.31.20.163
The cluster is successfully removed.
```

## <a name="clean-up-aws-resources"></a>Limpe os recursos do AWS

* Faça logon na sua conta do AWS
* Vá para o Console EC2.
* Selecione os três nós que você criou na primeira parte do tutorial.
* Clique em **Ações** > **Estado da instância** > **Terminar**

## <a name="next-steps"></a>Próximas etapas

Na parte quatro da série, você aprendeu a limpar os recursos que foram criados nas etapas anteriores.

> [!div class="checklist"]
> * Limpar seus recursos

> [!div class="nextstepaction"]
> [Voltando ao início](service-fabric-tutorial-standalone-create-infrastructure.md)