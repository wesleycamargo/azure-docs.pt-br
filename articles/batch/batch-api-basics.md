<properties
	pageTitle="Visão geral de recursos do Lote do Azure para desenvolvedores | Microsoft Azure"
	description="Conheça os recursos do serviço de Lote e suas APIs do ponto de vista de um desenvolvedor."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="09/08/2016"
	ms.author="marsma"/>

# Visão geral do recurso de Lote para desenvolvedores

Nesta visão geral dos componentes principais do serviço de Lote do Azure, vamos abordar os recursos do serviço primário que os desenvolvedores do Lote podem usar para criar soluções de computação paralela em grande escala.

Se você está desenvolvendo um aplicativo computacional distribuído, um serviço que envia chamadas diretas à API [REST de Lote][batch_rest_api] ou usando um dos [SDKs de Lote](batch-technical-overview.md#batch-development-apis), usará muitos dos recursos analisados a seguir.

> [AZURE.TIP] Para obter uma introdução de nível superior do serviço de Lote, consulte os [Fundamentos do Lote do Azure](batch-technical-overview.md).

## Fluxo de trabalho de serviço do Lote

O seguinte fluxo de trabalho de alto nível é típico de quase todos os aplicativos e serviços que usam o serviço de Lote para o processamento de cargas de trabalho paralelas:

1. Carregue os **arquivos de dados** que você deseja processar para uma conta do [Armazenamento do Azure][azure_storage]. O Lote inclui suporte interno para acessar o armazenamento de Blobs do Azure, e as tarefas podem baixar esses arquivos para [nós de computação](#compute-node) quando são executadas.

2. Carregue os **arquivos de aplicativo** que as tarefas executarão. Esses arquivos podem ser binários ou scripts e suas dependências e são executados pelas tarefas em seus trabalhos. As tarefas podem baixar esses arquivos de sua conta de armazenamento ou você pode usar o recurso dos [pacotes de aplicativos](#application-packages) de lote para a implantação e o gerenciamento dos aplicativos.

3. Crie um [pool](#pool) de nós de computação. Quando você cria um pool, pode especificar o número de nós de computação para o pool, seu tamanho e o sistema operacional. Quando cada tarefa no trabalho é executada, ela é atribuída para ser executada em um de nós no pool.

4. Crie um [trabalho](#job). Um trabalho gerencia uma coleção de tarefas. Você associa cada trabalho a um pool específico no qual as tarefas do trabalho serão executadas.

5. Adicione [tarefas](#task) ao trabalho. Cada tarefa executa o aplicativo ou script que você carregou para processar os arquivos de dados que ele baixa de sua conta do Armazenamento. À medida que cada tarefa é concluída, pode carregar sua saída para o Armazenamento do Azure.

6. Monitore o andamento dos trabalhos e recupere a saída de tarefas do Armazenamento do Azure.

As seções a seguir discutem esses e outros recursos do Lote que habilitarão que seu cenário de computação distribuída.

> [AZURE.NOTE] Você precisará de uma [Conta do lote](batch-account-create-portal.md) para usar o serviço de Lote. Além disso, quase todas as soluções usarão uma conta de [Armazenamento do Azure][azure_storage] para o armazenamento e a recuperação dos arquivos. Atualmente, o Lote suporta apenas o tipo da conta de armazenamento **de finalidade geral**, como descrito na etapa 5 de [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).

## Recursos do serviço de lote

Alguns dos recursos a seguir - contas, nó de computação, pool, trabalhos e tarefa - são necessários para todas as soluções que usam o serviço de Lote. Outros, como agendas de trabalho e pacotes de aplicativos, são recursos úteis, mas opcionais.

- [Conta](#account)
- [Nó de computação](#compute-node)
- [Pool](#pool)
- [Trabalho](#job)

  - [Agendas de trabalho](#scheduled-jobs)

- [Tarefa](#task)

  - [Iniciar tarefa](#start-task)
  - [Tarefa do Gerenciador de Trabalhos](#job-manager-task)
  - [Tarefas de preparação e liberação do trabalho](#job-preparation-and-release-tasks)
  - [MPI (tarefa de várias instâncias)](#multi-instance-tasks)
  - [Dependências da tarefa](#task-dependencies)

- [Pacotes de aplicativos](#application-packages)

## Conta

Uma conta do Batch é uma entidade identificada exclusivamente no serviço Batch. Todo o processamento é feito por meio de uma conta do Lote. Quando executar operações com o serviço Lote, você precisará do nome da conta e de uma de suas chaves. Você pode criar uma [conta do Lote do Azure usando o Portal do Azure](batch-account-create-portal.md).

## Nó de computação

Um nó de computação é uma máquina virtual (VM) do Azure dedicada ao processamento de uma parte da carga de trabalho do aplicativo. O tamanho de um nó determina o número de núcleos de CPU, a capacidade da memória e o tamanho do sistema de arquivos local alocado para o nó. Você pode criar pools de nós do Windows ou do Linux usando os Serviços de Nuvem ou as imagens de Marketplace das Máquinas Virtuais. Consulte o seguinte seção [Pool](#pool) para obter mais informações sobre essas opções.

Os nós podem executar qualquer executável ou script que tenha suporte no ambiente do sistema operacional do nó. Isso inclui *.exe, *.cmd, *.bat e os scripts do PowerShell para Windows e binários, shell e scripts Python para Linux.

Todos os nós de computação no Lote também incluem:

- Uma [estrutura de pastas](#files-and-directories) padrão e as [variáveis de ambiente](#environment-settings-for-tasks) associadas disponíveis para a referência por tarefas.
- As configurações de **firewall** que são definidas para controlar o acesso.
- [Acesso remoto](#connecting-to-compute-nodes) para os nós do Windows (Remote Desktop Protocol (RDP)) e do Linux (Secure Shell (SSH)).

## Pool

Um pool é uma coleção de nós na qual seu aplicativo é executado. O pool pode ser criado manualmente por você ou automaticamente pelo serviço de Lote quando você especifica o trabalho a ser feito. Você pode criar e gerenciar um pool que atenda às exigências de recursos de seu aplicativo. Um pool pode ser usado somente pela conta do Lote na qual foi criado. Uma conta do Batch pode ter mais de um pool.

Os pools do Lote do Azure se baseiam na plataforma de computação principal do Azure. Eles fornecem alocação em larga escala, instalação de aplicativos, distribuição de dados, monitoramento de integridade e ajuste flexível do número de nós de computação em um pool ([dimensionamento](#scaling-compute-resources)).

Todos os nós adicionados a um pool recebem um nome e um endereço IP exclusivos. Quando um nó é removido de um pool, todas as alterações feitas no sistema operacional ou arquivos são perdidas e seu nome e endereço IP são liberados para o uso futuro. Quando um nó deixa um pool, seu tempo de vida termina.

Ao criar um pool, você pode especificar os seguintes atributos:

- **Sistema operacional** e **versão** do nó de computação

	Você tem duas opções ao selecionar um sistema operacional para os nós no pool: **Configuração da Máquina Virtual** e **Configuração dos Serviços de Nuvem**.

	A **Configuração de Máquina Virtual** fornece imagens Linux e Windows para nós de computação do [Marketplace de Máquinas Virtuais do Azure][vm_marketplace]. Ao criar um pool que contém nós de Configuração da Máquina Virtual, você deve especificar não apenas o tamanho dos nós, mas também a **referência da imagem da máquina virtual** e a **SKU do agente de nó** do Lote a ser instalada nos nós. Para saber mais sobre como especificar essas propriedades de pool, confira [Provisionar nós de computação do Linux em pools do Lote do Azure](batch-linux-nodes.md).

	A **Configuração de Serviços de Nuvem** fornece *apenas* nós de computação do Windows. Os sistemas operacionais disponíveis para pools de Configuração de Serviços de Nuvem são listados na [matriz de compatibilidade de SDK e versões de SO Convidado do Azure](../cloud-services/cloud-services-guestos-update-matrix.md). Ao criar um pool que contém os nós dos Serviços de Nuvem, você precisa especificar apenas o tamanho do nó e sua *Família de SOs*. Quando você crie pools de nós de computação do Windows, os Serviços de Nuvem são usados com mais frequência.

    - A *Família de SO* também determina quais versões do .NET são instaladas com o sistema operacional.
	- Assim como ocorre com as funções de trabalho nos Serviços de Nuvem, você pode especificar uma *Versão do SO* (para obter mais informações sobre as funções de trabalho, consulte a seção [Sobre os serviços de nuvem](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) na [Visão geral dos Serviços de Nuvem](../cloud-services/cloud-services-choose-me.md)).
    - Assim como ocorre com as funções de trabalho, é recomendável especificar `*` para a *Versão do SO* de forma que os nós sejam automaticamente atualizados e não haja nenhum trabalho necessário para atender as versões recém-lançadas. O caso de uso principal para selecionar uma versão específica do SO é garantir a compatibilidade dos aplicativos, permitindo que os testes de compatibilidade retroativa sejam executados antes de permitir que a versão seja atualizada. Após a validação, a *Versão do SO* para o pool pode ser atualizada e a nova imagem do SO pode ser instalada - as tarefas em execução serão interrompidas e colocadas novamente na fila.

- **Tamanho dos nós**

	Os tamanhos de nós de computação da **Configuração de Serviços de Nuvem** são listados em [Tamanhos para Serviços de Nuvem](../cloud-services/cloud-services-sizes-specs.md). O Lote dá suporte a todos os tamanhos de Serviços de Nuvem, exceto `ExtraSmall`.

	Os tamanhos de nós de computação da **Configuração de Máquina Virtual** são listados em [Tamanhos de máquinas virtuais no Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) e [Tamanhos de máquinas virtuais no Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). O Lote dá suporte a todos os tamanhos de VM do Azure, exceto `STANDARD_A0` e aqueles com armazenamento premium (série `STANDARD_GS`, `STANDARD_DS` e `STANDARD_DSV2`).

	Ao selecionar um tamanho de nó, considere as características e os requisitos dos aplicativos que serão executados nos nós de computação. Geralmente, você selecionará um tamanho de nó supondo que uma tarefa será executada no nó por vez. Para ajudar a determinar o tamanho do nó mais adequado e econômico, considere aspectos como se o aplicativo tem multithread e quanta memória ele consome. É possível ter várias tarefas e, assim, várias instâncias do aplicativo [executadas em paralelo](batch-parallel-node-tasks.md). Nesse caso, você normalmente escolherá um nó maior. Confira a seguinte seção "Política de agendamento de tarefas" para obter mais informações.

	Todos os nós em um pool têm o mesmo tamanho. Se você for executar os aplicativos com diferentes requisitos do sistema e/ou níveis de carga, deverá usar pools separados.

- **Número de nós de destino**

	Esse é o número de nós de computação que você deseja implantar no pool. Isso é conhecido como um *destino* porque, em algumas situações, o pool pode não alcançar o número desejado de nós. Um pool pode não alcançar o número desejado de nós se atingir a [cota de núcleos](batch-quota-limit.md#batch-account-quotas) de sua conta do Lote ou se houver uma fórmula de dimensionamento automático que você aplicou no pool que limita o número máximo de nós (consulte a seguinte seção “Política de dimensionamento”).

- **Política de dimensionamento**

	Além de especificar um número estático de nós, você pode escrever e aplicar uma [fórmula de dimensionamento automático](#scaling-compute-resources) em um pool. O serviço de Lote avalia periodicamente a fórmula e ajusta o número de nós no pool com base em vários parâmetros do pool, trabalho e tarefa que você pode especificar.

- **Política de agendamento de tarefas**

	A opção de configuração [máx. de tarefas por nó](batch-parallel-node-tasks.md) determina o número máximo de tarefas que podem ser executadas em paralelo em cada nó de computação no pool.

	A configuração padrão é que uma tarefa de cada vez seja executada em um nó, mas há situações em que é útil ter mais de uma tarefa em execução simultaneamente em um nó. Consulte o [cenário de exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo sobre as [tarefas de nó simultâneas](batch-parallel-node-tasks.md) para ver como você pode aproveitar as várias tarefas por nó.

	Você também pode especificar um *tipo de preenchimento* que determina se o Lote distribui as tarefas igualmente entre todos os nós de um pool ou empacota cada nó com o número máximo de tarefas antes de atribuir tarefas a outro nó.

- **Status de comunicação** de nós de computação

	Na maioria dos cenários, as tarefas operam de forma independente e não precisam comunicar-se umas com as outras. Mas pode haver alguns aplicativos nos quais as tarefas devem comunicar-se (como nos [cenários MPI](batch-mpi.md)).

	Você pode configurar um pool para permitir a comunicação entre os nós contidos nele - **comunicação entre nós**. Quando a comunicação entre nós é habilitada, os nós nos pools de Configuração dos Serviços de Nuvem podem comunicar-se uns com os outros nas portas acima de 1100 e os pools de Configuração da Máquina Virtual não restringem o tráfego em nenhuma porta.

	Observe que habilitar a comunicação entre nós também afeta a colocação dos nós nos clusters e pode limitar o número máximo de nós em um pool devido às restrições da implantação. Se seu aplicativo não precisar da comunicação entre os nós, o serviço de Lote poderá alocar um número potencialmente grande de nós para o pool a partir de vários clusters e data centers diferentes para permitir uma capacidade maior do processamento paralelo.

- **Tarefa inicial** para nós de computação

	A *tarefa inicial* opcional é executada em cada nó quando ele ingressa no pool e sempre que um nó é reiniciado ou sua imagem é refeita. A tarefa inicial é particularmente útil para preparar nós de computação para a execução de tarefas, como instalar aplicativos que as tarefas executarão nos nós de computação.

- **Pacotes de aplicativos**

	Você pode especificar [pacotes de aplicativos](#application-packages) para implantar os nós de computação no pool. Os pacotes de aplicativos fornecem uma implantação simplificada e controle de versão dos aplicativos que suas tarefas executam. Os pacotes de aplicativos que você especifica para um pool são instalados em cada nó que ingressa no pool e sempre que um nó é reinicializado ou sua imagem é recriada. No momento, os pacotes de aplicativos não têm suporte nos nós de computação do Linux.

- **Configuração de rede**

	Você pode especificar a ID de uma [rede virtual (VNet)](../virtual-network/virtual-networks-overview.md) do Azure em que nós de computação do pool devem ser criados. Os requisitos para especificar uma rede virtual para seu pool podem ser encontrados em [Adicionar um pool a uma conta][vnet] na referência de API REST do Lote.

> [AZURE.IMPORTANT] Todas as contas do Lote têm uma **cota** padrão que limita o número de **núcleos** (e, portanto, nós de computação) em uma conta do Lote. Você encontrará as cotas padrão e as instruções sobre como [aumentar uma cota](batch-quota-limit.md#increase-a-quota) (como o número máximo de núcleos em sua conta do Lote) em [Cotas e limites para o serviço Lote do Azure](batch-quota-limit.md). Se você estiver se perguntando: "Por que meu pool não alcança mais do que X nós?", essa cota de núcleos poderá ser a causa.

## Trabalho

Um trabalho é uma coleção de tarefas. Ele gerencia como a computação é realizada por suas tarefas nos nós de computação em um pool.

- O trabalho especifica o **pool** no qual o trabalho será executado. Você pode criar um novo pool para cada trabalho ou usar um pool para vários trabalhos. Você pode criar um pool para cada trabalho associado a um agendamento de trabalho ou para todos os trabalhos associados a um agendamento de trabalho.

- Você pode especificar uma **prioridade de trabalho** opcional. Quando um trabalho é enviado com uma prioridade mais alta que os trabalhos em andamento atualmente, as tarefas do trabalho com prioridade mais alta são inseridas na fila antes das tarefas dos trabalhos com prioridade mais baixa. As tarefas com prioridade mais baixa que já estão em execução não serão antecipadas.

- Você pode usar as restrições do **trabalho** para especificar certos limites para seus trabalhos:

	Você pode definir uma **hora do relógio máxima**, de modo que se um trabalho for executado por mais tempo que a hora do relógio máxima especificada, o trabalho e todas as suas tarefas serão encerrados.

	O Lote pode detectar e repetir as tarefas com falha. Você pode especificar o **número máximo de tentativas de tarefa** como uma restrição, inclusive se uma tarefa é repetida *sempre* ou *nunca*. Repetir uma tarefa significa que a tarefa é colocada na fila para ser executada novamente.

- O aplicativo do cliente pode adicionar tarefas a um trabalho ou você pode especificar uma [tarefa do gerenciador de trabalhos](#job-manager-task). Uma tarefa do gerenciador de trabalhos contém as informações necessárias para criar as tarefas necessárias para um trabalho, com a tarefa do gerenciador de trabalhos sendo executada em um de nós de computação no pool. A tarefa do gerenciador de trabalhos é tratada especificamente pelo Lote – ela é colocada na fila assim que o trabalho é criado e é reiniciada, caso falhe. Uma tarefa do gerenciador de trabalhos é *necessária* para os trabalhos criados por um [agendamento de trabalho](#scheduled-jobs), pois é a única maneira de definir as tarefas antes do trabalho ser instanciado.

- Por padrão, os trabalhos permanecem no estado ativo quando todas as tarefas no trabalho são concluídas. Você pode alterar esse comportamento para que o trabalho seja encerrado automaticamente quando todas as tarefas no trabalho forem concluídas. Defina a propriedade **onAllTasksComplete** do trabalho ([OnAllTasksComplete][net_onalltaskscomplete] no .NET no Lote) como *terminatejob* para encerrar automaticamente o trabalho quando todas as suas tarefas estiverem no estado concluído.

	Observe que o serviço de Lote considera que um trabalho *sem* tarefas tem todas as suas tarefas concluídas. Portanto, essa opção é mais comumente usada com uma [tarefa do gerenciador de trabalhos](#job-manager-task). Se você quiser usar o encerramento automático de trabalho sem um gerenciador de trabalhos, defina inicialmente a propriedade **onAllTasksComplete** de um novo trabalho como *noaction*. Depois, defina-a como *terminatejob* somente depois que você terminar de adicionar tarefas ao trabalho.

### Prioridade do trabalho

Você pode atribuir uma prioridade a trabalhos criados no Lote. O serviço Lote usa o valor da prioridade do trabalho para determinar a ordem de agendamento dos trabalhos em uma conta (isso não deve ser confundido com um [trabalho agendado](#scheduled-jobs)). Os valores de prioridade variam de -1000 a 1000, em que -1000 é a prioridade mais baixa e 1000 a mais alta. Você pode atualizar a prioridade de um trabalho usando a operação [Atualizar as propriedades de um trabalho][rest_update_job] \(REST do Lote) ou modificando a propriedade [CloudJob.Priority][net_cloudjob_priority] \(.NET do Lote).

Em uma mesma conta, os trabalhos com prioridade mais alta têm precedência no agendamento sobre aqueles com prioridade mais baixa. Um trabalho com valor de prioridade mais alto em uma conta não tem precedência no agendamento sobre outro trabalho com valor de prioridade mais baixo em uma conta diferente.

O plano de trabalho em pools é independente. Entre pools diferentes, não é garantido que um trabalho com prioridade mais alta seja agendado primeiro, caso faltem nós ociosos em seu pool associado. No mesmo pool, trabalhos com o mesmo nível de prioridade têm a mesma chance de ser agendados.

### Trabalhos agendados

Os [planos de trabalho][rest_job_schedules] o habilitam a criar trabalhos recorrentes no serviço Lote. Um plano de trabalho especifica quando executar trabalhos e inclui as especificações para os trabalhos a serem executados. Você pode especificar a duração do agendamento - quanto tempo e quando o agendamento fica em vigor – e com que frequência, durante esse período de tempo, os trabalhos devem ser criados.

## Tarefa

Uma tarefa é uma unidade de computação que está associada a um trabalho. Ela é executada em um nó. As tarefas são atribuídas a um nó para execução ou estão na fila até que um nó fique livre. Resumindo, uma tarefa executa um ou mais programas ou scripts em um nó de computação para executar o trabalho necessário.

Ao criar uma tarefa, você pode especificar:

- A **linha de comando** da tarefa. Essa é a linha de comando que executa o aplicativo ou script nos nós de computação.

	É importante observar que a linha de comando realmente não é executada em um shell. Portanto, não é possível aproveitar nativamente os recursos do shell, como a expansão da [variável do ambiente](#environment-settings-for-tasks) (isso inclui `PATH`). Para aproveitar esses recursos, você deve chamar o shell na linha de comando – por exemplo, iniciando `cmd.exe` nos nós do Windows ou `/bin/sh` no Linux:

	`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

	`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

	Se as tarefas precisarem executar um aplicativo ou script que não esteja no `PATH` do nó ou fazer referência às variáveis de ambiente, chame o shell explicitamente na linha de comando da tarefa.

- **Arquivos de recursos** que contêm os dados a serem processados. Esses arquivos são copiados automaticamente para o nó do armazenamento de Blobs em uma conta de Armazenamento do Azure de **finalidade geral** antes da linha de comando da tarefa ser executada. Para obter mais informações, consulte as seções [Tarefa inicial](#start-task) e [Arquivos e diretórios](#files-and-directories).

- As **variáveis de ambiente** que são exigidas pelo aplicativo. Para obter mais informações, consulte a seção [Configurações do ambiente para tarefas](#environment-settings-for-tasks).

- As **restrições** de acordo com as quais a tarefa deve ser executada. Por exemplo, o tempo máximo durante o qual a tarefa pode ser executada, o número máximo de vezes que uma tarefa com falha deve ser repetida e o máximo de tempo durante o qual os arquivos no diretório de trabalho da tarefa são mantidos.

- **Pacotes de aplicativos** para implantar no nó de computação no qual a tarefa está agendada para ser executada. Os [pacotes de aplicativos](#application-packages) fornecem uma implantação simplificada e controle de versão dos aplicativos que suas tarefas executam. Os pacotes de aplicativos de nível de tarefa são especialmente úteis em ambientes de pool compartilhado, em que diferentes trabalhos são executados em um pool e o pool não é excluído quando um trabalho é concluído. Se o trabalho tiver menos tarefas do que os nós no pool, pacotes de aplicativos de tarefa poderão minimizar a transferência de dados, pois o aplicativo é implantado apenas para os nós que executam tarefas.

Além das tarefas que você pode definir para realizar computação em um nó, as tarefas especiais a seguir também são fornecidas pelo serviço Lote:

- [Iniciar tarefa](#start-task)
- [Tarefa do Gerenciador de Trabalhos](#job-manager-task)
- [Tarefas de preparação e liberação do trabalho](#job-preparation-and-release-tasks)
- [MPI (Tarefas de várias instâncias)](#multi-instance-tasks)
- [Dependências da tarefa](#task-dependencies)

### Tarefa de inicialização

Associando uma **tarefa inicial** a um pool, você pode preparar o ambiente operacional de seus nós. Por exemplo, você pode executar ações como instalar os aplicativos que suas tarefas executarão e iniciar processos em segundo plano. A tarefa inicial é executada sempre que um nó inicia, contanto que ele permaneça no pool - incluindo quando o nó é adicionado pela primeira vez ao pool e quando ele é reiniciado ou sua imagem é recriada.

O principal benefício da tarefa inicial é que ela pode conter todas as informações necessárias para configurar um nó de computação e instalar os aplicativos necessários para a execução da tarefa. Assim, aumentar o número de nós em um pool é tão simples quanto especificar a nova contagem de nós de destino - o Lote já tem as informações necessárias para configurar os novos nós e prepará-los para aceitar as tarefas.

Como com qualquer tarefa de Lote do Azure, você pode especificar uma lista de **arquivos de recurso** no [Armazenamento do Azure][azure_storage], além uma **linha de comando** a ser executada. Primeiro, o Lote copia os arquivos de recurso para o nó do Armazenamento do Azure, depois, executa a linha de comando. Para uma tarefa inicial do pool, a lista de arquivos normalmente contém o aplicativo da tarefa e suas dependências.

No entanto, ela também pode incluir dados de referência a serem usados por todas as tarefas que estão em execução no nó de computação. Por exemplo, a linha de comando da tarefa inicial pode executar uma `robocopy` operação para copiar os arquivos do aplicativo (que foram especificados como arquivos de recurso e baixados para o nó) do diretório de trabalho da [tarefa inicial](#files-and-directories) para a [pasta compartilhada](#files-and-directories), em seguida, executar um MSI ou `setup.exe`.

> [AZURE.IMPORTANT] Atualmente, o Lote suporta *apenas* o tipo da conta de armazenamento **de finalidade geral**, como descrito na etapa 5 de [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md). As tarefas do Lote (incluindo as tarefas padrão, tarefas iniciais, tarefas de preparação do trabalho e tarefas de liberação do trabalho) devem especificar os arquivos de recurso que residem *somente* nas contas de armazenamento de **finalidade geral**.

Geralmente é desejável que o serviço de Lote aguarde a conclusão da tarefa inicial antes de considerar o nó pronto para ter tarefas atribuídas, mas isso pode ser configurado.

Se uma tarefa de inicialização falhar em um nó de computação, o estado do nó será atualizado para refletir a falha e o nó não estará disponível para as tarefas a serem atribuídas. Uma tarefa inicial poderá falhar se houver um problema ao copiar seus arquivos de recursos do armazenamento ou se o processo executado por sua linha de comando retornar um código de saída diferente de zero.

Se você adicionar ou atualizar a tarefa inicial para um pool *existente*, deverá reinicializar seus nós de computação para que a tarefa inicial seja aplicado aos nós.

### Tarefa do gerenciador de trabalho

Você geralmente usa uma **tarefa do gerenciador de trabalhos** para controlar e/ou monitorar a execução do trabalho — por exemplo, para criar e enviar tarefas para um trabalho, determinar as tarefas adicionais a executar e quando o trabalho é concluído. No entanto, uma tarefa do gerenciador de trabalhos não está limitada a essas atividades. É uma tarefa completa que pode executar as ações necessárias para o trabalho. Por exemplo, uma tarefa do gerenciador de trabalhos pode baixar um arquivo especificado como um parâmetro, analisar o conteúdo desse arquivo e enviar tarefas adicionais com base no conteúdo.

Uma tarefa do gerenciador de trabalho é iniciada antes de todas as outras tarefas. Ela fornece os seguintes recursos:

- Ela é criada automaticamente pelo serviço Lote quando o trabalho é criado.

- É agendada para execução antes das outras tarefas em um trabalho.

- O nó associado é o último a ser removido de um pool quando o pool está sendo reduzido.

- Seu término pode ser vinculado ao término de todas as tarefas existentes no trabalho.

- Uma tarefa do gerenciador de trabalhos tem a prioridade mais alta quando precisa ser reiniciada. Se não houver um nó ocioso disponível, o serviço de Lote poderá encerrar uma das outras tarefas em execução no pool para liberar espaço para a tarefa do gerenciador de trabalhos ser executada.

- Uma tarefa de gerenciador de trabalhos em um trabalho não tem prioridade sobre tarefas em outros trabalhos. Entre diferentes trabalhos, somente as prioridades de nível de trabalho são observadas.

### Tarefas de preparação e liberação do trabalho

O Lote fornece tarefas de preparação do trabalho para a instalação de execução pré-trabalho. As tarefas de liberação do trabalho são para a manutenção ou a limpeza pós-trabalho.

- **Tarefa de preparação do trabalho** – a tarefa de preparação do trabalho é executada em todos os nós de computação agendados para executar as tarefas, antes de qualquer outra tarefa do trabalho ser executada. É possível usar a tarefa de preparação do trabalho para copiar os dados compartilhados por todas as tarefas, mas é exclusiva para o trabalho, por exemplo.
- **Tarefa de liberação do trabalho** – quando o trabalho for concluído, a tarefa de liberação do trabalho será executada em cada nó no pool que executou pelo menos uma tarefa. É possível usar a tarefa de liberação do trabalho para excluir os dados copiados pela tarefa de preparação do trabalho ou compactar e carregar os dados do log de diagnóstico, por exemplo.

As tarefas de preparação e liberação do trabalho permitem especificar uma linha de comando a ser executada quando a tarefa é chamada. Elas oferecem recursos, como download de arquivo, execução elevada, variáveis de ambiente personalizadas, duração máxima da execução, contagem de repetição e tempo de retenção do arquivo.

Para saber mais sobre tarefas de preparação e de liberação de trabalho, consulte [Executar tarefas de preparação e de conclusão de trabalhos em nós de computação do Lote do Azure](batch-job-prep-release.md).

### Tarefa de várias instâncias

Uma [tarefa de várias instâncias](batch-mpi.md) é a que é configurada para ser executada simultaneamente em mais de um nó de computação. Com as tarefas de várias instâncias, você pode habilitar os cenários de computação de alto desempenho, que requerem um grupo de nós de computação alocados juntos para processar uma única carga de trabalho (como a Interface de Troca de Mensagens (MPI)).

Para obter uma análise detalhada sobre como executar os trabalhos da MPI no Lote usando a biblioteca .NET do Lote, confira [Usar tarefas de várias instâncias para executar os aplicativos da MPI (Interface de Troca de Mensagens) no Lote do Azure](batch-mpi.md).

### Dependências da tarefa

As [dependências de tarefas](batch-task-dependencies.md), como o nome indica, permitem especificar que uma tarefa depende da conclusão de outras tarefas antes de sua execução. Este recurso fornece suporte para situações em que uma tarefa "downstream" consome a saída de uma tarefa "upstream" - ou quando uma tarefa upstream executa alguma inicialização necessária para uma tarefa downstream. Para usar esse recurso, primeiro você deve habilitar as dependências em seu trabalho do Lote. Em seguida, para cada tarefa que dependa de outra (ou de muitas outras), especifique as tarefas das quais essa tarefa depende.

Com as dependências de tarefas, você pode configurar cenários como o seguinte:

* A *tarefaB* depende da *tarefaA* (a *tarefaB* não começará sua execução até que a *tarefaA* tenha terminado).
* A *tarefaC* depende da *tarefaA* e da *tarefaB*.
* A *tarefaD* depende de várias tarefas, como as tarefas de *1* a *10*, antes de ser executada.

Confira [Dependências de tarefas no Lote do Azure](batch-task-dependencies.md) e o exemplo de código [TaskDependencies][github_sample_taskdeps] no repositório do GitHub [azure-batch-samples][github_samples] para obter detalhes mais aprofundados sobre esse recurso.

## Configurações de ambiente para tarefas

Cada tarefa executada em um trabalho do Lote tem acesso às variáveis de ambiente definidas pelo serviço de Lote (definidas pelo serviço, como descrito na tabela abaixo) e às variáveis de ambiente personalizadas que podem ser definidas para suas tarefas. Os aplicativos e scripts executados nos nós por suas tarefas têm acesso a essas variáveis de ambiente durante a execução.

Você pode definir variáveis de ambiente personalizadas no nível de tarefa ou de trabalho populando a propriedade *configurações de ambiente* para essas entidades. Por exemplo, veja a operação [Adicionar uma tarefa a um trabalho][rest_add_task] \(API REST do Lote) ou as propriedades [CloudTask.EnvironmentSettings][net_cloudtask_env] e [CloudJob.CommonEnvironmentSettings][net_job_env] no .NET do Lote.

O aplicativo cliente ou serviço pode obter variáveis de ambiente de uma tarefa, definidas pelo serviço e personalizadas, usando a operação [Obter informações sobre uma tarefa][rest_get_task_info] \(REST do Lote) ou acessando a propriedade [CloudTask.EnvironmentSettings][net_cloudtask_env] \(.NET do Lote). Os processos em execução em um nó de computação podem acessar essas e outras variáveis de ambiente no nó, por exemplo, usando a sintaxe familiar do `%VARIABLE_NAME%` (Windows) ou `$VARIABLE_NAME` (Linux).

As seguintes variáveis de ambiente são definidas pelo serviço de Lote e estão disponíveis para acesso por suas tarefas:

| Nome de variável de ambiente | Descrição |
|---------------------------------|--------------------------------------------------------------------------|
| `AZ_BATCH_ACCOUNT_NAME` | O nome da conta à qual a tarefa pertence. |
| `AZ_BATCH_JOB_ID` | A ID do trabalho ao qual a tarefa pertence. |
| `AZ_BATCH_JOB_PREP_DIR` | O caminho completo do diretório de tarefa de preparação de trabalho no nó. |
| `AZ_BATCH_JOB_PREP_WORKING_DIR` | O caminho completo do diretório de trabalho da tarefa de preparação de trabalho no nó. |
| `AZ_BATCH_NODE_ID` | A ID do nó no qual a tarefa está em execução. |
| `AZ_BATCH_NODE_ROOT_DIR` | O caminho completo do diretório raiz no nó. |
| `AZ_BATCH_NODE_SHARED_DIR` | O caminho completo do diretório compartilhado no nó. |
| `AZ_BATCH_NODE_STARTUP_DIR` | O caminho completo do diretório da tarefa de inicialização do nó de computação no nó. |
| `AZ_BATCH_POOL_ID` | A ID do pool no qual a tarefa está em execução. |
| `AZ_BATCH_TASK_DIR` | O caminho completo do diretório de tarefa no nó. |
| `AZ_BATCH_TASK_ID` | A ID da tarefa atual. |
| `AZ_BATCH_TASK_WORKING_DIR` | O caminho completo do diretório de trabalho da tarefa no nó. |

>[AZURE.IMPORTANT] Essas variáveis de ambiente estão disponíveis somente no contexto do **usuário da tarefa**, ou seja, a conta de usuário no nó em que uma tarefa é executada. Você *não* as verá se [conectar-se remotamente](#connecting-to-compute-nodes) a um nó de computação via Remote Desktop Protocol (RDP) ou Secure Shell (SSH) e listar as variáveis de ambiente. Isso ocorre porque a conta de usuário usada para a conexão remota não é igual à conta usada pela tarefa.

## Arquivos e diretórios

Cada tarefa tem um *diretório de trabalho* em que ela cria zero ou mais arquivos e diretórios. Esse diretório de trabalho pode ser usado para armazenar o programa executado pela tarefa, os dados que ele processa e a saída do processamento executado. Todos os arquivos e diretórios de uma tarefa são pertencentes ao usuário de tarefa.

O serviço de Lote exibe uma parte do sistema de arquivos em um nó como o *diretório-raiz*. As tarefas podem acessar esse diretório-raiz referenciando a variável de ambiente `AZ_BATCH_NODE_ROOT_DIR`. Para saber mais sobre como usar as variáveis de ambiente, consulte [Configurações de ambiente para tarefas](#environment-settings-for-tasks).

O diretório raiz contém a seguinte estrutura de diretório:

![Estrutura de diretórios do nó de computação][1]

- **compartilhado** – esse diretório fornece acesso de leitura/gravação a *todas* as tarefas executadas em um nó. Qualquer tarefa executada no nó pode criar, ler, atualizar e excluir arquivos nesse diretório. As tarefas podem acessar esse diretório referenciando a variável de ambiente `AZ_BATCH_NODE_SHARED_DIR`.

- **inicialização** – esse diretório é usado por uma tarefa inicial como seu diretório de trabalho. Todos os arquivos que são baixados para o nó pela tarefa de inicialização são armazenados aqui. A tarefa inicial pode criar, ler, atualizar e excluir arquivos nesse diretório. As tarefas podem acessar esse diretório referenciando a variável de ambiente `AZ_BATCH_NODE_STARTUP_DIR`.

- **Tarefas** - um diretório é criado para cada tarefa executada no nó. É acessado referenciando a variável de ambiente `AZ_BATCH_TASK_DIR`.

	Em cada diretório de tarefas, o serviço Lote cria um diretório de trabalho (`wd`) cujo caminho exclusivo é especificado pela variável de ambiente `AZ_BATCH_TASK_WORKING_DIR`. Esse diretório oferece acesso de leitura/gravação à tarefa. A tarefa pode criar, ler, atualizar e excluir arquivos contidos nesse diretório. Esse diretório é mantido com base na restrição *RetentionTime* especificada para a tarefa.

	`stdout.txt` e `stderr.txt` – esses arquivos são gravados na pasta de tarefas durante a execução da tarefa.

>[AZURE.IMPORTANT] Quando um nó é removido do pool, *todos* os arquivos armazenados no nó são removidos.

## Pacotes de aplicativos

O recurso dos [pacotes de aplicativos](batch-application-packages.md) fornece um gerenciamento e implantação fáceis dos aplicativos para os nós de computação em seus pools. Você pode carregar e gerenciar várias versões dos aplicativos executados por suas tarefas, incluindo seus binários e arquivos de suporte. Então, você pode implantar automaticamente um ou mais desses aplicativos nos nós de computação em seu pool.

Você pode especificar os pacotes de aplicativos no nível do pool e de tarefa. Quando você especifica os pacotes de aplicativos do pool, o aplicativo é implantado para todos os nós no pool. Quando você especifica os pacotes de aplicativos de tarefa, o aplicativo é implantado apenas para nós que estão agendados para execução de pelo menos uma das tarefas do trabalho, antes que a linha de comando da tarefa seja executada.

O Lote lida com os detalhes de como trabalhar com o Armazenamento do Azure para armazenar pacotes de aplicativos e implantá-los para nós de computação. Portanto, a sobrecarga de gerenciamento e código pode ser simplificada.

Para saber mais sobre o recurso do pacote de aplicativos, confira a [Implantação de aplicativos com pacotes de aplicativos do Lote do Azure](batch-application-packages.md).

>[AZURE.NOTE] Se você adicionar pacotes de aplicativos do pool a um pool *existente*, deverá reinicializar seus nós de computação para que os pacotes de aplicativos sejam implantados nos nós.

## Tempo de vida de nó de computação e de pool

Ao projetar sua solução do Lote do Azure, você deve tomar uma decisão de design sobre como e quando os pools são criados, e por quanto tempo os nós de computação nesses pools ficarão disponíveis.

Por outro lado, você pode criar um pool para cada trabalho quando o trabalho é enviado e tem seus nós removidos assim que a execução das tarefas termina. Isso maximizaria a utilização, pois os nós serão alocados somente quando forem absolutamente necessários - e finalizariam assim que ficassem ociosos. Embora isso signifique que o trabalho deva aguardar até que os nós sejam alocados, é importante observar que as tarefas serão agendadas para os nós assim que estiverem disponíveis individualmente, alocadas e a tarefa inicial tiver sido concluída O Lote *não* aguarda até que todos os nós em um pool estejam disponíveis antes de atribuir as tarefas. Isso garante a máxima utilização de todos os nós disponíveis.

Por outro lado, se ter os trabalhos iniciados imediatamente for a prioridade mais alta, você poderá criar um pool antecipadamente e tornar seus nós disponíveis antes dos trabalhos serem enviados. Nesse cenário, as tarefas do trabalho podem começar imediatamente, mas os nós poderão ficar ociosos enquanto aguardam a atribuição das tarefas.

Uma abordagem combinada normalmente é usada para lidar com uma carga variável, mas em andamento. Você pode ter um pool para o qual vários trabalhos são enviados, mas pode aumentar ou diminuir o número de nós de acordo com a carga de trabalho (confira [Dimensionando os recursos de computação](#scaling-compute-resources) na seção a seguir). Isso pode ser feito de maneira reativa, com base na carga atual, ou proativamente, se a carga puder ser prevista.

## Dimensionando recursos de computação

Com o [dimensionamento automático](batch-automatic-scaling.md), você pode deixar que o serviço de Lote ajuste dinamicamente o número de nós de computação em um pool de acordo com a carga de trabalho e o uso de recursos atuais do cenário de computação. Isso permite reduzir o custo geral de execução do aplicativo usando apenas os recursos necessários e liberando os que você não precisa.

Você habilita o dimensionamento automático escrevendo uma [fórmula de dimensionamento automático](batch-automatic-scaling.md#automatic-scaling-formulas) e associando-a a um pool. O serviço de Lote usa a fórmula para determinar o número de nós no pool de destino para o próximo intervalo de dimensionamento (um intervalo que você pode configurar). Você pode especificar as configurações de dimensionamento automático para um pool ao criá-lo ou habilitar o dimensionamento mais tarde em um pool. Você também pode atualizar as configurações de dimensionamento em um pool com dimensionamento habilitado.

Por exemplo, talvez um trabalho exija que você envie um grande número de tarefas a serem executadas. Você pode atribuir uma fórmula de dimensionamento ao pool que ajusta o número de nós nele com base no número atual de tarefas enfileiradas e a taxa de conclusão das tarefas no trabalho. Periodicamente, o serviço de Lote avalia a fórmula e redimensiona o pool com base na carga de trabalho (adiciona nós para muitas tarefas na fila e remove os nós das tarefas sem fila ou em execução) e as outras configurações da fórmula.

Uma fórmula de dimensionamento pode basear-se nas seguintes métricas:

- As **métricas do tempo** são baseadas nas estatísticas coletadas a cada cinco minutos no número de horas especificado.

- As **métricas do recurso** são baseadas nos usos da CPU, da largura de banda e da memória, e no número de nós.

- As **métricas de tarefa** são baseadas no estado da tarefa, como *Ativa* (na fila), *Em execução* ou *Concluída*.

Quando o dimensionamento automático diminui o número de nós de computação em um pool, você deve considerar como lidar com as tarefas em execução no momento da operação de redução. Para aceitar isso, o Lote fornece uma *opção de desalocação do nó* que você pode incluir em suas fórmulas. Por exemplo, você pode especificar que as tarefas em execução sejam interrompidas imediatamente, interrompidas imediatamente e colocadas novamente na fila para execução em outro nó ou concluídas antes que o nó seja removido do pool.

Para saber mais sobre o dimensionamento automático de um aplicativo, consulte [Dimensionar automaticamente nós de computação em um pool do Lote do Azure](batch-automatic-scaling.md).

> [AZURE.TIP] Para maximizar a utilização de recursos de computação, defina o número de destino de nós como zero ao fim de um trabalho, mas permita a conclusão das tarefas em execução.

## Segurança com certificados

Normalmente, você precisa usar certificados ao criptografar ou descriptografar as informações confidenciais das tarefas, como a chave para uma [conta de Armazenamento do Azure][azure_storage]. Para dar suporte a isso, você pode instalar certificados nos nós. Os segredos criptografados são passados para tarefas por meio dos parâmetros de linha de comando ou incorporados em um dos recursos de tarefa, e os certificados instalados podem ser usados para descriptografá-los.

Você usa a operação [Adicionar certificado][rest_add_cert] \(REST do Lote) ou o método [CertificateOperations.CreateCertificate][net_create_cert] \(.NET do Lote) para adicionar um certificado a uma conta do Lote. Em seguida, você pode associar o certificado a um pool novo ou existente. Quando um certificado está associado a um pool, o serviço em lote instala o certificado em cada nó presente no pool. O serviço Lote instala os certificados apropriados quando o nó é inicializado, antes que ele execute qualquer tarefa (incluindo a tarefa inicial e a tarefa do gerenciador de trabalhos).

Se você adicionar certificados a um pool *existente*, deverá reinicializar seus nós de computação para que os certificados sejam aplicados aos nós.

## Tratamento de erros

Talvez seja necessário lidar com as falhas da tarefa e do aplicativo em sua solução do Lote.

### Manipulação de falha de tarefa
As falhas de tarefas se enquadram nestas categorias:

- **Falhas de agendamento**

	Se a transferência dos arquivos especificada para uma tarefa falhar por algum motivo, um "erro de agendamento" será definido para a tarefa.

	Os erros de agendamento podem ocorrer se os arquivos de recurso da tarefa foram movido, a conta de Armazenamento não está mais disponível ou foi encontrado outro problema que impediu a cópia bem-sucedida dos arquivos para o nó.

- **Falhas de aplicativo**

	O processo especificado pela linha de comando da tarefa também pode falhar. O processo é considerado com falha quando é retornado um código de saída diferente de zero pelo processo executado pela tarefa (consulte *Códigos de saída da tarefa* na tarefa a seguir).

	Para as falhas do aplicativo, você pode configurar o Lote para repetir automaticamente a tarefa até um número especificado de vezes.

- **Falhas de restrição**

	Você pode definir uma restrição que especifique a duração máxima da execução de um trabalho ou uma tarefa, *maxWallClockTime*. Isso pode ser útil para o encerramento de tarefas “paradas”.

	Quando o tempo máximo tiver sido excedido, a tarefa será marcada como *concluída*, mas o código de saída será definido para `0xC000013A` e o campo *schedulingError* será marcado como `{ category:"ServerError", code="TaskEnded"}`.

### Falhas de depuração de aplicativos

- `stderr` e `stdout`

	Durante a execução, um aplicativo pode produzir uma saída de diagnóstico que pode ser usada para solucionar os problemas. Conforme mencionado na seção [Arquivos e diretórios](#files-and-directories) anterior, o serviço de Lote grava a saída padrão e os erros padrão nos arquivos `stdout.txt` e `stderr.txt` no diretório da tarefa no nó de computação. Você pode usar o portal do Azure ou um dos SDKs do Lote para baixar esses arquivos. Por exemplo, você pode recuperar esses e outros arquivos para a solução de problemas usando [ComputeNode.GetNodeFile][net_getfile_node] e [CloudTask.GetNodeFile][net_getfile_task] na biblioteca .NET do Lote.

- **Códigos de saída de tarefas**

	Conforme mencionado anterior, uma tarefa é marcada como tendo falhas pelo serviço de Lote se o processo executado pela tarefa retorna um código de saída diferente de zero. Quando uma tarefa executa um processo, o Lote preenche a propriedade do código de saída da tarefa com o *código de retorno do processo*. É importante observar que o código de saída da tarefa **não** é determinado pelo serviço de Lote, mas pelo processo em si ou pelo sistema operacional no qual o processo é executado.

### Contabilidade de interrupções ou de falhas de tarefas

As tarefas podem falhar ou ser interrompidas ocasionalmente. O próprio aplicativo da tarefa pode falhar, o nó no qual a tarefa está em execução pode ser reinicializado ou o nó pode ser removido do pool durante uma operação de redimensionamento, caso a política de desalocação do pool seja definida para remover o nó imediatamente sem esperar que as tarefas sejam concluídas. Em todos os casos, a tarefa pode ser automaticamente recolocada na fila pelo Lote para a execução em outro nó.

Também é possível que um problema intermitente faça com que uma tarefa falhe ou demore muito para ser executada. Você pode definir o tempo máximo de execução de uma tarefa. Se ele for excedido, o Lote irá interromper o aplicativo da tarefa.

### Conectar-se a nós de computação

Você pode executar uma depuração e solução de problemas adicionais conectando um nó de computação remotamente. Você pode usar o portal do Azure para baixar um arquivo RDP (Remote Desktop Protocol) para os nós do Windows e obter informações da conexão SSH (Secure Shell) para os nós do Linux. Você também pode fazer isso usando as APIs do Lote, por exemplo, com o [.NET do Lote][net_rdpfile] ou o [Python do Lote](batch-linux-nodes.md#connect-to-linux-nodes).

>[AZURE.IMPORTANT] Para se conectar a um nó via RDP ou SSH, primeiro você deve criar um usuário no nó. Para fazer isso, você pode usar o portal do Azure, [adicionar uma conta de usuário a um nó][rest_create_user] usando a API REST do Lote, chamar o método [ComputeNode.CreateComputeNodeUser][net_create_user] no .NET do Lote ou chamar o método [add\_user][py_add_user] no módulo Python do Lote.

### Solução de problemas de nós de computação "inválidos"

Em situações em que algumas das tarefas falham, o aplicativo cliente ou o serviço de Lote pode examinar os metadados das tarefas com falha para identificar um nó com comportamento inadequado. Cada nó em um pool tem uma ID exclusiva, e o nó no qual uma tarefa é executada é incluído nos metadados da tarefa. Após identificar um nó com problemas, você poderá executar várias ações nele:

- **Reiniciar o nó** ([REST][rest_reboot] | [.NET][net_reboot])

	Às vezes, reiniciar o nó pode corrigir problemas latentes, como processos bloqueados ou com falha. Observe que, se o pool usar uma tarefa de inicialização ou se o trabalho usar uma tarefa de preparação de trabalho, eles serão executados quando o nó for reiniciado.

- **Refazer a imagem do nó** ([REST][rest_reimage] | [.NET][net_reimage])

	Esse procedimento reinstala o sistema operacional no nó. Assim como ocorre com a reinicialização de um nó, as tarefas de inicialização e de preparação de trabalho são executadas novamente depois que a imagem do nó é refeita.

- **Remover o nó do pool** ([REST][rest_remove] | [.NET][net_remove])

	Às vezes, é necessário remover completamente o nó do pool.

- **Desabilitar o agendamento de tarefas no nó** ([REST][rest_offline] | [.NET][net_offline])

	Isso efetivamente coloca o nó "offline" para que nenhuma tarefa adicional seja atribuída a ele, mas permite que o nó permaneça em execução e no pool. Isso permite continuar investigando mais a causa das falhas sem perder os dados da tarefa com falha - e sem que o nó cause falhas da tarefa adicionais. Por exemplo, você pode desabilitar o agendamento de tarefas no nó e [fazer logon remotamente](#connecting-to-compute-nodes) para examinar os logs de evento do nó ou solucionar outros problemas. Após concluir a investigação, você pode colocar o nó novamente online habilitando o agendamento de tarefas ([REST][rest_online] | [.NET][net_online]) ou executar uma das outras ações analisadas anteriormente.

> [AZURE.IMPORTANT] Com cada ação descrita nesta seção – reinicializar, refazer a imagem, remover e desabilitar o agendamento de tarefas – é possível especificar como as tarefas atualmente em execução no nó são lidadas quando você executa a ação. Por exemplo, ao desabilitar o agendamento de tarefas em um nó usando a biblioteca de clientes .NET do Lote, você pode especificar um valor de enumeração [DisableComputeNodeSchedulingOption][net_offline_option] para indicar se deseja **Terminar** as tarefas em execução, **Recolocar as tarefas na fila** para o agendamento em outros nós ou permitir que a execução das tarefas seja concluída antes de executar a ação (**TaskCompletion**).

## Próximas etapas

- Veja o passo a passo do aplicativo de exemplo do Lote em [Introdução à Biblioteca do Lote do Azure para .NET](batch-dotnet-get-started.md). Também há uma [versão em Python](batch-python-tutorial.md) do tutorial que executa uma carga de trabalho nos nós de computação do Linux.

- Baixe e compile o projeto de exemplo do [Gerenciador do Lote][github_batchexplorer] a ser usado durante o desenvolvimento de soluções do Lote. Usando o Gerenciador do Lote, você pode executar o seguinte e muito mais:
  - Monitorar e manipular pools, trabalhos e tarefas em sua conta do Lote
  - Baixe `stdout.txt`, `stderr.txt` e outros arquivos de nós
  - Criar usuários em nós e baixar arquivos RDP para logon remoto

- Saiba como [criar pools de nós de computação do Linux](batch-linux-nodes.md).

- Acesse o [Fórum do Lote do Azure][batch_forum] no MSDN. O fórum é um bom lugar para fazer perguntas se você está apenas aprendendo ou se é especialista no Lote.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

<!---HONumber=AcomDC_0914_2016-->