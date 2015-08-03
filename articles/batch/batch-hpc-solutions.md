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
   ms.date="07/01/2015"
   ms.author="danlep"/>

# Soluções do Lote e de HPC

Este artigo apresenta soluções do Azure para Lote e computação de alto desempenho (HPC) - também chamadas de *Big Compute*. Embora o artigo seja direcionado principalmente para os responsáveis por decisões técnicas, os gerentes de TI e fornecedores independentes de software, outros desenvolvedores e profissionais de TI podem usá-lo para se familiarizar com essas soluções.

As organizações têm problemas de computação em larga escala, incluindo o design de engenharia e análise, renderização de imagem, modelagem complexa, simulações de Monte Carlo e cálculos de riscos financeiros. Para ajudar as organizações a resolver esses problemas e tomar decisões com os recursos, a escala e a agenda de que precisam, o Azure oferece recursos e serviços de computação escaláveis, sob demanda. Com o Azure, as organizações podem:

* Criar soluções híbridas, estendendo um cluster HPC local para descarregar cargas de trabalho de pico para a nuvem

* Executar cargas de trabalho de cluster HPC inteiramente no Azure usando conjuntos de ferramentas e os aplicativos existentes

* Usar um serviço do Azure gerenciável e escalável como [Lote](http://azure.microsoft.com/documentation/services/batch/) para executar cargas de trabalho de computação intensa sem a necessidade de implantar e gerenciar a infraestrutura de computação

O Azure também oferece ferramentas e serviços de desenvolvimento para organizações e fornecedores de software para criar soluções personalizadas, de ponta a ponta, de Big Compute.


## Informações: aplicativos de HPC e Lote

Diferentemente dos aplicativos web e muitos aplicativos de linha de negócios, aplicativos Lote e HPC têm um início e fim definido e podem ser executados de acordo com um planejamento ou sob demanda, às vezes, por horas ou mais tempo. A maioria se enquadra em duas categorias principais:*intrinsecamente paralelas*(às vezes chamadas "excessivamente paralelas", porque os problemas que elas resolvem fazem com que elas sejam executadas paralelamente em vários computadores ou processadores) e sejam *rigidamente acopladas*. Consulte a tabela a seguir para obter mais informações sobre esses tipos de aplicativos. Algumas abordagens de soluções do Azure funcionam melhor para um tipo ou outro.

>[AZURE.NOTE]Em soluções do Lote e HPC, uma instância em execução de um aplicativo normalmente é chamada de *trabalho* e cada trabalho pode ser dividido em *tarefas*. E os recursos de computação em cluster para o aplicativo são geralmente chamados de *nós de computação*.

Tipo | Características | Exemplos
------------- | ----------- | ---------------
**Intrinsecamente paralelo**<br/><br/>![Intrinsecamente paralelo][parallel] |• Computadores individuais executam lógica de aplicativo independentemente<br/><br/> • Adicionar computadores permite que o aplicativo dimensione e diminua o tempo de computação<br/><br/>• O aplicativo é formado por executáveis separados ou é dividido em um grupo de serviços chamados por um cliente (um aplicativo de arquitetura orientada a serviços, ou SOA) |• Modelagem de riscos financeiros<br/><br/>• Renderização de imagem e processamento de imagem<br/><br/>• Codificação e transcodificação de mídia<br/><br/>• Simulações de Monte Carlo<br/><br/>• Testes de software
**Acoplado rigidamente**<br/><br/>![Acoplado rigidamente][coupled] |• O aplicativo requer que os nós de computação interajam ou troquem resultados intermediários<br/><br/>• Nós de computação podem se comunicar usando a Interface de Troca de Mensagens (MPI), um protocolo de comunicação comum para computação paralela<br/><br/>• O aplicativo é sensível à latência de rede e largura de banda<br/><br/>• O desempenho do aplicativo pode ser melhorado com o uso da infraestrutura de computação que dá suporte a tecnologias de rede de alta velocidade, como InfiniBand e acesso remoto direto à memória (RDMA) |• Modelagem do reservatório de petróleo e gás<br/><br/>• Design e análise de engenharia, como dinâmica de fluidos computacional<br/><br/>• Simulações físicas como colisões de carro e reações nucleares<br/><br/>• Previsão do tempo

### Considerações sobre a execução de aplicativos do Lote e HPC na nuvem

Você pode migrar facilmente muitos aplicativos projetados para execução em clusters HPC local para Azure ou um ambiente híbrido (entre locais). No entanto, pode haver algumas restrições ou considerações, incluindo:


* **Disponibilidade ininterrupta dos recursos de nuvem** - Dependendo do tipo de recursos de computação de nuvem selecionado para a solução, você não poderá contar com disponibilidade contínua do computador para a duração da execução de um trabalho. A manipulação de estado e indicação de verificação de progresso são técnicas comuns para lidar com possíveis falhas transitórias e são mais necessários ao utilizar recursos de nuvem.


* **Acesso a dados** - Técnicas de acesso a dados geralmente disponíveis em um cluster de rede corporativa, como NFS, podem exigir configuração especial na nuvem, ou talvez seja necessário adotar práticas e padrões de acesso de dados diferentes para a nuvem.

* **Movimentação de dados** - Para aplicativos que processam grandes quantidades de dados, são necessárias estratégias para mover os dados para armazenamento em nuvem e recursos de computação, e talvez você precise considerar redes de alta velocidade entre os locais, como a [Rota Expressa do Azure](http://azure.microsoft.com/services/expressroute/). Também considere as limitações legais, regulatórias ou de políticas para armazenar e acessar esses dados.


* **Licenciamento** - Verifique com o fornecedor de qualquer aplicativo comercial quanto ao licenciamento ou outras restrições de execução na nuvem. Nem todos os fornecedores oferecem licenciamento pré-pago. Talvez seja necessário planejar um servidor de licenciamento na nuvem para sua solução, ou uma conexão a um servidor de licença do local.


### Big Compute ou Big Data?

As linhas divisórias entre aplicativos de Big Compute e Big Data nem sempre são claras e alguns aplicativos podem ter características de ambos. Ambos envolvem executar cálculos em larga escala, geralmente em clusters de computadores que executam localmente, na nuvem, ou ambos. Mas as abordagens para soluções e ferramentas de suporte podem diferir.

•**Big Compute** tende a envolver aplicativos que dependem da capacidade e memória da CPU, como simulações de engenharia, modelagem de riscos financeiros e renderização digital. Os clusters que habilitam uma solução de Big Compute podem incluir computadores com processadores de vários núcleos especializados para executar a computação bruta e hardware de rede de alta velocidade especializado para conectar os computadores.

•**Big Data** resolve problemas de análise de dados que envolvem grandes quantidades de dados que não podem ser gerenciados por um único computador ou sistema de gerenciamento de banco de dados, como grandes volumes de logs da web ou outros dados de business intelligence. Big Data tende a contar mais com a capacidade de disco e desempenho de E/S do que da capacidade da CPU, e uma solução de Big Data geralmente usa ferramentas especializadas, como o Apache Hadoop, para gerenciar o cluster e particionar os dados. (Para obter informações sobre o Azure HDInsight e outras soluções do Azure Hadoop, consulte [Hadoop](http://azure.microsoft.com/solutions/hadoop/).)

## Gerenciamento de recursos e agendamento de trabalho

Executar o aplicativo do Lote e HPC geralmente inclui um *Gerenciador de cluster* e um *Agendador de trabalhos* para ajudar a gerenciar recursos de computação em cluster e alocá-los para os aplicativos que executam as tarefas. Essas funções podem ser feitas por ferramentas separadas ou uma ferramenta integrada.

* **Gerenciador de cluster** - Provisiona, libera e administra recursos de computação (ou nós de computação). Dependendo da ferramenta, um gerenciador de cluster pode automatizar a instalação de imagens do sistema operacional e aplicativos em nós de computação, dimensionar os recursos de computação de acordo com as demandas e monitorar o desempenho de nós.

* **Agendador de trabalhos** - Especifica os recursos (como processadores ou memória) de que um aplicativo necessita e as condições em que ele será executado. Um agendador de trabalho mantém uma fila de trabalhos e aloca recursos de acordo com uma prioridade atribuída ou outras características.

Ferramentas de cluster e agendamento para clusters baseados em Linux e Windows, ou aquelas desenvolvidas de forma independente, podem ser bem migradas para o Azure. Por exemplo, [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) é a solução de cluster de computação gratuita da Microsoft para computadores baseados em Windows e Windows Server. Para reduzir a necessidade de recursos de computação dedicados locais, você pode estender um cluster de HPC Pack para usar nós de computação do Azure sob demanda ou implantar um cluster totalmente em máquinas virtuais do Azure.

### Fluxos de trabalho de Big Compute

Ferramentas de gerenciamento de cluster e agendamento de trabalho ajudam a alcançar um fluxo de trabalho de Big Compute com etapas previsíveis. Dependendo da solução, você pode omitir algumas etapas na lista a seguir ou introduzir ferramentas adicionais personalizadas. Fluxos de trabalho intensivos de dados podem envolver as etapas de pré e pós-processamento de dados adicionais (não listadas).

1. **Provisionamento** - Preparar o ambiente de computação com a infraestrutura necessária, recursos de computação e armazenamento para executar aplicativos

2. **Estágio** - Mova aplicativos e dados de entrada para o ambiente de computação

3. **Agendamento** - Configure os trabalhos e tarefas e aloque recursos de computação para eles quando os recursos estiverem disponíveis

4. **Monitoramento** - Forneça informações de status sobre o progresso de trabalhos e tarefas; trate os erros ou exceções

5. **Conclusão** - Retorne resultados e dados de saída pós-processamento, se necessário

## Serviços do Azure para Big Compute

O Azure tem uma série de serviços de computação, dados, rede e serviços relacionados que você pode usar para fluxos de trabalho e soluções de Big Compute. Para obter orientações detalhadas sobre cada um desses serviços, consulte a documentação de serviços do Azure. Consulte os [cenários de solução](#solution-scenarios) neste artigo para algumas abordagens comuns com aplicativos HPC e Lote.

>[AZURE.NOTE]Novos serviços são introduzidos regularmente na plataforma do Azure e podem ser úteis para seu cenário. O uso dos serviços de visualização é recomendado somente para teste ou implantações de verificação de conceito, não cargas de trabalho de produção. Se você tiver dúvidas, entre em contato com um [parceiro do Azure](https://pinpoint.microsoft.com/en-US/search?keyword=azure) ou email *bigcompute@microsoft.com*.

### Serviços de computação

Serviços de computação no Azure são a essência de uma solução de Big Compute. Os serviços de computação na tabela a seguir são usados com frequência e oferecem vantagens para cenários diferentes. Em um nível básico, esses serviços oferecem modos diferentes para que os aplicativos sejam executados em instâncias de computação baseadas em máquina virtual que o Azure fornece usando a tecnologia Hyper-V do Windows Server. Dependendo do serviço, essas instâncias podem executar uma variedade de sistemas operacionais e ferramentas padrão e personalizadas para Linux e Windows. O Azure fornece uma [variedade de tamanhos de instância](../virtual-machines/virtual-machines-sizes-specs.md) com diferentes configurações de núcleos de CPU, memória, capacidade do disco e outras características. Dependendo de suas necessidades, você pode dimensionar as instâncias para milhares de núcleos e, em seguida, reduzir verticalmente, quando você precisa de menos recursos.

>[AZURE.NOTE]Você pode tirar proveito das instâncias A8-A11 para melhorar o desempenho de algumas cargas de trabalho de computação intensa, incluindo aplicativos MPI paralelos que exigem uma rede de aplicativos de alta taxa de transferência e baixa latência. Consulte [Sobre as instâncias A8, A9, A10 e A11 com uso intensivo de computação](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md).

O Barramento de | Descrição
------------- | -----------
**[Serviços de Nuvem](http://azure.microsoft.com/documentation/services/cloud-services)**<br/><br/> |• Pode executar aplicativos de Big Compute em instâncias de função de trabalho, que são máquinas virtuais que executam uma versão do Windows Server e são totalmente gerenciadas pelo Azure<br/><br/>• Permite aplicativos escalonáveis e confiáveis com pouca sobrecarga administrativa, executados em um modelo de plataforma como serviço (PaaS)<br/><br/>• Pode exigir ferramentas adicionais ou desenvolvimento para integrar soluções de cluster HPC local
**[Máquinas Virtuais](http://azure.microsoft.com/documentation/services/virtual-machines)**<br/><br/> |• Fornece infraestrutura de computação como serviço (IaaS) usando a tecnologia Microsoft Hyper-V<br/><br/>• Permite provisionar e gerenciar com flexibilidade computadores persistentes de nuvem por meio de imagens padrão do Windows Server ou Linux ou imagens e discos de dados fornecido ou por meio [Azure Marketplace](https://azure.microsoft.com/marketplace/)<br/><br/>• Executa ferramentas e aplicativos de cluster de computação local totalmente na nuvem
**[Lote](http://azure.microsoft.com/documentation/services/batch)**<br/><br/> |• Executado cargas de trabalho de larga escala paralelas e em lote, como renderização de imagem e codificação e transcodificação de mídia em um serviço completamente gerenciado<br/><br/>• Fornece o plano de trabalho e o dimensionamento automático de um pool de máquinas virtuais gerenciado<br/><br/>• Permite aos desenvolvedores compilar e executar aplicativos como um serviço ou habilitar pela nuvem aplicativos existentes<br/>

### Serviços de armazenamento

Uma solução de Big Compute geralmente opera em um conjunto de dados de entrada e gera dados para seus resultados. Alguns dos serviços de armazenamento do Azure usados em muitas soluções de Big Compute incluem:

* [Blob, tabela e armazenamento de fila](http://azure.microsoft.com/documentation/services/storage) - Gerencie grandes quantidades de dados não estruturados, dados NoSQL e mensagens para fluxo de trabalho e comunicação, respectivamente. Por exemplo, você pode usar o armazenamento de blob para conjuntos de dados técnicos grandes ou as imagens de entrada ou arquivos de mídia que seu aplicativo processa. Você pode usar filas para comunicação assíncrona em uma solução. Consulte [Introdução ao Armazenamento do Microsoft Azure](../storage/storage-introduction.md) para obter mais informações sobre essas soluções de armazenamento.

* [Armazenamento de arquivo do Azure](http://azure.microsoft.com/services/storage/files/) - Compartilha arquivos e dados em comum no Azure usando o protocolo SMB padrão, que é necessário para algumas soluções de cluster HPC.

### Serviços de dados e análise

Alguns cenários de Big Compute envolvem fluxos de dados em grande escala ou geram dados que precisam de mais análise ou processamento. Para lidar com isso, o Azure oferece uma série de serviços de dados e análise incluindo:

* [Data Factory](http://azure.microsoft.com/documentation/services/data-factory) - Compila fluxos de trabalho orientados a dados (pipelines) que unem, agregam e transformam dados originados local, baseados em nuvem e de repositórios de dados da Internet.

* [Banco de dados SQL](http://azure.microsoft.com/documentation/services/sql-database) - Fornece os principais recursos de um sistema de gerenciamento de banco de dados relacional do Microsoft SQL Server em um serviço de plataforma gerenciada.

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight)- Implanta e provisiona clusters do Apache Hadoop no Windows Server ou baseados em Linux na nuvem para gerenciar, analisar e relatar Big Data com alta confiabilidade e disponibilidade.

* [Aprendizado de Máquina](http://azure.microsoft.com/documentation/services/machine-learning) - Ajuda a criar, testar, operar e gerenciar soluções analíticas de previsão em um serviço de plataforma totalmente gerenciado.

### Serviços adicionais

Talvez seja necessário que sua solução de Big Compute inclua outros serviços de plataforma e infraestrutura do Azure para se conectar aos recursos locais ou em outros ambientes. Os exemplos incluem:

* [Rede virtual](http://azure.microsoft.com/documentation/services/virtual-network) - Cria uma seção logicamente isolada no Azure para conectar recursos do Azure ao seu data center local ou um único computador cliente usando o protocolo IPSec; permite que aplicativos Big Compute acessem dados locais, serviços do Active Directory e servidores de licenças

* [Rota Expressa](http://azure.microsoft.com/documentation/services/expressroute) - Cria uma conexão privada entre data centers da Microsoft e a infraestrutura que está local ou em um ambiente de colocalização, com maior segurança, mais confiabilidade, velocidades mais rápidas e latências menores que as conexões típicas através da Internet.

* [Barramento de Serviço](http://azure.microsoft.com/documentation/services/service-bus) - Fornece vários mecanismos para que os aplicativos se comuniquem ou troquem dados, independentemente de eles estarem localizados no Azure, em outra plataforma de nuvem ou em um data center.

## Cenários de solução

A seguir estão cenários comuns para executar cargas de trabalho Big Compute no Azure, aproveitando as soluções de cluster HPC existentes, os serviços do Azure ou uma combinação dos dois.

>[AZURE.NOTE]Para organizações com cargas de trabalho computação intensiva que não são adequadas para as ferramentas de cluster HPC padrão ou que não migram diretamente para os serviços do Azure, o Azure fornece aos desenvolvedores e parceiros um conjunto completo de recursos de computação, serviços, opções de arquitetura e ferramentas de desenvolvimento. O Azure pode dar suporte a fluxos de trabalho de Big Compute personalizados que podem ser dimensionados para milhares de núcleos de computação.

### Cenário 1. Disparar um cluster HPC local para o Azure

**Quando você escolheria isso?** - Talvez você já tenha um cluster HPC local executando suas cargas de trabalho intensivas de computação, mas precisa de recursos de computação extras para períodos de pico, como relatórios de final de mês ou em projetos especiais. Em vez de comprar, implantar e gerenciar hardwares e softwares adicionais que podem ficar ociosos na maioria do tempo, você pode usar o Azure para adicionar capacidade de computação sob demanda ao cluster existente.

Por exemplo, se seu cluster HPC local existente for compilado com o [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), você pode adicionar recursos de computação extras na forma de instâncias de função de trabalho do Azure em execução em um serviço de nuvem. Consulte a figura a seguir. Para obter mais informações e instruções passo a passo, consulte [Intermitência para Azure com Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

![Intermitência de cluster][burst_cluster]

>[AZURE.NOTE]Se você quiser minimizar o tamanho do cluster de HPC Pack, você poderia reduzir o cluster local apenas ao nó principal do HPC Pack. Em seguida, adicione todos os recursos de computação sob demanda no Azure. Para um tutorial que percorra esse cenário, consulte [Configurar um cluster de computação híbrido com Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md).

Essa solução híbrida aproveita o investimento existente em um cluster local, mas permite que você dimensione a infraestrutura local fixa para cargas de trabalho típicas (não pico). Se você precisar acessar um armazenamento de dados ou servidor de licença local, você pode configurar uma rede virtual do Azure para conectar o cluster local ao Azure.

### Cenário 2: Criar um cluster de HPC inteiramente no Azure

**Quando você escolheria isso?** - Talvez você já tenha um investimento substancial em um cluster HPC do Windows ou Linux local incluindo o gerenciamento e agendamento de ferramentas e aplicativos personalizados. Você pode precisar de capacidade de cluster adicional para executar seus aplicativos e conjuntos de ferramentas existentes, mas talvez não queira investir em uma infraestrutura local adicional ou modificar seus aplicativos. Ou talvez seja necessário criar um novo cluster por um período de tempo curto ou longo, mas talvez você não queira pagar o custo de aquisição, manutenção e operação ou alocar espaço para instalar e implantá-lo.

Você pode usar ferramentas de automação do Azure para criar um cluster HPC em máquinas virtuais do Azure para criar a capacidade necessária. Dependendo das máquinas virtuais implantadas, você pode executar uma variedade de cargas de trabalho de HPC e paralelas, incluindo aplicativos MPI rigidamente acoplados.

>[AZURE.NOTE]Verifique com o fornecedor de sua solução e aplicativos de cluster local os requisitos adicionais e as práticas recomendadas para execução em uma nuvem pública fornecendo infraestrutura como serviço (IaaS).

Por exemplo, você pode criar um cluster HPC baseado no Windows Server em com o [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) em máquinas virtuais de serviços de infraestrutura do Azure (IaaS) para executar suas cargas de trabalho, conforme mostrado na seguinte figura simplificada. Um usuário de cluster pode enviar um trabalho com segurança para o cluster de nuvem por meio de ferramentas de envio de trabalho de HPC Pack padrão em execução em um computador cliente. Consulte [Microsoft HPC Pack em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/dn518135.aspx) para obter detalhes e opções de implantação.

![Cluster em IaaS][iaas_cluster]

**Implantação automatizada** - Para implantar um grande número de máquinas virtuais do Windows Server ou Linux, você pode usar imagens de máquina virtual padrão ou personalizadas e ferramentas de automação do Azure como a [Interface de linha de comando do Azure](../xplat-cli.md) ou [PowerShell do Azure](../powershell-install-configure.md). Os exemplos incluem:

* Para implantar um cluster de HPC Pack em serviços de infraestrutura do Azure, você pode executar um [script do PowerShell do Azure](https://msdn.microsoft.com/library/azure/dn864734.aspx) flexível de um computador cliente; o script usa uma imagem de máquina virtual do Windows Server com o HPC Pack pré-instalado. Você também pode usar um [modelo de início rápido](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) com o Azure PowerShell ou a CLI do Azure para implantar um cluster de HPC Pack.

* Você pode usar um [modelo de início rápido](https://azure.microsoft.com/documentation/templates/slurm/) com o Azure PowerShell ou a CLI do Azure para implantar um cluster do Linux executando o gerenciamento de carga de trabalho de software livre [SLURM](https://computing.llnl.gov/linux/slurm/).

Colocar um cluster HPC inteiro na nuvem pode proporcionar benefícios claros.

* Uma organização pode executar trabalhos de HPC sem comprar e gerenciar hardwares locais adicionais e pode controle o tamanho do cluster para usar recursos de computação com eficiência.

* Muitos aplicativos de cluster locais podem ser executados sem alterações, ou com pequenas modificações, em um cluster baseado em nuvem.

* Em comparação com uma solução híbrida que estende um cluster local para a nuvem, a execução de um aplicativo totalmente na nuvem pode simplificar o acesso a dados. Em vez de dividir dados entre as instalações locais e de nuvem ou fazer uma parte do aplicativo acessar os dados remotamente, todos os dados de aplicativo podem ser armazenados na nuvem.

* Alguns fornecedores de software otimizam seus aplicativos para executar clusters baseados em nuvem. Por exemplo, ao implantar o [servidor de computação distribuída MATLAB](http://www.mathworks.com/products/distriben/), da MathWorks, em um cluster de HPC Pack em máquinas virtuais do Azure, você pode executar trabalhos do MATLAB em paralelo inteiramente com recursos de computação baseados em nuvem.

### Cenário 3: Escale horizontalmente um aplicativo paralelo para o Azure

**Quando você escolheria isso?** - Talvez você esteja executando um aplicativo de computação intensiva como uma simulação de Monte Carlo, renderização de animação ou transcodificação de mídia em estações de trabalho locais ou em um pequeno cluster. Você não deseja gerenciar recursos de computação ou de um agendador de trabalho; em vez disso, você quer se concentrar na execução do aplicativo com eficiência para resolver seus problemas de negócios. Ou talvez você queira descarregar seu aplicativo de computação intensiva, ou um aplicativo de terceiros, para que seja executado inteiramente como um serviço na nuvem.

Dependendo da carga de trabalho, você pode tirar proveito de um serviço de Big Compute existente no Azure, hospedado pela Microsoft ou por outro fornecedor de serviço para simplificar o gerenciamento da infraestrutura e do aplicativo para sua solução. Alguns serviços hospedam aplicativos específicos para os clientes em setores selecionados. Alguns serviços se conectam a aplicativos locais, permitindo uma solução híbrida. Outros, como os [Serviços de Mídia do Azure](http://azure.microsoft.com/documentation/services/media-services) são serviços de plataforma dedicada.

Para habilitar a nuvem escalar horizontalmente com facilidade um aplicativo executado hoje, o [lote](http://azure.microsoft.com/documentation/services/batch) fornece APIs para agendar trabalhos e gerenciar os recursos de computação em um serviço. O Lote gerencia a implantação e o dimensionamento automático de máquinas virtuais, agendamento de trabalho, a recuperação de desastres, movimentação de dados, gerenciamento de dependência, implantação de aplicativos e todos os outros detalhes necessários para executar trabalhos na nuvem. Atualmente, o Lote é ideal para aplicativos intrinsecamente paralelos, como renderização de imagem, modelagem de riscos financeiros e simulações de Monte Carlo em execução em recursos de computação do Windows Server.

Consulte a figura a seguir para um fluxo de trabalho típico, que um desenvolvedor pode criar com o Lote.

![Lote do Azure][batch_proc]

1. Carregue arquivos de entrada (como dados ou imagens de origem, arquivos .exe ou de script necessários ao aplicativo e suas dependências) no armazenamento do Azure.

2. Crie um serviço do Lote que vai:

    a. Implante um pool de máquinas virtuais idênticas do Azure para executar o aplicativo, análogas a nós de computação em um cluster de HPC. O desenvolvedor especifica seu tamanho, o sistema operacional em que são executadas e outras propriedades, como se o pool pode ter dimensionamento automático. Quando uma tarefa é executada, uma máquina virtual desse pool é automaticamente atribuída a ela.

    b. Crie um trabalho para executar o aplicativo. O desenvolvedor pode especificar um agendamento e a prioridade do trabalho ou o trabalho pode ser executado sob demanda.

    c. Dividir o trabalho em tarefas. Cada tarefa é essencialmente uma linha de comando que é executada em uma das máquinas virtuais no pool para processar informações de um dos arquivos de dados carregados no armazenamento.

3. Executar o serviço e monitorar os resultados.


## Próximas etapas

* Consulte [Recursos técnicos para Lote e HPC](big-compute-resources.md) para encontrar orientações técnicas para sua solução.

* Para os anúncios mais recentes, consulte o [blog da equipe do Microsoft HPC e Lote](http://blogs.technet.com/b/windowshpc/) e o [blog do Azure](http://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

<!---HONumber=July15_HO4-->