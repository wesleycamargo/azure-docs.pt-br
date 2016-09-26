 <properties
   pageTitle="Azure e Linux | Microsoft Azure"
   description="Descreve os serviços de Computação, Armazenamento e Rede do Azure com máquinas virtuais Linux."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="rickstercdn"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/06/2016"
   ms.author="rclaus"/>

# Azure e Linux
O Microsoft Azure é uma coleção crescente de serviços de nuvem públicos integrados, incluindo análise, Máquinas Virtuais, bancos de dados, mobilidade, rede, armazenamento, e Web, ideais para hospedar suas soluções. O Microsoft Azure fornece uma plataforma de computação dimensionável que lhe permite pagar apenas pelo que você usa, quando quiser, sem precisar investir em hardware local. O Azure está pronto quando você está, para escalar suas soluções horizontal e verticalmente, em qualquer escala necessária para atender às necessidades de seus clientes.
 
## Máquinas Virtuais do Azure
As máquinas virtuais do Azure permitem que você implante uma ampla gama de soluções de computação de forma ágil. Você pode implantar praticamente qualquer carga de trabalho e qualquer linguagem em praticamente qualquer sistema operacional, Windows, Linux, ou um personalizado criado de nossa lista cada vez maior de parceiros. Ainda não consegue encontrar o que você está procurando? Não se preocupe: você também pode usar suas próprias imagens locais.
 
## Introdução ao Linux no Microsoft Azure

Use Máquinas Virtuais do Microsoft Azure, Armazenamento e Rede juntos para fornecer "infraestrutura-como um serviço" em escala de Internet para sua necessidades de computação do Linux. Para começar a usar o Linux no Azure, você precisará do seguinte:

1. Uma conta de avaliação gratuita. [Obtenha uma](https://azure.microsoft.com/pricing/free-trial/).
2. A Interface de Linha de Comando do Azure para Linux, Mac e Windows (a CLI do Azure). [Instale-a](../xplat-cli-install.md).
3. Uma VM do Linux. [Crie-a](virtual-machines-linux-quick-create-cli.md).
4. Mais informações sobre o Linux e o Azure, incluindo como se qualificar para o [SLA (Contrato de Nível de Serviço)](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/). **Leia este documento, mesmo se você odeia documentos legais**.

## Logística: regiões, distribuições, disponibilidade, tamanhos de VM e cotas
### Regiões
Os recursos do Microsoft Azure são distribuídos em várias regiões geográficas em todo o mundo. Uma "região" representa vários datacenters em uma única área geográfica. A partir de 1º de janeiro de 2016, isso inclui: oito na América, dois na Europa, seis no Pacífico Asiático, dois na China continental e três na Índia. Se você quiser uma lista completa de todas as regiões do Azure, manteremos **[aqui](https://azure.microsoft.com/regions/)** uma lista de regiões existentes e anunciadas recentemente.

### Distribuições
O Microsoft Azure dá suporte à execução de várias distribuições do Linux populares fornecidas e mantidas por vários parceiros. Você encontra distribuições como CentOS, Debian, Red Hat Enterprise, Ubuntu, FreeBSD e muito mais no Azure Marketplace. Trabalhamos ativamente com várias comunidades do Linux para adicionar ainda mais opções à lista de Distribuições Endossadas. **[Confira as distribuições atuais](virtual-machines-linux-endorsed-distros.md)** Se sua distribuição preferencial do Linux não estiver presente na galeria no momento, você poderá "trazer sua própria VM do Linux" seguindo as diretrizes **[nesta página.](virtual-machines-linux-create-upload-generic.md)**

## Disponibilidade e o SLA do Microsoft Azure
Para sua implantação se qualificar para nosso Contrato de Nível de Serviço de 99,95 de VM, você precisará implantar duas ou mais VMs que executem sua carga de trabalho dentro de um conjunto de disponibilidade. Isso garante que suas VMs sejam distribuídas entre vários domínios de falha em nossos datacenters e sejam implantadas em hosts com janelas de manutenção diferentes. Para obter detalhes completos sobre nosso SLA, você pode exibi-lo **[online aqui](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)**.

## Cotas e tamanhos de VM
Ao implantar uma VM no Azure, você seleciona um tamanho de VM de nossa série de tamanhos que é adequado para sua carga de trabalho. O tamanho também afeta a capacidade de processamento, a memória e o armazenamento da máquina virtual. Você será cobrado com base na quantidade de tempo pelo qual a máquina virtual estiver em execução e consumindo seus recursos alocados. Para obter uma lista mais completa, confira o artigo a seguir sobre [tamanhos de máquinas virtuais](virtual-machines-linux-sizes.md).

Aqui estão algumas diretrizes básicas para selecionar um tamanho de VM de uma de nossas séries (A, D, DS, G e GS).

* VMs da série A são nossas VMs básicas econômicas para cargas de trabalho leves e cenários de desenvolvimento e teste. Elas estão amplamente disponíveis em todas as regiões e podem se conectar a e usar todos os recursos padrão disponíveis para máquinas virtuais.
* Os tamanhos da série A (A8 - A11) são configurações especiais com uso intensivo de computação adequadas para aplicativos de cluster com computação de alto desempenho.
* As VMs da série D são projetadas para executar aplicativos que exigem maior capacidade de computação e de desempenho de disco temporário. As VMs da série D fornecem processadores mais rápidos, uma maior taxa de memória por núcleo e uma unidade de estado sólido (SSD) para o disco temporário.
* A série Dv2 é a última versão de nossa série D, com uma CPU mais poderosa. A CPU da série Dv2 é aproximadamente 35% mais rápida do que a CPU da série D. Ela se baseia na última geração do processador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz e, com a Intel Turbo Boost Technology 2.0, pode chegar a até 3,2 GHz. A série Dv2 tem as mesmas configurações de memória e disco que a série D.
* As VMs da série G oferecem a maior memória e são executadas em hosts com processadores da família Intel Xeon E5 V3.

> [AZURE.NOTE] As VMs da série DS e da série GS têm acesso ao Armazenamento Premium: nosso armazenamento de alto desempenho e baixa latência com suporte de SSD para cargas de trabalho com uso intensivo de E/S. O Armazenamento Premium está disponível em determinadas regiões. Para obter detalhes, confira **[Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../storage/storage-premium-storage.md)**.

Cada assinatura do Azure tem limites de cota padrão que podem afetar a implantação de muitas VMs para seu projeto. O limite atual por assinatura é de 20 VMs por região. Esses limites de cota podem ser aumentados com a emissão de um tíquete de suporte para solicitar um aumento de limite. Para obter mais detalhes sobre os limites de cota, confira **[Limites do serviço de assinatura do Azure](../azure-subscription-service-limits.md)**

## Próximas etapas

Uma conta de avaliação gratuita. **[Obtenha uma.](https://azure.microsoft.com/pricing/free-trial/)** Se você já tiver uma, para experimentá-la, **[instale a CLI do Azure.](../xplat-cli-install.md)** Se tiver feito isso, [crie uma VM do Linux agora](virtual-machines-linux-quick-create-cli.md).

<!---HONumber=AcomDC_0914_2016-->