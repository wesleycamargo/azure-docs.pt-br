<!-- F-series, Fs-series* -->

A série Fsv2 é baseado no processador Intel® Xeon® Platinum 8168, com uma frequência de núcleo básico de 2,7 GHz e uma frequência máxima turbo de núcleo único de 3,7 GHz. As instruções Intel® AVX-512, que são novas em Processadores Escalonáveis da Intel, fornecerão um aumento de até duas vezes de desempenho para cargas de trabalho de processamento de vetor em operações de ponto flutuante de precisão simples e dupla. Em outras palavras, eles são muito rápidos para qualquer carga de trabalho computacional. 

A um preço de lista por hora inferior, a série Fsv2 é o melhor valor de preço/desempenho no portfólio do Azure com base na ACU (Unidade de Computação do Azure) por vCPU. 

A série F é baseada no processador 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell), que pode obter velocidades de relógio tão altas quanto 3.1 GHz com o Intel Turbo Boost Technology 2.0. Esse é o mesmo desempenho de CPU que o das VMs da série Dv2.  

As VMs da série F são uma ótima opção para as cargas de trabalho que exigem CPUs mais rápidas, mas não precisam de tanta memória ou armazenamento temporário por vCPU.  Cargas de trabalho como análise, servidores de jogos, servidores Web e de processamento em lote serão beneficiados com o valor da série F.

A série Fs fornece todas as vantagens da série F, além do armazenamento Premium.

# <a name="fsv2-series"></a>Série Fsv2

ACU: 195 - 210

| Tamanho             | da vCPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Máximo de NICs/Desempenho de rede esperado (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|------------------------------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 (32)                                                             | Moderado                                       |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 (64)                                                             | Moderado                                       |
| Standad_F8s_v2   | 8      | 16          | 64             | 16             | 16000 (128)                                                           | Alto                                           |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 (256)                                                           | Alto                                           |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 (512)                                                           | Extremamente Alto                                 |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 (1024)                                                         | Extremamente Alto                                 |
| Standard_F72s_v2 | 72     | 144         | 576            | 32             | 144000 (1520)                                                         | Extremamente Alto                                 |


## <a name="fs-series"></a>Série Fs*

ACU: 210 - 250

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache e armazenamento temporário: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | Máximo de NICs/Desempenho de rede esperado (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |2 |4.000 / 32 (12) |3.200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |4 |8.000 / 64 (24) |6.400 / 96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |8 |16.000 / 128 (48) |12.800 / 192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |16 |32.000 / 256 (96) |25.600 / 384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |32 |64.000 / 512 (192) |51.200 / 768 |8 / 6000-12000 &#8224; |

MBps = 10^6 bytes por segundo e GiB = 1024^3 bytes.

*A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série Fs pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/storage/common/storage-premium-storage.md).


<br>

## <a name="f-series"></a>Série F

ACU: 210 - 250

| Tamanho         | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | Máximo de NICs/Desempenho de rede esperado (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 2 / 2 x 500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 4 / 4 x 500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 8 / 8 x 500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 16 / 16 x 500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 32 / 32 x 500                       | 8 / 6000 - 12000 &#8224;           |


<br>


