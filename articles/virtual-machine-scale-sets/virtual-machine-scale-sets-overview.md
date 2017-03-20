---
title: "Visão Geral dos Conjuntos de Dimensionamento de Máquina Virtual do Azure | Microsoft Docs"
description: "Saiba mais sobre os Conjuntos de Dimensionamento de Máquina Virtual do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/10/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 2071debe24ef2705b2ee05ce1210a813234cf277
ms.lasthandoff: 03/10/2017


---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>O que são conjuntos de dimensionamento de máquinas virtuais no Azure?
Os conjuntos de dimensionamento de máquina virtual são um recurso de Computação do Azure que você pode usar para implantar e gerenciar um conjunto de VMs idênticas. Com todas as VMs configuradas igualmente, os conjuntos de dimensionamento foram desenvolvidos para dar suporte ao verdadeiro dimensionamento automático (não é necessário o pré-provisionamento das VMs), facilitando a criação de serviços em grande escala visando big compute, big data e cargas de trabalho contidas.

Para aplicativos que necessitam dimensionar os recursos de computação, as operações de dimensionamento são balanceadas implicitamente entre domínios de falha e atualização. Para ver uma introdução aos conjuntos de dimensionamento, confira o [Comunicado no blog do Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Assista a esses vídeos para saber mais sobre conjuntos de dimensionamento:

* [Mark Russinovich fala sobre os conjuntos de dimensionamento do Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Conjuntos de Dimensionamento de Máquina Virtual com Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-scale-sets"></a>Criando e gerenciando conjuntos de dimensionamento
Você pode criar um conjunto de dimensionamento no [Portal do Azure](https://portal.azure.com) selecionando *novo* e digitando "escala" na barra de pesquisa. "Conjunto de dimensionamento de máquinas virtuais" está listado nos resultados. A partir daí, você pode preencher os campos obrigatórios para personalizar e implantar o conjunto de dimensionamento. Observe existe também são opções para configurar as regras básicas AutoEscala baseadas no uso da CPU no portal.

Os conjuntos de dimensionamento também podem ser definidos e implantados usando modelos JSON e [APIs REST](https://msdn.microsoft.com/library/mt589023.aspx) , assim como as VMs individuais do Azure Resource Manager. Portanto, quaisquer métodos de implantação do Gerenciador de Recursos do Azure padrão podem ser usados. Para obter mais informações sobre modelos, confira [Criação de modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md).

Um conjunto de modelos de exemplo para conjuntos de dimensionamento de máquinas virtuais pode ser encontrado no repositório GitHub [aqui](https://github.com/Azure/azure-quickstart-templates) (procure modelos com *vmss* no título).

Você verá um botão que vincula ao recurso de implantação do portal nas páginas de detalhes para esses modelos. Para implantar o conjunto de dimensionamento, clique no botão e preencha todos os parâmetros obrigatórios no portal. Se você não tiver certeza se um recurso oferece suporte a letras maiúsculas ou combinadas, é mais seguro usar sempre letras minúsculas e números em valores de parâmetro. Também é uma prática dissecar vídeo de um modelo de conjunto de dimensionamento aqui:

[Dissecação de Modelo do conjunto de dimensionamento de VM](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-scale-set-out-and-in"></a>Expandindo e reduzindo um conjunto de dimensionamento
Você pode alterar a capacidade de um conjunto de dimensionamento definido no portal do Azure clicando na seção _Dimensionamento_ em _Configurações_. 

Para alterar a capacidade do conjunto de dimensionamento na linha de comando, a [CLI do Azure](https://github.com/Azure/azure-cli) oferece um comando _scale_. Por exemplo, para definir um conjunto de dimensionamento definido com uma capacidade de 10 VMs:

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

Para definir o número de VMs em um conjunto de dimensionamento definido usando o PowerShell, use o comando _Update-AzureRmVmss_:

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Para aumentar ou diminuir o número de máquinas virtuais em um conjunto de dimensionamento, usando um modelo do Azure Resource Manager, altere a propriedade *capacity* e reimplante o modelo. Essa simplicidade facilita a integração de conjuntos de dimensionamento com o dimensionamento automático do Azure ou a criação de sua própria camada de dimensionamento personalizada se você precisar definir eventos de escala personalizada que não têm suporte do dimensionamento automático do Azure. 

Se estiver reimplantando um modelo do Azure Resource Manager para alterar a capacidade, você pode definir um modelo muito menor que inclui apenas o pacote de propriedades ‘SKU’ com a capacidade atualizada. Veja um exemplo [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing).

## <a name="autoscale"></a>Autoscale

Um conjunto de dimensionamento pode ser configurado opcionalmente com configurações de dimensionamento automático quando é criado no portal do Azure, permitindo que o número de VMs seja aumentado ou diminuído com base no uso médio de CPU. Muitos dos modelos de conjunto de dimensionamento em [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) definem configurações de dimensionamento automático. Você também pode adicionar configurações de dimensionamento automático a um conjunto de dimensionamento existente. Por exemplo, aqui temos um script do Azure PowerShell para adicionar o dimensionamento automático de CPU com base em um conjunto de dimensionamento:

```PowerShell

$subid = "yoursubscriptionid"
$rgname = "yourresourcegroup"
$vmssname = "yourscalesetname"
$location = "yourlocation" # e.g. southcentralus

$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionValue 1
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator LessThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Decrease -ScaleActionValue 1
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "autoprofile1"
Add-AzureRmAutoscaleSetting -Location $location -Name "autosetting1" -ResourceGroup $rgname -TargetResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -AutoscaleProfiles $profile1
```

 Você pode encontrar uma lista de métricas válidas para dimensionar aqui: [Métricas com suporte no Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md) sob o título _Microsoft.Compute/virtualMachineScaleSets_. Existem opções de dimensionamento automático mais avançadas disponíveis, incluindo o dimensionamento automático com base em agendamento e o uso de webhooks para integração com sistemas de alertas.

## <a name="monitoring-your-scale-set"></a>Monitorando seu conjunto de dimensionamento
O [portal do Azure](https://portal.azure.com) lista os conjuntos de dimensionamento e mostra suas propriedades. O portal também dá suporte a operações de gerenciamento, que podem ser executadas em conjuntos de dimensionamento e nas VMs individuais dentro de um conjunto de dimensionamento. O portal também fornece um gráfico de uso de recursos personalizável. Se você precisar ver ou editar a definição JSON subjacente de um recurso do Azure, também poderá usar o [Gerenciador de Recursos do Azure](https://resources.azure.com). Os conjuntos de dimensionamento são um recurso encontrado no Provedor de Recursos do Azure Microsoft.Compute, de modo que nesse site, você pode vê-los expandindo os seguintes links:

**Assinaturas -> sua assinatura -> resourceGroups -> provedores -> Microsoft.Compute -> virtualMachineScaleSets -> seu conjunto de dimensionamento -> etc.**

## <a name="scale-set-scenarios"></a>Cenários do conjunto de dimensionamento
Esta seção lista alguns cenários típicos de conjunto de dimensionamento. Além disso, alguns serviços do Azure de nível mais alto (como Lote, Service Fabric, Serviço de Contêiner do Azure) usam esses cenários.

* **RDP/SSH para instâncias de conjunto de dimensionamento** : um conjunto de dimensionamento é criado dentro de uma VNET e as VMs individuais dentro dele não são endereços IP públicos alocados. Essa política evita a sobrecarga de despesas e de gerenciamento da alocação de endereços IP públicos diferentes para todos os nós na sua grade de computação. Você pode se conectar a essas VMs de outros recursos em sua VNET, por exemplo, carregar balanceadores e máquinas virtuais autônomas, que podem ser alocadas a endereços IP públicos.
* **Conectar-se a VMs usando regras de NAT** - você pode criar um endereço IP público, atribuí-lo a um balanceador de carga e definir um pool NAT de entrada que mapeia portas no endereço IP para uma porta em uma máquina virtual no conjunto de dimensionamento. Por exemplo:
  
  | Fonte | Porta de origem | Destino | Porta de destino |
  | --- | --- | --- | --- |
  |  IP público |Porta 50000 |vmss\_0 |Porta 22 |
  |  IP público |Porta 50001 |vmss\_1 |Porta 22 |
  |  IP público |Porta 50002 |vmss\_2 |Porta 22 |
  
   Neste exemplo, regras NAT são definidas para habilitar uma conexão SSH para cada VM em um conjunto de dimensionamento usando um único endereço IP público: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   Veja um exemplo de como fazer o mesmo com RDP e Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **Conectar às VMs usando uma "jumpbox"** - Se você criar um conjunto de dimensionamento e uma VM autônoma na mesma rede virtual, a VM autônoma e o conjunto de dimensionamento podem se conectar um ao outro usando seus endereços IP internos, conforme definido pela sub-rede/rede virtual. Se você criar um endereço IP público e atribuí-lo à VM autônoma, pode realizar RDP ou SSH para a VM autônoma e conectar-se nessa máquina para suas instâncias de conjunto de dimensionamento. Você pode perceber, nesse momento, que um simples conjunto de dimensionamento é inerentemente mais seguro do que uma simples VM autônoma com um endereço IP público em sua configuração padrão.
  
   Por exemplo, este modelo implanta um conjunto de escala simples com uma VM autônoma: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)
* **Balanceamento de carga para instâncias de conjunto de dimensionamento** – se desejar entregar trabalho a um cluster de VMs de cálculo usando uma abordagem "round-robin", você poderá configurar um balanceador de carga do Azure com regras de balanceamento de carga de camada&4; apropriadas. É possível definir investigações para verificar se o aplicativo está em execução ao executar ping de portas com um protocolo especificado, intervalo e caminho de solicitação. O [Gateway de Aplicativo](https://azure.microsoft.com/services/application-gateway/) do Azure também oferece suporte aos conjuntos de dimensionamento, além de cenários de balanceamento de carga de camada&7; e mais sofisticados.
  
   Esse exemplo que um conjunto de dimensionamento executando servidores Web Apache e usa um balanceador de carga para balancear a carga que cada VM recebe: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) (observe o tipo de recurso de Microsoft.Network/loadBalancers e networkProfile e extensionProfile em virtualMachineScaleSet)

   Este exemplo usa um Gateway de Aplicativo. Linux:  [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway). Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway) 

* **Implantar um conjunto de dimensionamento como um cluster de computação em um gerenciador de cluster de PaaS** - Os conjuntos de dimensionamento são muitas vezes descritos como uma função de trabalho de próxima geração. Embora seja uma descrição válida, eles correm o risco de confundir os recursos de conjunto de dimensionamento com recursos dos Serviços de Nuvem do Azure. De certo modo, os conjuntos de dimensionamento fornecem uma verdadeira "função de trabalho" ou recurso de trabalho, já que eles são um recurso de computação generalizado que é independente de plataforma/tempo de execução, personalizável e se integra à IaaS do Azure Resource Manager.
  
   Uma função de trabalho dos Serviços de Nuvem, embora limitada em termos de suporte à plataforma/tempo de execução (apenas imagens da plataforma Windows) também inclui serviços como troca de VIP, definições de atualização configuráveis, configurações específicas de implantação de aplicativo/tempo de execução, que *ainda* não estão disponíveis nos conjuntos de dimensionamento ou são fornecidos por outros serviços PaaS de nível mais alto, como o Service Fabric. Você pode examinar os conjuntos de dimensionamento como uma infraestrutura que dá suporte a PaaS. Soluções PaaS como o [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) criam com base nessa infraestrutura.
  
   Para obter um exemplo dessa abordagem, o [serviço de contêiner do Azure](https://azure.microsoft.com/services/container-service/) implanta um cluster com base em conjuntos de dimensionamento com um orquestrador de contêiner: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos).

## <a name="scale-set-performance-and-scale-guidance"></a>Orientação de dimensionamento e desempenho do conjunto de dimensionamento
* Conjuntos de dimensionamento dão suporte a até 1000 VMs em um conjunto de dimensionamento. Se você criar e carregar suas próprias imagens VM personalizadas, o limite será 100. Para considerações sobre o uso de conjuntos de dimensionamento grandes, confira [Trabalhando com conjuntos de dimensionamento de máquinas virtuais grandes](virtual-machine-scale-sets-placement-groups.md).
* Você não precisa criar contas de armazenamento do Azure previamente para usar conjuntos de dimensionamento. Os conjuntos de dimensionamento dão suporte a Azure Managed Disks, o que elimina as preocupações de desempenho em relação ao número de discos por conta de armazenamento. Para saber mais, confira [Conjuntos de dimensionamento de máquinas virtuais do Azure e discos gerenciados](virtual-machine-scale-sets-managed-disks.md).
* Considere o uso de armazenamento Premium do Azure, em vez de armazenamento Standard para ter tempos de provisionamento de VM mais rápidos e mais previsíveis, além de um melhor desempenho de E/S.
* O número de VMs que você pode criar é limitado pela cota de núcleos na região em que você está implantando. Talvez seja necessário contatar o Atendimento ao Cliente para aumentar o limite da cota de computação mesmo que hoje você tenha um limite alto de núcleos para uso com serviços de nuvem do Azure. Para consultar sua cota, execute o seguinte comando da CLI do Azure: `azure vm list-usage` ou o seguinte comando do PowerShell: `Get-AzureRmVMUsage` (se estiver usando uma versão do PowerShell abaixo de 1.0, use `Get-AzureVMUsage`).

## <a name="scale-set-frequently-asked-questions"></a>Conjunto de dimensionamento - Perguntas frequentes
**P.** Quantas VMs você pode ter em um conjunto de dimensionamento?

**A.** Um conjunto de dimensionamento pode ter entre 0 e 1.000 VMs baseadas em imagens da plataforma ou 0-100 máquinas virtuais baseiam em imagens personalizadas. 

**P.** Há suporte para os discos de dados nos conjuntos de dimensionamento?

**A.** Sim. Um conjunto de dimensionamento pode definir uma configuração de unidades de dados anexadas que se aplica a todas as máquinas virtuais no conjunto. Para saber mais, confira (conjuntos de dimensionamento do Azure e discos de dados anexados)[virtual-machine-scale-sets-attached-disks.md]. Outras opções para armazenamento de dados incluem:

* Arquivos do Azure (unidades compartilhada de SMB)
* Unidade do sistema operacional
* Unidade TEMP (local, não tem relação com o armazenamento do Azure)
* Serviço de dados do Azure (por exemplo, Tabelas e Blobs do Azure)
* Serviço de dados externo (por exemplo, banco de dados remoto)

**P.** Quais são as regiões do Azure que dão suporte aos conjuntos de dimensionamento?

**A.** Todas as regiões dão suporte aos conjuntos de dimensionamento.

**P.** Como criar um conjunto de dimensionamento usando uma imagem personalizada?

**A.** Crie um Managed Disk com base em seu VHD de imagem personalizada e faça referência a ele em seu modelo de conjunto de dimensionamento. Aqui está um exemplo: [https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**P.** Se eu reduzir a capacidade do meu conjunto de dimensionamento de 20 para 15, quais VMs serão removidas?

**A.** Máquinas virtuais são removidas do conjunto de dimensionamento uniformemente entre domínios de atualização e domínios de falha para maximizar a disponibilidade. VMs com as IDs mais altas são removidas primeiro.

**P.** E se eu aumentar a capacidade de 15 para 18?

**A.** Se você aumentar a capacidade para 18, serão criadas três novas VMs. Em casa caso, a ID da instância VM será incrementada do valor mais alto anterior (por exemplo, 20, 21, 22). As VMs são balanceadas entre FDs e UDs.

**P.** Ao usar várias extensões em um conjunto de dimensionamento, posso impor uma sequência de execução?

**A.** Não diretamente, mas para a extensão de customScript, o script poderia aguardar por outra extensão ser concluída ([por exemplo, monitorando o log de extensão](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). Encontre orientações adicionais sobre o sequenciamento de extensão nesta postagem de blog: [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)(Sequenciamento de extensão em conjuntos de dimensionamento de VM do Azure).

**P.** Os conjuntos de dimensionamento funcionam com os conjuntos de disponibilidade do Azure?

**A.** Sim. Um conjunto de dimensionamento é um conjunto de disponibilidade implícito com 5 FDs e 5 UDs. Os conjuntos de dimensionamento com mais de 100 máquinas virtuais abrangem vários ‘grupos de posicionamento’ que são equivalentes a vários conjuntos de disponibilidade. Para saber mais sobre grupos de posicionamento, confira [Como trabalhar com conjuntos de dimensionamento grandes de máquinas virtuais](virtual-machine-scale-sets-placement-groups.md). Um conjunto de disponibilidade de máquinas virtuais pode existir na mesma rede virtual como um conjunto de escala de VMs. Uma configuração comum é colocar as VMs que geralmente exigem configuração exclusiva no conjunto de disponibilidade do nó de controle e nós de dados no conjunto de dimensionamento.

Mais perguntas frequentes sobre conjuntos de dimensionamento podem ser encontradas nas [Perguntas frequentes sobre conjunto de dimensionamento de máquinas virtuais do Azure](virtual-machine-scale-sets-faq.md).

