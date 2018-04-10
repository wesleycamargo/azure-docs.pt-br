---
title: Visão geral das Instâncias de Contêiner do Azure
description: Compreender as Instâncias de Contêiner do Azure
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 03/29/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: e10d4046f543fbcc479421f4ca8f10221abc806f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2018
---
# <a name="azure-container-instances"></a>Instâncias de Contêiner do Azure

Os contêineres estão se tornando o modo preferido para empacotar, implantar e gerenciar aplicativos de nuvem. As Instâncias de Contêiner do Azure oferecem a maneira mais rápida e simples para executar um contêiner no Azure, sem a necessidade de gerenciar máquinas virtuais nem adotar um serviço de nível superior.

As Instâncias de Contêiner do Azure são uma ótima solução para qualquer cenário que possa ser usado em contêineres isolados, incluindo aplicativos simples, automação de tarefas e criação de trabalhos. Para cenários em que você precisa de orquestração de contêineres completa, incluindo descoberta do serviço em vários contêineres, dimensionamento automático e atualizações de aplicativo coordenadas, recomendamos o [AKS (Serviço de Contêiner do Azure)](../aks/index.yml).

## <a name="fast-startup-times"></a>Inicialização mais rápida

Os contêineres oferecem vantagens significativas de inicialização em relação às máquinas virtuais. As Instâncias de Contêiner do Azure podem iniciar contêineres no Azure em segundos, sem a necessidade de provisionar nem gerenciar VMs.

## <a name="public-ip-connectivity-and-dns-name"></a>Conectividade de IP público e nome DNS

As Instâncias de Contêiner do Azure permitem expor seus contêineres diretamente à Internet com um endereço IP e um FQDN (nome de domínio totalmente qualificado). Quando você cria uma instância de contêiner, pode especificar uma etiqueta de nome de DNS personalizado para que o seu aplicativo seja acessível em *customlabel*.*azureregion*.azurecontainer.io.

## <a name="hypervisor-level-security"></a>Segurança em nível de hipervisor

Historicamente, os contêineres ofereciam isolamento de dependência de aplicativo e governança de recursos, mas não eram considerados suficientemente protegidos para uso com vários locatários hostis. As Instâncias de Contêiner do Azure garantem que seu aplicativo fique tão isolado em um contêiner quanto ficaria em uma VM.

## <a name="custom-sizes"></a>Tamanhos personalizados

Os contêineres normalmente são otimizados para executar apenas um único aplicativo, mas as necessidades exatas desses aplicativos podem variar significativamente. As Instâncias de Contêiner do Azure fornecem uma utilização ideal ao permitirem especificações exatas de núcleos e memória da CPU. Você paga com base no que precisar e é cobrado por segundo, assim, é possível ajustar seus gastos eficientemente com base em suas necessidades reais.

## <a name="persistent-storage"></a>Armazenamento persistente

Para recuperar e persistir estados com as Instâncias de Contêiner do Azure, nós oferecemos a [montagem direta de compartilhamentos de arquivos do Azure](container-instances-mounting-azure-files-volume.md).

## <a name="linux-and-windows-containers"></a>Contêineres do Windows e do Linux

As Instâncias de Contêiner do Azure podem agendar contêineres do Windows e do Linux com a mesma API. Basta especificar o tipo de sistema operacional ao criar seus [grupos de contêiner](container-instances-container-groups.md).

Alguns recursos são atualmente restritos a contêineres do Linux. Enquanto trabalhamos para trazer paridade de recursos para contêineres do Windows, você pode encontrar diferenças da plataforma atual em [Cotas e disponibilidade de região para Instâncias de Contêiner do Azure](container-instances-quotas.md).

## <a name="co-scheduled-groups"></a>Grupos coagendados

As Instâncias de Contêiner do Azure são compatíveis com a programação de [grupos com vários contêineres](container-instances-container-groups.md) que compartilham um computador host, uma rede local, um armazenamento e um ciclo de vida. Isso permite que você combine seu aplicativo principal com outros contêineres com função de suporte, como sidecars de log.

## <a name="next-steps"></a>Próximas etapas

Tente implantar um contêiner no Azure com um único comando usando nosso guia de início rápido:

> [!div class="nextstepaction"]
> [Início Rápido de Instâncias de Contêiner do Azure](container-instances-quickstart.md)
