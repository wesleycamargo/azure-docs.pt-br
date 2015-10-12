<properties
 pageTitle="Tamanhos de máquina virtual"
 description="Lista os diferentes tamanhos de máquinas virtuais e suas capacidades."
 services="virtual-machines"
 documentationCenter=""
 authors="cynthn"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>

<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="09/02/2015"
 ms.author="cynthn"/>

# Tamanhos das máquinas virtuais

## Visão geral

Este artigo descreve os tamanhos e as opções disponíveis para os recursos de computação baseados em máquina virtual que você pode usar para executar seus aplicativos e cargas de trabalho. Ele também fornece considerações de implantação a serem observadas ao planejar o uso desses recursos.

As Máquinas Virtuais e os Serviços de Nuvem do Azure são dois dos vários tipos de recursos de computação oferecidos pelo Azure. Para obter explicações, consulte [Computar opções de hospedagem fornecidas pelo Azure](http://go.microsoft.com/fwlink/p/?LinkID=311926)

>[AZURE.NOTE]Para ver os limites relacionados do Azure, consulte [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md).

## Considerações sobre planejamento

As máquinas virtuais estão disponíveis em duas camadas: básica e padrão. Os dois tipos oferecem uma variedade de tamanhos, mas a camada básica não oferece alguns recursos disponíveis com a camada padrão, como balanceamento de carga e dimensionamento automático. A camada padrão de tamanhos é formada por séries diferentes: A, D, DS, G e GS. Entre as considerações sobre algumas dessas dimensões estão:

*   As VMs da série D são projetadas para executar aplicativos que exigem maior capacidade de computação e de desempenho de disco temporário. As VMs da série D fornecem processadores mais rápidos, uma maior taxa de memória por núcleo e uma unidade de estado sólido (SSD) para o disco temporário. Para obter detalhes, confira o anúncio no blog do Azure, [Novos tamanhos de máquina virtual da série D](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

*   A série Dv2, uma continuação da série D original, apresenta uma CPU mais potente. A CPU da série Dv2 é aproximadamente 35% mais rápida do que a CPU da série D. Ela se baseia na última geração do processador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz e, com a Intel Turbo Boost Technology 2.0, pode chegar a até 3,2 GHz. A série Dv2 tem as mesmas configurações de memória e disco que a série D. Para obter detalhes, veja o anúncio [Novos tamanhos de máquina virtual da série Dv2](http://azure.microsoft.com/blog/2015/10/01/new-dv2-series-virtual-machine-sizes/) no blog do Azure.

*   As VMs da série G oferecem o melhor desempenho e maior tamanho e são executadas em hosts com processadores da família Intel Xeon E5 V3.

*   As VMs das séries DS e GS podem usar o Armazenamento Premium, que fornece armazenamento de alto desempenho e baixa latência para cargas de trabalho com uso intenso de E/S. Essas VMs usam SSDs (unidades de estado sólido) para hospedar os discos da máquina virtual e também oferecem um cache de disco SSD local. O Armazenamento Premium está disponível em determinadas regiões. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../storage-premium-storage-preview-portal.md).

O tamanho da máquina virtual afeta os preços. O tamanho também afeta a capacidade de processamento, memória e armazenamento da máquina virtual. Os custos de armazenamento são calculados separadamente com base nas páginas usadas na conta de armazenamento. Para obter detalhes, confira [Detalhes de preços de máquinas virtuais](http://azure.microsoft.com/pricing/details/virtual-machines/) e [Preços de armazenamento do Azure](http://azure.microsoft.com/pricing/details/storage/). Para obter mais detalhes sobre armazenamento em VMs, veja [Sobre discos e VHDs para máquinas virtuais](virtual-machines-disks-vhds.md).

As considerações a seguir podem ajudá-lo a escolher um tamanho:

*   O tamanho A0\\Basic\_A0 só está disponível com o SDK do Azure versão 1.3 ou posterior.  

*   O A1\\Basic\_A1 é o menor tamanho recomendado para cargas de trabalho de produção.

*   Selecione uma máquina virtual com quatro ou oito núcleos de CPU ao usar o SQL Server Enterprise Edition.

*   Alguns dos hosts físicos em data centers do Azure podem não aceitar tamanhos de máquina virtual maiores, como A5 – A11. Como resultado, você poderá ver a mensagem de erro **Falha ao configurar a máquina virtual <machine name>** ou **Falha ao criar a máquina virtual <machine name>** ao redimensionar uma máquina virtual existente para um novo tamanho, criar uma nova máquina virtual em uma rede virtual criada antes de 16 de abril de 2013 ou adicionar uma nova máquina virtual a um serviço de nuvem. Confira o tópico [Erro: "falha ao configurar a máquina virtual"](https://social.msdn.microsoft.com/Forums/pt-BR/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) no fórum de suporte de soluções alternativas para cada cenário de implantação.

*   Os tamanhos de máquina virtual A8/A10 e A9/A11 têm as mesmas capacidades. As instâncias de máquina virtual A8 e A9 incluem um adaptador de rede adicional que é conectado a uma rede de Acesso Remoto Direto à Memória (RDMA) para uma comunicação rápida entre máquinas virtuais. As instâncias A8 e A9 destinam-se a aplicativos de computação de alto desempenho que exigem comunicação constante e de baixa latência entre os nós durante a execução como, por exemplo, os aplicativos que usam MPI (Message Passing Interface). As instâncias de máquina virtual A10 e A11 não incluem o adaptador de rede adicional. As instâncias A10 e A11 destinam-se a aplicativos de computação de alto desempenho que não exigem comunicação constante e de baixa latência entre os nós, também conhecidos como aplicativos paramétricos ou totalmente paralelos.

*   As séries Dv2 e D são ideais para aplicativos que exigem CPUs mais rápidas, melhor desempenho de disco local ou que têm uma maior demanda de memória. Elas oferecem uma combinação poderosa para vários aplicativos de nível empresarial.

## Limites gerais

Esta tabela mostra os limites que se aplicam independentemente do tamanho da máquina virtual, para máquinas virtuais criadas usando o modelo de implantação do Gerenciamento de Serviços.

[AZURE.INCLUDE [azure-virtual-machines-limits](../../includes/azure-virtual-machines-limits.md)]

Esta tabela mostra os limites que se aplicam independentemente do tamanho da máquina virtual, para máquinas virtuais criadas usando o modelo de implantação do Gerenciador de Recursos.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

## Tabelas de tamanho

As tabelas a seguir mostram os tamanhos e as capacidades oferecidas.

>[AZURE.NOTE]A capacidade de armazenamento é representada usando-se 1024^3 bytes como a unidade de medida para GB. Isso às vezes é chamado de gibibyte ou definição de base 2. Ao comparar tamanhos que usam diferentes sistemas de base, lembre-se de que os tamanhos de base 2 podem parecer menores do que os de base 10, mas para qualquer tamanho específico (por exemplo, 1 GB), um sistema de base 2 fornece mais capacidade do que um sistema de base 10, porque 1024^3 é maior que 1000^3.

### Camada básica

|Tamanho – Portal do Azure\\cmdlets e APIs|Núcleos de CPU|Memória|Tamanhos máx. de disco – máquina virtual|Máx. de discos de dados (1023 GB)|Máx. IOPS (300 por disco)|
|---|---|---|---|---|---|
|A0\\Basic\_A0|1|768 MB|<p>SO = 1023 GB</p><p>Temporário = 20 GB</p>|1|1 x 300|
|A1\\Basic\_A1|1|1,75 GB|<p>SO = 1023 GB</p><p>Temporário = 40 GB</p>|2|2 x 300|
|A2\\Basic\_A2|2|3,5 GB|<p>SO = 1023 GB</p><p>Temporário = 60 GB</p>|4|4 x 300|
|A3\\Basic\_A3|4|7 GB|<p>SO = 1023 GB</p><p>Temporário = 120 GB</p>|8|8 x 300|
|A4\\Basic\_A4|8|14 GB|<p>SO = 1023 GB</p><p>Temporário = 240 GB</p>|16|16 x 300|

### Camada Standard: série A

|Tamanho – Portal do Azure\\cmdlets e APIs|Núcleos de CPU|Memória|Tamanhos máx. de disco – máquina virtual|Máx. de discos de dados (1023 GB cada)|Máx. IOPS (500 por disco)|
|---|---|---|---|---|---|
|A0\\ extrapequeno|1|768 MB|<p>SO = 1023 GB</p><p>Temporário = 20 GB</p>|1|1 x 500|
|A1\\pequeno|1|1,75 GB|<p>SO = 1023 GB</p><p>Temporário = 70 GB</p>|2|2x500|
|A2\\médio|2|3,5 GB|<p>SO = 1023 GB</p><p>Temporário = 135 GB</p>|4|4x500|
|A3\\grande|4|7 GB|<p>SO = 1023 GB</p><p>Temporário = 285 GB</p>|8|8 x 500|
|A4\\extragrande|8|14 GB|<p>SO = 1023 GB</p><p>Temporário = 605 GB</p>|16|16 x 500|
|A5\\mesmo|2|14 GB|<p>SO = 1023 GB</p><p>Temporário = 135 GB</p>|4|4X500|
|A6\\mesmo|4|28 GB|<p>SO = 1023 GB</p><p>Temporário = 285 GB</p>|8|8 x 500|
|A7\\mesmo|8|56 GB|<p>SO = 1023 GB</p><p>Temporário = 605 GB</p>|16|16 x 500|
|A8\\mesmo|8|56 GB|<p><p>SO = 1.023 GB</p><p>Temporário = 382 GB</p><blockquote><p>Observação: para obter informações e considerações sobre o uso desse tamanho, veja <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Sobre as instâncias A8, A9, A10 e A11 de computação intensiva</a>.</p></blockquote>|16|16 x 500|
|A9\\mesmo|16|112 GB|<p><p>SO = 1.023 GB</p><p>Temporário = 382 GB</p><blockquote><p>Observação: para obter informações e considerações sobre o uso desse tamanho, veja <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Sobre as instâncias A8, A9, A10 e A11 de computação intensiva</a>.</p></blockquote>|16|16 x 500|
|A10\\mesmo|8|56 GB|<p><p>SO = 1.023 GB</p><p>Temporário = 382 GB</p><blockquote><p>Observação: para obter informações e considerações sobre o uso desse tamanho, veja <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Sobre as instâncias A8, A9, A10 e A11 de computação intensiva</a>.</p></blockquote>|16|16 x 500|
|A11\\mesmo|16|112 GB|<p><p>SO = 1.023 GB</p><p>Temporário = 382 GB</p><blockquote><p>Observação: para obter informações e considerações sobre o uso desse tamanho, veja <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Sobre as instâncias A8, A9, A10 e A11 de computação intensiva</a>.</p></blockquote>|16|16 x 500|

### Camada Standard: série D

|Tamanho – Portal do Azure\\cmdlets e APIs|Núcleos de CPU|Memória|Tamanhos máx. de disco – máquina virtual|Máx. de discos de dados (1023 GB cada)|Máx. IOPS (500 por disco)|
|---|---|---|---|---|---|
|Standard\_D1\\mesmo|1|3,5 GB|<p></p>SO = 1023 GB<p>Temporário (SSD) = 50 GB</p>|2|2x500|
|Standard\_D2\\mesmo|2|7 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 100 GB</p>|4|4x500|
|Standard\_D3\\mesmo|4|14 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 200 GB</p>|8|8 x 500|
|Standard\_D4\\mesmo|8|28 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 400 GB</p>|16|16 x 500|
|Standard\_D11\\mesmo|2|14 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 100 GB</p>|4|4x500|
|Standard\_D12\\mesmo|4|28 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 200 GB</p>|8|8 x 500|
|Standard\_D13\\mesmo|8|56 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 400 GB</p>|16|16 x 500|
|Standard\_D14\\mesmo|16|112 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 800 GB</p>|32|32 x 500|

### Camada Standard: série Dv2

|Tamanho – Portal do Azure\\cmdlets e APIs|Núcleos de CPU|Memória|Tamanhos máx. de disco – máquina virtual|Máx. de discos de dados (1023 GB cada)|Máx. IOPS (500 por disco)|
|---|---|---|---|---|---|
|Standard\_D1\_v2\\same|1|3,5 GB|<p></p>SO = 1023 GB<p>Temporário (SSD) = 50 GB</p>|2|2x500|
|Standard\_D2\_v2\\same|2|7 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 100 GB</p>|4|4x500|
|Standard\_D3\_v2\\same|4|14 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 200 GB</p>|8|8 x 500|
|Standard\_D4\_v2\\same|8|28 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 400 GB</p>|16|16 x 500|
|Standard\_D5\_v2\\same|16|56 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 800 GB</p>|32|32 x 500|
|Standard\_D11\_v2\\same|2|14 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 100 GB</p>|4|4x500|
|Standard\_D12\_v2\\same|4|28 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 200 GB</p>|8|8 x 500|
|Standard\_D13\_v2\\same|8|56 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 400 GB</p>|16|16 x 500|
|Standard\_D14\_v2\\same|16|112 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 800 GB</p>|32|32 x 500|

### Camada Standard: série DS*

|Tamanho – Portal do Azure\\cmdlets e APIs|Núcleos de CPU|Memória|Tamanhos máx. de disco – máquina virtual|Máx. de discos de dados (1023 GB cada)|Tamanho do cache (GB)|Máx. de IOPS de disco &amp; largura de banda|
|---|---|---|---|---|---|---|
|Standard\_DS1\\mesmo|1|3,5|<p>OS = 1023 GB</p><p>Disco SSD local = 7 GB</p>|2|43|<p>3.200</p><p>32 MB por segundo</p>|
|Standard\_DS2\\mesmo|2|7|<p>SO = 1023 GB</p><p>Disco SSD local = 14 GB</p>|4|86|<p>6.400</p><p>64 MB por segundo</p>|
|Standard\_DS3\\mesmo|4|14|<p>SO = 1023 GB</p><p>Disco SSD local = 28 GB</p>|8|172|<p>12.800</p><p>128 MB por segundo</p>|
|Standard\_DS4\\mesmo|8|28|<p>SO = 1023 GB</p><p>Disco SSD local = 56 GB</p>|16|344|<p>25.600</p><p>256 MB por segundo</p>|
|Standard\_DS11\\mesmo|2|14|<p>SO = 1023 GB</p><p>Disco SSD local = 28 GB</p>|4|72|<p>6.400</p><p>64 MB por segundo</p>|
|Standard\_DS12\\mesmo|4|28|<p>SO = 1023 GB</p><p>Disco SSD local = 56 GB</p>|8|144|<p>12.800</p><p>128 MB por segundo</p>|
|Standard\_DS13\\mesmo|8|56|<p>SO = 1023 GB</p><p>Disco SSD local = 112 GB</p>|16|288|<p>25.600</p><p>256 MB por segundo</p>|
|Standard\_DS14\\mesmo|16|112|<p>SO = 1023 GB</p><p>Disco SSD local = 224 GB</p>|32|576|<p>50.000</p><p>512 MB por segundo</p>|

**O máximo de operações de entrada/saída por segundo (IOPS) e de taxa de transferência (largura de banda) possível com uma VM da série DS é afetado pelo tamanho do disco. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../storage-premium-storage-preview-portal.md).

### Camada Standard: série G

|Tamanho – Portal do Azure\\cmdlets e APIs|Núcleos de CPU|Memória|Tamanhos máx. de disco – máquina virtual|Máx. de discos de dados (1023 GB cada)|Máx. IOPS (500 por disco)|
|---|---|---|---|---|---|
|Standard\_G1\\mesmo|2|28 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 384 GB</p>|4|4 x 500|
|Standard\_G2\\mesmo|4|56 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 768 GB</p>|8|8 x 500|
|Standard\_G3\\mesmo|8|112 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 1.536 GB</p>|16|16 x 500|
|Standard\_G4\\mesmo|16|224 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 3.072 GB</p>|32|32 x 500|
|Standard\_G5\\mesmo|32|448 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 6.144 GB</p>|64|<p>64 x 500</p>|

### Camada Standard: Série GS

|Tamanho – Portal do Azure\\cmdlets e APIs|Núcleos de CPU|Memória|Tamanhos máx. de disco – máquina virtual|Máx. de discos de dados (1023 GB cada)|Tamanho do cache (GB)|Máx. de IOPS de disco &amp; largura de banda|
|---|---|---|---|---|---|---|
|Standard\_GS1|2|28|<p>SO = 1023 GB</p><p>Disco SSD local = 56 GB</p>|4|264|<p>5.000</p><p>125 MB por segundo</p>|
|Standard\_GS2|4|56|<p>SO = 1023 GB</p><p>Disco SSD local = 112 GB</p>|8|528|<p>10.000</p><p>250 MB por segundo</p>|
|Standard\_GS3|8|112|<p>SO = 1023 GB</p><p>Disco SSD local = 224 GB</p>|16|1056|<p>20.000</p><p>500 MB por segundo</p>|
|Standard\_GS4|16|224|<p>SO = 1.023 GB</p><p>Disco SSD local = 448 GB</p>|32|2112|<p>40.000</p><p>1.000 MB por segundo</p>|
|Standard\_GS5|32|448|<p>SO = 1.023 GB</p><p>Disco SSD local = 896 GB</p>|64|4224|<p>80.000</p><p>2.000 MB por segundo</p>|


### Consulte também

[Assinatura do Azure e limite de serviços, cotas e restrições](../azure-subscription-service-limits.md)

[Sobre as instâncias A8, A9, A10 e A11 com uso intensivo de computação](virtual-machines-a8-a9-a10-a11-specs.md)

<!---HONumber=Oct15_HO1-->