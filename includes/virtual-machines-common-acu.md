 



Criamos o conceito da ACU (unidade de computação do Azure) para fornecer uma maneira de comparar o desempenho de computação (CPU) em SKUs do Azure. Isso ajudará você a identificar facilmente qual SKU é tem maior probabilidade de satisfazer suas necessidades de desempenho.  A ACU atualmente é padronizada como uma VM pequena (Standard_A1) sendo 100 e todas as SKUs representam, aproximadamente, o quanto a SKU pode executar um parâmetro de comparação padrão mais rapidamente. 

> [!IMPORTANT]
> A ACU é apenas uma diretriz.  Os resultados para sua carga de trabalho podem variar. 
> 
> 

<br>

| Família de SKU | ACU \ vCPU | vCPU:Core |
| --- | --- |---|
| [A0](../articles/virtual-machines/windows/sizes-general.md) |50 | 1:1 |
| [A4 A1](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A7 A5](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A1_v2 A8_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A2m_v2 A8m_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A8-A11](../articles/virtual-machines/windows/sizes-hpc.md) |225* | 1:1 |
| [D1 D14](../articles/virtual-machines/windows/sizes-general.md) |160 | 1:1 |
| [D1_v2 D15_v2](../articles/virtual-machines/windows/sizes-general.md) |210 - 250* | 1:1 |
| [DS14 DS1](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 | 1:1 |
| [DS1_v2 DS15_v2](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |210-250* | 1:1 |
| [D_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160-190* | 2:1** |
| [Ds_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160-190* | 2:1** |
| [E_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160-190* | 2:1** |
| [Es_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160-190* | 2:1** |
| [F2s_v2-F72s_v2](../articles/virtual-machines/windows/sizes-compute.md) |195-210* | 2:1** |
| [F1-F16](../articles/virtual-machines/windows/sizes-compute.md) |210-250* | 1:1 |
| [F1s-F16s](../articles/virtual-machines/windows/sizes-compute.md) |210-250* | 1:1 |
| [G5 G1](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* | 1:1 |
| [GS1 GS5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* | 1:1 |
| [H](../articles/virtual-machines/windows/sizes-hpc.md) |290 – 300* | 1:1 |
| [L4s-L32s](../articles/virtual-machines/windows/sizes-storage.md) |180 - 240* | 1:1 |
| [M](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) | 160-180 | 2:1** |

ACUs marcados com um * usam tecnologia Intel® Turbo para aumentar a frequência da CPU e fornecer um aumento de desempenho.  A quantidade do aumento pode variar com base no tamanho da VM, na carga de trabalho e em outras cargas de trabalho em execução no mesmo host.

**Hyper-threaded. 
