
<!-- A-series, Av2-series, D-series, Dv2-series, DS-series*, DSv2-series* -->

As VMs da série A e da série Av2 podem ser implantadas em uma variedade de tipos de hardware e processadores. O tamanho é limitado, com base no hardware, para oferecer desempenho de processador consistente para a instância em execução, independentemente do hardware em que é implantado. Para determinar o hardware físico no qual esse tamanho é implantado, consulte o hardware virtual de dentro da Máquina Virtual.

As VMs da série D são projetadas para executar aplicativos que exigem maior capacidade de computação e de desempenho de disco temporário. As VMs da série D fornecem processadores mais rápidos, uma maior taxa de memória por núcleo e uma unidade de estado sólido (SSD) para o disco temporário. Para obter detalhes, confira o anúncio no blog do Azure, [Novos tamanhos de máquina virtual da série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

A série Dv2, uma continuação da série D original, apresenta uma CPU mais potente. A CPU da série Dv2 é aproximadamente 35% mais rápida do que a CPU da série D. Ela se baseia na última geração do processador Intel Xeon® E5-2673 v3 (Haswell) de 2.4 GHz e, com a Intel Turbo Boost Technology 2.0, pode chegar a até 3.1 GHz. A série Dv2 tem as mesmas configurações de memória e disco que a série D.

Os tamanhos da camada básicos são principalmente para as cargas de trabalho de desenvolvimento e outros aplicativos que não requerem o balanceamento de carga, dimensionamento automático ou máquinas virtuais que consomem muita memória. Para obter informações sobre os tamanhos da VM mais adequados para os aplicativos de produção, consulte (Tamanhos das máquinas virtuais) [virtual-machines-size-specs.md] e para obter informações sobre os preços da VM, consulte [Preços das Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/).

> [!NOTE]
> Todos os tamanhos de máquina virtual do Azure oferecerão suporte a pelo menos 2 interfaces de rede (NICs) começando no dia 15 de abril. Para obter informações sobre uma região específica, fique atento na [atualização do serviço](https://azure.microsoft.com/en-us/updates/). Essas informações são indicadas abaixo com um "^".

## <a name="dsv2-series"></a>Série DSv2*

ACU: 210-250

| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |2 |4.000 / 32 (43) |3.200 / 48 |2 ^ moderada |
| Standard_DS2_v2 |2 |7 |14 |4 |8.000 / 64 (86) |6.400 / 96 |2 alta |
| Standard_DS3_v2 |4 |14 |28 |8 |16.000 / 128 (172) |12.800 / 192 |4 alta |
| Standard_DS4_v2 |8 |28 |56 |16 |32.000 / 256 (344) |25.600 / 384 |8 alta |
| Standard_DS5_v2 |16 |56 |112 |32 |64.000 / 512 (688) |51.200 / 768 |8 extremamente alta |



## <a name="dv2-series"></a>Série Dv2

ACU: 210-250

| Tamanho              | Núcleos de CPU | Memória: GiB | SSD local: GiB | Taxa de transferência máxima de disco local: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | NICs máximas / largura de banda da rede |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1_v2    | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 2 / 2 x 500                         | 2 ^/ moderada                 |
| Standard_D2_v2    | 2         | 7           | 100            | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / alta                     |
| Standard_D3_v2    | 4         | 14          | 200            | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / alta                     |
| Standard_D4_v2    | 8         | 28          | 400            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / alta                     |
| Standard_D5_v2    | 16        | 56          | 800            | 48000 / 750 / 375                                        | 32 / 32 x 500                       | 8 / extremamente alta           |

<br>

## <a name="ds-series"></a>Série DS*
| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e do disco local: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |2 |4.000 / 32 (43) |3.200 / 32 |2 ^/ moderada |
| Standard_DS2 |2 |7 |14 |4 |8.000 / 64 (86) |6.400 / 64 |2 / alta |
| Standard_DS3 |4 |14 |28 |8 |16.000 / 128 (172) |12.800 / 128 |4 / alta |
| Standard_DS4 |8 |28 |56 |16 |32.000 / 256 (344) |25.600 / 256 |8 / alta |

<br>
## <a name="d-series"></a>Série D 

ACU: 160

| Tamanho         | Núcleos de CPU | Memória: GiB | SSD local: GiB | Taxa de transferência máxima de disco local: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | NICs máximas / largura de banda da rede |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 2 / 2 x 500                         | 2 ^/ moderada                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / alta                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / alta                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / alta                     |

<br>


## <a name="av2-series"></a>Série Av2

ACU: 100

| Tamanho            | Núcleos de CPU | Memória: GiB | SSD local: GiB | Taxa de transferência máxima de disco local: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | NICs máximas / largura de banda da rede |
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500                         | 2 ^/ moderada                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500                         | 2 / moderada                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500                         | 4 / alta                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500                       | 8 / alta                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500                         | 2 / moderada                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500                         | 4 / alta                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16 x 500                       | 8 / alta                     |

<br>

## <a name="a-series"></a>Séria A

ACU: 50-100

| Tamanho | Núcleos de CPU | Memória: GiB | HDD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0* |1 |0,768 |20 |1 |1 x 500 |2 ^/ baixa |
| Standard_A1 |1 |1,75 |70 |2 |2x500 |2 ^/ moderada |
| Standard_A2 |2 |3,5 |135 |4 |4x500 |2 ^/ moderada |
| Standard_A3 |4 |7 |285 |8 |8 x 500 |2 / alta |
| Standard_A4 |8 |14 |605 |16 |16 x 500 |4 / alta |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 ^/ moderada |
| Standard_A6 |4 |28 |285 |8 |8 x 500 |2 / alta |
| Standard_A7 |8 |56 |605 |16 |16 x 500 |4 / alta |
<br>

*O tamanho A0 está assinado em excesso no hardware físico. Para este tamanho específico somente, outras implantações de clientes podem afetar o desempenho da carga de trabalho em execução. O desempenho relativo é descrito a seguir como a linha de base esperada, sujeito a uma variação aproximada de 15%.

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4 usando CLI e PowerShell
No modelo de implantação clássica, alguns nomes de tamanhos de VM são ligeiramente diferentes na CLI e no PowerShell:

* Standard_A0 é ExtraSmall 
* Standard_A1 é pequeno
* Standard_A2 é médio
* Standard_A3 é grande
* Standard_A4 é ExtraLarge

## <a name="basic-a"></a>A Básico

|Tamanho – Tamanho\Nome |Núcleos de CPU|Memória|NICs (Máx.)|Tamanho máximo do disco temporário |Máx. discos de dados (1.023 GB cada)|Máx. IOPS (300 por disco)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2^| 20 GB|1|1 x 300|
|A1\Basic_A1|1|1,75 GB|2^| 40 GB |2|2 x 300|
|A2\Basic_A2|2|3,5 GB|2^| 60 GB|4|4 x 300|
|A3\Basic_A3|4|7 GB|2^| 120 GB |8|8 x 300|
|A4\Basic_A4|8|14 GB|2^| 240 GB |16|16 x 300|
