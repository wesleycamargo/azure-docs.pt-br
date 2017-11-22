Os tamanhos de VM otimizados para armazenamento oferecem taxa de transferência de disco alta e de E/S e são ideais para bancos de dados Big Data, SQL e NoSQL. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e desempenho de rede para cada tamanho neste agrupamento. 

A série Ls oferece até 32 vCPUs, usando a [família de processadores Intel® Xeon® E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). A série Ls obtém o mesmo desempenho de CPU da série G/GS e vem com 8 GiB de memória por vCPU.  

## <a name="ls-series"></a>Série Ls

ACU: 180-240
 
| Tamanho          | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/Desempenho de rede esperado (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s  | 4    | 32   | 678   | 8              | NA / NA (0)          | 5.000 / 125                               | 2 / 4000       | 
| Standard_L8s  | 8    | 64   | 1.388 | 16             | NA / NA (0)          | 10.000 / 250                              | 4 / 8000  | 
| Standard_L16s | 16   | 128  | 2.807 | 32             | NA / NA (0)          | 20.000 / 500                              | 8 / 6000 - 16000 &#8224; | 
| Standard_L32s* | 32 | 256  | 5.630 | 64             | NA / NA (0)          | 40.000 / 1.000                            | 8 / 20000 | 
 

A taxa de transferência máxima possível do disco com VMs da série Ls pode ser limitada pelo número, tamanho e divisão dos discos anexados. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/virtual-machines/windows/premium-storage.md). 

*A instância é isolada em hardware dedicado a um único cliente.

