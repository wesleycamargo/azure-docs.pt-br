
Para ver os limites gerais em VMs do Azure, consulte [Limites de assinatura e serviços do Azure, cotas e restrições](../articles/azure-subscription-service-limits.md).

Os tamanhos padrão consistem em várias séries: A, D, DS, F, Fs, G e GS. Entre as considerações sobre algumas dessas dimensões estão:

*   As VMs da série D são projetadas para executar aplicativos que exigem maior capacidade de computação e de desempenho de disco temporário. As VMs da série D fornecem processadores mais rápidos, uma maior taxa de memória por núcleo e uma unidade de estado sólido (SSD) para o disco temporário. Para obter detalhes, confira o anúncio no blog do Azure, [Novos tamanhos de máquina virtual da série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

*   A série Dv2, uma continuação da série D original, apresenta uma CPU mais potente. A CPU da série Dv2 é aproximadamente 35% mais rápida do que a CPU da série D. Ela se baseia na última geração do processador Intel Xeon® E5-2673 v3 (Haswell) de 2.4 GHz e, com a Intel Turbo Boost Technology 2.0, pode chegar a até 3.1 GHz. A série Dv2 tem as mesmas configurações de memória e disco que a série D.

* A série F é baseada no processador 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell), que pode obter velocidades de relógio tão altas quanto 3.1 GHz com o Intel Turbo Boost Technology 2.0. Esse é o mesmo desempenho de CPU que o das VMs da série Dv2. A um preço de lista inferior por hora, a série F é o melhor valor de preço/desempenho no portfólio do Azure com base na ACU (Unidade de Computação do Azure) por núcleo.

	A série F também introduz um novo padrão em nomenclatura de tamanho de VM para o Azure. Para esta série e os tamanhos de VM lançados no futuro, o valor numérico após a letra do sobrenome corresponderá ao número de núcleos de CPU. Recursos adicionais, como armazenamento otimizado para premium, serão designados por letras após a contagem de núcleos de CPU numérica. Esse formato de nomeação será usado para futuras tamanhos de VM lançados mas não vai retroativamente alterar os nomes de quaisquer tamanhos VM existentes que foram lançados.


*   As VMs da série G oferecem a maior memória e são executadas em hosts com processadores da família Intel Xeon E5 V3.


*   As VMs das séries DS, DSv2, Fs e GS podem usar o Armazenamento Premium, que fornece armazenamento de alto desempenho e baixa latência para cargas de trabalho com uso intenso de E/S. Essas VMs usam SSDs (unidades de estado sólido) para hospedar os discos da máquina virtual e também oferecem um cache de disco SSD local. O Armazenamento Premium está disponível em determinadas regiões. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).


*   As VMs da série A podem ser implantadas em uma variedade de tipos de hardware e processadores. O tamanho é limitado, com base no hardware, para oferecer desempenho de processador consistente para a instância em execução, independentemente do hardware em que é implantado. Para determinar o hardware físico no qual esse tamanho é implantado, consulte o hardware virtual de dentro da Máquina Virtual.

*   O tamanho A0 está assinado em excesso no hardware físico. Para este tamanho específico somente, outras implantações de clientes podem afetar o desempenho da carga de trabalho em execução. O desempenho relativo é descrito a seguir como a linha de base esperada, sujeito a uma variação aproximada de 15%.


O tamanho da máquina virtual afeta os preços. O tamanho também afeta a capacidade de processamento, memória e armazenamento da máquina virtual. Os custos de armazenamento são calculados separadamente com base nas páginas usadas na conta de armazenamento. Para obter detalhes, confira [Detalhes de preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) e [Preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).


As considerações a seguir podem ajudá-lo a escolher um tamanho:


* Os tamanhos A8-A11 também são conhecidos como *instâncias de computação intensiva*. O hardware de datacenter do Azure que executa esses tamanhos é projetado e otimizado para aplicativos de uso intensivo de computação e rede, incluindo aplicativos, modelagem e simulações de cluster HPC (computação de alto desempenho). Para obter informações detalhadas e considerações sobre o uso desses tamanhos, confira [Sobre as instâncias de computação intensiva A8, A9, A10 e A11](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).


*	As séries Dv2, D e G e as equivalentes DS/GS são ideais para aplicativos que exigem CPUs mais rápidas, melhor desempenho de disco local ou que têm maior demanda de memória. Elas oferecem uma combinação poderosa para vários aplicativos de nível empresarial.

* As VMs série F são uma ótima opção para cargas de trabalho que exigem CPUs mais rápidas, mas não precisam de tanta memória ou SSD local por núcleo da CPU. Cargas de trabalho como análise, servidores de jogos, servidores Web e de processamento em lote serão beneficiados com o valor da série F.

*   Alguns dos hosts físicos em data centers do Azure podem não aceitar tamanhos de máquina virtual maiores, como A5 – A11. Como resultado, você poderá ver a mensagem de erro **Falha ao configurar a máquina virtual <nome da máquina>** ou **Falha ao criar a máquina virtual <nome da máquina>** ao redimensionar uma máquina virtual existente para um novo tamanho, criar uma nova máquina virtual em uma rede virtual criada antes de 16 de abril de 2013 ou adicionar uma nova máquina virtual a um serviço de nuvem. Confira [Erro: "Falha ao configurar a máquina virtual"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) no fórum de suporte de soluções alternativas para cada cenário de implantação.


## Considerações sobre o desempenho

Criamos o conceito da ACU (unidade de computação do Azure) para fornecer uma maneira de comparar o desempenho de computação (CPU) em SKUs do Azure. Isso ajudará você a identificar facilmente qual SKU é tem maior probabilidade de satisfazer suas necessidades de desempenho. A ACU atualmente é padronizada como uma VM pequena (Standard\_A1) sendo 100 e todas as SKUs representam, aproximadamente, o quanto a SKU pode executar um parâmetro de comparação padrão mais rapidamente.

>[AZURE.IMPORTANT] A ACU é apenas uma diretriz. Os resultados para sua carga de trabalho podem variar.

<br>

|Família de SKU |ACU/núcleo |
|---|---|
|[Standard\_A0](#a-series) |50 |
|[Standard\_A1-4](#a-series) |100 |
|[Standard\_A5-7](#a-series) |100 |
|[A8-A11](#a-series) |225*|
|[D1-14](#d-series) |160 |
|[D1-15v2](#dv2-series) |210 - 250*|
|[DS1-14](#ds-series) |160 |
|[DS1-15v2](#dsv2-series) |210-250* |
|[F1-F16](#f-series) | 210-250*|
|[F1s-F16s](#fs-series) | 210-250*|
|[G1-5](#g-series) |180 - 240*|
|[GS1-5](#gs-series) |180 - 240*|


ACUs marcados com um * usam tecnologia Intel® Turbo para aumentar a frequência da CPU e fornecer um aumento de desempenho. A quantidade do aumento pode variar com base no tamanho da VM, na carga de trabalho e em outras cargas de trabalho em execução no mesmo host.

## Tabelas de tamanho

As tabelas a seguir mostram os tamanhos e as capacidades oferecidas.

* A capacidade de armazenamento é mostrada em unidades de GiB ou de 1024^3 bytes. Ao comparar os discos medidos em GB (1000^3 bytes) com os discos medidos em GiB (1024^3), lembre-se de que os números de capacidade fornecidos em GiB poderão parecer menores. Por exemplo, 1023 GiB = 1098,4 GB

* A taxa de transferência do disco é medida em IOPS (operações de entrada/saída por segundo) e em MBps, em que MBps = 10^6 bytes/s.

* Os discos de dados podem operar nos modos em cache ou não armazenado em cache. Para a operação do disco de dados armazenados em cache, o modo de cache do host é definido como **ReadOnly** ou **ReadWrite**. Para as operação do disco de dados não armazenados em cache, o modo de cache do host é definido como **Nenhum**.


* A largura de banda de rede máxima é a largura de banda agregada máxima alocada e atribuída por tipo de VM. A largura de banda máxima fornece diretrizes para selecionar o tipo correto de VM, a fim de garantir que uma capacidade adequada de rede está disponível. Durante a movimentação entre Baixa, Moderada, Alta e Muito Alta, a taxa de transferência será aumentada de acordo. O desempenho real da rede dependerá de vários fatores, incluindo cargas de rede e aplicativos, bem como configurações de rede do aplicativo.


## Séria A

| Tamanho | Núcleos de CPU | Memória: GiB | Tamanho do disco local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximas / largura de banda da rede |
|-------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard\_A0 | 1 | 0,768 | 20 | 1 | 1 x 500 | 1 / baixa |
| Standard\_A1 | 1 | 1,75 | 70 | 2 | 2x500 | 1 / moderada |
| Standard\_A2 | 2 | 3,5 GB | 135 | 4 | 4x500 | 1 / moderada |
| Standard\_A3 | 4 | 7 | 285 | 8 | 8 x 500 | 2 / alta |
| Standard\_A4 | 8 | 14 | 605 | 16 | 16 x 500 | 4 / alta |
| Standard\_A5 | 2 | 14 | 135 | 4 | 4X500 | 1 / moderada |
| Standard\_A6 | 4 | 28 | 285 | 8 | 8 x 500 | 2 / alta |
| Standard\_A7 | 8 | 56 | 605 | 16 | 16 x 500 | 4 / alta |

<br>
## Série A – Instâncias de computação intensiva

Para obter informações e considerações sobre o uso desses tamanhos, confira [Sobre as instâncias A8, A9, A10 e A11 com computação intensiva](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).


| Tamanho | Núcleos de CPU | Memória: GiB | Tamanho do disco local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximas / largura de banda da rede |
|--------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard\_A8 | 8 | 56 | 382 | 16 | 16 x 500 | 2 / alta |
| Standard\_A9 | 16 | 112 | 382 | 16 | 16 x 500 | 4 / muito alta |
| Standard\_A10 | 8 | 56 | 382 | 16 | 16 x 500 | 2 / alta |
| Standard\_A11 | 16 | 112 | 382 | 16 | 16 x 500 | 4 / muito alta |

<br>
## Série D


| Tamanho | Núcleos de CPU | Memória: GiB | Tamanho do disco local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximas / largura de banda da rede |
|--------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard\_D1 | 1 | 3,5 | 50 | 2 | 2x500 | 1 / moderada |
| Standard\_D2 | 2 | 7 | 100 | 4 | 4x500 | 2 / alta |
| Standard\_D3 | 4 | 14 | 200 | 8 | 8 x 500 | 4 / alta |
| Standard\_D4 | 8 | 28 | 400 | 16 | 16 x 500 | 8 / alta |
| Standard\_D11 | 2 | 14 | 100 | 4 | 4x500 | 2 / alta |
| Standard\_D12 | 4 | 28 | 200 | 8 | 8 x 500 | 4 / alta |
| Standard\_D13 | 8 | 56 | 400 | 16 | 16 x 500 | 8 / alta |
| Standard\_D14 | 16 | 112 | 800 | 32 | 32 x 500 | 8 / muito alta |

<br>
## Série Dv2

| Tamanho | Núcleos de CPU | Memória: GiB | Tamanho do disco local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximas / largura de banda da rede |
|-----------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard\_D1\_v2 | 1 | 3,5 | 50 | 2 | 2x500 | 1 / moderada |
| Standard\_D2\_v2 | 2 | 7 | 100 | 4 | 4x500 | 2 / alta |
| Standard\_D3\_v2 | 4 | 14 | 200 | 8 | 8 x 500 | 4 / alta |
| Standard\_D4\_v2 | 8 | 28 | 400 | 16 | 16 x 500 | 8 / alta |
| Standard\_D5\_v2 | 16 | 56 | 800 | 32 | 32 x 500 | 8 / extremamente alta |
| Standard\_D11\_v2 | 2 | 14 | 100 | 4 | 4x500 | 2 / alta |
| Standard\_D12\_v2 | 4 | 28 | 200 | 8 | 8 x 500 | 4 / alta |
| Standard\_D13\_v2 | 8 | 56 | 400 | 16 | 16 x 500 | 8 / alta |
| Standard\_D14\_v2 | 16 | 112 | 800 | 32 | 32 x 500 | 8 / extremamente alta |
| Standard\_D15\_v2 | 20 | 140 | 1\.000 | 40 | 40 x 500 | 8 / extremamente alta |

<br>
## Série DS*


| Tamanho | Núcleos de CPU | Memória: GiB | Tamanho do disco local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | NICs máximas / largura de banda da rede |
|---------------|-----------|--------------|--------------------------------|----------------|--------------------------------------------|----------------------------------------------|-----------------------|
| Standard\_DS1 | 1 | 3,5 | 7 | 2 | 4\.000 / 32 (42) | 3\.200 / 32 | 1 / moderada |
| Standard\_DS2 | 2 | 7 | 14 | 4 | 8\.000 / 64 (86) | 6\.400 / 64 | 2 / alta |
| Standard\_DS3 | 4 | 14 | 28 | 8 | 16\.000 / 128 (172) | 12\.800 / 128 | 4 / alta |
| Standard\_DS4 | 8 | 28 | 56 | 16 | 32\.000 / 256 (344) | 25\.600 / 256 | 8 / alta |
| Standard\_DS11 | 2 | 14 | 28 | 4 | 8\.000 / 64 (72) | 6\.400 / 64 | 2 / alta |
| Standard\_DS12 | 4 | 28 | 56 | 8 | 16\.000 / 128 (144) | 12\.800 / 128 | 4 / alta |
| Standard\_DS13 | 8 | 56 | 112 | 16 | 32\.000 / 256 (288) | 25\.600 / 256 | 8 / alta |
| Standard\_DS14 | 16 | 112 | 224 | 32 | 64\.000 / 512 (576) | 51\.200 / 512 | 8 / muito alta |

MBps = 10^6 bytes por segundo.

*A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série DS pode ser limitada pelo número, tamanho e distribuição dos discos anexados. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).



<br>
## Série DSv2*


| Tamanho | Núcleos de CPU | Memória: GiB | Tamanho do disco de SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | NICs máximas / largura de banda da rede |
|------------------|-----------|--------------|---------------------------|----------------|-------------------------------------------------|-------------------------------------------------|------------------------------|
| Standard\_DS1\_v2 | 1 | 3,5 | 7 | 2 | 4\.000 / 32 (43) | 3\.200 / 48 | 4 moderada |
| Standard\_DS2\_v2 | 2 | 7 | 14 | 4 | 8\.000 / 64 (86) | 6\.400 / 96 | 2 alta |
| Standard\_DS3\_v2 | 4 | 14 | 28 | 8 | 16\.000 / 128 (172) | 12\.800 / 192 | 4 alta |
| Standard\_DS4\_v2 | 8 | 28 | 56 | 16 | 32\.000 / 256 (344) | 25\.600 / 384 | 8 alta |
| Standard\_DS5\_v2 | 16 | 56 | 112 | 32 | 64\.000 / 512 (688) | 51\.200 / 768 | 8 extremamente alta |
| Standard\_DS11\_v2 | 2 | 14 | 28 | 4 | 8\.000 / 64 (72) | 6\.400 / 96 | 2 alta |
| Standard\_DS12\_v2 | 4 | 28 | 56 | 8 | 16\.000 / 128 (144) | 12\.800 / 192 | 4 alta |
| Standard\_DS13\_v2 | 8 | 56 | 112 | 16 | 32\.000 / 256 (288) | 25\.600 / 384 | 8 alta |
| Standard\_DS14\_v2 | 16 | 112 | 224 | 32 | 64\.000 / 512 (576) | 51\.200 / 768 | 8 extremamente alta |
| Standard\_DS15\_v2 | 20 | 140 GB | 280 | 40 | 80\.000 / 640 (720) | 64\.000 / 960 | 8 extremamente alta |

MBps = 10^6 bytes por segundo.

*A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série DSv2 pode ser limitada pelo número, tamanho e distribuição dos discos anexados. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).


<br>
## Série F


| Tamanho | Núcleos de CPU | Memória: GiB | Tamanho do disco de SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco: IOPS | NICs máximas / largura de banda da rede |
|--------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard\_F1 | 1 | 2 | 16 | 2 | 2x500 | 1 / moderada |
| Standard\_F2 | 2 | 4 | 32 | 4 | 4x500 | 2 / alta |
| Standard\_F4 | 4 | 8 | 64 | 8 | 8 x 500 | 4 / alta |
| Standard\_F8 | 8 | 16 | 128 | 16 | 16 x 500 | 8 / alta |
| Standard\_F16 | 16 | 32 | 256 | 32 | 32 x 500 | 8 / extremamente alta |

<br>
## Série Fs*

| Tamanho | Núcleos de CPU | Memória: GiB | Tamanho do disco de SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | NICs máximas / largura de banda da rede |
|---------------|-------|-----|----------|--------|------------------------------|---------------------------------|---------------|
| Standard\_F1s | 1 | 2 | 4 | 2 | 4\.000 / 32 (12) | 3\.200 / 48 | 1 / moderada |
| Standard\_F2s | 2 | 4 | 8 | 4 | 8\.000 / 64 (24) | 6\.400 / 96 | 2 / alta |
| Standard\_F4s | 4 | 8 | 16 | 8 | 16\.000 / 128 (48) | 12\.800 / 192 | 4 / alta |
| Standard\_F8s | 8 | 16 | 32 | 16 | 32\.000 / 256 (96) | 25\.600 / 384 | 8 / alta |
| Standard\_F16s | 16 | 32 | 64 | 32 | 64\.000 / 512 (192) | 51\.200 / 768 | 8 / extremamente alta |

MBps = 10^6 bytes por segundo.

*A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série Fs pode ser limitada pelo número, tamanho e distribuição dos discos anexados. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho das máquinas virtuais do Azure](../articles/storage/storage-premium-storage.md).


<br>
## Série G

| Tamanho | Núcleos de CPU | Memória: GiB | Tamanho do SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco: IOPS | NICs máximas / largura de banda da rede |
|-------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard\_G1 | 2 | 28 | 384 | 4 | 4 x 500 | 1 / alta |
| Standard\_G2 | 4 | 56 | 768 | 8 | 8 x 500 | 2 / alta |
| Standard\_G3 | 8 | 112 | 1\.536 | 16 | 16 x 500 | 4 / muito alta |
| Standard\_G4 | 16 | 224 | 3\.072 | 32 | 32 x 500 | 8 / extremamente alta |
| Standard\_G5 | 32 | 448 | 6\.144 | 64 | 64 x 500 | 8 / extremamente alta |



<br>
## Série GS*


| Tamanho | Núcleos de CPU | Memória: GiB | Tamanho do disco de SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco em cache: IOPS / MBps (tamanho do cache em GiB) | Taxa de transferência máxima do disco não armazenado em cache: IOPS / MBps | NICs máximas / largura de banda da rede |
|--------------|-----------|--------------|---------------------------|--------------------------------|----------------|--------------------------------------------|----------------------------------------------|-----------------------|
| Standard\_GS1 | 2 | 28 | 56 | 4 | 10\.000 / 100 (264) | 5\.000 / 125 | 1 / alta |
| Standard\_GS2 | 4 | 56 | 528 | 8 | 20\.000 / 200 (528) | 10\.000 / 250 | 2 / Alta |
| Standard\_GS3 | 8 | 112 | 1\.056 | 16 | 40\.000 / 400 (1.056) | 20\.000 / 500 | 4 / muito alta |
| Standard\_GS4 | 16 | 224 | 2\.112 | 32 | 80\.000 / 800 (2.112) | 40\.000 / 1.000 | 8 / extremamente alta |
| Standard\_GS5 | 32 | 448 | 4\.224 | 64 | 160\.000 / 1.600 (4.224) | 80\.000 / 2.000 | 8 / extremamente alta |

MBps = 10^6 bytes por segundo.

*A taxa de transferência máxima possível do disco (IOPS ou MBps) com uma VM da série GS pode ser limitada pelo número, tamanho e distribuição dos discos anexados.



## Série N (visualização)

Os tamanhos NC e NV também são conhecidos como instâncias habilitadas para GPU. Trata-se de máquinas virtuais especializadas que incluem as placas GPU da NVIDIA, otimizadas para diferentes cenários e casos de uso. Os tamanhos NV são otimizados e projetados para cenários de visualização remota, streaming, jogos, codificação e VDI usando estruturas como OpenGL e DirectX. Os tamanhos NC são mais otimizados para aplicativos que fazem uso intensivo de computação e da rede, algoritmos, incluindo aplicativos baseados em CUDA e OpenCL, e simulações.


### Instâncias NV
As instâncias NV têm a tecnologia das GPUs Tesla M60 da NVIDIA e do NVIDIA GRID para aplicativos acelerados de área de trabalho e áreas de trabalho virtuais, em que os clientes poderão visualizar seus dados ou simulações. Os usuários poderão visualizar seus fluxos de trabalho com uso intensivo de gráficos em instâncias NV para obter capacidade gráfica superior, além de executar cargas de trabalho de precisão única, como codificação e renderização. O Tesla M60 oferece 4096 núcleos CUDA em um design de GPU duplo com até 36 fluxos de 1080p H.264.


| Tamanho | Núcleos de CPU | Memória: GiB | Tamanho do disco de SSD local: GiB | GPU |
|---------------|-----------|--------------|---------------------------|----------------|
| Standard\_NV6 | 6 | 56 | 380 | 1 x NVIDIA M60 |
| Standard\_NV12 | 12 | 112 | 680 | 2 x NVIDIA M60 |
| Standard\_NV24 | 24 | 224 | 1440 | 4 x NVIDIA M60 |



### Instâncias de NC

As instâncias de NC têm a tecnologia Tesla K80 da NVIDIA. Agora, os usuários podem analisar dados muito mais rapidamente tirando proveito do CUDA para aplicativos de exploração de energia, simulações de falhas, renderização com ray tracing, aprendizado aprofundado e muito mais. O Tesla K80 oferece 4992 núcleos CUDA com um design de GPU duplo, desempenho de até 2,91 teraflops de precisão dupla e até 8,93 teraflops de precisão simples.


| Tamanho | Núcleos de CPU | Memória: GiB | Tamanho do disco de SSD local: GiB | GPU |
|---------------|-----------|--------------|---------------------------|----------------|
| Standard\_NC6 | 6 | 56 | 380 | 1 x NVIDIA K80 |
| Standard\_NC12 | 12 | 112 | 680 | 2 x NVIDIA K80 |
| Standard\_NC24 | 24 | 224 | 1440 | 4 x NVIDIA K80 |



## Observações: Standard A0 - A4 usando a CLI e o PowerShell 


No modelo de implantação clássica, alguns nomes de tamanhos de VM são ligeiramente diferentes na CLI e no PowerShell:

* Standard\_A0 é ExtraSmall
* Standard\_A1 é pequeno
* Standard\_A2 é médio
* Standard\_A3 é grande
* Standard\_A4 é ExtraLarge


## Próximas etapas

- Saiba mais sobre a [assinatura do Azure e limites de serviços, cotas e restrições](../articles/azure-subscription-service-limits.md).
- Saiba mais [sobre as instâncias A8, A9, A10 e A11 com computação intensiva](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) para cargas de trabalho como HPC (Computação de Alto Desempenho).

<!---HONumber=AcomDC_0831_2016-->