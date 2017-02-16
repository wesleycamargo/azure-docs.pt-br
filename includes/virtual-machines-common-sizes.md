


Há vários tamanhos padrão para você escolher no Azure. Entre as considerações sobre algumas dessas dimensões estão:

* As VMs da série D são projetadas para executar aplicativos que exigem maior capacidade de computação e de desempenho de disco temporário. As VMs da série D fornecem processadores mais rápidos, uma maior taxa de memória por núcleo e uma unidade de estado sólido (SSD) para o disco temporário. Para obter detalhes, confira o anúncio no blog do Azure, [Novos tamanhos de máquina virtual da série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).
* A série Dv2, uma continuação da série D original, apresenta uma CPU mais potente. A CPU da série Dv2 é aproximadamente 35% mais rápida do que a CPU da série D. Ela se baseia na última geração do processador Intel Xeon® E5-2673 v3 (Haswell) de 2.4 GHz e, com a Intel Turbo Boost Technology 2.0, pode chegar a até 3.1 GHz. A série Dv2 tem as mesmas configurações de memória e disco que a série D.
* A série F é baseada no processador 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell), que pode obter velocidades de relógio tão altas quanto 3.1 GHz com o Intel Turbo Boost Technology 2.0. Esse é o mesmo desempenho de CPU que o das VMs da série Dv2.  A um preço de lista inferior por hora, a série F é o melhor valor de preço/desempenho no portfólio do Azure com base na ACU (Unidade de Computação do Azure) por núcleo. 
  
    A série F também introduz um novo padrão em nomenclatura de tamanho de VM para o Azure. Para esta série e os tamanhos de VM lançados no futuro, o valor numérico após a letra do sobrenome corresponderá ao número de núcleos de CPU. Recursos adicionais, como armazenamento otimizado para premium, serão designados por letras após a contagem de núcleos de CPU numérica. Esse formato de nomeação será usado para futuras tamanhos de VM lançados mas não vai retroativamente alterar os nomes de quaisquer tamanhos VM existentes que foram lançados.
* As VMs da série G oferecem a maior memória e são executadas em hosts com processadores da família Intel Xeon E5 V3.
* As VMs das séries DS, DSv2, Fs e GS podem usar o Armazenamento Premium, que fornece armazenamento de alto desempenho e baixa latência para cargas de trabalho com uso intenso de E/S. Essas VMs usam SSDs (unidades de estado sólido) para hospedar os discos da máquina virtual e também oferecem um cache de disco SSD local. O Armazenamento Premium está disponível em determinadas regiões. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).
*   As VMs da série A e da série Av2 podem ser implantadas em uma variedade de tipos de hardware e processadores. O tamanho é limitado, com base no hardware, para oferecer desempenho de processador consistente para a instância em execução, independentemente do hardware em que é implantado. Para determinar o hardware físico no qual esse tamanho é implantado, consulte o hardware virtual de dentro da Máquina Virtual.
* O tamanho A0 está assinado em excesso no hardware físico. Para este tamanho específico somente, outras implantações de clientes podem afetar o desempenho da carga de trabalho em execução. O desempenho relativo é descrito a seguir como a linha de base esperada, sujeito a uma variação aproximada de 15%.

O tamanho da máquina virtual afeta os preços. O tamanho também afeta a capacidade de processamento, memória e armazenamento da máquina virtual. Os custos de armazenamento são calculados separadamente com base nas páginas usadas na conta de armazenamento. Para obter detalhes, confira [Detalhes de preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) e [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). 

As considerações a seguir podem ajudá-lo a escolher um tamanho:

* Os tamanhos A8-A11 e série H também são conhecidos como *instâncias de computação intensiva*. O hardware de datacenter do Azure que executa esses tamanhos é projetado e otimizado para aplicativos de uso intensivo de computação e rede, incluindo aplicativos, modelagem e simulações de cluster HPC (computação de alto desempenho). A série de A8-A11 usa Intel Xeon E5-2670 @ 2,6 GHz e a série H usa Intel Xeon E5-2667 v3 @ 3,2 GHz. Para obter informações detalhadas e considerações sobre o uso desses tamanhos, confira [Sobre VMs série H ou série A com computação intensiva](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* As séries Dv2, D e G, bem como as equivalentes DS/GS são ideais para aplicativos que exigem CPUs mais rápidas, melhor desempenho de disco local ou que têm maior demanda de memória.  Elas oferecem uma combinação poderosa para vários aplicativos de nível empresarial.
* As VMs série F são uma ótima opção para cargas de trabalho que exigem CPUs mais rápidas, mas não precisam de tanta memória ou SSD local por núcleo da CPU.  Cargas de trabalho como análise, servidores de jogos, servidores Web e de processamento em lote serão beneficiados com o valor da série F.
* Alguns dos hosts físicos em data centers do Azure podem não aceitar tamanhos de máquina virtual maiores, como A5 – A11. Como resultado, você poderá ver a mensagem de erro **Falha ao configurar a máquina virtual <machine name>** ou **Falha ao criar a máquina virtual <machine name>** ao redimensionar uma máquina virtual existente para um novo tamanho, criar uma nova máquina virtual em uma rede virtual criada antes de 16 de abril de 2013 ou adicionar uma nova máquina virtual a um serviço de nuvem. Confira [Erro: "Falha ao configurar a máquina virtual"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) no fórum de suporte de soluções alternativas para cada cenário de implantação.  
* Sua assinatura também pode limitar o número de núcleos que você pode implantar em determinadas famílias de tamanho. Para aumentar a cota, entre em contato com o Suporte do Azure.

## <a name="performance-considerations"></a>Considerações sobre o desempenho
Criamos o conceito da ACU (unidade de computação do Azure) para fornecer uma maneira de comparar o desempenho de computação (CPU) em SKUs do Azure. Isso ajudará você a identificar facilmente qual SKU é tem maior probabilidade de satisfazer suas necessidades de desempenho.  A ACU atualmente é padronizada como uma VM pequena (Standard_A1) sendo 100 e todas as SKUs representam, aproximadamente, o quanto a SKU pode executar um parâmetro de comparação padrão mais rapidamente. 

> [!IMPORTANT]
> A ACU é apenas uma diretriz.  Os resultados para sua carga de trabalho podem variar. 
> 
> 

<br>

| Família de SKU | ACU/núcleo |
| --- | --- |
| [Standard_A0](#a-series) |50 |
| [Standard_A1-4](#a-series) |100 |
| [Standard_A5-7](#a-series) |100 |
| [Standard_A1-8v2](#av2-series) |100 |
| [Standard_A2m-8mv2](#av2-series) |100 |
| [A8-A11](#a-series) |225* |
| [D1-14](#d-series) |160 |
| [D1-15v2](#dv2-series) |210 - 250* |
| [DS1-14](#ds-series) |160 |
| [DS1-15v2](#dsv2-series) |210-250* |
| [F1-F16](#f-series) |210-250* |
| [F1s-F16s](#fs-series) |210-250* |
| [G1-5](#g-series) |180 - 240* |
| [GS1-5](#gs-series) |180 - 240* |
| [H](#h-series) |290 – 300* |

ACUs marcados com um * usam tecnologia Intel® Turbo para aumentar a frequência da CPU e fornecer um aumento de desempenho.  A quantidade do aumento pode variar com base no tamanho da VM, na carga de trabalho e em outras cargas de trabalho em execução no mesmo host.

## <a name="size-tables"></a>Tabelas de tamanho
As tabelas a seguir mostram os tamanhos e as capacidades oferecidas.

* A capacidade de armazenamento é mostrada em unidades de GiB ou de 1024^3 bytes. Ao comparar os discos medidos em GB (1000^3 bytes) com os discos medidos em GiB (1024^3), lembre-se de que os números de capacidade fornecidos em GiB poderão parecer menores. Por exemplo, 1023 GiB = 1098,4 GB
* A taxa de transferência do disco é medida em IOPS (operações de entrada/saída por segundo) e em MBps, em que MBps = 10^6 bytes/s.
* Os discos de dados podem operar nos modos em cache ou não armazenado em cache.  Para a operação do disco de dados armazenados em cache, o modo de cache do host é definido como **ReadOnly** ou **ReadWrite**.  Para as operação do disco de dados não armazenados em cache, o modo de cache do host é definido como **Nenhum**.
* A largura de banda de rede máxima é a largura de banda agregada máxima alocada e atribuída por tipo de VM. A largura de banda máxima fornece diretrizes para selecionar o tipo correto de VM, a fim de garantir que uma capacidade adequada de rede está disponível. Durante a movimentação entre Baixa, Moderada, Alta e Muito Alta, a taxa de transferência será aumentada de acordo. O desempenho real da rede dependerá de vários fatores, incluindo cargas de rede e aplicativos, bem como configurações de rede do aplicativo.

## <a name="a-series"></a>Séria A
| Tamanho | Núcleos de CPU | Memória: GiB | HDD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 |1 |0,768 |20 |1 |1 x 500 |1 / baixa |
| Standard_A1 |1 |1,75 |70 |2 |2x500 |1 / moderada |
| Standard_A2 |2 |3,5 |135 |4 |4x500 |1 / moderada |
| Standard_A3 |4 |7 |285 |8 |8 x 500 |2 / alta |
| Standard_A4 |8 |14 |605 |16 |16 x 500 |4 / alta |
| Standard_A5 |2 |14 |135 |4 |4x500 |1 / moderada |
| Standard_A6 |4 |28 |285 |8 |8 x 500 |2 / alta |
| Standard_A7 |8 |56 |605 |16 |16 x 500 |4 / alta |

<br>

## <a name="a-series---compute-intensive-instances"></a>Série A – Instâncias de computação intensiva
Para obter informações e considerações sobre o uso desses tamanhos, confira [Sobre VMs série H ou série A com computação intensiva](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Tamanho | Núcleos de CPU | Memória: GiB | HDD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16 x 500 |2 / alta |
| Standard_A9* |16 |112 |382 |16 |16 x 500 |4 / muito alta |
| Standard_A10 |8 |56 |382 |16 |16 x 500 |2 / alta |
| Standard_A11 |16 |112 |382 |16 |16 x 500 |4 / muito alta |

*Compatível com RDMA

<br>

## <a name="av2-series"></a>Série Av2

| Tamanho        | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximas / largura de banda da rede |
|-------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A1_v2 | 1         | 2            | 10                   | 2              | 2x500              | 1 / moderada              |
| Standard_A2_v2 | 2         | 4            | 20                   | 4              | 4x500              | 2 / moderada              |
| Standard_A4_v2 | 4         | 8            | 40                   | 8              | 8 x 500              | 4 / alta                  |
| Standard_A8_v2 | 8         | 16           | 80                   | 16             | 16 x 500             | 8 / alta                  |
| Standard_A2m_v2 | 2        | 16           | 20                   | 4              | 4x500              | 2 / moderada              |
| Standard_A4m_v2 | 4        | 32           | 40                   | 8              | 8 x 500              | 4 / alta                  |
| Standard_A8m_v2 | 8        | 64           | 80                   | 16             | 16 x 500             | 8 / alta                  |


## <a name="d-series"></a>Série D
| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1 |1 |3,5 |50 |2 |2x500 |1 / moderada |
| Standard_D2 |2 |7 |100 |4 |4x500 |2 / alta |
| Standard_D3 |4 |14 |200 |8 |8 x 500 |4 / alta |
| Standard_D4 |8 |28 |400 |16 |16 x 500 |8 / alta |
| Standard_D11 |2 |14 |100 |4 |4x500 |2 / alta |
| Standard_D12 |4 |28 |200 |8 |8 x 500 |4 / alta |
| Standard_D13 |8 |56 |400 |16 |16 x 500 |8 / alta |
| Standard_D14 |16 |112 |800 |32 |32 x 500 |8 / muito alta |

<br>

## <a name="dv2-series"></a>Série Dv2
| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |3,5 |50 |2 |2x500 |1 / moderada |
| Standard_D2_v2 |2 |7 |100 |4 |4x500 |2 / alta |
| Standard_D3_v2 |4 |14 |200 |8 |8 x 500 |4 / alta |
| Standard_D4_v2 |8 |28 |400 |16 |16 x 500 |8 / alta |
| Standard_D5_v2 |16 |56 |800 |32 |32 x 500 |8 / extremamente alta |
| Standard_D11_v2 |2 |14 |100 |4 |4x500 |2 / alta |
| Standard_D12_v2 |4 |28 |200 |8 |8 x 500 |4 / alta |
| Standard_D13_v2 |8 |56 |400 |16 |16 x 500 |8 / alta |
| Standard_D14_v2 |16 |112 |800 |32 |32 x 500 |8 / extremamente alta |
| Standard_D15_v2 |20 |140 |1.000 |40 |40 x 500 |8 / extremamente alta* |

* Em algumas regiões, a rede acelerada está disponível para o tamanho Standard_D15_v2. Para saber mais sobre o uso e a disponibilidade, consulte [Accelerated Networking is in Preview (A rede acelerada está em versão de visualização)](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) e [Rede acelerada para uma máquina virtual](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md).

<br>

## <a name="ds-series"></a>Série DS*
| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |2 |4.000 / 32 (43) |3.200 / 32 |1 / moderada |
| Standard_DS2 |2 |7 |14 |4 |8.000 / 64 (86) |6.400 / 64 |2 / alta |
| Standard_DS3 |4 |14 |28 |8 |16.000 / 128 (172) |12.800 / 128 |4 / alta |
| Standard_DS4 |8 |28 |56 |16 |32.000 / 256 (344) |25.600 / 256 |8 / alta |
| Standard_DS11 |2 |14 |28 |4 |8.000 / 64 (72) |6.400 / 64 |2 / alta |
| Standard_DS12 |4 |28 |56 |8 |16.000 / 128 (144) |12.800 / 128 |4 / alta |
| Standard_DS13 |8 |56 |112 |16 |32.000 / 256 (288) |25.600 / 256 |8 / alta |
| Standard_DS14 |16 |112 |224 |32 |64.000 / 512 (576) |51.200 / 512 |8 / muito alta |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

*A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série DS pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).

<br>

## <a name="dsv2-series"></a>Série DSv2*
| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |2 |4.000 / 32 (43) |3.200 / 48 |Um moderado |
| Standard_DS2_v2 |2 |7 |14 |4 |8.000 / 64 (86) |6.400 / 96 |2 alta |
| Standard_DS3_v2 |4 |14 |28 |8 |16.000 / 128 (172) |12.800 / 192 |4 alta |
| Standard_DS4_v2 |8 |28 |56 |16 |32.000 / 256 (344) |25.600 / 384 |8 alta |
| Standard_DS5_v2 |16 |56 |112 |32 |64.000 / 512 (688) |51.200 / 768 |8 extremamente alta |
| Standard_DS11_v2 |2 |14 |28 |4 |8.000 / 64 (72) |6.400 / 96 |2 alta |
| Standard_DS12_v2 |4 |28 |56 |8 |16.000 / 128 (144) |12.800 / 192 |4 alta |
| Standard_DS13_v2 |8 |56 |112 |16 |32.000 / 256 (288) |25.600 / 384 |8 alta |
| Standard_DS14_v2 |16 |112 |224 |32 |64.000 / 512 (576) |51.200 / 768 |8 extremamente alta |
| Standard_DS15_v2 |20 |140 |280 |40 |80.000 / 640 (720) |64.000 / 960 |8 extremamente alta** |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

*A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série DSv2 pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).

** Em algumas regiões, a rede acelerada está disponível para o tamanho Standard_DS15_v2. Para saber mais sobre o uso e a disponibilidade, consulte [Accelerated Networking is in Preview (A rede acelerada está em versão de visualização)](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) e [Rede acelerada para uma máquina virtual](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md).

<br>

## <a name="f-series"></a>Série F
| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco: IOPS | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_F1 |1 |2 |16 |2 |2x500 |1 / moderada |
| Standard_F2 |2 |4 |32 |4 |4x500 |2 / alta |
| Standard_F4 |4 |8 |64 |8 |8 x 500 |4 / alta |
| Standard_F8 |8 |16 |128 |16 |16 x 500 |8 / alta |
| Standard_F16 |16 |32 |256 |32 |32 x 500 |8 / extremamente alta |

<br>

## <a name="fs-series"></a>Série Fs*
| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |2 |4.000 / 32 (12) |3.200 / 48 |1 / moderada |
| Standard_F2s |2 |4 |8 |4 |8.000 / 64 (24) |6.400 / 96 |2 / alta |
| Standard_F4s |4 |8 |16 |8 |16.000 / 128 (48) |12.800 / 192 |4 / alta |
| Standard_F8s |8 |16 |32 |16 |32.000 / 256 (96) |25.600 / 384 |8 / alta |
| Standard_F16s |16 |32 |64 |32 |64.000 / 512 (192) |51.200 / 768 |8 / extremamente alta |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

*A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série Fs pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).

<br>

## <a name="g-series"></a>Série G
| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco: IOPS | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 |2 |28 |384 |4 |4 x 500 |1 / alta |
| Standard_G2 |4 |56 |768 |8 |8 x 500 |2 / alta |
| Standard_G3 |8 |112 |1.536 |16 |16 x 500 |4 / muito alta |
| Standard_G4 |16 |224 |3.072 |32 |32 x 500 |8 / extremamente alta |
| Standard_G5 |32 |448 |6.144 |64 |64 x 500 |8 / extremamente alta |

<br>

## <a name="gs-series"></a>Série GS*
| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |28 |56 |4 |10.000 / 100 (264) |5.000 / 125 |1 / alta |
| Standard_GS2 |4 |56 |112 |8 |20.000 / 200 (528) |10.000 / 250 |2 / Alta |
| Standard_GS3 |8 |112 |224 |16 |40.000 / 400 (1.056) |20.000 / 500 |4 / muito alta |
| Standard_GS4 |16 |224 |448 |32 |80.000 / 800 (2.112) |40.000 / 1.000 |8 / extremamente alta |
| Standard_GS5 |32 |448 |896 |64 |160.000 / 1.600 (4.224) |80.000 / 2.000 |8 / extremamente alta |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

*A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série GS pode ser limitada pelo número, tamanho e distribuição dos discos anexados. 

<br>

## <a name="h-series"></a>Série H
As máquinas virtuais da série H do Azure são a próxima geração de VMs de computação de alto desempenho voltadas para as necessidades computacionais de alto nível, como modelagem molecular e dinâmica de fluido computacional. Essas VMs de 8 e 16 núcleos baseiam-se na tecnologia do processador Intel Haswell E5-2667 V3 apresentando memória DDR4 e armazenamento SSD local. 

Além de potência de CPU considerável, a série H oferece diversas opções para RDMA e rede de baixa latência usando FDR InfiniBand e várias configurações de memória para dar suporte a requisitos computacionais com uso intensivo de memória.

Para obter informações e considerações sobre o uso desses tamanhos, confira [Sobre VMs série H ou série A com computação intensiva](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco: IOPS | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |16 |16 x 500 |2 / alta |
| Standard_H16 |16 |112 |2000 |32 |32 x 500 |4 / muito alta |
| Standard_H8m |8 |112 |1000 |16 |16 x 500 |2 / alta |
| Standard_H16m |16 |224 |2000 |32 |32 x 500 |4 / muito alta |
| Standard_H16r* |16 |112 |2000 |32 |32 x 500 |4 / muito alta |
| Standard_H16mr* |16 |224 |2000 |32 |32 x 500 |4 / muito alta |

*Compatível com RDMA

<br>

## <a name="n-series"></a>Série N
Os tamanhos NC e NV também são conhecidos como instâncias habilitadas para GPU. Trata-se de máquinas virtuais especializadas que incluem as placas GPU da NVIDIA, otimizadas para diferentes cenários e casos de uso. Os tamanhos NV são otimizados e projetados para cenários de visualização remota, streaming, jogos, codificação e VDI usando estruturas como OpenGL e DirectX. Os tamanhos NC são mais otimizados para aplicativos que fazem uso intensivo de computação e da rede, algoritmos, incluindo aplicativos baseados em CUDA e OpenCL, e simulações. 

### <a name="nv-instances"></a>Instâncias NV
As instâncias NV têm a tecnologia das GPUs Tesla M60 da NVIDIA e do NVIDIA GRID para aplicativos acelerados de área de trabalho e áreas de trabalho virtuais, em que os clientes poderão visualizar seus dados ou simulações. Os usuários poderão visualizar seus fluxos de trabalho com uso intensivo de gráficos em instâncias NV para obter capacidade gráfica superior, além de executar cargas de trabalho de precisão única, como codificação e renderização. O Tesla M60 oferece 4096 núcleos CUDA em um design de GPU duplo com até 36 fluxos de 1080p H.264. 


| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 |
| Standard_NV12 |12 |112 |680 | 2 |
| Standard_NV24 |24 |224 |1440 | 4 |

1 GPU = metade de um cartão M60.

**Sistemas operacionais com suporte**

* Windows Server 2016, Windows Server 2012 R2 - confira a [instalação do driver N-series para Windows](../articles/virtual-machines/virtual-machines-windows-n-series-driver-setup.md)

### <a name="nc-instances"></a>Instâncias de NC
As instâncias de NC têm a tecnologia do Tesla K80 da NVIDIA. Agora, os usuários podem analisar dados muito mais rapidamente tirando proveito do CUDA para aplicativos de exploração de energia, simulações de falhas, renderização com ray tracing, aprendizado aprofundado e muito mais. O Tesla K80 oferece 4992 núcleos CUDA com um design de GPU duplo, desempenho de até 2,91 teraflops de precisão dupla e até 8,93 teraflops de precisão simples.

| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 |

1 GPU = metade de um cartão K80.

*Compatível com RDMA

**Sistemas operacionais com suporte**

* Windows Server 2016, Windows Server 2012 R2 - confira a [instalação do driver N-series para Windows](../articles/virtual-machines/virtual-machines-windows-n-series-driver-setup.md)
* Ubuntu 16.04 LTS - confira a [instalação do driver N-series para Linux](../articles/virtual-machines/virtual-machines-linux-n-series-driver-setup.md)

<br>

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Observações: Standard A0 - A4 usando a CLI e o PowerShell
No modelo de implantação clássica, alguns nomes de tamanhos de VM são ligeiramente diferentes na CLI e no PowerShell:

* Standard_A0 é ExtraSmall 
* Standard_A1 é pequeno
* Standard_A2 é médio
* Standard_A3 é grande
* Standard_A4 é ExtraLarge

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [assinatura do Azure e limites de serviços, cotas e restrições](../articles/azure-subscription-service-limits.md).
* Saiba mais [sobre as VMs da série H e da série A com computação intensiva](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para cargas de trabalho como HPC (Computação de Alto Desempenho).



<!--HONumber=Dec16_HO1-->


