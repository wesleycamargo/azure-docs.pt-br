---
title: "Visão Geral dos Conjuntos de Dimensionamento de Máquina Virtual | Microsoft Docs"
description: "Saiba mais sobre os Conjuntos de Dimensionamento de Máquina Virtual"
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
ms.date: 1/25/2017
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 868d65642ab9ca3e1c35d33a7cb9e9dd8c31c430
ms.openlocfilehash: f945ac0357c11c70780dea8e62f094457a213d96


---
# <a name="virtual-machine-scale-sets-overview"></a>Conjuntos de Dimensionamento de Máquina Virtual - Visão Geral
Os conjuntos de dimensionamento de máquina virtual são um recurso de Computação do Azure que você pode usar para implantar e gerenciar um conjunto de VMs idênticas. Com todas as VMs configuradas igualmente, os conjuntos de escala de VM foram desenvolvidos para dar suporte ao verdadeiro dimensionamento automático (não é necessário o pré-provisionamento das VMs) e, desse modo, facilitar a criação de serviços em grande escala visando big compute, big data e cargas de trabalho contidas.

Para aplicativos que necessitam dimensionar os recursos de computação, as operações de dimensionamento são balanceadas implicitamente entre domínios de falha e atualização. Para ver uma introdução aos conjuntos de dimensionamento de VM, confira o [Comunicado no blog do Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Examine esses vídeos para saber mais sobre conjuntos de dimensionamento de VM:

* [Mark Russinovich fala sobre os Conjuntos de Dimensionamento do Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Conjuntos de Dimensionamento de Máquina Virtual com Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>Criando e gerenciando conjuntos de dimensionamento de VM
Você pode criar um conjunto de dimensionamento de VM no [Portal do Azure](https://portal.azure.com) selecionando *novo* e digitando "escala" na barra de pesquisa. Você verá "Escala de máquina virtual definida" nos resultados. A partir daí, você pode preencher os campos obrigatórios para personalizar e implantar o conjunto de escala. Observe existe também são opções no portal para configurar as regras básicas AutoEscala baseadas no uso da CPU.

Os conjuntos de dimensionamento de VM também podem ser definidos e implantados usando modelos JSON e [APIs REST](https://msdn.microsoft.com/library/mt589023.aspx) , assim como as VMs individuais do Azure Resource Manager. Portanto, quaisquer métodos de implantação do Gerenciador de Recursos do Azure padrão podem ser usados. Para obter mais informações sobre modelos, confira [Criação de modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md).

Um conjunto de modelos de exemplo para conjuntos de escala de VM pode ser encontrado no repositório GitHub [aqui](https://github.com/Azure/azure-quickstart-templates) (procure modelos com *vmss* no título)

Nas páginas de detalhes para esses modelos, você verá um botão que vincula ao recurso de implantação do portal. Para implantar o conjunto de dimensionamento de VM, clique no botão e preencha todos os parâmetros obrigatórios no portal. Se você não tiver certeza se um recurso oferece suporte a letras maiúsculas ou combinadas, é mais seguro usar sempre letras minúsculas e números em valores de parâmetro. Também é uma prática dissecar vídeo de um modelo de conjunto de dimensionamento de VM aqui:

[Dissecação de Modelo do Conjunto de Dimensionamento de VM](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>Expandindo e reduzindo um conjunto de dimensionamento de VM
Para aumentar ou diminuir o número de máquinas virtuais em um conjunto de dimensionamento de VM, basta alterar a propriedade *capacity* e reimplantar o modelo. Essa simplicidade facilita escrever sua própria camada de dimensionamento personalizada se você quiser definir eventos de escala personalizada que não têm suporte do dimensionamento automático do Azure.

Se estiver reimplantando um modelo para alterar a capacidade, você pode definir um modelo muito menor que inclui apenas o pacote de propriedades ‘SKU’ com a capacidade atualizada. Veja um exemplo disso [aqui.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)

Para percorrer as etapas para criar um conjunto de dimensionamento que foi dimensionado automaticamente, confira [Dimensionar automaticamente as máquinas em um Conjunto de dimensionamento de máquina virtual](virtual-machine-scale-sets-windows-autoscale.md)

## <a name="monitoring-your-vm-scale-set"></a>Monitoramento de seu conjunto de dimensionamento de VM
O [portal do Azure](https://portal.azure.com) listas conjuntos de escala e mostra as propriedades e operações básicas, incluindo listar VMs no conjunto e um gráfico de uso de recursos. Para obter mais detalhes você pode usar o [Azure Resource Explorer](https://resources.azure.com) para exibir os conjuntos de dimensionamento de VM. Os conjuntos de dimensionamento de VM são um recurso encontrado em Microsoft.Compute, de modo que nesse site, você pode vê-los expandindo os seguintes links:

**Assinaturas -> sua assinatura -> resourceGroups -> provedores -> Microsoft.Compute -> virtualMachineScaleSets -> seu conjunto de escala de VM -> etc.**

## <a name="vm-scale-set-scenarios"></a>Cenários do conjunto de dimensionamento de VM
Esta seção lista alguns cenários típicos de conjunto de dimensionamento de VM. Além disso, alguns serviços do Azure de nível mais alto (como Lote, Service Fabric, Serviço de Contêiner do Azure) usarão esses cenários.

* **RDP/SSH para instâncias de conjunto de escala de VM** : um conjunto de escala de VM é criado dentro de uma VNET e as VMs individuais dentro dele não são endereços IP públicos alocados. Isso é bom, pois geralmente você não quer as despesas e os custos gerais de gerenciamento de alocar endereços IP públicos separados para todos os recursos sem estado na sua grade de computação, e você pode se conectar facilmente a essas VMs de outros recursos na sua rede virtual, incluindo aqueles que têm endereços IP públicos como balanceadores de carga ou máquinas virtuais autônomas.
* **Conectar-se a VMs usando regras de NAT** - você pode criar um endereço IP público, atribuí-lo a um balanceador de carga e definir um pool NAT de entrada que mapeará portas no endereço IP para uma porta em uma máquina virtual no conjunto de dimensionamento de VM. Por exemplo:
  
  | Fonte | Porta de origem | Destino | Porta de destino |
  | --- | --- | --- | --- |
  |  IP público |Porta 50000 |vmss\_0 |Porta 22 |
  |  IP público |Porta 50001 |vmss\_1 |Porta 22 |
  |  IP público |Porta 50002 |vmss\_2 |Porta 22 |
  
   Veja um exemplo de como criar um conjunto de dimensionamento de VM que usa regras NAT para habilitar a conexão SSH para cada VM em um conjunto de dimensionamento usando um único IP público: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   Veja um exemplo de como fazer o mesmo com RDP e Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **Conectar às VMs usando uma "jumpbox"** - Se você criar um conjunto de dimensionamento de VM e uma VM autônoma na mesma rede virtual, a VM autônoma e o conjunto de dimensionamento de VM podem se conectar um ao outro usando seus endereços IP internos, conforme definido pela sub-rede/rede virtual. Se você criar um endereço IP público e atribuí-lo à VM autônoma, pode realizar RDP ou SSH para a VM autônoma e conectar-se nessa máquina para suas instâncias de conjunto de dimensionamento de VM. Você pode perceber, nesse momento, que um simples conjunto de escalas de VM é inerentemente mais seguro do que uma simples VM autônoma com um endereço IP público em sua configuração padrão.
  
   Por exemplo, este modelo implanta um conjunto de escala simples com uma VM autônoma: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)
* **Balanceamento de carga para instâncias de conjunto de dimensionamento de VM** – se desejar entregar trabalho a um cluster de VMs de cálculo usando uma abordagem "round-robin", você poderá configurar um balanceador de carga do Azure com regras de balanceamento de carga de camada&4; apropriadas. É possível definir investigações para verificar se o aplicativo está em execução ao executar ping de portas com um protocolo especificado, intervalo e caminho de solicitação. O [Gateway de Aplicativo](https://azure.microsoft.com/services/application-gateway/) do Azure também oferece suporte aos conjuntos de dimensionamento, além de cenários de balanceamento de carga de camada&7; e mais sofisticados.
  
   Aqui está um exemplo que cria um Conjunto de Escala de VM executando servidores Web Apache e usa um balanceador de carga para balancear a carga que cada VM recebe: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) (observe o tipo de recurso de Microsoft.Network/loadBalancers e networkProfile e extensionProfile em virtualMachineScaleSet)
* **Implantar um  conjunto de dimensionamento de VM como um cluster de computação em um gerenciador de cluster de PaaS** - Os  conjuntos de dimensionamento de VM são muitas vezes descritos como uma função de trabalho de próxima geração. É uma descrição válida, mas há o risco de confundir recursos do conjunto de escalas com recursos da função de trabalho PaaS v1. De certo modo, os conjuntos de dimensionamento de VMs fornecem uma verdadeira "função de trabalho" ou recurso de trabalho, já que eles fornecem um recurso de computação generalizado que é independente de plataforma/tempo de execução, personalizável e se integra à IaaS do Azure Resource Manager.
  
   Uma função de trabalho PaaS v1, embora limitada em termos de suporte à plataforma/tempo de execução (apenas imagens da plataforma Windows) também inclui serviços como troca de VIP, definições de atualização configuráveis, configurações específicas de implantação de aplicativo/tempo de execução, que *ainda* não estão disponíveis nos conjuntos de dimensionamento de VMs ou serão fornecidos por outros serviços PaaS de nível mais alto, como o Service Fabric. Com isso em mente, você pode observar os conjuntos de escalas de VM como uma infraestrutura que oferece suporte à PaaS. Isto é, as soluções PaaS, como o Service Fabric ou os gerenciadores de clusters, como o Mesos, podem ser criadas com base nos conjuntos de escalas de VM como uma camada de computação escalonável.
  
   Para obter um exemplo dessa abordagem, o serviço de contêiner do Azure implanta um cluster com base em conjuntos de escala com um orquestrador de contêiner: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos).

## <a name="vm-scale-set-performance-and-scale-guidance"></a>Orientação de dimensionamento e desempenho do conjunto de dimensionamento de VM
* Planeje no máximo 20 VMs por conta de armazenamento ao usar conjuntos de dimensionamento com discos não gerenciados (a menos que você defina a propriedade *provisionamento excessivo* como "false", quando então que você pode ir até 40). Com discos gerenciados, esses limites de conta por armazenamento não são aplicáveis.
* Espalhar as primeiras letras dos nomes de conta de armazenamento tanto quanto possíveis ao usar a escala conjuntos com discos não gerenciados. Os modelos de VMSS de exemplo nos [Modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/) fornecem exemplos de como fazer isso.
* Se estiver usando VMs personalizadas com discos não gerenciados, planeje-se para ter não mais que 40 VMs por conjunto de escalas de VM, em uma única conta de armazenamento. Você precisará da imagem previamente copiada na conta de armazenamento para poder começar a implantação do conjunto de escalas de VM. Conjuntos de escala com discos gerenciados oferecerá suporte a até 100 VMs de imagem personalizada. Consulte as Perguntas frequentes para obter mais informações.
* Planeje-se para ter não mais que 4.096 VMs por rede virtual.
* O número de VMs que você pode criar é limitado pela cota de núcleos na região em que você está implantando. Talvez seja necessário contatar o Atendimento ao Cliente para aumentar o limite da cota de computação mesmo que hoje você tenha um limite alto de núcleos para uso com serviços de nuvem ou IaaS v1. Para consultar sua cota, você pode executar o seguinte comando da CLI do Azure: `azure vm list-usage` e o seguinte comando do PowerShell: `Get-AzureRmVMUsage` (se estiver usando uma versão do PowerShell abaixo de 1.0, use `Get-AzureVMUsage`).

## <a name="vm-scale-set-frequently-asked-questions"></a>Conjunto de dimensionamento de VM - Perguntas frequentes
**P.** Quantas VMs você pode ter em um conjunto de dimensionamento de VM?

**A.** Com discos gerenciados seu conjunto de escala pode ter entre 0 e 1.000 VMs baseadas em imagens da plataforma ou 0-100 máquinas virtuais baseiam em imagens personalizadas. Para discos não gerenciados (onde você define suas próprias contas de armazenamento), o limite é 100 VMs para imagens de plataforma e até 40 para imagens personalizadas (se o *provisionamento excessivo* propriedade for definida como 20 "false", por padrão), como imagens personalizadas com discos não gerenciados são limitadas a uma única conta de armazenamento.

**P.** Há suporte para os discos de dados nos conjuntos de dimensionamento de VM?

**A.** Sim. Um conjunto de dimensionamento definido com armazenamento gerenciado pode definir uma configuração de unidades de dados anexadas que se aplique a todas as máquinas virtuais no conjunto. Conjuntos de escala que não são definidos com armazenamento gerenciado não tem unidades de dados anexado. Outras opções para armazenamento de dados incluem:

* Arquivos do Azure (unidades compartilhada de SMB)
* Unidade do sistema operacional
* Unidade TEMP (local, não tem relação com o armazenamento do Azure)
* Serviço de dados do Azur (por exemplo, Tabelas e Blobs do Azure)
* Serviço de dados externo (por exemplo, Banco de Dados Remoto)

**P.** Quais são as regiões do Azure que dão suporte aos conjuntos de escala de VM?

**A.** Todas as regiões dão suporte aos conjuntos de dimensionamento de VMs.

**P.** Como criar um conjunto de escala de VM usando uma imagem personalizada?

**A.** Deixe a propriedade vhdContainers em branco (ou omita-a) e forneça o uri da propriedade de imagem. Por exemplo: [201-vmss-windows-customimage]. (https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-customimage)


**P.** Se eu reduzir a capacidade do meu conjunto de dimensionamento de VM de 20 para 15, quais VMs serão removidas?

**A.** Máquinas virtuais são removidas do conjunto de dimensionamento uniformemente entre domínios de atualização e domínios de falha para maximizar a disponibilidade. VMs com as IDs mais altas são removidas primeiro.

**P.** E se eu aumentar a capacidade de 15 para 18?

**A.** Se você aumentar a capacidade para 18, serão criadas três novas VMs. Cada vez que a ID da instância VM será incrementada do valor mais alto anterior (por exemplo, 20, 21, 22). As VMs são balanceadas entre FDs e UDs.

**P.** Ao usar várias extensões em um conjunto de dimensionamento de VM, posso impor uma sequência de execução?

**A.** Não diretamente, mas para a extensão de customScript, o script poderia aguardar por outra extensão ser concluída ([por exemplo, monitorando o log de extensão](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). Encontre orientações adicionais sobre o sequenciamento de extensão nesta postagem de blog: [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)(Sequenciamento de extensão em conjuntos de dimensionamento de VM do Azure).

**P.** Os conjuntos de dimensionamento de VM funcionam com os conjuntos de disponibilidade do Azure?

**A.** Sim. Um conjunto de escala de VM é um conjunto de disponibilidade implícito com 5 FDs e 5 UDs. Não é preciso configurar nada em virtualMachineProfile. Os conjuntos de dimensionamento de VMs com mais de 100 máquinas virtuais abrangem vários ‘grupos de posicionamento’ que são equivalentes a vários conjuntos de disponibilidade. Um conjunto de disponibilidade de máquinas virtuais pode existir na mesma rede virtual como um conjunto de escala de VMs. Uma configuração comum é colocar as VMs que geralmente exigem configuração exclusiva no conjunto de disponibilidade do nó de controle e nós de dados no conjunto de escala.



<!--HONumber=Feb17_HO1-->


