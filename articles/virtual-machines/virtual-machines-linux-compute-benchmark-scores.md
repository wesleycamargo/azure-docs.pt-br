<properties
 pageTitle="Pontuações de parâmetro de comparação de computação de VMs do Linux | Microsoft Azure"
 description="Compare as pontuações de parâmetro de comparação de computação do CoreMark de VMs do Linux que executam o Linux"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="danlep"/>

# Pontuações de parâmetro de comparação de computação de VMs do Linux

As pontuações de parâmetro de comparação CoreMark a seguir mostram o desempenho de computação de uma lista organizada de VMs de alto desempenho do Azure que executam o Ubuntu. As pontuações de parâmetro de comparação de computação também estão disponíveis para [VMs do Windows](virtual-machines-windows-compute-benchmark-scores.md).




## Série A – computação intensiva


Tamanho | vCPUs | Nós NUMA | CPU | Execuções | Iterações/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_A8 | 8 | 1 | Intel Xeon CPU E5-2670 0 a 2,6 GHz| 179 | 110\.294 | 554
Standard\_A9 | 16 | 2 | Intel Xeon CPU E5-2670 0 a 2,6 GHz| 189 | 210\.816| 2\.126
Standard\_A10 | 8 | 1 | Intel Xeon CPU E5-2670 0 a 2,6 GHz| 188 | 110\.025 | 1\.045
Standard\_A11 | 16 | 2 | Intel Xeon CPU E5-2670 0 a 2,6 GHz| 188 | 210\.727| 2\.073

## Série Dv2


Tamanho | vCPUs | Nós NUMA | CPU | Execuções | Iterações/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_D1\_v2 | 1 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 140 | 14\.852 | 780
Standard\_D2\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 133 | 29\.467 | 1\.863
Standard\_D3\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 139 | 56\.205 | 1\.167
Standard\_D4\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 126 | 108\.543 | 3\.446
Standard\_D5\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 a 2,4 GHz | 126 | 205\.332 | 9\.998
Standard\_D11\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 125 | 28\.598 | 1\.510
Standard\_D12\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 131 | 55\.673 | 1\.418
Standard\_D13\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 140 | 107\.986 | 3\.089
Standard\_D14\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 a 2,4 GHz | 140 | 208\.186 | 8\.839
Standard\_D15\_v2 | 20 | 2 | Intel Xeon E5-2673 v3 a 2,4 GHz |28 | 268\.560 | 4\.667

## Série F

Tamanho | vCPUs | Nós NUMA | CPU | Execuções | Iterações/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_F1 | 1 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 154 | 15\.602 | 787
Standard\_F2 | 2 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 126 | 29\.519 | 1\.233
Standard\_F4 | 4 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 147 | 58\.709 | 1\.227
Standard\_F8 | 8 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 224 | 112\.772 | 3\.006
Standard\_F16 | 16 | 2 | Intel Xeon E5-2673 v3 a 2,4 GHz | 42 | 218\.571 | 5\.113



## Série G


Tamanho | vCPUs | Nós NUMA | CPU | Execuções | Iterações/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_G1 | 2 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 83 | 31\.310 | 2\.891
Standard\_G2 | 4 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 84 | 60\.112 | 3\.537
Standard\_G3 | 8 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 84 | 107\.522 | 4\.537
Standard\_G4 | 16 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 83 | 195\.116 | 5\.024
Standard\_G5 | 32 | 2 | Intel Xeon E5-2698B v3 a 2 GHz | 84 | 360\.329 | 14\.212

## Série GS


Tamanho | vCPUs | Nós NUMA | CPU | Execuções | Iterações/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_GS1 | 2 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 84 | 28\.613 | 1\.884
Standard\_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 83 | 54\.348 | 3\.474
Standard\_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 83 | 104\.564 | 1\.834
Standard\_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 84 | 194\.111 | 4\.735
Standard\_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 a 2 GHz | 84 | 357\.396 | 16\.228


## Série H

Tamanho | vCPUs | Nós NUMA | CPU | Execuções | Iterações/s | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_H8 | 8 | 1 | Intel Xeon E5-2667 v3 a 3,2 GHz | 28 | 140\.782 | 2\.512
Standard\_H16 | 16 | 2 | Intel Xeon E5-2667 v3 a 3,2 GHz | 35 | 275\.289 | 7\.110 
Standard\_H18m | 8 | 1 | Intel Xeon E5-2667 v3 a 3,2 GHz | 28 | 139\.071 | 3\.988 
Standard\_H16m | 16 | 2 | Intel Xeon E5-2667 v3 a 3,2 GHz | 28 | 275\.988 | 6\.963 
Standard\_H16r | 16 | 2 | Intel Xeon E5-2667 v3 a 3,2 GHz | 28 | 273\.982 | 6\.069 
Standard\_H16mr | 16 | 2 | Intel Xeon E5-2667 v3 a 3,2 GHz | 28 | 274\.523 | 5\.698 



## Sobre o CoreMark

Os números do Linux foram calculados com a execução do [CoreMark](http://www.eembc.org/coremark/faq.php) no Ubuntu. O CoreMark foi configurado com o número de threads definidos como o número de CPUs virtuais e a simultaneidade definida como PThreads. O número de iterações de destino foi ajustado com base no desempenho esperado para fornecer um tempo de execução de pelo menos 20 segundos (normalmente muito mais). A pontuação final representa o número de iterações concluídas dividido pelo número de segundos necessário para executar o teste. Cada teste foi executado, no mínimo, sete vezes em cada VM. Em outubro de 2015, foram executados testes em várias VMs (exceto para série H) em todas as regiões públicas do Azure nas quais havia suporte para a VM na data de execução.

## Próximas etapas



* Para obter capacidades de armazenamento, detalhes do disco e considerações adicionais sobre como escolher um dos diferentes tamanhos de VM, veja [Tamanhos das máquinas virtuais](virtual-machines-linux-sizes.md).

* Para executar os scripts do CoreMark nas VMs do Linux, baixe o [pacote de scripts do CoreMark](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip).

<!---HONumber=AcomDC_0928_2016-->