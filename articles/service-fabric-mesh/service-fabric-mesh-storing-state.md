---
title: Opções de armazenamento de estado na Malha do Microsoft Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre como armazenar o estado de forma confiável em aplicativos de Malha do Service Fabric em execução na Malha do Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 6aa268cf56bfb8be9c27a9e0d9e5c9f4464b0c9d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075134"
---
# <a name="state-management-with-service-fabric"></a>Gerenciamento de estado com o Service Fabric
O Service Fabric dá suporte a várias opções diferentes para armazenamento de estado. Para obter uma visão geral conceitual dos padrões de gerenciamento de estado e do Service Fabric, consulte [Conceitos do Service Fabric: Estado](/azure/service-fabric/service-fabric-concepts-state). Os mesmos conceitos se aplicam se seus serviços forem executados dentro ou fora da Malha do Service Fabric. 

## <a name="state-storage-options-in-azure-service-fabric-mesh"></a>Opções de armazenamento de estado na Malha do Microsoft Azure Service Fabric
Com o serviço de Malha do Service Fabric, você pode facilmente implantar um novo aplicativo e conectá-lo em um repositório de dados existente, hospedado no Azure. Além de usar qualquer banco de dados remoto, há várias opções para armazenar dados, dependendo se o serviço deseja armazenamento local ou remoto. 

* Dados replicados armazenados localmente
  * Coleções confiáveis (não disponíveis em versão prévia)
    * Uma biblioteca que implementa as estruturas de dados, como filas e os pares chave-valor para usar no serviço
    * Isso fornece a maneira mais fácil e rápida de interagir com os dados, oferecendo à partição fácil roteamento em combinação com o roteamento inteligente na Malha do Service Fabric
  * Driver de volume do Service Fabric (não disponível em versão prévia)
    * Um driver de volume do docker para montar um volume local para um contêiner
    * Isso lhe dá a flexibilidade definitiva em armazenar dados localmente, por meio de qualquer API, que dá suporte ao armazenamento de arquivos.

* Armazenamento remoto
  * Volume de driver de Arquivos do Azure
    * Um driver de volume do docker para montar um compartilhamento de Arquivos do Azure para um contêiner
    * Fornece um menor desempenho, mas uma opção de dados também mais barata e completamente flexível e confiável, por meio de qualquer API, que dá suporte ao armazenamento de arquivos.
    * [Guia de instruções: Implantar um aplicativo com o volume de Arquivos do Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)
    
## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre o modelo de aplicativo, consulte [recursos de Service Fabric](service-fabric-mesh-service-fabric-resources.md)
