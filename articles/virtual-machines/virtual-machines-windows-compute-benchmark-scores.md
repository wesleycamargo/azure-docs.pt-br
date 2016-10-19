<properties
 pageTitle="Pontuações de parâmetro de comparação de computação de VMs do Windows | Microsoft Azure"
 description="Compare as pontuações de parâmetro de comparação de computação do SPECint de VMs do Linux que executam o Windows Server"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="danlep"/>

# Pontuações de parâmetro de comparação de computação de VMs do Windows

As pontuações de parâmetro de comparação SPECInt a seguir mostram o desempenho de computação de uma lista organizada de VMs de alto desempenho do Azure que executam o Windows Server. As pontuações de parâmetro de comparação de computação também estão disponíveis para [VMs do Linux](virtual-machines-linux-compute-benchmark-scores.md).



## Série A – computação intensiva


Tamanho | vCPUs | Nós NUMA | CPU | Execuções | Taxa base média | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_A8 | 8 | 1 | Intel Xeon CPU E5-2670 0 a 2,6 GHz | 10 | 236\.1 | 1,1
Standard\_A9 | 16 | 2 | Intel Xeon CPU E5-2670 0 a 2,6 GHz | 10 | 450\.3 | 7\.0
Standard\_A10 | 8 | 1 | Intel Xeon CPU E5-2670 0 a 2,6 GHz | 5 | 235\.6 | 0\.9
Standard\_A11 | 16 | 2 | Intel Xeon CPU E5-2670 0 a 2,6 GHz |7 | 454\.7 | 4\.8

## Série Dv2


Tamanho | vCPUs | Nós NUMA | CPU | Execuções | Taxa base média | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_D1\_v2 | 1 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 83 | 36\.6 | 2\.6
Standard\_D2\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 27 | 70\.0 | 3\.7
Standard\_D3\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 19 | 130\.5 | 4\.4
Standard\_D4\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 19 | 238\.1 | 5\.2
Standard\_D5\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 a 2,4 GHz | 14 | 460\.9 | 15\.4
Standard\_D11\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 19 | 70\.1 | 3\.7
Standard\_D12\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 2 | 132\.0 | 1\.4
Standard\_D13\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 17 | 235\.8 | 3\.8
Standard\_D14\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 a 2,4 GHz | 15 | 460\.8 | 6\.5


## Série G, série GS


Tamanho | vCPUs | Nós NUMA | CPU | Execuções | Taxa base média | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_G1, Standard\_GS1 | 2 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 31 | 71\.8 | 6\.5
Standard\_G2, Standard\_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 5 | 133\.4 | 13\.0
Standard\_G3, Standard\_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 6 | 242\.3 | 6,0
Standard\_G4, Standard\_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 15 | 398\.9 | 6,0
Standard\_G5, Standard\_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 a 2 GHz | 22 | 762\.8 | 3\.7

## Série H

Tamanho | vCPUs | Nós NUMA | CPU | Execuções | Iterações/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_H8 | 8 | 1 | Intel Xeon E5-2667 v3 a 3,2 GHz | 5 | 297,4 | 0\.9
Standard\_H16 | 16 | 2 | Intel Xeon E5-2667 v3 a 3,2 GHz | 5 | 575,8 | 6,8
Standard\_H8m | 8 | 1 | Intel Xeon E5-2667 v3 a 3,2 GHz | 5 | 297 | 1\.2
Standard\_H16m | 16 | 2 | Intel Xeon E5-2667 v3 a 3,2 GHz | 5 | 572,2 | 3\.9
Standard\_H16r | 16 | 2 | Intel Xeon E5-2667 v3 a 3,2 GHz | 5 | 573,2 | 2,9
Standard\_H16mr | 16 | 2 | Intel Xeon E5-2667 v3 a 3,2 GHz | 7 | 569,6 | 2\.8

## Sobre o SPECint

Os números do Windows foram calculados executando o [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) no Windows Server. O SPECint foi executado usando a opção de taxa base (SPECint\_rate2006), com uma cópia por núcleo. O SPECint consiste em 12 testes separados, cada um deles executado três vezes, usando o valor mediano de cada teste e ponderando-os para formar uma pontuação composta. Em seguida, eles foram executados em várias VMs para fornecer as pontuações médias mostradas.


## Próximas etapas

* Para obter capacidades de armazenamento, detalhes do disco e considerações adicionais sobre como escolher um dos diferentes tamanhos de VM, veja [Tamanhos das máquinas virtuais](virtual-machines-windows-sizes.md).

<!---HONumber=AcomDC_0928_2016-->