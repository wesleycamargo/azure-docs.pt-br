<properties
 pageTitle="Tamanhos dos serviços de nuvem"
 description="Lista os tamanhos diferentes da máquina virtual para funções Web e de trabalho do serviço de nuvem do Azure."
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="05/25/2016"
 ms.author="adegeo"/>

# Tamanhos dos serviços de nuvem

Este tópico descreve os tamanhos disponíveis e as opções para instâncias de função de Serviço de Nuvem (funções web e funções de trabalho). Ele também fornece considerações de implantação a serem observadas ao planejar o uso desses recursos.

O Serviços de Nuvem é um dos vários tipos de recursos de computação oferecidos pelo Azure. Clique [aqui](cloud-services-choose-me.md) para obter mais informações sobre Serviços de Nuvem.

> [AZURE.NOTE]Para ver os limites relacionados do Azure, confira [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md).

## Tamanhos de instâncias de função web e de trabalho

As considerações a seguir podem ajudá-lo a escolher um tamanho:

* As instâncias das VMs da série D são projetadas para executar aplicativos que exigem maior capacidade de computação e de desempenho de disco temporário. As VMs da série D fornecem processadores mais rápidos, uma maior taxa de memória por núcleo e uma unidade de estado sólido (SSD) para o disco temporário. Para obter detalhes, confira o anúncio no blog do Azure, [Novos tamanhos de máquina virtual da série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).  

* A série Dv2, uma continuação da série D original, apresenta uma CPU mais potente. A CPU da série Dv2 é aproximadamente 35% mais rápida do que a CPU da série D. Ela se baseia na última geração do processador Intel Xeon® E5-2673 v3 (Haswell) de 2.4 GHz e, com a Intel Turbo Boost Technology 2.0, pode chegar a até 3.1 GHz. A série Dv2 tem as mesmas configurações de memória e disco que a série D.

* Funções web e funções de trabalho exigem mais espaço em disco temporário que as máquinas virtuais do Azure devido aos requisitos de sistema. Os arquivos do sistema reservam 4 GB de espaço para o arquivo de paginação do Windows e 2 GB de espaço para o arquivo de despejo de memória do Windows.

* O disco do sistema operacional contém o SO Windows convidado e inclui a pasta Arquivos de Programas (incluindo as instalações feitas por meio de tarefas de inicialização, a menos que você especifique outro disco), as alterações no Registro, a pasta System32 e o .NET Framework.

* O **disco de armazenamento temporário** contém logs e arquivos de configuração do Azure, Diagnóstico do Azure (que inclui os logs do IIS) e quaisquer recursos de armazenamento locais que você definir.

* O **disco de aplicativo** é onde o .cspkg é extraído e inclui seu site, binários, processo de host de função, tarefas de inicialização, web.config e assim por diante.

* Os tamanhos de máquina virtual A8/A10 e A9/A11 têm as mesmas capacidades. As instâncias de máquina virtual A8 e A9 incluem um adaptador de rede adicional que é conectado a uma rede de Acesso Remoto Direto à Memória (RDMA) para uma comunicação rápida entre máquinas virtuais. As instâncias A8 e A9 destinam-se a aplicativos de computação de alto desempenho que exigem comunicação constante e de baixa latência entre os nós durante a execução como, por exemplo, os aplicativos que usam MPI (Message Passing Interface). As instâncias de máquina virtual A10 e A11 não incluem o adaptador de rede adicional. As instâncias A10 e A11 destinam-se a aplicativos de computação de alto desempenho que não exigem comunicação constante e de baixa latência entre os nós, também conhecidos como aplicativos paramétricos ou totalmente paralelos.

    >[AZURE.NOTE] Se você estiver considerando tamanhos de A8 até A11, leia [estas](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) informações.

>[AZURE.NOTE] Todos os tamanhos de computador fornecem um **disco de aplicativo** que armazena todos os arquivos do pacote de serviço de nuvem; ele tem um tamanho de cerca de 1,5 GB.

Não deixe de examinar o [preço](https://azure.microsoft.com/pricing/details/cloud-services/) de cada tamanho do Serviço de Nuvem.

## Propósito geral

Para sites, banco de dados pequenos e médios e outros aplicativos cotidianos.

>[AZURE.NOTE] A capacidade de armazenamento é representada usando-se 1024^3 bytes como a unidade de medida para GB. Isso às vezes é chamado de gibibyte ou definição de base 2. Ao comparar tamanhos que usam diferentes sistemas de base, lembre-se de que os tamanhos de base 2 podem parecer menores do que os de base 10, mas para qualquer tamanho específico (por exemplo, 1 GB), um sistema de base 2 fornece mais capacidade do que um sistema de base 10, porque 1024^3 é maior que 1000^3.

| Tamanho (id) | Núcleos | Ram | Largura de banda da rede | Tamanho total do disco |
| --------------- | :-------: | ------: | :-----------: | -------: |
| ExtraSmall | 1 | 0,75 GB | Baixo | 19 GB |
| Pequena | 1 | 1,75 GB | Moderado | 224 GB |
| Média | 2 | 3,5 GB | Moderado | 489 GB |
| Grande | 4 | 7 GB | Alto | 999 GB |
| ExtraLarge | 8 | 14 GB | Alto | 2\.039 GB |

>[AZURE.NOTE] **ExtraSmall** a **ExtraLarge** também podem ter o nome **A0-A4**, respectivamente.

## Uso intensivo de memória

Para banco de dados maiores, farms do servidor SharePoint e aplicativos de alto rendimento.

| Tamanho (id) | Núcleos | Ram | Largura de banda da rede | Tamanho total do disco |
| --------------- | :-------: | ------: | :-----------: | ------:  |
| A5 | 2 | 14 GB | Moderado | 489 GB |
| A6 | 4 | 28 GB | Alto | 999 GB |
| A7 | 8 | 56 GB | Alto | 2\.039 GB |

## Rede otimizado com suporte a InfiniBand

Disponível em data centers seletos. As máquinas virtuais A8 e A9 têm [processadores Intel® Xeon® E5](http://www.intel.com/content/www/us/en/processors/xeon/xeon-processor-e5-family.html). Adiciona uma rede **InfiniBand** de 32 Gbits/s com a tecnologia RDMA (acesso remoto direto à memória). Ideal para os aplicativos de MPI (interface de passagem de mensagem), clusters de alto desempenho, modelagem e simulações, codificação de vídeo e outros cenários com uso intensivo de computação ou rede.

| Tamanho (id) | Núcleos | Ram | Largura de banda da rede | Tamanho total do disco |
| --------------- | :-------: | ------: | :-----------: | ------: |
| A8 | 8 | 56 GB | Alto | 382 GB |
| A9 | 16 | 112 GB | Muito alto | 382 GB |

## Computação intensiva

Disponível em data centers seletos. As máquinas virtuais A10 e A11 têm [processadores Intel® Xeon® E5](http://www.intel.com/content/www/us/en/processors/xeon/xeon-processor-e5-family.html). Para clusters de alto desempenho, modelagem e simulações, codificação de vídeo e outros cenários com uso intensivo de computação ou rede. Similar à configuração de instância A8 e A9 sem a rede InfiniBand e a tecnologia RDMA.

| Tamanho (id) | Núcleos | Ram | Largura de banda da rede | Tamanho total do disco |
| --------------- | :-------: | ------: | :-----------: | ------: |
| A10 | 8 | 56 GB | Alto | 382 GB |
| A11 | 16 | 112 GB | Muito alto | 382 GB |

## Série D: computação otimizada

As máquinas virtuais D-series contêm as SSDs (unidades de estado sólido) e processadores mais rápidos que o A-series (60% mais rápidos) e também estão disponíveis para a web ou funções de trabalho nos Serviços de Nuvem do Azure. Esta série é ideal para aplicativos que exigem CPUs mais rápidas, melhor desempenho de disco local ou memória maior.

## Propósito geral (D)

Para sites, banco de dados pequenos e médios e outros aplicativos cotidianos.

| Tamanho (id) | Núcleos | Ram | Largura de banda da rede | Tamanho total do disco |
| --------------- | :-------: | ------: | :-----------: | ------: |
| Standard\_D1 | 1 | 3,5 GB | Moderado | 50 GB |
| Standard\_D2 | 2 | 7 GB | Alto | 100 GB |
| Standard\_D3 | 4 | 14 GB | Alto | 200 GB |
| Standard\_D4 | 8 | 28 GB | Alto | 400 GB |

## Uso intensivo de memória (D)

Para banco de dados maiores, farms do servidor SharePoint e aplicativos de alto rendimento.

| Tamanho (id) | Núcleos | Ram | Largura de banda da rede | Tamanho total do disco |
| --------------- | :-------: | ------: | :-----------: | ------: |
| Standard\_D11 | 2 | 14 GB | Alto | 100 GB |
| Standard\_D12 | 4 | 28 GB | Alto | 200 GB |
| Standard\_D13 | 8 | 56 GB | Alto | 400 GB |
| Standard\_D14 | 16 | 112 GB | Muito alto | 800 GB |

## Série Dv2: computação otimizada

As instâncias da série Dv2 são a próxima geração de instâncias da série D, que podem ser usadas como Máquinas Virtuais ou Serviços de Nuvem. As instâncias da série Dv2 terão CPUs mais potentes, que estão, em média, 35% mais rápidas que as instâncias da série D, e terão as mesmas configurações de memória e disco que a série D. As instâncias da série Dv2 baseiam-se no processador de última geração Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz e, com a Intel Turbo Boost Technology 2.0, é possível obter 3,1 GHz. A série Dv2 e a série D são perfeitas para aplicativos que demandam CPUs mais rápidas, desempenho de disco local aprimorado ou mais memória, e elas oferecem uma combinação poderosa para diversos aplicativos em nível empresarial.

## Propósito geral (Dv2)

Para sites, banco de dados pequenos e médios e outros aplicativos cotidianos.

| Tamanho (id) | Núcleos | Ram | Largura de banda da rede | Tamanho total do disco |
| --------------- | :-------: | ------: | :-----------: | ------: |
| Standard\_D1\_v2 | 1 | 3,5 GB | Moderado | 50 GB |
| Standard\_D2\_v2 | 2 | 7 GB | Alto | 100 GB |
| Standard\_D3\_v2 | 4 | 14 GB | Alto | 200 GB |
| Standard\_D4\_v2 | 8 | 28 GB | Alto | 400 GB |
| Standard\_D5\_v2 | 16 | 56 GB | Muito alto | 800 GB |

## Uso intensivo de memória (Dv2)

Para banco de dados maiores, farms de servidores SharePoint e aplicativos de alto rendimento

| Tamanho (id) | Núcleos | Ram | Largura de banda da rede | Tamanho total do disco |
| --------------- | :-------: | ------: | :-----------: | -------: |
| Standard\_D11\_v2 | 2 | 14 GB | Alto | 100 GB |
| Standard\_D12\_v2 | 4 | 28 GB | Alto | 200 GB |
| Standard\_D13\_v2 | 8 | 56 GB | Alto | 400 GB |
| Standard\_D14\_v2 | 16 | 112 GB | Muito alto | 800 GB |

## Configurar tamanhos para os Serviços de Nuvem

Você pode especificar o tamanho da Máquina Virtual de uma instância de função como parte do modelo de serviço descrito pelo [arquivo de definição de serviço](cloud-services-model-and-package.md#csdef). O tamanho da função determina o número de núcleos de CPU, a capacidade de memória e o tamanho do sistema de arquivos local alocados para uma instância em execução. Escolha o tamanho da função com base em requisitos de recurso do aplicativo.

Veja um exemplo de definição do tamanho da função como [Standard\_D2](#general-purpose-d) para uma instância de Função Web:

```xml
<WebRole name="WebRole1" vmsize="<mark>Standard_D2</mark>">
...
</WebRole>
```

<!---HONumber=AcomDC_0615_2016-->