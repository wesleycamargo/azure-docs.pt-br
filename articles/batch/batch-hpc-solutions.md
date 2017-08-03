---
title: "Soluções de Lote e HPC na nuvem – Azure | Microsoft Docs"
description: "Saiba mais sobre opções de cenários e de soluções de computação em lote e de alto desempenho (HPC e Big Compute) no Azure"
services: batch, virtual-machines, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: aab5401d-2baf-4cf2-bf20-ad224de33888
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 32569d7e75a7a4ddee28041c0487ff158c20fd78
ms.contentlocale: pt-br
ms.lasthandoff: 07/12/2017

---
# <a name="batch-and-hpc-solutions-for-large-scale-computing-workloads"></a>Soluções HPC e Lote para cargas de trabalho de computação em larga escala

O Azure oferece soluções em nuvem eficientes, escalonáveis para lote e HPC (computação de alto desempenho), também chamado de *Big Compute*. Saiba mais sobre cargas de trabalho de Big Compute e sobre os serviços do Azure que oferecem suporte para isso, ou vá diretamente aos [cenários de solução](#scenarios) mais adiante neste artigo. Este artigo é direcionado principalmente aos responsáveis por decisões técnicas, aos gerentes de TI e fornecedores independentes de software, mas outros desenvolvedores e profissionais de TI podem usá-lo para se familiarizar com essas soluções.

As organizações têm problemas de computação em larga escala: design de engenharia e análise, renderização de imagem, modelagem complexa, simulações de Monte Carlo, cálculos de riscos financeiros, entre outros. O Azure ajuda as organizações a solucionar problemas com os recursos, escala e cronograma necessários. Com o Azure, as organizações podem:

* Criar soluções híbridas, estendendo um cluster HPC local para descarregar cargas de trabalho de pico para a nuvem
* Executar cargas de trabalho e ferramentas de cluster HPC totalmente no Azure
* Usar serviços do Azure gerenciáveis e escalonáveis como o [Lote](https://azure.microsoft.com/documentation/services/batch/) para executar cargas de trabalho de computação intensa sem a necessidade de implantar e gerenciar a infraestrutura de computação

Embora isso esteja fora do escopo deste artigo, o Azure também fornece aos desenvolvedores e parceiros um conjunto completo de recursos, opções de arquitetura e ferramentas de desenvolvimento para a criação de fluxos de trabalho de Big Compute personalizados e de larga escala. Além disso, há um ecossistema cada vez maior de parceiros para ajudar você a tornar seus fluxos de trabalho de Big Compute produtivos na nuvem do Azure.

## <a name="batch-and-hpc-applications"></a>Aplicativos de lote e HPC
Diferentemente dos aplicativos web e muitos aplicativos de linha de negócios, aplicativos Lote e HPC têm um início e fim definido e podem ser executados de acordo com um planejamento ou sob demanda, às vezes, por horas ou mais tempo. A maioria se enquadra em duas categorias principais: *intrinsecamente paralelas* (às vezes chamadas "excessivamente paralelas", porque os problemas que elas resolvem fazem com que elas sejam executadas paralelamente em vários computadores ou processadores) e sejam *rigidamente acopladas*. Consulte a tabela a seguir para obter mais informações sobre esses tipos de aplicativos. Algumas abordagens de soluções do Azure funcionam melhor para um tipo ou outro.

> [!NOTE]
> Em soluções do Lote e HPC, uma instância em execução de um aplicativo normalmente é chamada de *trabalho* e cada trabalho pode ser dividido em *tarefas*. E os recursos de computação em cluster para o aplicativo são geralmente chamados de *nós de computação*.
> 
> 

| Tipo | Características | Exemplos |
| --- | --- | --- |
| **Intrinsecamente paralelo**<br/><br/>![Intrinsecamente paralelo][parallel] |• Computadores individuais executam lógica de aplicativo independentemente<br/><br/> • Adicionar computadores permite que o aplicativo dimensione e diminua o tempo de computação<br/><br/>• O aplicativo é formado por executáveis separados ou é dividido em um grupo de serviços chamados por um cliente (um aplicativo de arquitetura orientada a serviços, ou SOA) |• Modelagem de riscos financeiros<br/><br/>• Renderização e processamento de imagens<br/><br/>• Codificação e transcodificação de mídia<br/><br/>• Simulações de Monte Carlo<br/><br/>• Testes de software |
| **Tightly coupled**<br/><br/>![Tightly coupled][coupled] |• O aplicativo requer que os nós de computação interajam ou troquem resultados intermediários<br/><br/>• Nós de computação podem se comunicar usando a MPI (Message Passing Interface), um protocolo de comunicação comum para computação paralela<br/><br/>• O aplicativo é sensível à largura de banda e latência de rede<br/><br/>• O desempenho do aplicativo pode ser melhorado com o uso de tecnologias de rede de alta velocidade, como InfiniBand e RDMA (acesso remoto direto à memória) |• Modelagem do reservatório de petróleo e gás<br/><br/>• Design de engenharia e análise, como dinâmica de fluidos computacional<br/><br/>• Simulações físicas como colisões de carro e reações nucleares<br/><br/>• Previsão do tempo |

### <a name="considerations-for-running-batch-and-hpc-applications-in-the-cloud"></a>Considerações sobre a execução de aplicativos do Lote e HPC na nuvem
Você pode migrar facilmente muitos aplicativos projetados para execução em clusters HPC local para Azure ou um ambiente híbrido (entre locais). No entanto, pode haver algumas restrições ou considerações, incluindo:

* **Disponibilidade de recursos de nuvem** - Dependendo do tipo de recursos de computação de nuvem usado, talvez não seja possível contar com disponibilidade contínua do computador durante a execução de um trabalho. A manipulação de estado e indicação de verificação de progresso são técnicas comuns para lidar com possíveis falhas transitórias e são mais necessários ao utilizar recursos de nuvem.
* **Acesso a dados** - as técnicas de acesso a dados geralmente disponíveis em clusters de empresa, como NFS, podem exigir configuração especial na nuvem. Ou, talvez seja necessário adotar práticas e padrões de acesso de dados diferentes para a nuvem.
* **Movimentação de dados** - para os aplicativos que processam grandes quantidades de dados, são necessárias estratégias para mover os dados para armazenamento em nuvem e para recursos de computação. Talvez você precise de redes entre locais de alta velocidade, como [ExpressRoute do Azure](https://azure.microsoft.com/services/expressroute/). Também considere as limitações legais, regulatórias ou de políticas para armazenar e acessar esses dados.
* **Licenciamento** - Verifique com o fornecedor de qualquer aplicativo comercial quanto ao licenciamento ou outras restrições de execução na nuvem. Nem todos os fornecedores oferecem licenciamento pré-pago. Talvez seja necessário planejar um servidor de licenciamento na nuvem para sua solução, ou uma conexão com um servidor de licença local.

### <a name="big-compute-or-big-data"></a>Big Compute ou Big Data?
As linhas divisórias entre aplicativos de Big Compute e Big Data nem sempre são claras e alguns aplicativos podem ter características de ambos. Os dois envolvem a execução de cálculos em larga escala, geralmente em clusters de computadores. Mas as abordagens da solução e as ferramentas de suporte podem diferir.

• **Big Compute** tende a envolver aplicativos que dependem da capacidade e memória da CPU, como simulações de engenharia, modelagem de riscos financeiros e renderização digital. A infraestrutura para uma solução de Big Compute podem incluir computadores com processadores de vários núcleos especializados para executar a computação bruta e hardware de rede de alta velocidade especializado para conectar os computadores.

• **Big Data** resolve problemas de análise de dados que envolvem grandes quantidades de dados que não podem ser gerenciados por um único computador ou sistema de gerenciamento de banco de dados. Os exemplos incluem grandes volumes de logs da Web ou outros dados de business intelligence. Big Data tende a contar mais com a capacidade de disco e de desempenho de E/S do que com a capacidade da CPU. Há também ferramentas especializadas de Big Data, como o Apache Hadoop, para gerenciar o cluster e particionar os dados. (Para obter informações sobre o Azure HDInsight e outras soluções do Azure Hadoop, consulte [Hadoop](https://azure.microsoft.com/solutions/hadoop/).)

## <a name="compute-management-and-job-scheduling"></a>Gerenciamento de computação e agendamento de trabalho
A execução de aplicativos do Lote e HPC com frequência inclui um *gerenciador de cluster* e um *agendador de trabalhos* para ajudar a gerenciar recursos de computação em cluster e alocá-los para os aplicativos que executam os trabalhos. Essas funções podem ser feitas por ferramentas separadas ou por uma ferramenta ou serviço integrado.

* **Gerenciador de cluster** - Provisiona, libera e administra recursos de computação (ou nós de computação). Um gerenciador de cluster pode automatizar a instalação de imagens do sistema operacional e de aplicativos em nós de computação, dimensionar os recursos de computação de acordo com as demandas e monitorar o desempenho dos nós.
* **Agendador de trabalhos** - especifica os recursos (como processadores ou memória) de que um aplicativo necessita e as condições em que ele é executado. Um agendador de trabalho mantém uma fila de trabalhos e aloca recursos de acordo com uma prioridade atribuída ou outras características.

As ferramentas de cluster e agendamento de trabalhos para clusters baseados em Linux e Windows podem migrar sem problemas para o Azure. Por exemplo, o [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), a solução de cluster de computação gratuita da Microsoft para cargas de trabalho de HPC do Windows e Linux, oferece diversas opções para execução no Azure. Você também pode criar clusters do Linux para executar ferramentas de software livre, como Torque e SLURM. Você também pode usar soluções de grade comercial no Azure, como [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/), [IBM Spectrum Symphony e Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/), além de [Univa Grid Engine](http://www.univa.com/products/grid-engine).

Conforme mostram as seções a seguir, você também pode aproveitar os serviços do Azure para gerenciar recursos de computação e agendar trabalhos sem (ou usando também) as ferramentas de gerenciamento de cluster tradicionais.

## <a name="scenarios"></a>Cenários
Veja a seguir os três cenários comuns para execução de cargas de trabalho Big Compute no Azure, utilizando as soluções de cluster HPC existentes, os serviços do Azure ou uma combinação dos dois. As considerações importantes para a escolha de cada cenário estão listadas, mas não é uma lista completa. Veja mais adiante no artigo mais informações sobre os serviços do Azure disponíveis que você pode usar em sua solução.

| Cenário | Por que escolher isso? |
| --- | --- | --- |
| **Fazer disparo contínuo de um cluster HPC para o Azure**<br/><br/>[![Cluster burst][burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> Saiba mais:<br/>• [Disparo contínuo para instâncias de trabalho do Azure com o HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [Configurar um cluster de cálculo híbrido com o HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>• [Fazer disparo contínuo para o lote do Azure com o HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)<br/><br/> |• Combinar o [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) ou outro cluster local com recursos adicionais do Azure em uma solução híbrida.<br/><br/>• Ampliar suas cargas de trabalho de computação intensa para execução em instâncias de máquina virtual (atualmente apenas Windows Server) do tipo PaaS (plataforma como serviço).<br/><br/>• Acesse um servidor de licenças local ou repositório de dados usando uma rede virtual do Azure opcional |
| **Criar um cluster de HPC inteiramente no Azure**<br/><br/>[![Cluster in IaaS][iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>Saiba mais:<br/>• [Soluções de cluster HPC no Azure](big-compute-resources.md)<br/><br/> |• Implante de forma rápida e consistente seus aplicativos e ferramentas de cluster em máquinas virtuais de IaaS (Infraestrutura como serviço) do Windows ou Linux padrão ou personalizadas.<br/><br/>• Execute várias cargas de trabalho de computação intensa usando soluções de agendamento de trabalho de sua escolha.<br/><br/>• Use outros serviços do Azure, incluindo rede e armazenamento, para criar soluções completas baseadas em nuvem. |
| **Escale horizontalmente um aplicativo paralelo para o Azure**<br/><br/>[![Azure Batch][batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>Saiba mais:<br/>• [Noções básicas de Lote do Azure](batch-technical-overview.md)<br/><br/>• [Introdução à biblioteca do Lote do Azure para .NET](batch-dotnet-get-started.md) |• Desenvolva com o [Lote do Azure](https://azure.microsoft.com/documentation/services/batch/) escalar horizontalmente várias cargas de trabalho de computação intensa para execução em pools de máquinas virtuais dp Windows ou Linux.<br/><br/>• Use um serviço de plataforma do Azure para gerenciar a implantação e o dimensionamento automático de máquinas virtuais, agendamento de trabalho, recuperação de desastres, movimentação de dados, gerenciamento de dependência e implantação de aplicativos. |

## <a name="azure-services-for-big-compute"></a>Serviços do Azure para Big Compute
Veja mais sobre os serviços de computação, dados, rede e outros serviços relacionados que você pode combinar para fluxos de trabalho e soluções de Big Compute. Para obter orientações detalhadas sobre os serviços do Azure, confira a [documentação](https://azure.microsoft.com/documentation/)dos serviços do Azure. Os [cenários](#scenarios) exibidos anteriormente neste artigo mostram apenas algumas maneiras de usar esses serviços.

> [!NOTE]
> O Azure apresenta regularmente novos serviços que podem ser úteis para seu cenário. Se você tiver dúvidas, entre em contato com um [parceiro do Azure](https://pinpoint.microsoft.com/en-US/search?keyword=azure) ou envie um email para *bigcompute@microsoft.com*.
> 
> 

### <a name="compute-services"></a>Serviços de computação
Os serviços de computação do Azure são a base de uma solução de Big Compute, e os serviços de computação diferentes oferecem vantagens para diferentes cenários. Em um nível básico, esses serviços oferecem modos diferentes para que os aplicativos sejam executados em instâncias de computação baseadas em máquina virtual que o Azure fornece usando a tecnologia Hyper-V do Windows Server. Essas instâncias podem executar sistemas operacionais e ferramentas padrão e personalizadas para Linux e Windows. O Azure fornece várias opções de [tamanhos de instância](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) com diferentes configurações de núcleos de CPU, de memória, de capacidade do disco e outras características. Dependendo de suas necessidades, você pode dimensionar as instâncias para milhares de núcleos e, em seguida, reduzir verticalmente, quando você precisa de menos recursos.

> [!NOTE]
> Tire proveito das [instâncias de computação intensiva do Azure como a série H](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para melhorar o desempenho e a escalabilidade das cargas de trabalho HPC. Essas instâncias também dão suporte a aplicativos MPI paralelos que exigem uma rede do aplicativo de baixa latência e alta taxa de transferência. Também estão disponíveis VMs [série N](../virtual-machines/windows/sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) com GPUs NVIDIA, para expandir o intervalo de cenários de computação e de visualização no Azure.  
> 
> 

| O Barramento de | Descrição |
| --- | --- |
| **[Máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Fornecem infraestrutura de computação como serviço (IaaS) usando a tecnologia Microsoft Hyper-V<br/><br/>• Permite provisionar e gerenciar de maneira flexível computadores de nuvem persistentes de imagens padrão do Windows Server ou Linux do [Azure Marketplace](https://azure.microsoft.com/marketplace/), ou então imagens e discos de dados fornecidos por você<br/><br/>• Podem ser implantados e gerenciados como [Conjuntos de Dimensionamento de VMs](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/) para a criação de serviços em larga escala de máquinas virtuais idênticas, com o dimensionamento automático para aumentar ou diminuir a capacidade automaticamente<br/><br/>• Execute ferramentas e aplicativos de cluster de cálculo locais totalmente na nuvem<br/><br/> |
| **[Serviços de nuvem](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Pode executar aplicativos de Big Compute em instâncias de função de trabalho, que são máquinas virtuais que executam uma versão do Windows Server e são totalmente gerenciadas pelo Azure<br/><br/>• Permite aplicativos escalonáveis e confiáveis com pouca sobrecarga administrativa, executando em um modelo PaaS (plataforma como serviço)<br/><br/>• Pode exigir ferramentas adicionais ou desenvolvimento para integrar soluções de cluster HPC local |
| **[Lote](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Executa cargas de trabalho de larga escala paralelas e em lote em um serviço completamente gerenciado<br/><br/>• Fornece o plano de trabalho e o dimensionamento automático de um pool de máquinas virtuais gerenciado<br/><br/>• Permite aos desenvolvedores compilar e executar aplicativos como um serviço ou habilitar aplicativos existentes pela nuvem<br/> |

### <a name="storage-services"></a>Serviços de armazenamento
Uma solução de Big Compute geralmente opera em um conjunto de dados de entrada e gera dados para seus resultados. Alguns dos serviços de armazenamento do Azure usados em soluções de Big Compute incluem:

* [Blob, tabela e armazenamento de fila](https://azure.microsoft.com/documentation/services/storage/) - gerencie grandes quantidades de dados não estruturados, dados NoSQL e mensagens para fluxo de trabalho e comunicação, respectivamente. Por exemplo, você pode usar o armazenamento de blobs para conjuntos de dados técnicos grandes ou para as imagens de entrada ou arquivos de mídia que seu aplicativo processa. Você pode usar filas para comunicação assíncrona em uma solução. Confira [Introdução ao Armazenamento do Microsoft Azure](../storage/storage-introduction.md).
* [Armazenamento de arquivos do Azure](https://azure.microsoft.com/services/storage/files/) - compartilha arquivos e dados em comum no Azure usando o protocolo SMB padrão, que é necessário para algumas soluções de cluster HPC.
* [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) - fornece um sistema de Arquivos Distribuído do Apache Hadoop para a nuvem, útil para análise de lote, interativa e em tempo real.

### <a name="data-and-analysis-services"></a>Serviços de dados e análise
Alguns cenários de Big Compute envolvem fluxos de dados em grande escala ou geram dados que precisam de mais análise ou processamento. O Azure oferece vários serviços de dados e de análise, incluindo:

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) - cria fluxos de trabalho orientados a dados (pipelines) que unem, agregam e transformam dados de repositórios de dados locais, em nuvem e na Internet.
* [Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/) - fornece os principais recursos de um sistema de gerenciamento de banco de dados relacional do Microsoft SQL Server em um serviço gerenciado.
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - implanta e provisiona clusters do Apache Hadoop baseados no Windows Server ou Linux na nuvem para gerenciar, analisar e gerar relatórios sobre Big Data.
* [Aprendizado de Máquina](https://azure.microsoft.com/documentation/services/machine-learning/) - ajuda a criar, testar, operar e gerenciar soluções analíticas de previsão em um serviço totalmente gerenciado.

### <a name="additional-services"></a>Serviços adicionais
Talvez sua solução de Big Compute precise de outros serviços do Azure para se conectar aos recursos locais ou em outros ambientes. Os exemplos incluem:

* [Rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) - cria uma seção logicamente isolada no Azure para conectar recursos do Azure entre si ou ao seu data center local. Com uma rede virtual entre locais, os aplicativos de Big Compute podem acessar dados locais, serviços do Active Directory e servidores de licença
* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) - cria conexões privadas entre os datacenters da Microsoft e a infraestrutura no local ou em um ambiente de colocalização. O ExpressRoute oferece maior segurança, mais confiabilidade, velocidades maiores e latências menores do que conexões típicas pela Internet.
* [Barramento de Serviço](https://azure.microsoft.com/documentation/services/service-bus/) - fornece vários mecanismos para que os aplicativos se comuniquem ou troquem dados, independentemente de eles estarem localizados no Azure, em outra plataforma de nuvem ou em um data center.

## <a name="next-steps"></a>Próximas etapas
* Confira [Recursos técnicos para o Lote e HPC](big-compute-resources.md) e encontre diretrizes técnicas para criar sua solução.
* Discuta as opções do Azure com parceiros, incluindo Cycle Computing, Rescale e UberCloud.
* Leia sobre as soluções de Big Compute do Azure fornecidas por [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/), [ANSYS](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/) e [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088).
* Para os anúncios mais recentes, consulte o [blog da equipe do Microsoft HPC e Lote](http://blogs.technet.com/b/windowshpc/) e o [blog do Azure](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

