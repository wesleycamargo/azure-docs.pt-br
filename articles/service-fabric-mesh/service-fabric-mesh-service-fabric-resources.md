---
title: Introdução ao Modelo de Recursos do Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre o Modelo de Recursos do Service Fabric, uma abordagem simplificada para definir aplicativos de Malha do Service Fabric.
services: service-fabric-mesh
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 6a96995a46cd2ad0a1afe4f4afe49d62409f2b68
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075020"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Introdução ao Modelo de Recurso do Service Fabric

Modelo de Recursos do Service Fabric descreve uma abordagem simples para definir os recursos que compõem um aplicativo de Malha do Service Fabric. Atualmente, há suporte para os seguintes tipos de recursos neste modelo:

- APLICATIVOS
- Serviços
- Volumes
- Redes

Cada recurso é descrito declarativamente em um arquivo de recurso, que é um simple YAML ou o documento JSON que descreve o aplicativo de malha e é provisionado pela plataforma do Service Fabric.

## <a name="resource-overview"></a>Visão Geral de Recursos

### <a name="applications-and-services"></a>Aplicativos e serviços

Um recurso de aplicativo é a unidade de implantação, controle de versão e tempo de vida de um aplicativo de Malha. Ele é composto de um ou mais recursos de serviço que representam um microsserviço. Cada recurso de serviço, por sua vez, é composto de um ou mais pacotes de códigos que descrevem todos os componentes necessários para executar a imagem de contêiner associada ao pacote de código, incluindo o seguinte:

- Registro, a versão e o nome do contêiner
- Recursos de CPU e memória necessários para cada contêiner
- Pontos de extremidade de rede
- Volumes de montagem no contêiner, fazendo referência a um recurso de volume separado.

Todos os pacotes de código definidos como parte de um recurso de serviço são implantados e ativados juntos como um grupo. O recurso de Serviço também descreve quantas instâncias do serviço para executar e também faz referência a outros recursos (por exemplo, o recurso de rede) ele depende.

Se um Aplicativo de Malha é composto de mais de um serviço, eles não são garantidos para ser executado em conjunto no mesmo nó. Além disso, durante uma atualização do aplicativo, falha de atualização de um único serviço resultará em todos os serviços que está sendo revertidos para sua versão anterior.



```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

Como mencionado anteriormente, o ciclo de vida de cada instância do aplicativo pode ser gerenciado independentemente. Por exemplo, uma instância do aplicativo pode ser atualizada independentemente de outras instâncias do aplicativo. Normalmente, você mantém o número de serviços em um aplicativo bem pequeno, como mais serviços que incluir em um aplicativo, mais difícil torna-se gerenciar cada serviço de modo independente.

### <a name="networks"></a>Redes

Recurso de rede é recurso implantado individualmente, independente de um recurso de aplicativo ou serviço que pode fazer referência a ele como suas dependências. Ele é usado para criar uma rede privada para seus aplicativos. Vários Serviços de Aplicativos diferentes podem fazer parte da mesma rede.

> [!NOTE]
> A visualização atual dá suporte apenas a um mapeamento um-para-um entre aplicativos e redes

### <a name="volumes"></a>Volumes

Os volumes são diretórios que são montados dentro de suas instâncias de contêiner que você pode usar para manter o estado. O recurso de Volume é uma maneira declarativa para descrever como um diretório é montado e o armazenamento de backup para ele.

## <a name="programming-models"></a>Modelos de programação
Recurso de serviço requer apenas uma imagem de contêiner em execução, o que é referenciada nos pacotes de código associados ao recurso. Você pode executar qualquer código escrito em qualquer linguagem, usando qualquer estrutura dentro do contêiner sem a necessidade de saber ou usar APIs específicas de Malha do Service Fabric. 

O código do aplicativo permanece portátil até mesmo fora da Malha do Service Fabric e suas implantações de aplicativo permanecem consistentes, independentemente da linguagem ou estrutura usada para implementar seus serviços. Se seu aplicativo é apenas um conjunto de processos e scripts, Go ou ASP.NET Core, o modelo de implantação de recursos de Malha do Service Fabric permanece o mesmo. 

## <a name="deployment"></a>Implantação

Ao implantar a Malha do Service Fabric, os recursos são implantados como modelos do Azure Resource Manager para o Azure por meio de HTTP ou a CLI do Azure. 


## <a name="next-steps"></a>Próximas etapas 
Para saber mais sobre Malha do Service Fabric, leia a visão geral:
- [Visão geral da Malha do Service Fabric](service-fabric-mesh-overview.md)
