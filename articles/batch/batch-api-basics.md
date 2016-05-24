<properties
	pageTitle="Visão geral dos recursos do Lote do Azure | Microsoft Azure"
	description="Conheça os recursos do serviço de Lote e suas APIs do ponto de vista de um desenvolvedor."
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="05/12/2016"
	ms.author="yidingz;marsma"/>

# Visão geral dos recursos do Lote do Azure

Este artigo fornece uma visão geral básica dos principais recursos de API do serviço Lote do Azure. Desenvolvendo uma solução de computação distribuída usando as APIs [REST do Lote][batch_rest_api] ou [.NET do Lote][batch_net_api], você usará muitas das entidades e dos recursos discutidos a seguir.

> [AZURE.TIP] Para obter uma visão geral técnica de nível mais alto do Lote, confira [Noções básicas do Lote do Azure](batch-technical-overview.md).

## <a name="workflow"></a>Fluxo de trabalho do serviço de Lote

O fluxo de trabalho de alto nível a seguir é tipicamente usado em quase todos os cenários computacionais distribuídos desenvolvidos no serviço Lote:

1. Carregue os *arquivos de dados* que você deseja usar em seu cenário de computação distribuído para uma conta de [Armazenamento do Azure][azure_storage]. Esses arquivos devem estar na conta de Armazenamento para que o serviço Lote possa acessá-los. As tarefas baixarão esses arquivos para [nós de computação](#computenode) quando forem executadas.

2. Carregue os *arquivos binários* dependentes para a conta de Armazenamento. Os arquivos binários incluem o programa a ser executado pelas tarefas e os assemblies dependentes. Esses arquivos também deverão ser acessados de sua conta do Armazenamento para que eles possam ser baixados para os nós de computação pelas tarefas.

3. Crie um [Pool](#pool) de nós de computação. Especifique o [tamanho de nós de computação][cloud_service_sizes] a ser usado quando o pool for criado e quando uma tarefa for executada e, ele será atribuído a um nó nesse pool.

4. Crie um [Trabalho](#job). Um trabalho permite que você gerencie as tarefas de coleta.

5. Adicione [Tarefas](#task) ao trabalho. Cada tarefa usa o programa que você carregou para processar informações de arquivos de dados carregados em sua conta de Armazenamento.

6. Monitore o andamento do trabalho e recupere os resultados.

> [AZURE.NOTE] Você precisará de uma [conta do Lote](batch-account-create-portal.md) para usar o serviço Lote e quase todas as soluções usarão uma conta do [Armazenamento do Azure][azure_storage] para o armazenamento e a recuperação de arquivos. No momento, o Lote dá suporte somente ao tipo de conta de armazenamento de **Finalidade geral**, como descrito na etapa 5 [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).

Nas seções a seguir, você aprenderá sobre cada um dos recursos mencionados no fluxo de trabalho acima, bem como muitos outros recursos do Lote que habilitarão seu cenário de computação distribuído.

## <a name="resource"></a> Recursos do serviço de lote

Ao utilizar o Lote, você usará muitos dos recursos a seguir. Alguns desses recursos, como contas, nós de computação, pools, trabalhos e tarefas, são usados em todas as soluções do Lote. Outros, como agendas de trabalho e pacotes de aplicativos, são recursos úteis, mas opcionais.

- [Conta](#account)
- [Nó de computação](#computenode)
- [Pool](#pool)
- [Trabalho](#job)
- [Tarefa](#task)
	- [Iniciar tarefa](#starttask)
	- [Tarefa do Gerenciador de Trabalhos](#jobmanagertask)
	- [Tarefas de preparação e liberação do trabalho](#jobpreprelease)
	- [Tarefas de várias instâncias](#multiinstance)
    - [Dependências da tarefa](#taskdep)
- [Agendas de trabalho](#jobschedule)
- [Pacotes de aplicativos](#appkg)

### <a name="account"></a>Conta

Uma conta do Batch é uma entidade identificada exclusivamente no serviço Batch. Todo o processamento é feito por meio de uma conta do Lote. Quando você executar operações com o serviço Lote, precisará do nome da conta e da chave da conta. Para criar e gerenciar uma conta do Lote, confira [Criar e gerenciar uma conta do Lote do Azure no portal do Azure](batch-account-create-portal.md).

### <a name="computenode"></a>Nó de computação

Um nó de computação é um nó de máquina virtual do Azure que é dedicado a uma carga de trabalho específica para seu aplicativo. O tamanho de um nó determina o número de núcleos de CPU, a capacidade de memória e o tamanho do sistema de arquivos local alocados para o nó. Um nó pode ser de qualquer [tamanho de nó de serviço de nuvem][cloud_service_sizes], exceto A0.

Os nós podem executar executáveis e scripts, incluindo arquivos executáveis (.exe), arquivos de comando (.cmd), arquivos de lote (.bat) e scripts do PowerShell. Um nó também tem os seguintes atributos:

- Uma **estrutura de pastas** padrão e as **variáveis de ambiente** associadas, detalhando os caminhos criados em cada nó de computação. Consulte [Arquivos e diretórios](#files) abaixo para obter mais informações.
- **Variáveis de ambiente** disponíveis para referência por tarefas.
- As configurações de **firewall** que são definidas para controlar o acesso.
- Se o **acesso remoto** a um nó de computação for necessário (para depuração, por exemplo), um arquivo RDP poderá ser obtido e que poderá então usado para acessar o nó por meio da *Área de Trabalho Remota*.

### <a name="pool"></a>Pool

Um pool é uma coleção de nós na qual seu aplicativo é executado. O pool pode ser criado por você ou pelo serviço Lote automaticamente quando você especifica o trabalho a ser feito. Você pode criar e gerenciar um pool que atenda às necessidades do seu aplicativo e os pools podem ser usados apenas pela conta do Lote na qual ele foi criado. Uma conta do Batch pode ter mais de um pool.

Os pools do Lote do Azure são criados na plataforma de computação principal do Azure: os pools do Lote oferecem alocação em larga escala, instalação de aplicativos, distribuição de dados e monitoramento de integridade, além do ajuste flexível do número de nós de computação em um pools (dimensionamento).

Todos os nós adicionados a um pool recebem um nome e um endereço IP exclusivos. Quando um nó for removido de um pool, todas as alterações feitas no sistema operacional ou arquivos serão perdidas e seu nome e endereço IP serão liberados para uso futuro. Quando um nó deixa um pool, seu tempo de vida termina.

Você pode configurar um pool para permitir a comunicação entre os nós contidos nele. Se for solicitada comunicação entre pools em um determinado pool, o serviço do Lote habilita portas com número superior a 1100 em cada nó contido no pool. Cada nó no pool está configurado para permitir conexões de entrada somente para este intervalo de portas e somente de outros nós do pool. Se seu aplicativo não precisar de comunicação entre nós, o serviço Lote pode alocar um número potencialmente grande de nós para o pool de vários clusters e data centers diferentes para habilitar o poder cada vez maior de processamento paralelo.

Ao criar um pool, você pode especificar os seguintes atributos:

- O **tamanho dos nós** no pool
	- Um tamanho apropriado de nó deve ser escolhido, levando em consideração as características e os requisitos do aplicativo ou dos aplicativos que serão executados nos nós. O tamanho de nó é normalmente selecionado supondo que uma tarefa por vez será executada no nó. A consideração de aspectos como se o aplicativo é multithread e quanta memória ele consome ajuda a determinar o tamanho do nó mais adequado e econômico. É possível ter várias tarefas atribuídas e várias instâncias do aplicativo sendo executadas em paralelo, caso em que geralmente será escolhido um nó maior – consulte "Política de agendamento de tarefas” abaixo para saber mais.
	- Todos os nós em um pool precisam ser do mesmo tamanho. Se diferentes aplicativos tiverem de ser executados com requisitos de sistema e/ou níveis de carga diferentes,deverão ser criados pools separados.
	- Todos os [tamanhos de nó de serviço de nuvem][cloud_service_sizes] podem ser configurados para um pool, exceto A0.

- A **família do sistema operacional** e a **versão** que é executada nos nós
	- Assim como acontece com as funções de trabalho nos Serviços de Nuvem, a *Família de Sistemas Operacionais* e a *Versão do Sistema Operacional* podem ser especificadas (para saber mais sobre funções de trabalho, consulte a seção [Sobre os serviços de nuvem][about_cloud_services] em *Opções de hospedagem de computação fornecidas pelo Azure*).
	- A família de sistema operacional também determina quais versões do .NET são instaladas com o sistema operacional.
	- Assim como acontece com as funções de trabalho, é recomendável que `*` seja especificado para a Versão do Sistema Operacional de forma que os nós sejam automaticamente atualizados e não haja trabalho necessário para atender às versões recém-criadas. O caso de uso principal para escolher uma versão específica do sistema operacional é garantir que a compatibilidade do aplicativo seja mantida, permitindo que seja realizado um teste de compatibilidade com versões anteriores antes de permitir que a versão seja atualizada. Uma vez validada, a versão do sistema operacional para o pool pode ser atualizada e a nova imagem do sistema operacional pode ser instalada - qualquer tarefa em execução será interrompida e colocada novamente na fila.

- **Número de destino dos nós** que devem estar disponíveis para o pool

- **Política de dimensionamento** para o pool.
	- Além do número de nós, você também pode especificar uma [fórmula de dimensionamento automático](batch-automatic-scaling.md) para um pool. O serviço Lote executará a fórmula e ajustará o número de nós no pool com base nos diversos parâmetros de pool, de trabalho e de tarefa que você pode especificar.

- Política de **agendamento de tarefas**
	- A opção de configuração [máx. de tarefas por nó](batch-parallel-node-tasks.md) determina o número máximo de tarefas que podem ser executadas em paralelo em cada nó do pool.
	- A configuração padrão é para que uma tarefa seja executada a qualquer momento em um nó de computação, mas há cenários em que é útil ter mais de uma tarefa executada ao mesmo tempo em um nó. Um exemplo é aumentar a utilização de nó se um aplicativo precisar aguardar E/S. Ter mais de um aplicativo executado ao mesmo tempo aumentará a utilização da CPU. Outro exemplo é reduzir o número de nós no pool. Isso pode reduzir a quantidade de transferência de dados necessários para referência de grandes conjuntos de dados - se um tamanho de nó A1 for suficiente para um aplicativo, então o tamanho do nó A4 poderia ser escolhido e o pool configurado para 8 tarefas em paralelo, cada uma usando um núcleo.
	- Um "tipo de preenchimento" também pode ser especificado para determinar se o Lote distribuirá as tarefas igualmente entre todos os nós ou pacotes de cada nó com o número máximo de tarefas antes de atribuir tarefas para outro nó no pool.

- **Status de comunicação** dos nós no pool
	- Um pool pode ser configurado para permitir a comunicação entre os nós no pool que determina sua infraestrutura de rede subjacente. Observe que isso também afeta a colocação de nós nos clusters.
	- Na maioria dos cenários, as tarefas operam de forma independente e não precisam se comunicar umas com as outras, mas pode haver alguns aplicativos nos quais as tarefas devem se comunicar.

- **Tarefa inicial** para os nós no pool
	- Uma *tarefa inicial* pode ser especificada para ser executada sempre que um nó de computação ingressar no pool e quando um nó for reiniciado. Isso geralmente é usado para instalar um aplicativo a ser utilizado pelas tarefas em execução no nó.

### <a name="job"></a>Trabalho

Um trabalho é uma coleção de tarefas e especifica como a computação é executada nos nós de computação contidos em um pool.

- O trabalho especifica o **pool** no qual o trabalho será executado. O pool pode ser um pool existente, anteriormente criado para ser usado por vários trabalhos ou criado sob demanda para cada trabalho associado a um plano de trabalho ou para todos os trabalhos associados a um plano de trabalho.
- Pode ser especificada uma **prioridade de trabalho** opcional. Quando um trabalho é enviado com uma prioridade mais alta do que os trabalhos em andamento, as tarefas do trabalho de prioridade mais alta serão inseridas na fila antes das tarefas de trabalho com prioridade mais baixa. As tarefas com prioridade mais baixa que já estão sendo executadas não poderão ser antecipadas.
- As **restrições** de trabalho especificam determinados limites para seus trabalhos.
	- Pode ser definido um **tempo total máximo** para os trabalhos. Se os trabalhos forem executados por mais tempo que o tempo de wallclock máximo especificado, o trabalho e todas as tarefas associadas a ele serão encerrados.
	- O lote do Azure pode detectar tarefas que falham e repetir as tarefas. O **número máximo de novas tentativas de tarefa** pode ser especificado como uma restrição, incluindo a especificação de que uma tarefa é sempre repetida ou nunca é repetida. Repetir uma lista de tarefas significa que a tarefa está na fila novamente para ser executada uma vez mais.
- As tarefas podem ser adicionadas ao trabalho pelo seu aplicativo cliente ou uma [tarefa do Gerenciador de Trabalhos](#jobmanagertask) pode ser especificada. Uma tarefa de gerenciador de trabalhos usa a API do Lote e contém as informações para criar as tarefas necessárias para um trabalho com a tarefa que está sendo executada em um dos nós de computação do pool. A tarefa do Gerenciador de Trabalhos é tratada especificamente pelo Lote – é colocadas na fila assim que o trabalho é criado e reiniciado, caso ele falhe. Uma tarefa do Gerenciador de Trabalhos é necessária para trabalhos criados por um plano de trabalho, pois é a única maneira de definir as tarefas antes de o trabalho ser instanciado. Mais informações sobre o gerenciador de tarefas são exibidas abaixo.

### <a name="task"></a>Tarefa

Uma tarefa é uma unidade de computação que está associada a um trabalho e é executada em um nó. As tarefas são atribuídas a um nó para execução ou estão na fila até que um nó fique livre. Uma tarefa usa os seguintes recursos:

- O aplicativo especificado na **linha de comando** da tarefa.

- **Arquivos de recursos** que contêm os dados a serem processados. Esses arquivos são copiados automaticamente para o nó do armazenamento de blobs em uma conta do Armazenamento do Azure de **Finalidade geral**. Para saber mais, veja *Tarefa inicial* e [Arquivos e diretórios](#files) abaixo.

- As **variáveis de ambiente** exigidas pelo aplicativo. Para saber mais, consulte as [Configurações de ambiente para tarefas](#environment) abaixo.

- As **restrições** sob as quais a computação deve ocorrer. Por exemplo, o tempo máximo pelo qual a tarefa pode ser executada, o número máximo de vezes que se deve tentar novamente executar uma tarefa se ela falhar e o tempo máximo pelo qual os arquivos no diretório de trabalho são mantidos.

Além das tarefas que você pode definir para realizar computação em um nó, as tarefas especiais a seguir também são fornecidas pelo serviço Lote:

- [Iniciar tarefa](#starttask)
- [Tarefa do Gerenciador de Trabalhos](#jobmanagertask)
- [Tarefas de preparação e liberação do trabalho](#jobmanagertask)
- [Tarefas de várias instâncias](#multiinstance)
- [Dependências da tarefa](#taskdep)

#### <a name="starttask"></a>Iniciar tarefa

Associando uma **tarefa inicial** a um pool, você pode configurar o ambiente operacional de seus nós, executando ações como instalar software ou iniciar processos em segundo plano. A tarefa inicial será executada sempre que um nó for iniciado, enquanto esse nó permanecer no pool, incluindo quando o nó for adicionado pela primeira vez ao pool. O principal benefício da tarefa inicial é que ele contém todas as informações necessárias para configurar nós de computação e instalar aplicativos necessários para a execução da tarefa de trabalho. Assim, aumentar o número de nós em um pool é tão simples quanto especificar a nova contagem de nós de destino - o Lote já tem todas as informações necessárias para configurar os novos nós e prepará-los para a aceitação de tarefas.

Como com qualquer tarefa do Lote, uma lista de **arquivos de recursos** no [Armazenamento do Azure][azure_storage] pode ser especificada além de uma **linha de comando** a ser executada. O Lote do Azure copiará primeiro os arquivos do Armazenamento do Azure e executará a linha de comando. Para uma tarefa inicial do pool, a lista de arquivos geralmente contém os arquivos de aplicativos ou o pacote de aplicativos, mas ela também pode incluir dados de referência que serão usados por todas as tarefas em execução nos nós de computação. A linha de comando da tarefa inicial poderia executar um script do PowerShell ou executar uma operação `robocopy`, por exemplo, para copiar arquivos de aplicativo para a pasta "compartilhada", depois executar um MSI ou `setup.exe`.

> [AZURE.IMPORTANT] No momento, o Lote dá suporte *somente* ao tipo de conta de armazenamento de **Finalidade geral**, como descrito na etapa 5 [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md). As tarefas do Lote (incluindo tarefas padrão, tarefas iniciais, preparação de trabalho e tarefas de versão de trabalho) devem especificar os arquivos de recursos que residem *somente* nas contas de armazenamento de **Finalidade geral** .

Isso é tipicamente desejável que o serviço Lote aguarde a conclusão da tarefa inicial antes de considerar o nó pronto para ter tarefas atribuídas, mas isso é configurável.

Se uma tarefa de inicialização falhar em um nó de computação, o estado do nó será atualizado para refletir a falha e o nó não estará disponível para as tarefas a serem atribuídas. Uma tarefa inicial poderá falhar se houver um problema ao copiar seus arquivos de recursos do armazenamento ou se o processo executado por sua linha de comando retornar um código de saída diferente de zero.

#### <a name="jobmanagertask"></a>Tarefa do Gerenciador de Trabalhos

Uma **tarefa do Gerenciador de Trabalhos** normalmente é usada no controle e/ou no monitoramento da execução do trabalho. Por exemplo, a criação e o envio das tarefas para um trabalho, a determinação da execução de tarefas adicionais e a determinação de quando o trabalho está concluído. No entanto, uma tarefa do Gerenciador de Trabalhos não está restrita a essas atividades - é uma tarefa capaz que pode executar qualquer ação exigida pelo trabalho. Por exemplo, uma tarefa do Gerenciador de Trabalhos pode baixar um arquivo especificado como um parâmetro, analisar o conteúdo desse arquivo e enviar tarefas adicionais com base no conteúdo.

Uma tarefa do gerenciador de trabalhos é iniciada antes de todas as outras tarefas e fornece os seguintes recursos:

- Ela é criada automaticamente pelo serviço Lote quando o trabalho é criado.

- É agendada para execução antes das outras tarefas em um trabalho.

- O nó associado é o último a ser removido de um pool quando o pool está sendo reduzido.

- Seu término pode ser vinculado ao término de todas as tarefas existentes no trabalho.

- A tarefa do gerenciador de trabalhos tem a prioridade mais alta quando precisa ser reiniciada. Se não houver um nó ocioso disponível, o serviço em lote pode encerrar uma das outras tarefas em execução no pool para liberar espaço para a tarefa do gerenciador de trabalhos ser executada.

- Uma tarefa de gerenciador de trabalhos em um trabalho não tem prioridade sobre tarefas em outros trabalhos. Entre diferentes trabalhos, somente as prioridades de nível de trabalho são observadas.

#### <a name="jobpreprelease"></a>Tarefas de preparação e liberação do trabalho

O Lote fornece a tarefa de preparação do trabalho para configuração de execução do pré-trabalho e a tarefa de liberação do trabalho para manutenção ou limpeza pós-trabalho.

- **Tarefa de preparação de trabalho** - a tarefa de preparação do trabalho é executada em todos os nós de computação e agendados para execução de tarefas, antes de qualquer uma das tarefas do trabalho. Use a tarefa de preparação do trabalho para copiar dados compartilhados por todas as tarefas, mas exclusivos para o trabalho, por exemplo.
- **Tarefa de liberação do trabalho** - quando o trabalho for concluído, a tarefa de liberação do trabalho será executada em cada nó no pool que tiver executado pelo menos uma tarefa. Use a tarefa de lançamento do trabalho para excluir dados copiados pela tarefa de preparação de trabalho ou para compactar e carregar dados do log de diagnóstico, por exemplo.

As tarefas de preparação e liberação de trabalho permitem que você especifique uma linha de comando para ser executada quando a tarefa é invocada e oferecem recursos como o download do arquivo, a execução privilegiada, variáveis de ambiente personalizadas, duração de execução máxima, contagem de repetição e tempo de retenção de arquivo.

Para saber mais sobre tarefas de preparação e de liberação de trabalho, consulte [Executar tarefas de preparação e de conclusão de trabalhos em nós de computação do Lote do Azure](batch-job-prep-release.md).

#### <a name="multiinstance"></a>Tarefas de várias instâncias

Uma [tarefa de várias instâncias](batch-mpi.md) é a que é configurada para ser executada simultaneamente em mais de um nó de computação. Com as tarefas de várias instâncias, você pode habilitar cenários de computação de alto desempenho, como MPI (Message Passing Interface), que exige um grupo de nós de computação alocados juntos para processar uma única carga de trabalho.

Para obter uma discussão detalhada sobre como executar trabalhos da MPI no Lote usando a Biblioteca .NET do Lote, confira [Usar tarefas de várias instâncias para executar aplicativos da MPI (Message Passing Interface) no Lote do Azure](batch-mpi.md).

#### <a name="taskdep"></a>Dependências de tarefas

As dependências de tarefas, como o nome indica, permitem especificar que uma tarefa depende da conclusão de outras tarefas antes de sua execução. Este recurso fornece suporte para situações em que uma tarefa "downstream" consome a saída de uma tarefa "upstream", ou quando uma tarefa upstream executa alguma inicialização necessária para uma tarefa downstream. Para usar esse recurso, primeiro você deve habilitar as dependências em seu trabalho do Lote. Em seguida, para cada tarefa que dependa de outra (ou de muitas outras), especifique as tarefas das quais essa tarefa depende.

Com as dependências de tarefas, você pode configurar cenários como o seguinte:

* A *tarefaB* depende de *tarefaA* (a execução da *tarefaB* não iniciará até a conclusão da *tarefaA*)
* A *tarefaC* depende da *tarefaA* e da *tarefaB*
* A *tarefaD* depende de uma variedade de tarefas, como as tarefas *1* a *10*, antes de ser executada

Confira o exemplo de código [TaskDependencies][github_sample_taskdeps] no repositório GitHub [azure-batch-samples][github_samples]. Nele, você verá como configurar as tarefas que dependem de outras tarefas usando a biblioteca [.NET do Lote][batch_net_api].

### <a name="jobschedule"></a>Trabalhos agendados

Os planos de trabalho permitem que você crie trabalhos recorrentes no serviço Lote. Um plano de trabalho especifica quando executar trabalhos e inclui as especificações para os trabalhos a serem executados. Um plano de trabalho permite a especificação da duração da agenda - quanto tempo e quando a agenda entrará em vigor - e com que frequência durante esse período os trabalhos deverão ser criados.

### <a name="appkg"></a>Pacotes de aplicativos

O recurso de [pacotes de aplicativos](batch-application-packages.md) possibilita fácil gerenciamento e implantação de aplicativos nos nós de computação em seus pools. Com pacotes de aplicativos, você pode carregar e gerenciar facilmente várias versões dos aplicativos executados por suas tarefas, incluindo binários e arquivos de suporte, e implantar automaticamente um ou mais desses aplicativos nos nós de computação no pool.

O Lote lida com os detalhes do trabalho com o Armazenamento do Azure em segundo plano para armazenar e implantar com segurança os pacotes de aplicativos em nós de computação, para que o código e a sobrecarga de gerenciamento sejam simplificados.

Para saber mais sobre o recurso de pacote de aplicativos, confira [Implantação de aplicativos com pacotes de aplicativos do Lote do Azure](batch-application-packages.md).

## <a name="files"></a>Arquivos e diretórios

Cada tarefa tem um diretório de trabalho sob o qual ela cria zero ou mais arquivos e diretórios para armazenar o programa que é executado pela tarefa, os dados processados por uma tarefa e a saída do processamento realizado por uma tarefa. Esses arquivos e diretórios ficam disponíveis para uso por outras tarefas durante a execução de um trabalho. Todas as tarefas, arquivos e diretórios em um nó pertencem a uma conta de usuário único.

O serviço em lote expõe uma parte do sistema de arquivos em um nó, como o “diretório raiz”. O diretório raiz está disponível para uma tarefa acessando a variável do ambiente `%AZ_BATCH_NODE_ROOT_DIR%`. Para saber mais sobre como usar as variáveis de ambiente, consulte [Configurações de ambiente para tarefas](#environment).

![Estrutura de diretórios do nó de computação][1]

O diretório raiz contém a seguinte estrutura de diretório:

- **Compartilhado** – esse local é uma pasta compartilhada para todas as tarefas executadas em um nó, independentemente do trabalho. No nó, o diretório compartilhado é acessado por meio de `%AZ_BATCH_NODE_SHARED_DIR%`. Esse diretório oferece acesso de leitura/gravação para todas as tarefas executadas no nó. As tarefas podem criar, ler, atualizar e excluir arquivos nesse diretório.

- **Inicialização** – esse local é usado por uma tarefa de inicialização como seu diretório de trabalho. Todos os arquivos que são baixados pelo serviço Batch para executar a tarefa de inicialização também são armazenados nesse diretório. No nó, o diretório inicial está disponível por meio da variável de ambiente `%AZ_BATCH_NODE_STARTUP_DIR%`. A tarefa inicial pode criar, ler, atualizar e excluir arquivos contidos nesse diretório, que pode ser usado pelas tarefas de inicialização para configurar o sistema operacional.

- **Tarefas** -um diretório é criado para cada tarefa executada no nó, acessado via `%AZ_BATCH_TASK_DIR%`. Em cada diretório de tarefas, o serviço Lote cria um diretório de trabalho (`wd`) cujo caminho exclusivo é especificado pela variável de ambiente `%AZ_BATCH_TASK_WORKING_DIR%`. Esse diretório oferece acesso de leitura/gravação à tarefa. A tarefa pode criar, ler, atualizar e excluir arquivos nesse diretório, e esse diretório é mantido com base na restrição *RetentionTime* especificada para a tarefa.
  - `stdout.txt` e `stderr.txt` - esses arquivos são gravados na pasta de tarefas durante a execução da tarefa.

Quando um nó é removido do pool, todos os arquivos armazenados no nó também são removidos.

## <a name="lifetime"></a>Tempo de vida de nó de computação e de pool

Ao projetar sua solução do Lote do Azure, uma decisão de design deve ser tomada em relação a como e quando os pools são criados e por quanto tempo os nós de computação nesses pools ficarão disponíveis.

Em um extremo do espectro, um pool seria criado para cada trabalho quando o trabalho fosse enviado e os nós seriam removidos assim que a execução das tarefas fosse finalizada. Isso maximizaria a utilização, pois o nó seria alocado somente quando absolutamente necessário e desligaria assim que se tornasse ocioso. Embora isso signifique que o trabalho deva aguardar até que os nós sejam alocados, é importante observar que as tarefas serão agendadas para os nós assim que estiverem disponíveis individualmente, alocadas e a tarefa inicial tiver sido concluída O Lote *não* aguardar até que todos os nós dentro de um pool estejam disponíveis antes de atribuir tarefas, garantindo assim a utilização máxima de todos os nós disponíveis.

Na outra extremidade do espectro, se fazer com que os trabalhos sejam iniciados imediatamente for a prioridade mais alta, um pool poderá ser criado de forma antecipada e seus nós serão disponibilizados antes do envio dos trabalhos. Nesse cenário, as tarefas de trabalho podem começar imediatamente, mas os nós poderão ficar ociosos enquanto aguardam a atribuição de tarefas.

Uma abordagem combinada, normalmente usada para tratar a carga variável mas contínua, é ter um pool para o qual vários trabalhos são enviados, mas dimensione o número de nós para cima ou para baixo de acordo com a carga de trabalho (consulte *Escalonando aplicativos* abaixo). Isso pode ser feito de maneira reativa, com base na carga atual ou proativamente, se a carga puder ser prevista.

## <a name="scaling"></a>Escalonamento de aplicativos

Com o [dimensionamento automático](batch-automatic-scaling.md), você pode deixar que o serviço de Lote ajuste dinamicamente o número de nós de computação em um pool de acordo com a carga de trabalho e o uso de recursos atuais do cenário de computação. Isso permite reduzir o custo geral de execução do aplicativo usando apenas os recursos que são necessários e liberando os que não são. É possível especificar as configurações de dimensionamento automático para um pool quando ele é criado ou habilitar o dimensionamento mais tarde e você pode atualizar as configurações de dimensionamento em um pool habilitado para dimensionamento automático.

O dimensionamento automático é realizado com a especificação de uma **fórmula de dimensionamento automático** para um pool. O serviço de Lote usa a fórmula para determinar o número de destino de nós no pool para o próximo intervalo de dimensionamento (um intervalo que você pode especificar).

Por exemplo, talvez um trabalho exija que você envie um grande número de tarefas a serem agendadas para execução. Você pode atribuir uma fórmula de dimensionamento ao pool que ajusta o número de nós nele com base no número atual de tarefas pendentes e a taxa de conclusão dessas tarefas. Periodicamente, o serviço de Lote avalia a fórmula e redimensiona o pool com base na carga de trabalho e nas configurações da fórmula.

Uma fórmula de dimensionamento pode basear-se nas seguintes métricas:

- **Métricas de tempo** – com base em estatísticas coletadas a cada cinco minutos no número de horas especificado.

- **Métricas de recurso** – com base no número de nós e no uso da CPU, largura de banda e memória.

- **Métricas de tarefa** – com base no status das tarefas, como Ativa, Pendente e Concluída.

Quando o dimensionamento automático diminui o número de nós de computação em um pool, as tarefas atualmente em execução devem ser consideradas. Para acomodar isso, a fórmula pode incluir uma configuração de política de desalocação de nó que especifica se as tarefas em execução são interrompidas imediatamente ou podem terminar antes que o nó seja removido do pool.

> [AZURE.TIP] Para maximizar a utilização de recursos de computação, defina o número de destino de nós como zero ao fim de um trabalho, mas permita a conclusão das tarefas em execução.

Para saber mais sobre o dimensionamento automático de um aplicativo, consulte [Dimensionar automaticamente nós de computação em um pool do Lote do Azure](batch-automatic-scaling.md).

## <a name="cert"></a>Segurança com certificados

Normalmente, você precisa usar certificados ao criptografar ou descriptografar informações confidenciais para tarefas, como a chave para uma [conta do Armazenamento do Azure][azure_storage]. Para dar suporte a isso, os certificados podem ser instalados em nós. Os segredos criptografados são passados para tarefas por meio dos parâmetros de linha de comando ou incorporados em um dos recursos de tarefa, e os certificados instalados podem ser usados para descriptografá-los.

Você usa a operação [Adicionar certificado][rest_add_cert] (API REST do Lote) ou o método [CertificateOperations.CreateCertificate][net_create_cert] (.NET API do Lote) para adicionar um certificado a uma conta do Lote. Em seguida, você pode associar o certificado a um pool novo ou existente. Quando um certificado está associado a um pool, o serviço em lote instala o certificado em cada nó presente no pool. O serviço Lote instala os certificados apropriados quando o nó é inicializado, antes que ele execute qualquer tarefa, incluindo tarefas de inicialização e tarefas do gerenciador de trabalhos.

## <a name="scheduling"></a>Prioridade de agendamento

Você pode atribuir uma prioridade a trabalhos criados no Lote. O serviço Lote usa o valor da prioridade do trabalho para determinar a ordem de agendamento dos trabalhos em uma conta. Os valores de prioridade variam de -1000 a 1000, em que -1000 é a prioridade mais baixa e 1000 a mais alta. Você pode atualizar a prioridade de um trabalho usando a operação [Atualizar as propriedades de um trabalho][rest_update_job] (API REST do Lote) ou modificando a propriedade [CloudJob.Priority][net_cloudjob_priority] (.NET API do Lote).

Em uma mesma conta, os trabalhos com prioridade mais alta têm precedência no agendamento sobre aqueles com prioridade mais baixa. Um trabalho com valor de prioridade mais alto em uma conta não tem precedência, no agendamento, sobre outro trabalho com valor de prioridade inferior em uma conta diferente.

O plano de trabalho em pools é independente. Entre pools diferentes, não é garantido que um trabalho com prioridade mais alta seja agendado primeiro, caso faltem nós ociosos em seu pool associado. No mesmo pool, trabalhos com o mesmo nível de prioridade têm a mesma chance de ser agendados.

## <a name="environment"></a>Configurações de ambiente para tarefas

Cada tarefa executada em um trabalho do Lote tem acesso a variáveis de ambiente definidas pelo serviço Lote (definidas pelo sistema, consulte a tabela abaixo), bem como variáveis de ambiente definidas pelo usuário. Os aplicativos e os scripts executados por tarefas em nós de computação têm acesso a essas variáveis de ambiente durante a execução no nó.

Defina variáveis de ambiente definidas pelo usuário ao usar a operação [Adicionar uma tarefa a um trabalho][rest_add_task] (API REST do Lote) ou modificar a propriedade [CloudTask.EnvironmentSettings][net_cloudtask_env] (API .NET do Lote) ao adicionar tarefas a um trabalho.

Obtenha variáveis de ambiente da tarefa, do sistema e definidas pelo usuário, usando a operação [Obter informações sobre uma tarefa][rest_get_task_info] (API REST do Lote) ou acessando a propriedade [CloudTask.EnvironmentSettings][net_cloudtask_env] (.NET API do Lote). Como mencionado, os processos em execução em um nó de computação também podem acessar todas as variáveis de ambiente, por exemplo, usando a conhecida sintaxe `%VARIABLE_NAME%`.

Para cada tarefa agendada em um trabalho, o seguinte conjunto de variáveis de ambiente definidas pelo sistema é definido pelo serviço Lote:

| Nome de variável de ambiente | Descrição |
|---------------------------------|--------------------------------------------------------------------------|
| `AZ_BATCH_ACCOUNT_NAME` | O nome da conta à qual a tarefa pertence. |
| `AZ_BATCH_JOB_ID` | A ID do trabalho ao qual a tarefa pertence. |
| `AZ_BATCH_JOB_PREP_DIR` | O caminho completo do diretório de tarefa de preparação de trabalho no nó. |
| `AZ_BATCH_JOB_PREP_WORKING_DIR` | O caminho completo do diretório de trabalho da tarefa de preparação de trabalho no nó. |
| `AZ_BATCH_NODE_ID` | O nome do nó no qual a tarefa está sendo executada. |
| `AZ_BATCH_NODE_ROOT_DIR` | O caminho completo do diretório raiz no nó. |
| `AZ_BATCH_NODE_SHARED_DIR` | O caminho completo do diretório compartilhado no nó. |
| `AZ_BATCH_NODE_STARTUP_DIR` | O caminho completo do diretório da tarefa de inicialização do nó de computação no nó. |
| `AZ_BATCH_POOL_ID` | A ID do pool no qual a tarefa está sendo executada. |
| `AZ_BATCH_TASK_DIR` | O caminho completo do diretório de tarefa no nó. |
| `AZ_BATCH_TASK_ID` | A ID da tarefa atual. |
| `AZ_BATCH_TASK_WORKING_DIR` | O caminho completo do diretório de trabalho da tarefa no nó. |

>[AZURE.NOTE] Não é possível substituir qualquer uma das variáveis definidas pelo sistema acima - elas são somente leitura.

## <a name="errorhandling"></a>Tratamento de erros

Talvez seja necessário manipular falhas de tarefa e de aplicativo em sua solução do Lote.

### Manipulação de falha de tarefa
As falhas de tarefas se enquadram nestas categorias:

- **Falhas de agendamento**
	- Se a transferência de arquivos especificada para uma tarefa falhar por algum motivo, um "erro de agendamento" será definido para a tarefa.
	- As causas de erros de agendamento poderiam ser arquivos movidos, a conta do Armazenamento não está mais disponível ou foi encontrado um outro problema que impediu a cópia bem-sucedida de arquivos para o nó.
- **Falhas de aplicativo**
	- O processo especificado pela linha de comando da tarefa também pode falhar. O processo é considerado com falha quando é retornado um código de saída diferente de zero pelo processo executado pela tarefa.
	- Para as falhas de aplicativo, é possível configurar o Lote para repetir automaticamente a tarefa até um número especificado de vezes.
- **Falhas de restrição**
	- Uma restrição pode ser definida para especificar a duração máxima da execução de um trabalho ou de uma tarefa, o *maxWallClockTime*. Isso pode ser útil para o encerramento de tarefas “paradas”.
	- Quando o tempo máximo tiver sido excedido, a tarefa será marcada como *concluída*, mas o código de saída é definido como `0xC000013A` e o campo *schedulingError* será marcado como `{ category:"ServerError", code="TaskEnded"}`.

### Falhas de depuração de aplicativos

Durante a execução, um aplicativo pode produzir saída de diagnóstico que pode ser usada para solucionar problemas. Como mencionado em [Arquivos e diretórios](#files) acima, o serviço Lote envia saída de stdout e stderr para os arquivos `stdout.txt` e `stderr.txt` localizados no diretório da tarefa no nó de computação. Usando [ComputeNode.GetNodeFile][net_getfile_node] e [CloudTask.GetNodeFile][net_getfile_task] na .NET API do Lote, você pode recuperar esses e outros arquivos para fins de solução de problemas.

Uma depuração ainda mais extensa pode ser executada quando você efetuar logon em um nó de computação usando a *Área de Trabalho Remota*. Você pode [obter um arquivo de protocolo de área de trabalho remota de um nó][rest_rdp] (API REST do Lote) ou o uso do método [ComputeNode.GetRDPFile][net_rdp] (.NET API do Lote) para logon remoto.

>[AZURE.NOTE] Para se conectar a um nó via RDP, primeiro você deverá criar um usuário no nó. [Adicione uma conta de usuário a um nó][rest_create_user] à API REST do Lote ou use o método [ComputeNode.CreateComputeNodeUser][net_create_user] no .NET do Lote.

### Contabilidade de interrupções ou de falhas de tarefas

As tarefas podem falhar ou ser interrompidas ocasionalmente. O próprio aplicativo de tarefa pode falhar, o nó no qual a tarefa está em execução pode ser reinicializado ou o nó pode ser removido do pool durante uma operação de redimensionamento caso a política de desalocação do pool seja definida para remover o nó imediatamente sem esperar que a tarefa seja concluída. Em todos os casos, a tarefa pode ser automaticamente recolocada na fila pelo Lote para execução em outro nó.

Também é possível que um problema intermitente faça com que uma tarefa falhe ou demore muito para ser executada. O tempo de execução máximo pode ser definido para uma tarefa e se o Lote excedido interromperá o aplicativo da tarefa.

### Solução de problemas de nós de computação "inválidos"

Em situações em que algumas das tarefas falham, o aplicativo cliente ou o serviço de Lote pode examinar os metadados das tarefas com falha para identificar um nó com comportamento inadequado. Cada nó em um pool tem uma ID exclusiva, e o nó no qual uma tarefa é executada é incluído nos metadados da tarefa. Uma vez identificado, você pode executar várias ações:

- **Reiniciar o nó** ([REST][rest_reboot] | [.NET][net_reboot])

	Às vezes, reiniciar o nó pode corrigir problemas latentes, como processos bloqueados ou com falha. Observe que, se o pool usar uma tarefa de inicialização ou se o trabalho usar uma tarefa de preparação de trabalho, eles serão executados quando o nó for reiniciado.

- **Refazer imagem do nó** ([REST][rest_reimage] | [.NET][net_reimage])

	Esse procedimento reinstala o sistema operacional no nó. Assim como ocorre com a reinicialização de um nó, as tarefas de inicialização e de preparação de trabalho são executadas novamente depois que a imagem do nó é refeita.

- **Remover o nó do pool** ([REST][rest_remove] | [.NET][net_remove])

	Às vezes, é necessário remover completamente o nó do pool.

- **Desabilitar o agendamento de tarefas no nó** ([REST][rest_offline] | [.NET][net_offline])

	Isso efetivamente coloca o nó "offline" para que nenhuma tarefa adicional seja atribuída a ele, mas permite que o nó permaneça em execução e no pool. Isso o habilita a continuar a investigar a causa das falhas sem perder os dados da tarefa com falha e sem que o nó cause falhas de tarefas adicionais. Por exemplo, você pode desabilitar o agendamento de tarefas no nó e fazer logon remotamente para examinar os logs de eventos do nó ou solucionar outros problemas. Após concluir a investigação, você pode colocar o nó online novamente habilitando o agendamento de tarefas ([REST][rest_online], [.NET][net_online]) ou executar uma das outras ações discutidas acima.

> [AZURE.IMPORTANT] Com cada ação acima (reinicializar, refazer imagem, remover, desabilitar o agendamento da tarefas) é possível especificar como as tarefas atualmente em execução no nó serão manipuladas quando você executar a ação. Por exemplo, ao desabilitar o agendamento de tarefas em um nó com a biblioteca de cliente .NET do Lote, você pode especificar um valor de enumeração [DisableComputeNodeSchedulingOption][net_offline_option] para indicar se deseja **Terminar** tarefas em execução, **Recolocar tarefas na fila** para o agendamento em outros nós ou permitir que a execução das tarefas seja concluída antes de executar a ação (**TaskCompletion**).

## Próximas etapas

- Crie seu primeiro aplicativo do Lote seguindo as etapas em [Introdução à Biblioteca do Lote do Azure para .NET](batch-dotnet-get-started.md)
- Baixe e compile o projeto de exemplo do [Gerenciador do Lote][batch_explorer_project] a ser usado durante o desenvolvimento de soluções do Lote. Usando o Gerenciador do Lote, você pode executar o seguinte e muito mais:
  - Monitorar e manipular pools, trabalhos e tarefas em sua conta do Lote
  - Baixe `stdout.txt`, `stderr.txt` e outros arquivos de nós
  - Criar usuários em nós e baixar arquivos RDP para logon remoto

[1]: ./media/batch-api-basics/node-folder-structure.png

[about_cloud_services]: ../cloud-services/cloud-services-choose-me.md
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies

[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

<!---HONumber=AcomDC_0518_2016-->