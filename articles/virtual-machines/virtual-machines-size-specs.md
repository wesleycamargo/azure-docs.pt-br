<properties
 pageTitle="Tamanhos de máquina virtual"
 description="Lista os diferentes tamanhos de máquinas virtuais e suas capacidades."
 services="virtual-machines"
 documentationCenter=""
 authors="KBDAzure"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="07/02/2015"
 ms.author="kathydav"/>

# Tamanhos das máquinas virtuais

## Visão geral

Este artigo descreve os tamanhos e as opções disponíveis para os recursos de computação baseados em máquina virtual que você pode usar para executar seus aplicativos e cargas de trabalho. Ele também fornece considerações de implantação a serem consideradas ao planejar o uso desses recursos.

As Máquinas Virtuais e os Serviços de Nuvem do Azure são dois dos vários tipos de recursos de computação oferecidos pelo Azure. Para obter explicações, confira [Computar opções de hospedagem fornecidas pelo Azure](http://go.microsoft.com/fwlink/p/?LinkID=311926)

>[AZURE.NOTE]Para ver os limites relacionados do Azure, confira [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md).

## Considerações de planejamento

As máquinas virtuais estão disponíveis em duas camadas: básica e padrão. Os dois tipos oferecem uma variedade de tamanhos, mas a camada básica não oferece alguns recursos disponíveis com a camada padrão, como balanceamento de carga e dimensionamento automático. A camada padrão de tamanhos consiste em série A, série D, série DS e série G.

*   As VMs da série D são projetadas para executar aplicativos que exigem maior capacidade de computação e de desempenho de disco temporário. As VMs da série D fornecem processadores mais rápidos, uma maior taxa de memória por núcleo e uma unidade de estado sólido (SSD) para o disco temporário. Para obter detalhes, confira o anúncio no blog do Azure, [Novos tamanhos de máquina virtual da série D](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).  

*   As VMs da Série DS podem usar o armazenamento Premium, que fornece armazenamento de alto desempenho e baixa latência para cargas de trabalho com uso intenso de E/S. Usam unidades de estado sólido (SSDs) para hospedar os discos da máquina virtual e oferecem um cache de disco SSD local. O armazenamento Premium fica na Visualização e está disponível em determinadas regiões. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../storage-premium-storage-preview-portal.md).

*   As VMs da série G oferecem o melhor desempenho e maior tamanho e são executadas em hosts com processadores da família Intel Xeon E5 V3.

O tamanho da máquina virtual afeta os preços. O tamanho também afeta a capacidade de processamento, memória e armazenamento da máquina virtual. Os custos de armazenamento são calculados separadamente com base nas páginas usadas na conta de armazenamento. Para obter detalhes, confira [Detalhes de preços de máquinas virtuais](http://go.microsoft.com/fwlink/p/?LinkId=398570) e [Preços de armazenamento do Azure](http://azure.microsoft.com/pricing/details/storage/). Para saber mais sobre os discos e o armazenamento usados nas máquinas virtuais do Azure, confira [Sobre discos e imagens no Azure](https://msdn.microsoft.com/library/jj672979).

As considerações a seguir podem ajudá-lo a escolher um tamanho:

*   O tamanho A0\\Basic_A0 só está disponível usando o SDK do Azure versão 1.3 ou posterior.  

*   O A1\\Basic_A1 é o menor tamanho recomendado para cargas de trabalho de produção.

*   Selecione uma máquina virtual com 4 ou 8 núcleos de CPU quando usar o SQL Server Enterprise Edition.

*   Alguns dos hosts físicos em data centers do Azure podem não aceitar tamanhos de máquina virtual maiores, como A5 – A11. Como resultado, você poderá ver a mensagem de erro **Falha ao configurar a máquina virtual <machine name>** ou **Falha ao criar a máquina virtual <machine name>** ao redimensionar uma máquina virtual existente para um novo tamanho, criar uma nova máquina virtual em uma rede virtual criada antes de 16 de abril de 2013 ou adicionar uma nova máquina virtual a um serviço de nuvem. Confira o tópico [Erro: "falha ao configurar a máquina virtual"](http://social.msdn.microsoft.com/Forums/WAVirtualMachinesforWindows/thread/9693f56c-fcd3-4d42-850e-5e3b56c7d6be) no fórum de suporte de soluções alternativas para cada cenário de implantação.

*   Os tamanhos de máquina virtual A8/A10 e A9/A11 têm as mesmas capacidades. As instâncias de máquina virtual A8 e A9 incluem um adaptador de rede adicional que é conectado a uma rede de Acesso Remoto Direto à Memória (RDMA) para uma comunicação rápida entre máquinas virtuais. As instâncias A8 e A9 destinam-se a aplicativos de computação de alto desempenho que exigem comunicação constante e de baixa latência entre os nós durante a execução como, por exemplo, os aplicativos que usam MPI (Message Passing Interface). As instâncias de máquina virtual A10 e A11 não incluem o adaptador de rede adicional. As instâncias A10 e A11 destinam-se a aplicativos de computação de alto desempenho que não exigem comunicação constante e de baixa latência entre os nós, também conhecidos como aplicativos paramétricos ou totalmente paralelos.

## Limites gerais

Esta tabela mostra limites que se aplicam independentemente do tamanho da VM, para VMs criadas com ferramentas do Gerenciador de Serviços.


[AZURE.INCLUDE [azure-virtual-machines-limits](../../includes/azure-virtual-machines-limits.md)]

Esta tabela mostra limites que se aplicam independentemente do tamanho da VM, para VMs criadas com ferramentas do Gerenciador de Recursos.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

## Tabelas de tamanho

As tabelas a seguir mostram os tamanhos e as capacidades oferecidas por cada um.

>[AZURE.NOTE]A capacidade de armazenamento é representada usando-se 1024^3 bytes como a unidade de medida para GB. Isso às vezes é chamado de gibibyte ou definição de base 2. Ao comparar tamanhos que usam diferentes sistemas de base, lembre-se de que os tamanhos de base 2 podem parecer menores do que os de base 10, mas para qualquer tamanho específico (por exemplo, 1 GB), um sistema de base 2 fornece mais capacidade do que um sistema de base 10, porque 1024^3 é maior que 1000^3.

## Camada básica

|Tamanho – Portal de Gerenciamento\\cmdlets e APIs|Núcleos de CPU|Memória|Tamanhos máx. de disco – máquina virtual|Máx. de discos de dados (1023 GB)|Máx. IOPS (300 por disco)|
|---|---|---|---|---|---|
|A0\\Basic_A0|1|768 MB|<p>SO = 1023 GB</p><p>Temporário = 20 GB</p>|1|1 x 300|
|A1\\Basic_A1|1|1,75 GB|<p>SO = 1023 GB</p><p>Temporário = 40 GB</p>|2|2 x 300|
|A2\\Basic_A2|2|3,5 GB|<p>SO = 1023 GB</p><p>Temporário = 60 GB</p>|4|4 x 300|
|A3\\Basic_A3|4|7 GB|<p>SO = 1023 GB</p><p>Temporário = 120 GB</p>|8|8 x 300|
|A4\\Basic_A4|8|14 GB|<p>SO = 1023 GB</p><p>Temporário = 240 GB</p>|16|16 x 300|

## Camada padrão
### Série A e série D

|Tamanho – Portal de Gerenciamento\\cmdlets e APIs|Núcleos de CPU|Memória|Tamanhos máx. de disco – máquina virtual|Máx. de discos de dados (1023 GB cada)|Máx. IOPS (500 por disco)|
|---|---|---|---|---|---|
|A0\\ extrapequeno|1|768 MB|<p>SO = 1023 GB</p><p>Temporário = 20 GB</p>|1|1 x 500|
|A1\\pequeno|1|1,75 GB|<p>SO = 1023 GB</p><p>Temporário = 70 GB</p>|2|2x500|
|A2\\médio|2|3,5 GB|<p>SO = 1023 GB</p><p>Temporário = 135 GB</p>|4|4x500|
|A3\\grande|4|7 GB|<p>SO = 1023 GB</p><p>Temporário = 285 GB</p>|8|8 x 500|
|A4\\extragrande|8|14 GB|<p>SO = 1023 GB</p><p>Temporário = 605 GB</p>|16|16 x 500|
|A5(idem)|2|14 GB|<p>SO = 1023 GB</p><p>Temporário = 135 GB</p>|4|4X500|
|A6(idem)|4|28 GB|<p>SO = 1023 GB</p><p>Temporário = 285 GB</p>|8|8 x 500|
|A7(idem)|8|56 GB|<p>SO = 1023 GB</p><p>Temporário = 605 GB</p>|16|16 x 500|
|A8(idem)|8|56 GB|<p><p>OS = 1023 GB</p><p>Temporário = 382 GB</p><blockquote><p>Observação: para saber mais e para considerações sobre o uso desse tamanho, consulte <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Sobre as instâncias A8, A9, A10 e A11 com uso intensivo de computação</a>.</p></blockquote>|16|16 x 500|
|A9(idem)|16|112 GB|<p><p>OS = 1023 GB</p><p>Temporário = 382 GB</p><blockquote><p>Observação: para saber mais e para considerações sobre o uso desse tamanho, consulte <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Sobre as instâncias A8, A9, A10 e A11 com uso intensivo de computação</a>.</p></blockquote>|16|16 x 500|
|A10(idem)|8|56 GB|<p><p>OS = 1023 GB</p><p>Temporário = 382 GB</p><blockquote><p>Observação: para saber mais e para considerações sobre o uso desse tamanho, consulte <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Sobre as instâncias A8, A9, A10 e A11 com uso intensivo de computação</a>.</p></blockquote>|16|16 x 500|
|A11(idem)|16|112 GB|<p><p>OS = 1023 GB</p><p>Temporário = 382 GB</p><blockquote><p>Observação: para saber mais e para considerações sobre o uso desse tamanho, consulte <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Sobre as instâncias A8, A9, A10 e A11 com uso intensivo de computação</a>.</p></blockquote>|16|16 x 500|
|Standard_D1(idem)|1|3,5 GB|<p></p>SO = 1023 GB<p>Temporário (SSD) = 50 GB</p>|2|2x500|
|Standard_D2(idem)|2|7 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 100 GB</p>|4|4x500|
|Standard_D3(idem)|4|14 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 200 GB</p>|8|8 x 500|
|Standard_D4(idem)|8|28 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 400 GB</p>|16|16 x 500|
|Standard_D11(idem)|2|14 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 100 GB</p>|4|4x500|
|Standard_D12(idem)|4|28 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 200 GB</p>|8|8 x 500|
|Standard_D13(idem)|8|56 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 400 GB</p>|16|16 x 500|
|Standard_D14(idem)|16|112 GB|<p>SO = 1023 GB</p><p>Temporário (SSD) = 800 GB</p>|32|32 x 500|


### Camada padrão – série DS*

|Tamanho – Portal de Gerenciamento\\cmdlets e APIs|Núcleos de CPU|Memória|Tamanhos máx. de disco – máquina virtual|Máx. de discos de dados (1023 GB cada)|Tamanho do cache (GB)|Máx. de IOPS de disco &amp; largura de banda|
|---|---|---|---|---|---|---|
|Standard_DS1(idem)|1|3,5|<p>OS = 1023 GB</p><p>Disco SSD local = 7 GB</p>|2|43|<p>3.200</p><p>32 MB por segundo</p>|
|Standard_DS2(idem)|2|7|<p>SO = 1023 GB</p><p>Disco SSD local = 14 GB</p>|4|86|<p>6.400</p><p>64 MB por segundo</p>|
|Standard_DS3(idem)|4|14|<p>SO = 1023 GB</p><p>Disco SSD local = 28 GB</p>|8|172|<p>12.800</p><p>128 MB por segundo</p>|
|Standard_DS4(idem)|8|28|<p>SO = 1023 GB</p><p>Disco SSD local = 56 GB</p>|16|344|<p>25.600</p><p>256 MB por segundo</p>|
|Standard_DS11(idem)|2|14|<p>SO = 1023 GB</p><p>Disco SSD local = 28 GB</p>|4|72|<p>6.400</p><p>64 MB por segundo</p>|
|Standard_DS12(idem)|4|28|<p>SO = 1023 GB</p><p>Disco SSD local = 56 GB</p>|8|144|<p>12.800</p><p>128 MB por segundo</p>|
|Standard_DS13(idem)|8|56|<p>SO = 1023 GB</p><p>Disco SSD local = 112 GB</p>|16|288|<p>25.600</p><p>256 MB por segundo</p>|
|Standard_DS14(idem)|16|112|<p>SO = 1023 GB</p><p>Disco SSD local = 224 GB</p>|32|576|<p>50.000</p><p>512 MB por segundo</p>|

*O máximo de operações de entrada/saída por segundo (IOPS) e de taxa de transferência (largura de banda) possível com uma VM da série DS é afetado pelo tamanho do disco. Para obter detalhes, confira [Armazenamento Premium: Armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../storage-premium-storage-preview-portal.md).

### Camada padrão – série G

|Tamanho – Portal de Gerenciamento\\cmdlets e APIs|Núcleos de CPU|Memória|Tamanhos máx. de disco – máquina virtual|Máx. de discos de dados (1023 GB cada)|Máx. IOPS (500 por disco)|
|---|---|---|---|---|---|
|Standard_G1(idem)|2|28 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 384 GB</p>|4|4 x 500|
|Standard_G2(idem)|4|56 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 768 GB</p>|8|8 x 500|
|Standard_G3(idem)|8|112 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 1.536 GB</p>|16|16 x 500|
|Standard_G4(idem)|16|224 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 3.072 GB</p>|32|32 x 500|
|Standard_G5(idem)|32|448 GB|<p>SO = 1023 GB</p><p>Disco SSD local = 6.144 GB</p>|64|<p>64 x 500</p>|

### Consulte também

[Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md)

[Sobre as instâncias A8, A9, A10 e A11 com uso intensivo de computação](virtual-machines-a8-a9-a10-a11-specs.md)

<!---HONumber=July15_HO4-->