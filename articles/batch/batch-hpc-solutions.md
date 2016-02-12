<properties
   pageTitle="Soluções de Lote e HPC na nuvem | Microsoft Azure"
   description="Apresenta opções de cenários e soluções de computação em lote e de alto desempenho (Big Compute) no Azure"
   services="batch, virtual-machines, cloud-services"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="01/21/2016"
   ms.author="danlep"/>

# Soluções de Lote e HPC na nuvem do Azure

O Azure oferece soluções em nuvem eficientes, escalonáveis para lote e HPC (computação de alto desempenho), também chamado de *Big Compute*. Saiba mais sobre cargas de trabalho de Big Compute e sobre os serviços do Azure que oferecem suporte para isso, ou vá diretamente aos [cenários de solução](#scenarios) mais adiante neste artigo. Este artigo é direcionado principalmente aos responsáveis por decisões técnicas, aos gerentes de TI e fornecedores independentes de software, mas outros desenvolvedores e profissionais de TI podem usá-lo para se familiarizar com essas soluções.

As organizações têm problemas de computação em larga escala, incluindo o design de engenharia e análise, renderização de imagem, modelagem complexa, simulações de Monte Carlo e cálculos de riscos financeiros. O Azure ajuda as organizações a solucionar problemas e a tomar decisões com os recursos, escala e cronograma necessários. Com o Azure, as organizações podem:

* Criar soluções híbridas, estendendo um cluster HPC local para descarregar cargas de trabalho de pico para a nuvem

* Executar cargas de trabalho e ferramentas de cluster HPC totalmente no Azure

* Usar um serviço do Azure gerenciável e escalável como [Lote](https://azure.microsoft.com/documentation/services/batch/) para executar cargas de trabalho de computação intensa sem a necessidade de implantar e gerenciar a infraestrutura de computação

Embora isso esteja fora do escopo deste artigo, o Azure também fornece aos desenvolvedores e parceiros um conjunto completo de recursos, opções de arquitetura e ferramentas de desenvolvimento para a criação de fluxos de trabalho de Big Compute personalizados e de larga escala. Além disso, há um ecossistema cada vez maior de parceiros para ajudar você a tornar seus fluxos de trabalho de Big Compute produtivos na nuvem do Azure.


## Aplicativos de lote e HPC

Diferentemente dos aplicativos web e muitos aplicativos de linha de negócios, aplicativos Lote e HPC têm um início e fim definido e podem ser executados de acordo com um planejamento ou sob demanda, às vezes, por horas ou mais tempo. A maioria se enquadra em duas categorias principais:*intrinsecamente paralelas*(às vezes chamadas "excessivamente paralelas", porque os problemas que elas resolvem fazem com que elas sejam executadas paralelamente em vários computadores ou processadores) e sejam *rigidamente acopladas*. Consulte a tabela a seguir para obter mais informações sobre esses tipos de aplicativos. Algumas abordagens de soluções do Azure funcionam melhor para um tipo ou outro.

>[AZURE.NOTE] Em soluções do Lote e HPC, uma instância em execução de um aplicativo normalmente é chamada de *trabalho* e cada trabalho pode ser dividido em *tarefas*. E os recursos de computação em cluster para o aplicativo são geralmente chamados de *nós de computação*.

Tipo | Características | Exemplos
------------- | ----------- | ---------------
**Intrinsecamente paralelo**<br/><br/>![Intrinsecamente paralelo][parallel] |• Computadores individuais executam lógica de aplicativo independentemente<br/><br/> • Adicionar computadores permite que o aplicativo dimensione e diminua o tempo de computação<br/><br/>• O aplicativo é formado por executáveis separados ou é dividido em um grupo de serviços chamados por um cliente (um aplicativo de arquitetura orientada a serviços, ou SOA) |• Modelagem de riscos financeiros<br/><br/>• Renderização de imagem e processamento de imagem<br/><br/>• Codificação e transcodificação de mídia<br/><br/>• Simulações de Monte Carlo<br/><br/>• Testes de software
**Acoplado rigidamente**<br/><br/>![Acoplado rigidamente][coupled] |• O aplicativo requer que os nós de computação interajam ou troquem resultados intermediários<br/><br/>• Nós de computação podem se comunicar usando a Interface de Troca de Mensagens (MPI), um protocolo de comunicação comum para computação paralela<br/><br/>• O aplicativo é sensível à latência de rede e largura de banda<br/><br/>• O desempenho do aplicativo pode ser melhorado com o uso de tecnologias de rede de alta velocidade, como InfiniBand e RDMA (acesso remoto direto à memória) |• Modelagem do reservatório de petróleo e gás<br/><br/>• Design e análise de engenharia, como dinâmica de fluidos computacional<br/><br/>• Simulações físicas como colisões de carro e reações nucleares<br/><br/>• Previsão do tempo

### Considerações sobre a execução de aplicativos do Lote e HPC na nuvem

Você pode migrar facilmente muitos aplicativos projetados para execução em clusters HPC local para Azure ou um ambiente híbrido (entre locais). No entanto, pode haver algumas restrições ou considerações, incluindo:


* **Disponibilidade de recursos de nuvem** - Dependendo do tipo de recursos de computação de nuvem usado, talvez não seja possível contar com disponibilidade contínua do computador durante a execução de um trabalho. A manipulação de estado e indicação de verificação de progresso são técnicas comuns para lidar com possíveis falhas transitórias e são mais necessários ao utilizar recursos de nuvem.


* **Acesso a dados** - Técnicas de acesso a dados geralmente disponíveis em um cluster de rede corporativa, como NFS, podem exigir configuração especial na nuvem, ou talvez seja necessário adotar práticas e padrões de acesso de dados diferentes para a nuvem.

* **Movimentação de dados** - Para aplicativos que processam grandes quantidades de dados, há a necessidade de estratégias para movimentação dos dados para o armazenamento em nuvem e para calcular os recursos, e talvez você precise de redes de alta velocidade entre os locais, como a [Rota Expressa do Azure](https://azure.microsoft.com/services/expressroute/). Também considere as limitações legais, regulatórias ou de políticas para armazenar e acessar esses dados.


* **Licenciamento** - Verifique com o fornecedor de qualquer aplicativo comercial quanto ao licenciamento ou outras restrições de execução na nuvem. Nem todos os fornecedores oferecem licenciamento pré-pago. Talvez seja necessário planejar um servidor de licenciamento na nuvem para sua solução, ou uma conexão com um servidor de licença local.


### Big Compute ou Big Data?

As linhas divisórias entre aplicativos de Big Compute e Big Data nem sempre são claras e alguns aplicativos podem ter características de ambos. Os dois envolvem a execução de cálculos em larga escala, geralmente em clusters de computadores. Mas as abordagens da solução e as ferramentas de suporte podem diferir.

•**Big Compute** tende a envolver aplicativos que dependem da capacidade e memória da CPU, como simulações de engenharia, modelagem de riscos financeiros e renderização digital. Os clusters que habilitam uma solução de Big Compute podem incluir computadores com processadores de vários núcleos especializados para executar a computação bruta e hardware de rede de alta velocidade especializado para conectar os computadores.

•**Big Data** resolve problemas de análise de dados que envolvem grandes quantidades de dados que não podem ser gerenciados por um único computador ou sistema de gerenciamento de banco de dados, como grandes volumes de logs da web ou outros dados de business intelligence. O Big Data tende a contar mais com a capacidade de disco e com o desempenho de E/S do que com a capacidade da CPU. Além disso, pode usar ferramentas especializadas, como o Apache Hadoop, para gerenciar o cluster e particionar os dados. (Para obter informações sobre o Azure HDInsight e outras soluções do Azure Hadoop, consulte [Hadoop](https://azure.microsoft.com/solutions/hadoop/).)

## Gerenciamento de computação e agendamento de trabalho

A execução de aplicativos do Lote e HPC geralmente inclui um *gerenciador de cluster* e um *agendador de trabalhos* para ajudar a gerenciar recursos de computação em cluster e alocá-los para os aplicativos que executam os trabalhos. Essas funções podem ser feitas por ferramentas separadas ou por uma ferramenta ou serviço integrado.

* **Gerenciador de cluster** - Provisiona, libera e administra recursos de computação (ou nós de computação). Um gerenciador de cluster pode automatizar a instalação de imagens do sistema operacional e de aplicativos em nós de computação, dimensionar os recursos de computação de acordo com as demandas e monitorar o desempenho dos nós.

* **Agendador de trabalhos** - Especifica os recursos (como processadores ou memória) de que um aplicativo necessita e as condições em que ele será executado. Um agendador de trabalho mantém uma fila de trabalhos e aloca recursos de acordo com uma prioridade atribuída ou outras características.

As ferramentas de cluster e agendamento de trabalhos para clusters baseados em Linux e Windows podem migrar sem problemas para o Azure. Por exemplo, o [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), a solução de cluster de computação gratuita da Microsoft para cargas de trabalho de HPC do Windows e Linux, oferece diversas opções para execução no Azure. Você também pode criar clusters do Linux para executar ferramentas de software livre, como Torque e SLURM, ou para executar ferramentas comerciais como [TIBCO DataSynapse GridServer](http://www.tibco.com/products/automation/application-development/grid-computing/gridserver) e [Univa Grid Engine](http://www.univa.com/products/grid-engine) no Azure.

Conforme mostram as seções a seguir, você também pode aproveitar os serviços do Azure para gerenciar recursos de computação e agendar trabalhos sem (ou usando também) as ferramentas de gerenciamento de cluster tradicionais.


## Cenários

Veja a seguir os três cenários comuns para execução de cargas de trabalho Big Compute no Azure, aproveitando as soluções de cluster HPC existentes, os serviços do Azure ou uma combinação dos dois. As considerações importantes para a escolha de cada cenário estão listadas, mas não é uma lista completa. Veja mais adiante no artigo mais informações sobre os serviços do Azure disponíveis que você pode usar em sua solução.

 | Cenário | Por que escolher isso?
------------- | ----------- | ---------------
**Dispare um cluster HPC no Azure**<br/><br/>[![Intermitência de cluster][burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> Saiba mais:<br/>• [Entre no Azure com o Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [Estabeleça um cluster de cálculo híbrido com o Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>|• Combine seu cluster local do [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) com recursos adicionais do Azure em uma solução híbrida.<br/><br/>• Amplie suas cargas de trabalho de Big Compute para execução em instâncias de máquina virtual PaaS (Plataforma como serviço) (apenas para Windows Server no momento).<br/><br/>• Acesse um servidor de licenças local ou repositório de dados usando uma rede virtual do Azure opcional|• Você tem um cluster HPC Pack e precisa de mais recursos <br/><br/>• Você não deseja comprar e gerenciar a infraestrutura de cluster HPC adicional<br/><br/>• Você tem períodos de pico de demanda transitórios ou projetos especiais 
**Criar um cluster HPC inteiramente no Azure**<br/><br/>[![Cluster em IaaS][iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>Saiba mais:<br/>• [Soluções de cluster HPC no Azure](./big-compute-resources.md)<br/><br/>|• Implante de forma rápida e consistente seus aplicativos e ferramentas de cluster em máquinas virtuais de IaaS (Infraestrutura como serviço) do Windows ou Linux padrão ou personalizadas.<br/><br/>• Execute várias cargas de trabalho de Big Compute usando sua solução de agendamento de trabalhos preferida.<br/><br/>• Use outros serviços do Azure, incluindo rede e armazenamento, para criar soluções completas baseadas em nuvem. |• Você não quer comprar e gerenciar outras infraestruturas de cluster HPC do Linux ou Windows<br/><br/>• Você tem períodos de pico de demanda transitórios ou projetos especiais<br/><br/>• Você precisa de um cluster adicional durante um tempo, mas não quer investir em computadores e espaço para implantá-lo<br/><br/>• Você quer descarregar seu aplicativo com uso intensivo de computação para execução como um serviço totalmente na nuvem
**Escale horizontalmente um aplicativo paralelo no Azure**<br/><br/>[![Lote do Azure][batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>Saiba mais:<br/>• [Fundamentos básicos do Lote do Azure](./batch-technical-overview.md)<br/><br/>• [Introdução à biblioteca do Lote do Azure para .NET](./batch-dotnet-get-started.md)|• Desenvolva com APIs do [Lote do Azure](https://azure.microsoft.com/documentation/services/batch/) a fim de escalar horizontalmente várias cargas de trabalho de Big Compute para execução em pools de máquinas virtuais PaaS (Plataforma como serviço) (apenas para Windows Server no momento).<br/><br/>• Use um serviço do Azure para gerenciar a implantação e o dimensionamento automático de máquinas virtuais, agendamento de trabalho, recuperação de desastres, movimentação de dados, gerenciamento de dependência e implantação de aplicativos, sem a necessidade de um agendador de trabalho ou cluster HPC separado.|• Você não quer gerenciar os recursos de computação ou um agendador de trabalho; em vez disso, você deseja se concentrar na execução de seus aplicativos<br/><br/>• Você quer descarregar seu aplicativo com uso intensivo de computação para que seja executado como um serviço em nuvem<br/><br/>• Você deseja dimensionar automaticamente seus recursos de computação para coincidir com a carga de trabalho de computação 


## Serviços do Azure para Big Compute

Veja mais sobre os serviços de computação, dados, rede e outros serviços relacionados que você pode combinar para fluxos de trabalho e soluções de Big Compute. Para obter orientações detalhadas sobre os serviços do Azure, consulte a [documentação](https://azure.microsoft.com/documentation/) dos serviços do Azure. Os [cenários](#scenarios) exibidos anteriormente neste artigo mostram apenas algumas maneiras de usar esses serviços.

>[AZURE.NOTE] O Azure apresenta regularmente novos serviços que podem ser úteis para seu cenário. Se você tiver dúvidas, entre em contato com um [parceiro do Azure](https://pinpoint.microsoft.com/pt-BR/search?keyword=azure) ou envie um email para *bigcompute@microsoft.com*.

### Serviços de computação

Os serviços de computação do Azure são a base de uma solução de Big Compute, e os serviços de computação diferentes oferecem vantagens para diferentes cenários. Em um nível básico, esses serviços oferecem modos diferentes para que os aplicativos sejam executados em instâncias de computação baseadas em máquina virtual que o Azure fornece usando a tecnologia Hyper-V do Windows Server. Essas instâncias podem executar vários sistemas operacionais e ferramentas padrão e personalizadas para Linux e Windows. O Azure fornece várias opções de [tamanhos de instância](../virtual-machines/virtual-machines-size-specs.md) com diferentes configurações de núcleos de CPU, memória, capacidade do disco e outras características. Dependendo de suas necessidades, você pode dimensionar as instâncias para milhares de núcleos e, em seguida, reduzir verticalmente, quando você precisa de menos recursos.

>[AZURE.NOTE] Aproveite as instâncias A8-A11 para melhorar o desempenho de algumas cargas de trabalho de HPC, incluindo aplicativos MPI paralelos que exigem uma rede de aplicativos de alta taxa de transferência e baixa latência. Consulte [Sobre as instâncias A8, A9, A10 e A11 com uso intensivo de computação](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md).

O Barramento de | Descrição
------------- | -----------
**[Serviços de nuvem](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Pode executar aplicativos de Big Compute em instâncias de função de trabalho, que são máquinas virtuais que executam uma versão do Windows Server e são totalmente gerenciadas pelo Azure<br/><br/>• Permite aplicativos escalonáveis e confiáveis com pouca sobrecarga administrativa, executados em um modelo de plataforma como serviço (PaaS)<br/><br/>• Pode exigir ferramentas adicionais ou desenvolvimento para integrar soluções de cluster HPC local
**[Máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Fornece infraestrutura de computação como serviço (IaaS) usando a tecnologia Microsoft Hyper-V<br/><br/>• Permite provisionar e gerenciar com flexibilidade computadores persistentes de nuvem por meio de imagens padrão do Windows Server ou Linux ou imagens e discos de dados fornecido ou por meio [Azure Marketplace](https://azure.microsoft.com/marketplace/)<br/><br/>• Executa ferramentas e aplicativos de cluster de computação local totalmente na nuvem
**[Lote](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Executa cargas de trabalho de larga escala paralelas e em lote em um serviço completamente gerenciado<br/><br/>• Fornece o plano de trabalho e o dimensionamento automático de um pool de máquinas virtuais gerenciado<br/><br/>• Permite aos desenvolvedores compilar e executar aplicativos como um serviço ou habilitar pela nuvem aplicativos existentes<br/>

### Serviços de armazenamento

Uma solução de Big Compute geralmente opera em um conjunto de dados de entrada e gera dados para seus resultados. Alguns dos serviços de armazenamento do Azure usados em soluções de Big Compute incluem:

* [Blob, tabela e armazenamento de fila](https://azure.microsoft.com/documentation/services/storage/) - gerencie grandes quantidades de dados não estruturados, dados NoSQL e mensagens para fluxo de trabalho e comunicação, respectivamente. Por exemplo, você pode usar o armazenamento de blob para conjuntos de dados técnicos grandes ou as imagens de entrada ou arquivos de mídia que seu aplicativo processa. Você pode usar filas para comunicação assíncrona em uma solução. Confira [Introdução ao Armazenamento do Microsoft Azure](../storage/storage-introduction.md).

* [Armazenamento de arquivo do Azure](https://azure.microsoft.com/services/storage/files/) - compartilha arquivos e dados em comum no Azure usando o protocolo SMB padrão, que é necessário para algumas soluções de cluster HPC.

### Serviços de dados e análise

Alguns cenários de Big Compute envolvem fluxos de dados em grande escala ou geram dados que precisam de mais análise ou processamento. Para lidar com isso, o Azure oferece uma série de serviços de dados e análise incluindo:

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) - compila fluxos de trabalho orientados a dados (pipelines) que unem, agregam e transformam dados de repositórios de dados locais, em nuvem e na Internet.

* [Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/) - fornece os principais recursos de um sistema de gerenciamento de banco de dados relacional do Microsoft SQL Server em um serviço gerenciado.

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)- implanta e provisiona clusters do Apache Hadoop baseados no Windows Server ou Linux na nuvem para gerenciar, analisar e gerar relatórios sobre Big Data.

* [Aprendizado de Máquina](https://azure.microsoft.com/documentation/services/machine-learning/) - ajuda a criar, testar, operar e gerenciar soluções analíticas de previsão em um serviço totalmente gerenciado.

### Serviços adicionais

Talvez sua solução de Big Compute precise de outros serviços do Azure para se conectar aos recursos locais ou em outros ambientes. Os exemplos incluem:

* [Rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) - cria uma seção logicamente isolada no Azure para conectar recursos do Azure ao seu data center local ou um único computador cliente usando o protocolo IPSec; permite que aplicativos Big Compute acessem dados locais, serviços do Active Directory e servidores de licenças

* [Rota Expressa](https://azure.microsoft.com/documentation/services/expressroute/) - cria uma conexão privada entre data centers da Microsoft e a infraestrutura que está local ou em um ambiente de colocalização, com maior segurança, mais confiabilidade, velocidades mais rápidas e latências menores que as conexões típicas através da Internet.

* [Barramento de Serviço](https://azure.microsoft.com/documentation/services/service-bus/) - fornece vários mecanismos para que os aplicativos se comuniquem ou troquem dados, independentemente de eles estarem localizados no Azure, em outra plataforma de nuvem ou em um data center.

## Próximas etapas

* Confira os [Recursos técnicos para o Lote e o HPC](big-compute-resources.md) e encontre orientações técnicas para criar sua solução.

* Discuta as opções do Azure com parceiros, incluindo Cycle Computing e UberCloud.

* Leia sobre as soluções de Big Compute do Azure fornecidas por [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/), e [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088).

* Para os anúncios mais recentes, consulte o [blog da equipe do Microsoft HPC e Lote](http://blogs.technet.com/b/windowshpc/) e o [blog do Azure](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

<!---HONumber=AcomDC_0204_2016-->