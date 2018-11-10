---
title: Planejar seu sistema Avere vFXT – Azure
description: Explica o planejamento a ser feito antes de implantar o Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f0e5523565dc561ed457dbc340835ad1889cb876
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669863"
---
# <a name="plan-your-avere-vfxt-system"></a>Planejar seu sistema Avere vFXT

Este artigo explica como planejar um novo vFXT Avere para o cluster do Azure para garantir que o cluster que você cria seja posicionado e dimensionado adequadamente para as suas necessidades. 

Antes de passar para o Azure Marketplace ou criar qualquer VM, considere como o cluster vai interagir com outros elementos no Azure. Planeje o local em que os recursos de cluster estarão localizados em sua rede privada e sub-redes e decida qual será seu armazenamento de back-end. Certifique-se de que os nós de cluster que você cria sejam eficientes o suficiente para dar suporte ao seu fluxo de trabalho. 

Continue lendo para saber mais.

## <a name="resource-group-and-network-infrastructure"></a>Infraestrutura de rede e grupo de recursos

Considere o local em que os elementos de seu vFXT Avere para implantação do Azure estarão. O diagrama a seguir mostra uma possível disposição para o vFXT Avere para componentes do Azure:

![Diagrama mostrando o cluster de controlador e o cluster de VMs em uma sub-rede. Em torno da sub-rede limite há um limite de rede virtual. Dentro da vnet, há um hexágono que representa o ponto de extremidade de serviço de armazenamento; ele está conectado com uma seta tracejada para um Armazenamento de Blobs fora da rede virtual.](media/avere-vfxt-components-option.png)

Siga estas diretrizes ao planejar a infraestrutura de rede do seu sistema do Avere vFXT:

* Todos os elementos devem ser gerenciados com uma nova assinatura criada para a implantação do vFXT Avere. Essa estratégia simplifica o acompanhamento de custos e a limpeza e também ajuda a particionar as cotas de recursos. Uma vez que o vFXT Avere é usado com um grande número de clientes, isolar os clientes e o cluster em uma assinatura única protege outras cargas de trabalho críticas contra possível limitação de recurso durante o provisionamento do cliente.

* Localize seus sistemas de computação de cliente perto do cluster vFXT. O armazenamento de back-end pode ser mais remoto.  

* Para manter a simplicidade, localize o cluster vFXT e a VM do controlador de cluster na mesma rede virtual (vnet) e no mesmo grupo de recursos. Também devem usar a mesma conta de armazenamento. 

* O cluster deve estar localizado em sua própria sub-rede para evitar conflitos de endereço IP com clientes ou recursos de computação. 

## <a name="ip-address-requirements"></a>Requisitos de endereço IP 

Certifique-se de que a sub-rede do seu cluster tenha um intervalo de endereços IP grande o suficiente para dar suporte ao cluster. 

O cluster do Avere vFXT usa os seguintes endereços IP:

* Um endereço IP de gerenciamento de cluster. Esse endereço pode se mover de nó a nó no cluster, mas está sempre disponível para que você possa se conectar à ferramenta de configuração do Painel de Controle do Avere.
* Para cada nó de cluster:
  * Pelo menos um endereço IP voltado para o cliente. (Todos os endereços voltados ao cliente são gerenciados do *vserver* do cluster e você pode movê-los entre os nós conforme necessário.)
  * Um endereço IP para comunicação de cluster
  * Endereço IP de uma instância (atribuído à VM)

Se você usar o Armazenamento de Blobs do Azure, ele também poderá exigir endereços IP de rede de virtual do cluster:  

* Uma conta de Armazenamento de Blobs do Azure exige pelo menos cinco endereços IP. Lembre-se desse requisito se você localizar o Armazenamento de Blobs na mesma rede virtual do cluster.
* Se você usar um Armazenamento de Blobs do Azure que esteja fora da rede virtual para o cluster, deverá criar um ponto de extremidade de serviço de armazenamento dentro da vnet. Esse ponto de extremidade não usa um endereço IP.

Você tem a opção de localizar recursos de rede e Armazenamento de Blobs (se usado) em diferentes grupos de recursos do cluster.

## <a name="vfxt-node-sizes"></a>tamanhos de nó do vFXT 

As VMs que atuam como nós de cluster determinam a capacidade de armazenamento e a taxa de transferência de solicitação do seu cache. Você pode escolher entre dois tipos de instância, com diferentes características de memória, processador e armazenamento local. 

Cada nó vFXT será idêntico. Ou seja, se você criar um cluster de três nós, terá três VMs do mesmo tipo e tamanho. 

| Tipo de instância | vCPUs | Memória  | Armazenamento SSD local  | Discos de dados máximos | Taxa de transferência de disco sem cache | NIC (contagem) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D16s_v3 | 16  | 64 GiB  | 128 GiB  | 32 | 25.600 IOPS <br/> 384 MBps | 8.000 MBps (8) |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51.200 IOPS <br/> 768 MBps | 16.000 MBps (8)  |

O cache de disco por nó é configurável e pode variar de 1.000 GB a 8.000 GB. 1 TB por nó é o tamanho de cache recomendado para nós Standard_D16s_v3 e 4 TB por nó são recomendados para nós Standard_E32s_v3.

Para obter informações adicionais sobre essas VMs, leia os seguintes documentos do Microsoft Azure:

* [Tamanhos das máquinas virtuais de uso geral](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general)
* [Tamanhos de máquina virtual otimizada para memória](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Cota da conta

Verifique se sua assinatura tem a capacidade para executar o cluster de do Avere vFXT, bem como todos os sistemas de computação ou cliente que estejam sendo usados. Leia [Cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para obter detalhes.

## <a name="back-end-data-storage"></a>Armazenamento de dados de back-end

Quando não estiver no cache, o conjunto de trabalho será armazenado em um novo contêiner de Blob ou em um sistema de armazenamento de hardware ou de nuvem existente?

Se você quiser usar o Armazenamento de Blobs do Azure para o back-end, deverá criar um novo contêiner como parte da criação do cluster vFXT. Use o script ``create-cloud-backed-container`` de implantação e forneça a conta de armazenamento para o novo contêiner de Blob. Essa opção cria e configura o novo contêiner para que ele esteja pronto para uso assim que o cluster estiver pronto. Leia [Criar nós e configurar o cluster](avere-vfxt-deploy.md#create-nodes-and-configure-the-cluster) para obter detalhes.

> [!NOTE]
> Somente contêineres de Armazenamento de Blobs vazios podem ser usados como arquivistas principais para o sistema de vFXT Avere. O vFXT deve ser capaz de gerenciar seu armazenamento de objeto sem necessidade de preservar os dados existentes. 
>
> Leia [Movimentação de dados para o cluster do vFXT](avere-vfxt-data-ingest.md) para aprender a copiar dados para o novo contêiner do cluster com eficiência usando computadores cliente e o cache do Avere vFXT.

Se você quiser usar um sistema de armazenamento local existente, adicione-o ao cluster vFXT após sua criação. O script ``create-minimal-cluster`` de implantação cria um cluster de vFXT sem nenhum armazenamento de back-end. Leia [Configurar armazenamento](avere-vfxt-add-storage.md) para obter instruções detalhadas sobre como adicionar um sistema de armazenamento ao cluster do Avere vFXT. 

## <a name="next-step-understand-the-deployment-process"></a>Próxima etapa: compreender o processo de implantação

A [Visão geral da implantação](avere-vfxt-deploy-overview.md) apresenta uma visão geral de todas as etapas necessárias para criar um Avere vFXT para o sistema do Azure e prepará-lo para veicular dados.  