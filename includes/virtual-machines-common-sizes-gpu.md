
Os tamanhos de VM otimizados para GPU são para máquinas virtuais especializadas direcionados para renderização gráfica pesada e edição de vídeo. Disponível com uma ou várias GPUs. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e desempenho de rede para cada tamanho neste agrupamento. 


Os tamanhos NC e NV também são conhecidos como instâncias habilitadas para GPU. Trata-se de tamanhos de máquinas virtuais especializadas que incluem as placas de GPU da NVIDIA, otimizadas para diferentes cenários e casos de uso. Os tamanhos NV são otimizados e projetados para cenários de visualização remota, streaming, jogos, codificação e VDI usando estruturas como OpenGL e DirectX. Os tamanhos NC são mais otimizados para aplicativos que fazem uso intensivo de computação e da rede, algoritmos, incluindo aplicativos baseados em CUDA e OpenCL, e simulações. 


As instâncias NV têm a tecnologia das GPUs Tesla M60 da NVIDIA e do NVIDIA GRID para aplicativos acelerados de área de trabalho e áreas de trabalho virtuais, em que os clientes podem visualizar seus dados ou simulações. Os usuários podem visualizar seus fluxos de trabalho com uso intensivo de gráficos em instâncias NV para obter capacidade gráfica superior, além de executar cargas de trabalho de precisão única, como codificação e renderização. O Tesla M60 oferece 4096 núcleos CUDA em um design de GPU duplo com até 36 fluxos de 1080p H.264. 

As instâncias de NC têm a tecnologia do Tesla K80 da NVIDIA. Agora, os usuários podem analisar dados muito mais rapidamente tirando proveito do CUDA para aplicativos de exploração de energia, simulações de falhas, renderização com ray tracing, aprendizado aprofundado e muito mais. O Tesla K80 oferece 4992 núcleos CUDA com um design de GPU duplo, desempenho de até 2,91 teraflops de precisão dupla e até 8,93 teraflops de precisão simples.

## <a name="nv-instances"></a>Instâncias NV

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Discos de dados máximos |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 |

1 GPU = metade de um cartão M60.

## <a name="nc-instances"></a>Instâncias de NC

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Discos de dados máximos |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 |

1 GPU = metade de um cartão K80.

*Compatível com RDMA


