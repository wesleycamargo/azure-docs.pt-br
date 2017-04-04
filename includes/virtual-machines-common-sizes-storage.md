
A série Ls é otimizada para as cargas de trabalho que requerem um armazenamento local de baixa latência, como bancos de dados NoSQL (por exemplo, Cassandra, MongoDB, Cloudera e Redis). A série Ls oferece até 32 núcleos de CPU, usando a [família de processadores Intel® Xeon® E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Esse é o mesmo desempenho de CPU da série G/GS e vem com 8 GiB de memória por núcleo de CPU.  

## <a name="ls-series"></a>Série Ls

ACU: 180-240
 
| Tamanho          | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | NICs máximas / largura de banda da rede | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s  | 4    | 32   | 678   | 8              | NA / NA (0)          | 5.000 / 125                               | 2 / alta       | 
| Standard_L8s  | 8    | 64   | 1.388 | 16             | NA / NA (0)          | 10.000 / 250                              | 4 / muito alta  | 
| Standard_L16s | 16   | 128  | 2.807 | 32             | NA / NA (0)          | 20.000 / 500                              | 8 / extremamente alta | 
| Standard_L32s** | 32 | 256  | 5.630 | 64             | NA / NA (0)          | 40.000 / 1.000                            | 8 / extremamente alta | 
 
MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes. 

*A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série Ls pode ser limitada pelo número, tamanho e divisão do(s) disco(s) anexado(s). Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md). 

**A instância é isolada em hardware dedicado a um único cliente.