---
title: Executar experimentos e inferência em uma rede virtual
titleSuffix: Azure Machine Learning service
description: Executar experimentos de aprendizado de máquina e fazer inferências com segurança dentro de uma Rede Virtual do Microsoft Azure. Saiba como criar destinos de computação para treinamento de modelo e como fazer inferência dentro de uma Rede Virtual do Azure. Também aborda os requisitos para redes virtuais seguras, como exigir portas de entrada e saída.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: 21c4a9042a64a58f67f1f94c300b6438895eea5d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856274"
---
# <a name="securely-run-experiments-and-inferencing-inside-an-azure-virtual-network"></a>Executar experimentos e fazer inferências com segurança dentro de uma Rede Virtual

Neste artigo, você aprenderá a executar seus experimentos e fazer inferências dentro de uma Rede Virtual. Uma rede virtual atua como um limite de segurança, isolando os recursos do Azure da Internet pública. Também é possível unir uma Rede Virtual à sua rede local. Ela permite treinar com segurança os seus modelos, bem como acessar os modelos implantados para inferência.

O Serviço do Azure Machine Learning depende de outros serviços do Azure para usar os recursos de computação. Os recursos de computação (destinos de computação) são usados para treinar e implantar modelos. Esses destinos de computação podem ser criados dentro de uma rede virtual. Por exemplo, é possível usar uma VM de Ciência de Dados para treinar um modelo e, em seguida, implantar o modelo no Serviço de Kubernetes do Azure. Para saber mais sobre redes virtuais, consulte o documento [Visão geral de redes virtuais](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="storage-account-for-your-workspace"></a>Conta de armazenamento para o seu espaço de trabalho

Ao criar um espaço de trabalho do Serviço do Azure Machine Learning, ele exige uma conta de Armazenamento do Microsoft Azure. Não ative as regras de firewall para essa conta de armazenamento. O Serviço do Azure Machine Learning exige acesso irrestrito à conta de armazenamento.

Se você não tiver certeza de ter modificado essas configurações, consulte a seção __Alterar a regra de acesso à rede padrão__ do documento [Configurar firewalls e redes virtuais do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security) e use as etapas para _permitir o acesso_ de __Todas as redes__.

## <a name="use-machine-learning-compute"></a>Usar a Computação do Machine Learning

Para usar a Computação do Machine Learning em uma rede virtual, use as seguintes informações para entender os requisitos de rede:

- A rede virtual deve estar na mesma assinatura e região que o espaço de trabalho do Serviço do Azure Machine Learning.

- A sub-rede especificada para o cluster da Computação do Machine Learning deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs destinadas ao cluster. Se a sub-rede não tem endereços IP não atribuídos suficientes, o cluster é alocado parcialmente.

- Se você planeja proteger a rede virtual restringindo o tráfego, é preciso deixar algumas portas abertas para o serviço de Computação do Machine Learning. Para saber mais, consulte a seção [Portas necessárias](#mlcports).

- Verifique se as políticas de segurança, os bloqueios na assinatura da rede virtual ou o grupo de recursos restringem permissões para gerenciar a rede virtual.

- Se for colocar vários clusters da Computação do Machine Learning em uma rede virtual, talvez você precise solicitar um aumento de cota para um ou mais recursos.

    A Computação do Machine Learning aloca automaticamente recursos de rede adicionais no grupo de recursos que contém a rede virtual. Para cada cluster da Computação do Machine Learning, o Serviço do Azure Machine Learning aloca os seguintes recursos: 

    - Um grupo de segurança de rede (NSG)

    - Um endereço IP público

    - Um balanceador de carga

    Esses recursos são limitados pelas [cotas de recursos](https://docs.microsoft.com/azure/azure-subscription-service-limits) da assinatura. 

### <a id="mlcports"></a> Portas obrigatórias

Atualmente, a Computação do Machine Learning usa o serviço Lote do Azure para provisionar VMs na rede virtual especificada. A sub-rede deve permitir a comunicação de entrada do serviço Lote. Essa comunicação é usada para agendar execuções nos nós da Computação do Machine Learning e para comunicação com o Armazenamento do Microsoft Azure e outros recursos. O Lote adiciona NSGs no nível de interfaces de rede (NICs) anexadas às VMs. Esses NSGs configuraram automaticamente as regras de entrada e saída para permitir o tráfego a seguir:

- Tráfego TCP de entrada nas portas 29876 e 29877 a partir dos endereços IP com função de serviço de Lote. 
 
- Tráfego TCP de entrada na porta 22 para permitir acesso remoto.
 
- Tráfego de saída em qualquer porta para a rede virtual.

- Tráfego de saída em qualquer porta para a internet.

Tenha cuidado se você for modificar ou adicionar regras de entrada/saída nos NSGs configurados para Lote. Se um NSG bloquear a comunicação com os nós de computação, os serviços de Computação do Machine Learning definem o estado dos nós de computação como inutilizáveis.

Você não precisa especificar os NSGs no nível da sub-rede porque o Lote configura seus próprios NSGs. No entanto, se a sub-rede especificada tiver associado NSGs e/ou um firewall, configure as regras de segurança de entrada e saída conforme mencionado anteriormente. As seguintes capturas de tela mostram como a configuração da regra aparece no portal do Azure:

![Captura de tela de regras de NSG de entrada para Computação do Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Captura de tela de regras de NSG de saída para Computação do Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>Criar Computação do Machine Learning em uma rede virtual

Para criar um cluster de Computação do Machine Learning com o **portal do Azure**, use as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), selecione o seu espaço de trabalho do Serviço do Azure Machine Learning.

1. Na seção __Aplicativo__, selecione __Computação__. Em seguida, selecione __Adicionar computação__. 

    ![Como adicionar uma computação ao Serviço do Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Para configurar esse recurso de computação para usar uma rede virtual, use estas opções:

    - __Configuração de rede__: Selecione __Avançado__.

    - __Grupo de recursos__: Selecione o grupo de recursos que contém a rede virtual.

    - __Rede virtual__: Selecione a rede virtual que contém a sub-rede.

    - __Sub-rede__: Selecione a sub-rede a ser usada.

    ![Uma captura de tela mostrando configurações da rede virtual para computação do Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Você também pode criar um cluster de Computação do Machine Learning usando o **SDK do Azure Machine Learning**. O código a seguir cria um novo cluster de Computação do Machine Learning na sub-rede `default` de uma rede virtual chamada `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure Virtual Network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")
    
    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name = vnet_resourcegroup_name,
                                                           vnet_name = vnet_name,
                                                           subnet_name = subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    
    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Assim que o processo de criação for concluído, você pode treinar o seu modelo usando o cluster. Para saber mais, consulte o documento [Selecionar e usar um destino de computação para treinamento](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight"></a>Usar uma máquina virtual ou o HDInsight

Para usar uma máquina virtual ou cluster do HDInsight em uma Rede Virtual com seu espaço de trabalho, use as seguintes etapas:

> [!IMPORTANT]
> O Serviço do Azure Machine Learning oferece suporte somente a máquinas virtuais que executam o Ubuntu.

1. Crie uma VM ou um cluster do HDInsight usando o portal do Azure, a CLI do Azure, etc., e coloque-a em uma Rede Virtual do Microsoft Azure. Para obter mais informações, consulte um dos seguintes documentos:
    * [Criar e gerenciar Redes Virtuais do Azure para VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Estender o HDInsight usando Redes Virtuais do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Para permitir que o Serviço do Azure Machine Learning comunique-se com a porta SSH na VM ou no cluster, configure uma entrada de origem para o NSG. A porta SSH geralmente é a porta 22. Para permitir o tráfego dessa origem, use as seguintes informações:

    * __Fonte__: Selecione __Marca de Serviço__

    * __Marca de serviço de origem__: selecione __AzureMachineLearning__

    * __Intervalos de portas de origem__: Selecione __*__

    * __Destino__: selecione __Qualquer uma__

    * __Intervalos de portas de destino__: selecione __22__

    * __Protocolo__: selecione __Qualquer uma__

    * __Ação__: selecione __Permitir__

   ![Captura de tela de regras de entrada para fazer experimentos na VM ou no HDInsight dentro de uma rede virtual](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Mantenha as regras de saída padrão para o NSG. Para saber mais, consulte a seção sobre regras de segurança padrão na documentação de [Grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
    
1. Anexe a VM ou o cluster do HDInsight ao seu espaço de trabalho do Serviço do Azure Machine Learning. Para saber mais, consulte o documento [Configurar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md).

## <a name="use-azure-kubernetes-service-aks"></a>Usar o Serviço de Kubernetes do Azure (AKS)

> [!IMPORTANT]
> Verifique os pré-requisitos e planeje o endereçamento IP para o seu cluster antes de continuar com as etapas mencionadas abaixo. Você pode consultar [Configurar a rede avançada no Serviço de Kubernetes do Azure (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking)
> 
> Mantenha as regras de saída padrão para o NSG. Para saber mais, consulte a seção sobre regras de segurança padrão na documentação de [Grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).
>
> O Serviço de Kubernetes do Azure e a Rede Virtual do Microsoft Azure devem estar na mesma região.

Para adicionar o Serviço de Kubernetes do Azure em uma Rede Virtual ao seu espaço de trabalho, use as seguintes etapas do __portal do Azure__:

1. No [portal do Azure](https://portal.azure.com), selecione o seu espaço de trabalho do Serviço do Azure Machine Learning.

1. Na seção __Aplicativo__, selecione __Computação__. Em seguida, selecione __Adicionar computação__. 

    ![Como adicionar uma computação ao Serviço do Azure Machine Learning](./media/how-to-enable-virtual-network/add-compute.png)

1. Para configurar esse recurso de computação para usar uma rede virtual, use estas opções:

    - __Configuração de rede__: Selecione __Avançado__.

    - __Grupo de recursos__: Selecione o grupo de recursos que contém a rede virtual.

    - __Rede virtual__: Selecione a rede virtual que contém a sub-rede.

    - __Sub-rede__: Escolher a sub-rede.

    - __Intervalo de endereços do Serviço de Kubernetes__: selecione o intervalo de endereços do Serviço de Kubernetes. Esse intervalo de endereços usa um intervalo IP de notação CIDR para definir os endereços IP disponíveis para o cluster. Ele não deve se sobrepor a quaisquer intervalos de IP da sub-rede. Por exemplo:  10.0.0.0/16.

    - __Endereço IP do serviço DNS do Kubernetes__: selecione o endereço IP do serviço DNS do Kubernetes. Esse endereço IP é atribuído ao serviço DNS do Kubernetes. Ele deve estar dentro do intervalo de endereços do Serviço de Kubernetes. Por exemplo:  10.0.0.10.

    - __Endereço da ponte Docker__: selecione o endereço da ponte Docker. Esse endereço IP é atribuído à ponte Docker. Ele não deve estar em quaisquer intervalos de IP da sub-rede, nem no intervalo de endereços do Serviço de Kubernetes. Por exemplo:  172.17.0.1/16

   ![Serviço do Azure Machine Learning: Configurações da Rede Virtual da Computação do Machine Learning](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

    > [!TIP]
    > Se você já tem um cluster do AKS em uma Rede Virtual, é possível anexá-lo ao espaço de trabalho. Para saber mais, consulte [Como implantar no AKS](how-to-deploy-to-aks.md).

Você também pode usar o **SDK do Azure Machine Learning** para adicionar o serviço de Kubernetes do Azure a uma Rede Virtual. O código a seguir cria um novo Serviço de Kubernetes do Azure na sub-rede `default` de uma rede virtual chamada `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace = ws,
                                  name = "myaks",
                                  provisioning_configuration = config)
```

Quando o processo de criação estiver concluído, é possível fazer inferência em um cluster do AKS por trás de uma rede virtual. Para saber mais, consulte [Como implantar no AKS](how-to-deploy-to-aks.md).

## <a name="next-steps"></a>Próximas etapas

* [Configurar ambientes de treinamento](how-to-set-up-training-targets.md)
* [Onde implantar modelos](how-to-deploy-and-where.md)
* [Proteger os modelos implantados com SSL](how-to-secure-web-service.md)
