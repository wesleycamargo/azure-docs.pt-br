<properties
	pageTitle="Criar várias máquinas virtuais | Microsoft Azure"
	description="Opções para criar várias máquinas virtuais no Windows"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="guybo"/>

# Criar várias máquinas virtuais do Azure

Há muitos cenários em que você precisa criar um grande número de máquinas virtuais (VMs) semelhantes. Alguns exemplos incluem HPC (computação de alto desempenho), análise de dados em grande escala, servidores de camada intermediária ou back-end escalonáveis e, muitas vezes, sem monitoração de estado (como servidores Web) e bancos de dados distribuídos.

Este artigo discute as opções disponíveis para criar diversas VMs no Azure. Essas opções vão além dos simples casos em que você cria manualmente uma série de VMs. Para criar várias VMs, os processos que você normalmente usa não serão bem dimensionados se você precisar criar mais de um punhado de VMs.

Uma maneira de criar várias VMs semelhantes é usar o constructo de _loops de recursos_ do Azure Resource Manager.

## Loops de recursos

Os loops de recursos são um tipo de taquigrafia sintática em modelos do Azure Resource Manager. Os loops de recursos podem criar um conjunto de recursos configurados da mesma forma em um loop. Você pode usar os loops de recursos para criar várias contas de armazenamento, interfaces de rede ou máquinas virtuais. Para saber mais sobre os loops de recursos, consulte [Create VMs in Availability Sets using Resource Loops (Criar VMs em conjuntos de disponibilidade usando loops de recursos)](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/).

## Desafios da escala

Embora os loops de recursos facilitem a criação de uma infraestrutura de nuvem em escala e produzam modelos mais concisos, alguns desafios permanecem. Por exemplo, se você usar um loop de recursos para criar a 100 máquinas virtuais, precisará correlacionar os controladores de interface de rede (NICs) a VMs e a contas de armazenamento correspondentes. Como o número de VMs provavelmente será diferente do número de contas de armazenamento, você terá de lidar também com tamanhos de loop de recursos diferentes. Esses são problemas que podem ser resolvidos, mas a complexidade aumenta significativamente com a escala.

Outro desafio ocorre quando você precisa de uma infraestrutura que seja dimensionada elasticamente. Por exemplo, convém uma infraestrutura de dimensionamento automático que aumenta ou diminui o número de VMs em resposta à carga de trabalho. As VMs não fornecem um mecanismo integrado para variar em número (escalar ou reduzir horizontalmente). Se você reduzir horizontalmente com a remoção de VMs, será difícil garantir a alta disponibilidade ao verificar se as VMs estão balanceadas entre domínios de falha e atualização.

Por fim, quando você usa um loop de recursos, várias chamadas para criar recursos passam para a malha subjacente. Quando várias chamadas criam recursos semelhantes, o Azure tem uma oportunidade implícita de aprimorar o design e de fazer otimizações para melhorar o desempenho e a confiabilidade da implantação. É aí que entram os _conjuntos de escala de máquina virtual_.

## Conjuntos de escala de máquina virtual

Os conjuntos de escala de máquina virtual são um recurso do Azure Cloud Services para implantar e gerenciar um conjunto de VMs idênticas. Com todas as VMs configuradas da mesma forma, os conjuntos de escala de VM são fáceis de reduzir ou de expandir. Você simplesmente altera o número de VMs no conjunto. Você também pode configurar conjuntos de escala de VM para dimensionamento automático com base nas demandas da carga de trabalho.

Para aplicativos que necessitam dimensionar os recursos de computação, as operações de dimensionamento são balanceadas implicitamente entre domínios de falha e atualização.

Em vez de correlacionar vários recursos, como NICs e VMs, um conjunto de escala de VM tem propriedades de rede, armazenamento, máquina virtual e extensão que você pode configurar centralmente.

Para obter uma introdução aos conjuntos de escala de VM, consulte a [página de produto de conjuntos de escala de máquina virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/). Para obter informações mais detalhadas, acesse a [documentação de conjuntos de escala de máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).

<!---HONumber=AcomDC_0518_2016-->