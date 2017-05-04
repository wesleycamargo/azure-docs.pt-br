

* As séries Dv2, D e G, bem como as equivalentes DS/GS são ideais para aplicativos que exigem CPUs mais rápidas, melhor desempenho de disco local ou que têm maior demanda de memória.  Elas oferecem uma combinação poderosa para vários aplicativos de nível empresarial.

As VMs da série D são projetadas para executar aplicativos que exigem maior capacidade de computação e de desempenho de disco temporário. As VMs da série D fornecem processadores mais rápidos, uma maior taxa de memória por núcleo e uma unidade de estado sólido (SSD) para o disco temporário. Para obter detalhes, confira o anúncio no blog do Azure, [Novos tamanhos de máquina virtual da série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

A série Dv2, uma continuação da série D original, apresenta uma CPU mais potente. A CPU da série Dv2 é aproximadamente 35% mais rápida do que a CPU da série D. Ela se baseia na última geração do processador Intel Xeon® E5-2673 v3 (Haswell) de 2.4 GHz e, com a Intel Turbo Boost Technology 2.0, pode chegar a até 3.1 GHz. A série Dv2 tem as mesmas configurações de memória e disco que a série D.


## <a name="gs-series"></a>Série GS*

ACU: 180 - 240

| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e do disco local: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |28 |56 |4 |10.000 / 100 (264) |5.000 / 125 |2 / alta |
| Standard_GS2 |4 |56 |112 |8 |20.000 / 200 (528) |10.000 / 250 |2 / alta |
| Standard_GS3 |8 |112 |224 |16 |40.000 / 400 (1.056) |20.000 / 500 |4 / muito alta |
| Standard_GS4 |16 |224 |448 |32 |80.000 / 800 (2.112) |40.000 / 1.000 |8 / extremamente alta |
| Standard_GS5** |32 |448 |896 |64 |160.000 / 1.600 (4.224) |80.000 / 2.000 |8 / extremamente alta |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

*A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série GS pode ser limitada pelo número, tamanho e distribuição dos discos anexados. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md). 

**A instância é isolada em hardware dedicado a um único cliente.
<br>

## <a name="g-series"></a>Série G

ACU: 180 - 240

| Tamanho         | Núcleos de CPU | Memória: GiB | SSD local: GiB | Taxa de transferência máxima de disco local: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | NICs máximas / largura de banda da rede |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 4 / 4 x 500                       | 2 / alta                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 8 / 8 x 500                       | 2 / alta                     |
| Standard_G3  | 8         | 112         | 1.536          | 24000 / 375 / 187                                        | 16 / 16 x 500                     | 4 / muito alta                |
| Standard_G4  | 16        | 224         | 3.072          | 48000 / 750 / 375                                        | 32 / 32 x 500                     | 8 / extremamente alta           |
| Standard_G5* | 32        | 448         | 6.144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / extremamente alta           |

*A instância é isolada em hardware dedicado a um único cliente.
<br>


## <a name="dsv2-series"></a>Série DSv2*

ACU: 210 - 250

| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2 |2 |14 |28 |4 |8.000 / 64 (72) |6.400 / 96 |2 alta |
| Standard_DS12_v2 |4 |28 |56 |8 |16.000 / 128 (144) |12.800 / 192 |4 alta |
| Standard_DS13_v2 |8 |56 |112 |16 |32.000 / 256 (288) |25.600 / 384 |8 alta |
| Standard_DS14_v2 |16 |112 |224 |32 |64.000 / 512 (576) |51.200 / 768 |8 extremamente alta |
| Standard_DS15_v2*** |20 |140 |280 |40 |80.000 / 640 (720) |64.000 / 960 |8 extremamente alta** |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

*A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série DSv2 pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).

** Em algumas regiões, a rede acelerada está disponível para o tamanho Standard_DS15_v2. Para saber mais sobre o uso e a disponibilidade, consulte [Accelerated Networking is in Preview (A rede acelerada está em versão de visualização)](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) e [Rede acelerada para uma máquina virtual](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md).

***A instância é isolada em hardware dedicado a um único cliente.
<br>
Bps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

*A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série DS pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).

## <a name="dv2-series"></a>Série Dv2

ACU: 210 - 250

| Tamanho              | Núcleos de CPU | Memória: GiB | SSD local: GiB | Taxa de transferência máxima de disco local: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | NICs máximas / largura de banda da rede |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / alta                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / alta                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / alta                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 32 / 32 x 500                       | 8 / extremamente alta           |
| Standard_D15_v2** | 20        | 140         | 1.000          | 60000 / 937 / 468                                        | 40 / 40 x 500                       | 8 / extremamente alta*          |

* Em algumas regiões, a rede acelerada está disponível para o tamanho Standard_D15_v2. Para saber mais sobre o uso e a disponibilidade, consulte [Accelerated Networking is in Preview (A rede acelerada está em versão de visualização)](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) e [Rede acelerada para uma máquina virtual](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md).

**A instância é isolada em hardware dedicado a um único cliente.

<br>

## <a name="ds-series"></a>Série DS*

ACU: 160

| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |4 |8.000 / 64 (72) |6.400 / 64 |2 / alta |
| Standard_DS12 |4 |28 |56 |8 |16.000 / 128 (144) |12.800 / 128 |4 / alta |
| Standard_DS13 |8 |56 |112 |16 |32.000 / 256 (288) |25.600 / 256 |8 / alta |
| Standard_DS14 |16 |112 |224 |32 |64.000 / 512 (576) |51.200 / 512 |8 / muito alta |


MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

*A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série DS pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).

## <a name="d-series"></a>Série D

ACU: 160

| Tamanho         | Núcleos de CPU | Memória: GiB | SSD local: GiB | Taxa de transferência máxima de disco local: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | NICs máximas / largura de banda da rede |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / alta                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / alta                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / alta                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 32 / 32 x 500                       | 8 / muito alta                |
<br>


<br>

