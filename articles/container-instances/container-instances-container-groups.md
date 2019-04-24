---
title: Grupos de contêineres das Instâncias de Contêiner do Azure
description: Entender como vários contêineres grupos funcionam em instâncias de contêiner do Azure
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f4bbea8acd447a731cf5c56f9876baf9183735ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60518654"
---
# <a name="container-groups-in-azure-container-instances"></a>Grupos de contêineres em Instâncias de Contêiner do Azure

O recurso de nível superior em Instâncias de Contêiner do Azure é um *grupo de contêineres*. Este artigo descreve o que são os grupos de contêineres e os tipos de cenários que eles permitem.

## <a name="how-a-container-group-works"></a>Como um grupo de contêineres funciona

Um grupo de contêineres é uma coleção de contêineres que são agendados no mesmo computador host. Os contêineres em um grupo de contêineres compartilham um ciclo de vida, recursos, rede local e volumes de armazenamento. É semelhante em conceito a um *pod* na [Kubernetes][kubernetes-pod].

O diagrama a seguir mostra um exemplo de um grupo de contêineres que inclui vários contêineres:

![Diagrama de grupos de contêineres][container-groups-example]

Este grupo de contêineres de exemplo:

* Está agendado em um único computador host.
* É atribuído um rótulo de nome DNS.
* Expõe um único endereço IP público, com uma porta exposta.
* Consiste em dois contêineres. Um contêiner escuta na porta 80, enquanto o outro escuta na porta 5000.
* Inclui dois compartilhamentos de arquivos do Azure como montagens de volume e cada contêiner monta um dos compartilhamentos localmente.

> [!NOTE]
> Grupos com vários contêineres no momento oferecer suporte a contêineres do Linux. Para contêineres do Windows, as instâncias de contêiner do Azure só dá suporte à implantação de uma única instância. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar diferenças da plataforma atual no serviço do [visão geral](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Implantação

Aqui estão duas maneiras de implantar um grupo de vários contêineres: usar um [modelo do Resource Manager] [ resource-manager template] ou uma [arquivo YAML][yaml-file]. Um modelo do Resource Manager é recomendado quando você precisa implantar recursos adicionais de serviço do Azure (por exemplo, um [compartilhamento de arquivos do Azure][azure-files]) quando você implanta as instâncias de contêiner. Devido à natureza mais concisa desse formato YAML, um arquivo YAML é recomendado quando sua implantação inclui apenas as instâncias de contêiner.

Para preservar a configuração do grupo de um contêiner, você pode exportar a configuração para um arquivo YAML usando o comando de CLI do Azure [exportação do contêiner az][az-container-export]. Exportação permite que você armazene suas configurações de grupo de contêiner no controle de versão para "configuração como código". Ou então, usar o arquivo exportado como ponto de partida ao desenvolver uma nova configuração em YAML.

## <a name="resource-allocation"></a>Alocação de recurso

As instâncias de contêiner do Azure aloca recursos, como CPUs, memória e opcionalmente [GPUs] [ gpus] (versão prévia) para um grupo de contêineres, adicionando o [solicitações de recurso] [ resource-requests] das instâncias do grupo. Colocar recursos de CPU como exemplo, se você criar um grupo de contêineres com duas instâncias, cada solicitante 1 CPU, em seguida, o grupo de contêineres é alocado 2 CPUs.

Os recursos máximos disponíveis para um grupo de contêineres dependem de [região do Azure] [ region-availability] usado para a implantação.

### <a name="container-resource-requests-and-limits"></a>Limites e solicitações de recurso de contêiner

* Por padrão, instâncias de contêiner em um grupo compartilham os recursos solicitados do grupo. Em um grupo com duas instâncias de cada solicitação 1 CPU, o grupo como todo tem acesso a 2 CPUs. Cada instância pode usar até 2 CPUs e as instâncias podem competir por recursos de CPU enquanto eles estão em execução.

* Para limitar o uso de recursos por uma instância em um grupo, opcionalmente, defina uma [limite de recurso] [ resource-limits] para a instância. Em um grupo com duas instâncias, solicitando 1 CPU, um dos seus contêineres pode exigir mais CPUs executar que o outro.

  Nesse cenário, você pode definir um limite de recurso de 0,5 CPU para uma instância e um limite de 2 CPUs para o segundo. Essa configuração limita o uso de recursos do contêiner primeiro como 0,5 CPU, permitindo que o segundo contêiner para usar para as 2 CPUs se disponível.

Para obter mais informações, consulte o [ResourceRequirements] [ resource-requirements] API REST de grupos de propriedade no contêiner.

### <a name="minimum-and-maximum-allocation"></a>Alocação máximo e mínimo

* Alocar uma **mínimo** 1 CPU e 1 GB de memória para um grupo de contêineres. Instâncias de contêiner individual dentro de um grupo podem ser provisionadas com menos de 1 CPU e 1 GB de memória. 

* Para o **máximo** recursos em um grupo de contêiner, consulte a [disponibilidade de recursos] [aci--disponibilidade de região] para instâncias de contêiner do Azure na região de implantação.

## <a name="networking"></a>Rede

Grupos de contêineres compartilham um endereço IP e um namespace de porta nesse endereço IP. Para permitir que clientes externos alcancem um contêiner dentro do grupo, você deve expor a porta no endereço IP e do contêiner. Como contêineres dentro do grupo compartilham um namespace de porta, o mapeamento de porta não é suportado. Contêineres dentro de um grupo podem acessar uns aos outros por meio de localhost nas portas que eles têm expostos, mesmo se essas portas não estão expostas externamente no endereço IP do grupo.

Opcionalmente, implantar grupos de contêineres em um [rede virtual do Azure] [ virtual-network] (visualização) para permitir que os contêineres se comuniquem com segurança com outros recursos na rede virtual.

## <a name="storage"></a>Armazenamento

Você pode especificar volumes externos para montar dentro de um grupo de contêineres. Você pode mapear os volumes para caminhos específicos dentro dos contêineres individuais em um grupo.

## <a name="common-scenarios"></a>Cenários comuns

Grupos de vários contêineres são úteis quando você deseja dividir uma única tarefa funcional em um pequeno número de imagens de contêiner. Essas imagens podem, então, ser fornecidas por equipes diferentes e têm requisitos de recursos separados.

Os exemplos de uso podem incluir:

* Um contêiner que atende a um aplicativo Web e um contêiner efetuando pull do conteúdo mais recente do controle do código-fonte.
* Um contêiner de aplicativo e um contêiner de log. O contêiner de log coleta logs e métricas de saída do aplicativo principal e grava-as em armazenamento de longo prazo.
* Um contêiner de aplicativo e um contêiner de monitoramento. O contêiner de monitoramento faz uma solicitação periódica ao aplicativo para garantir que ele esteja em execução e respondendo corretamente e emite um alerta em caso negativo.
* Um contêiner de front-end e um contêiner de back-end. O front-end pode atender a um aplicativo web, com o execução de um serviço para recuperar dados de back-end. 

## <a name="next-steps"></a>Próximas etapas

Saiba como implantar um grupo de contêineres com vários contêineres com um modelo do Azure Resource Manager:

> [!div class="nextstepaction"]
> [Implantar um grupo de contêineres][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
