---
title: "Grupos de contêineres das Instâncias de Contêiner do Azure"
description: "Entenda como funcionam os grupos de contêineres em Instâncias de Contêiner do Azure"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/19/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a42c01917926a4297c97cf9c5dfd1333dbef6793
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="container-groups-in-azure-container-instances"></a>Grupos de contêineres em Instâncias de Contêiner do Azure

O recurso de nível superior em Instâncias de Contêiner do Azure é um *grupo de contêineres*. Este artigo descreve o que são os grupos de contêineres e os tipos de cenários que eles permitem.

## <a name="how-a-container-group-works"></a>Como um grupo de contêineres funciona

Um grupo de contêineres é uma coleção de contêineres que são agendados no mesmo computador host. Os contêineres de um grupo de contêineres compartilham um ciclo de vida, uma rede local e volumes de armazenamento. Ele é semelhante ao conceito de um *pod* no [Kubernetes][kubernetes-pod] e [DC/SO][dcos-pod].

O diagrama a seguir mostra um exemplo de um grupo de contêineres que inclui vários contêineres.

![Diagrama de grupos de contêineres][container-groups-example]

Este grupo de contêineres de exemplo:

* Está agendado em um único computador host.
* Expõe um único endereço IP público, com uma porta exposta.
* Consiste em dois contêineres. Um contêiner escuta na porta 80, enquanto o outro escuta na porta 5000.
* Inclui dois compartilhamentos de arquivos do Azure como montagens de volume e cada contêiner monta um dos compartilhamentos localmente.

> [!NOTE]
> Grupos com vários contêineres são atualmente restritos a contêineres do Linux. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças atuais de plataforma em [Cotas e disponibilidade de região para Instâncias de Contêiner do Azure](container-instances-quotas.md).

### <a name="networking"></a>Rede

Grupos de contêineres compartilham um endereço IP e um namespace de porta nesse endereço IP. Para permitir que clientes externos alcancem um contêiner dentro do grupo, você deve expor a porta no endereço IP e do contêiner. Já que contêineres dentro do grupo compartilham um namespace de porta, não há suporte para mapeamento de porta. Contêineres dentro de um grupo podem acessar uns aos outros por meio de localhost nas portas que eles têm expostos, mesmo se essas portas não estão expostas externamente no endereço IP do grupo.

### <a name="storage"></a>Armazenamento

Você pode especificar volumes externos para montar dentro de um grupo de contêineres. Você pode mapear os volumes para caminhos específicos dentro dos contêineres individuais em um grupo.

## <a name="common-scenarios"></a>Cenários comuns

Grupos de vários contêineres são úteis em casos nos quais você deseja dividir uma única tarefa funcional em um pequeno número de imagens de contêiner, que podem ser entregues por equipes diferentes e têm requisitos de recursos separados.

Os exemplos de uso podem incluir:

* Um contêiner de aplicativo e um contêiner de log. O contêiner de log coleta logs e métricas de saída do aplicativo principal e grava-as em armazenamento de longo prazo.
* Um aplicativo e um contêiner de monitoramento. O contêiner de monitoramento faz uma solicitação periódica ao aplicativo para garantir que ele esteja em execução e respondendo corretamente e emite um alerta em caso negativo.
* Um contêiner que atende a um aplicativo Web e um contêiner efetuando pull do conteúdo mais recente do controle do código-fonte.

## <a name="next-steps"></a>Próximas etapas

Saiba como [implantar um grupo de vários contêineres](container-instances-multi-container-group.md) com um modelo do Azure Resource Manager.

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
