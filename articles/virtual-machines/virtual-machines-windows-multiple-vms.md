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

Há muitos cenários em que você precisa criar um grande número de máquinas virtuais semelhantes, como em casos de HPC (computação de alto desempenho), análise de dados em grande escala, servidores de camada intermediária ou back-end escalonáveis e, muitas vezes, sem monitoração de estado (como servidores Web) e bancos de dados distribuídos.

Este artigo discute as opções disponíveis para criar várias VMs no Azure que vão além de casos simples de criação manual de uma série de máquinas virtuais, que não são escalonadas corretamente caso seja necessário criar mais que algumas VMs.

Uma maneira de otimizar a criação de várias VMs semelhantes é usar o constructo de _Loops de recursos_ do Azure Resource Manager.

## Loops de recursos

Loops de recursos são uma abreviatura sintática de modelos Azure Resource Manager que permitem que você crie um conjunto de recursos com configuração semelhante em um loop. Você pode usar os loops de recursos para criar várias contas de armazenamento, interfaces de rede, máquinas virtuais, por exemplo. Para obter mais informações sobre os loops de recursos, consulte [Create VMs in Availability Sets using Resource Loops (Criar VMs em conjuntos de disponibilidade usando loops de recursos)](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/).

## Desafios da escala

Embora os loops de recurso facilitem a criação de infraestrutura de nuvem em escala e permitam que haja modelos mais concisos, alguns desafios permanecem. Por exemplo, ao criar 100 máquinas virtuais usando um loop de recursos, ao definir as VMs, você precisaria correlacionar as NICs com as VMs e as contas de armazenamento. Provavelmente, haverá um número diferente de VMs com relação ao número de contas de armazenamento e, portanto, diferentes tamanhos de loops de recursos. Esses são problemas que podem ser resolvidos, mas a complexidade aumenta significativamente com a escala.

Outro desafio ocorre quando você precisa de uma infraestrutura com dimensionamento elástico, por exemplo, o dimensionamento automático - aumentar ou diminuir automaticamente o número de VMs em resposta à carga de trabalho. As VMs não fornecem um mecanismo integrado para variar em número (escalar ou reduzir horizontalmente). Se você reduzir horizontalmente com a remoção de VMs, garantir a alta disponibilidade verificando se as VMs estão balanceadas entre domínios de falha e atualização será outra tarefa difícil.

Por fim, embora os loops de recursos sejam uma abreviação sintática, quando você usa um, várias chamadas para criar recursos vão para a malha subjacente. Quando várias chamadas estão criando recursos semelhantes, há uma oportunidade implícita para o Azure aprimorar o design e fazer otimizações para melhorar o desempenho e a confiabilidade da implantação. É aí que entram os _Conjuntos de escala de máquina virtual_.

## Conjuntos de escala de máquina virtual

Os conjuntos de escala de máquina virtual são um recurso de computação do Azure para implantar e gerenciar um conjunto de VMs idênticas. Com todas as VMs configuradas da mesma forma, os Conjuntos de escala de VM são fáceis de reduzir e escalar horizontalmente simplesmente alterando o número de VMs no conjunto, e podem ser configurados para o dimensionamento automático com base nas demandas de carga de trabalho.

Para aplicativos que necessitam dimensionar os recursos de computação, as operações de dimensionamento são balanceadas implicitamente entre domínios de falha e atualização.

Em vez de correlacionar vários recursos, como NICs e VMs, um conjunto de escala tem propriedades de rede, armazenamento, máquina virtual e extensão que você pode configurar centralmente.

Para obter uma introdução aos conjuntos de escala de VM, consulte a [página do produto](https://azure.microsoft.com/services/virtual-machine-scale-sets/), e para obter informações mais detalhadas, consulte a [documentação](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).

<!---HONumber=AcomDC_0504_2016-->