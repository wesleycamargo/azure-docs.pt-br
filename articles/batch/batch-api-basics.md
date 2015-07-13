<properties 
	pageTitle="Noções básicas de API para o Lote do Azure" 
	description="Conceitos para apresentar os desenvolvedores ao serviço Lote e APIs do Lote do Azure" 
	services="batch" 
	documentationCenter=".net" 
	authors="yidingzhou" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="batch" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-compute" 
	ms.date="03/19/2015" 
	ms.author="yidingz"/>

<!--The next line, with one pound sign at the beginning, is the page title-->
# Noções básicas de API para o Lote do Azure

O serviço Azure Batch fornece uma estrutura de agendamento de trabalhos para a computação distribuída e dimensionável. O serviço Batch mantém um conjunto de máquinas virtuais que estão localizados em diferentes clusters e data centers no Azure. O serviço Batch realiza a computação distribuída executando um ou mais programas, seja sob demanda ou então agendando-os para serem executados em um momento específico, em uma coleção especificada dessas VMs. O serviço Batch gerencia essas VMs para executar suas tarefas de computação de acordo com as especificações, restrições e requisitos de recursos fornecidos por você.

Ao usar o serviço Batch, você pode eliminar a necessidade de escrever código para enfileiramento, agendamento, alocação e gerenciamento de recursos de computação. Isso permite que você se concentre no aplicativo específico e não precise se preocupar com a complexidade do gerenciamento de recursos e agendamento de trabalho na plataforma subjacente. Isso também permite ao serviço Batch otimizar a localização desses trabalhos, assim como o acesso que eles têm aos dados que precisam processar.

A seguir, alguns dos cenários que podem ser habilitados pelo uso do serviço Batch:

- Processamento paralelo computacionalmente intenso

- Limpeza diária de arquivos

- Processamento em lotes

## <a name="resource"></a> Recursos do serviço de lote

Quando usa o serviço Batch, você tira proveito dos seguintes recursos:

- [Conta](#account)

- [Máquina Virtual da Tarefa](#taskvm)

- [Pool](#pool)

- [Item de trabalho](#workitem)

- [Trabalho](#job)

- [Tarefa](#task)

	- [Iniciar tarefa](#starttask)
	
	- [Trabalho ManagerTask](#jobmanagertask)

### <a name="account"></a>Conta

Uma conta do Batch é uma entidade identificada exclusivamente no serviço Batch. Todo o processamento é feito através de uma conta do Batch. Quando você executar operações com o serviço Batch, precisará do nome da conta e da chave para essa conta. Para criar uma conta de lote, consulte a seção de conta de Lote da [Visão geral do Lote do Azure][].


### <a name="taskvm"></a>Máquina Virtual da Tarefa

Uma máquina virtual de tarefa (TVM) é uma VM do Azure que é dedicada a uma carga de trabalho específica para o aplicativo. O tamanho de uma TVM determina o número de núcleos de CPU, a capacidade de memória e o tamanho do sistema de arquivos local alocados para a TVM. Uma TVM pode ser uma máquina virtual pequena, grande ou extragrande, conforme descrito em [Tamnhos do Serviço de Nuvem e da Máquina Virtual do Azure](http://msdn.microsoft.com/library/dn197896.aspx).

Os tipos de programas que podem ser executados por uma TVM incluem arquivos executáveis (.exe), arquivos de comando (.cmd), arquivos de lote (.bat) e arquivos de script. Uma TVM também tem os seguintes atributos:

- As pastas do sistema de arquivos que são tanto específicas de determinadas tarefas quanto compartilhadas. As variáveis de ambiente e a estrutura de uma pasta são criadas em cada pool de VM. A seguinte estrutura de pastas é criada com uma pasta "compartilhada" para aplicativos e dados compartilhados entre tarefas, além de uma pasta para cada tarefa.

![][1]

- Arquivos stdout.txt e stderr.txt que são gravados em uma pasta específica de uma determinada tarefa

- Variáveis de ambiente para processamento

- Configurações de firewall que são realizadas para controlar o acesso

>Acesso à VM
>
>Se o acesso a uma máquina virtual for necessário, a depuração, por exemplo, do arquivo RDP pode ser obtida e usada para acessar a máquina virtual por meio da área de trabalho remota.


### <a name="pool"></a>Pool

Um pool é uma coleção de TVMs em que o aplicativo é executado. O pool pode ser criado por você ou o serviço Batch cria automaticamente o pool quando você especifica o trabalho a ser feito. Você pode criar e gerenciar um pool que atenda às necessidades do aplicativo. Um pool pode ser usado somente pela conta do Batch na qual ele foi criado. Uma conta do Batch pode ter mais de um pool.

Criar pools de Lote do Azure além da plataforma de computação principal do Azure. Os grupos de lote fornecem alocação em larga escala, aplicativos e instalação de dados, movimentação de dados, monitoramento de integridade e dimensionamento flexível de VMs.

A toda TVM que é adicionada a um pool, é atribuído um nome exclusivo e um endereço IP associado. Quando uma TVM é removida de um pool, ela perde as alterações feitas no sistema operacional, todos os seus arquivos locais, seu nome e seu endereço IP. Quando uma TVM deixa um pool, seu tempo de vida terminou.

Você pode configurar um pool para permitir a comunicação entre TVMs contidas nele. Se for solicitada comunicação entre TVMs em um determinado pool, o serviço Batch habilita portas com número superior a 1100 em cada TVM contida no pool. Cada TVM no pool está configurada para permitir e restringir conexões em entrada exclusivamente para esse intervalo de portas e somente por meio de outras TVMs no pool. Se o aplicativo não precisa de comunicação entre TVMs, o serviço Batch tem potencial para alocar um grande número de TVMs em diferentes clusters ou em data centers, para que o pool habilite mais processamento paralelo.

Ao criar um pool, você pode especificar os seguintes atributos:

- O **tamanho das VMs** no pool.
	- O tamanho apropriado de VM deve ser escolhido, dependendo das características e requisitos do(s) aplicativo(s) que será(ão) usado(s) na VM. Normalmente o tamanho da VM será escolhido supondo que uma tarefa será executada uma vez na VM; por exemplo, se o aplicativo for de multiprocesso e quanto a memória requer determinará o tamanho mais adequado e econômico da VM. É possível ter várias tarefas atribuídas e várias instâncias do aplicativo sendo executadas em paralelo, caso em que geralmente será escolhida uma VM maior – consulte abaixo em "máximo de tarefas por VM". 
	- Todos as VMs em um pool precisam ser do mesmo tamanho. Se diferentes aplicativos devem ser executados com requisitos de sistema e/ou com carga diferente, então, devem ser criados pools separados.
	- Todos os tamanhos de VM de serviço de nuvem podem ser configurados para um pool, exceto A0.

- A família do sistema operacional e a versão que é executada nas VMs.
	- Assim como acontece com funções de trabalho, a família do sistema operacional e a versão do sistema operacional podem ser configuradas.
	- A família de sistema operacional também determina quais versões do .NET são instaladas com o sistema operacional.
	- Da mesma forma que as funções de trabalho, para a versão do sistema operacional é recomendável que "*" seja usado para que as VMs sejam atualizadas automaticamente e não haja nenhum trabalho necessário para atender as novas versões. O caso de uso principal para escolher uma versão específica do sistema operacional é garantir que a compatibilidade do aplicativo seja mantida, permitindo que seja realizado um teste de compatibilidade com versões anteriores antes de permitir que a versão seja atualizada. Uma vez validada, a versão do sistema operacional para o pool pode ser atualizada e a nova imagem do sistema operacional pode ser instalada — qualquer tarefa em execução será interrompida e colocada novamente na fila.

- O número de destino das VMs que devem estar disponíveis para o pool.

- A política de dimensionamento para o pool. Além do número de máquinas virtuais, você também pode especificar uma fórmula de dimensionamento automático para cada pool. O serviço de lote executará a fórmula para ajustar o número de VMs com base nas estatísticas de pool e no item de trabalho.

- Configuração de programação
	- A configuração padrão é para que uma tarefa possa ser executada a qualquer momento em uma VM de pool, mas há situações em que é útil ter mais de uma tarefa disponível para ser executada ao mesmo tempo em uma máquina virtual. Um exemplo disso é aumentar a utilização da VM se um aplicativo precisar aguardar pela E/S; ter mais de um aplicativo sendo executado aumentará a utilização da CPU. Outro exemplo é reduzir o número de VMs no pool. Isso pode reduzir a quantidade de cópias de dados necessários para grandes conjuntos de dados de referência. Se uma A1 seria o tamanho correto para o aplicativo, uma A4 pode ser escolhida e a configuração definida para executar até 8 tarefas ao mesmo tempo, cada uma usando um núcleo.
	- A configuração "máx. de tarefas por VM" determina o número máximo de tarefas que podem ser executadas em paralelo.
	- Uma "política de preenchimento" também pode ser especificada, o que determina se o lote preenche primeiro a VM ou se as tarefas são distribuídas ao longo de toda a VM.
 
- O status de comunicação das VMs no pool.
 	- Em grande parte dos cenários, as tarefas operam de forma independente e não precisam se comunicar com outras tarefas, mas há alguns aplicativos nos quais as tarefas se comunicarão (por exemplo, os aplicativos que usam a MPI).
	- Há uma configuração que controla se a VM será capaz de se comunicar, que é usada para configurar a infraestrutura de rede subjacente e os impactos do posicionamento da VM.

- A tarefa inicial para as TVMs no pool.

Ao criar um pool, você pode especificar a conta de armazenamento à qual o pool deve ser associado. O serviço Batch aloca TVMs por meio dos data centers com melhor conectividade de rede e capacidade de largura de banda para a conta de armazenamento especificada. Isso permite que as cargas de trabalho acessem os dados com mais eficiência.

### <a name="workitem"></a>Item de trabalho

Um item de trabalho especifica como a computação é executada nas TVMs contidas em um pool.

- Um item de trabalho pode ter um ou vários trabalhos associados a ele. Uma agenda opcional pode ser especificada para um item de trabalho, caso em que um trabalho é criado para cada ocorrência da agenda. Se nenhum agendamento for especificado, para o trabalho sob demanda, um trabalho será criado imediatamente.
- O item de trabalho especifica o pool no qual o trabalho será executado. O pool pode ser um pool existente, já criado, que é usado por muitos itens de trabalho, mas também pode ser criado um pool para cada trabalho associado ao item de trabalho ou para todos os trabalhos associados ao item de trabalho.
- Pode ser especificada uma prioridade opcional. Quando um item de trabalho é enviado com uma prioridade maior que outros itens de trabalho em andamento, as tarefas de item de trabalho com prioridade mais alta são inseridas na fila antes das tarefas de item de trabalho com prioridade inferior. As tarefas com prioridade mais baixa que já estão sendo executadas não poderão ser antecipadas.
- Pode ser especificado que as restrições serão aplicadas ao trabalho ou aos trabalhos associado(s).
	- Pode ser definido um tempo de wallclock máximo para os trabalhos. Se os trabalhos forem executados por mais tempo que o tempo de wallclock máximo especificado, o trabalho e todas as tarefas associadas a ele serão encerrados.
	- O lote do Azure pode detectar tarefas que falham e repetir as tarefas. O número máximo padrão de novas tentativas de tarefa pode ser especificado como uma restrição, incluindo a especificação de que uma tarefa é sempre repetida ou nunca é repetida. Repetir uma lista de tarefas significa que a tarefa está na fila novamente e será executada uma vez mais.
- As tarefas a serem executadas para o item de trabalho podem ser especificadas pelo cliente, da mesma forma que o item de trabalho foi criado, mas também pode ser especificada uma tarefa do Gerenciador de trabalhos. Uma tarefa de Gerenciador de Trabalhos usa a API de lote e contém o código para criar as tarefas necessárias para um trabalho com a tarefa que está sendo executada em uma das VMs do pool. As tarefas de Gerenciador de Trabalhos são tratadas especificamente pelo Lote – são colocadas na fila assim que o trabalho é criado e reiniciado, caso ele falhe por qualquer motivo. Um Gerenciador de Trabalhos é necessário para itens de trabalho com um agendamento associado, pois é a única maneira de definir as tarefas antes de o trabalho ser instanciado.

### <a name="job"></a>Trabalho

Um trabalho é uma instância em execução de um item de trabalho e consiste em um conjunto de tarefas. O serviço Batch instancia um trabalho com base na configuração do item de trabalho. O trabalho usa TVMs do pool que está associado ao item de trabalho.

### <a name="task"></a>Tarefa

Uma tarefa é uma unidade de computação que está associada a um trabalho e é executada em uma TVM. As tarefas são atribuídas a uma máquina virtual para execução ou estão na fila até que uma VM fique livre. Uma tarefa usa os seguintes recursos:

- O programa que foi especificado no item de trabalho.

- Os arquivos de recursos que contêm os dados a serem processados. Esses arquivos são copiados automaticamente para o TVM do armazenamento de blob. Para obter mais informações, consulte Arquivos e diretórios.

- As configurações de ambiente que são requeridas pelo programa. Para obter mais informações, consulte as Configurações de ambiente para tarefas.

- As restrições sob as quais a computação deve ocorrer. Por exemplo, o tempo máximo pelo qual a tarefa pode ser executada, o número máximo de vezes que se deve tentar novamente executar uma tarefa se ela não funcionar e o tempo máximo pelo qual os arquivos no diretório de trabalho são mantidos.

Além das tarefas que você pode definir para realizar computação em uma TVM, você pode usar as seguintes tarefas especiais fornecidas pelo serviço Batch:

- [Iniciar tarefa](#starttask)

- [Tarefa do Gerenciador de Trabalhos](#jobmanagertask)

#### <a name="starttask"></a>Iniciar tarefa

Você pode configurar o sistema operacional de VMs em um pool, associando uma tarefa de inicialização ao pool. Instalar software e iniciar processos em segundo plano são algumas das ações que uma tarefa de inicialização pode executar. A tarefa de inicialização será executada sempre que uma VM for iniciada, sem interrupção enquanto essa VM permanecer no pool.

Como com qualquer tarefa em Lote, uma lista de arquivos no armazenamento do Azure pode ser especificada além de uma linha de comando que é executada pelo Lote. O Lote do Azure copiará primeiro os arquivos do Armazenamento do Azure e, em seguida, executará a linha de comando. Para uma tarefa de inicialização do pool, a lista de arquivos geralmente contém os arquivos de aplicativos ou o pacote, mas ela também pode incluir dados de referência que serão usados por todas as tarefas em execução no pool de VMs. A linha de comando pode executar qualquer script do PowerShell ou robocopy, por exemplo, para copiar os arquivos de aplicativo para a pasta "compartilhada"; ela também pode executar um MSI.

Normalmente, é desejável para o lote aguardar a tarefa de inicialização ser concluída e, em seguida, considerar a VM pronta para ser atribuída a tarefas, mas isso é configurável.

Se uma tarefa de inicialização falhar para um pool de VM, o estado da VM será atualizado para refletir a falha e a VM não estará disponível para as tarefas a serem atribuídas. Uma tarefa de inicialização pode falhar se houver um problema ao copiar os arquivos especificados para a tarefa de inicialização ou se o processo da tarefa de inicialização retornar algo diferente de zero.

O fato de todas as informações necessárias para configurar a VM e instalar os aplicativos estarem declaradas significa que aumentar o número de VMs em um pool é tão simples quanto especificar o novo número necessário. O Lote tem todas as informações necessárias para configurar as VMs e prepará-las para aceitar as tarefas.

Uma tarefa de inicialização é definida pela adição de uma seção JSON ao corpo da solicitação para a operação de Adicionar Pool. O exemplo a seguir mostra uma definição básica de uma tarefa de inicialização:

	{
		“commandLine”:”mypoolsetup.exe”,
		“resourceFiles”:
		[
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”mypoolsetup.exe”
			},
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp2.exe”
			}
		],
		“maxTaskRetryCount”:0
	}

Uma interface C# tem esta aparência:

	ICloudPool pool = pm.CreatePool(poolName, targetDedicated: 3, vmSize: "small", osFamily: "3");
	pool.StartTask = new StartTask();
	pool.StartTask.CommandLine = "mypoolsetup.exe";
	pool.StartTask.ResourceFiles = new List<IResourceFile>();
	pool.StartTask.ResourceFiles.Add(new ResourceFile("http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d", "mypoolsetup.exe"));
	pool.Commit();


#### <a name="jobmanagertask"></a>Tarefa do Gerenciador de Trabalhos

Uma tarefa do gerenciador de trabalho é iniciada antes de todas as outras tarefas. Uma tarefa de gerenciador de trabalho oferece os seguintes benefícios:

- É criada automaticamente pelo serviço Batch quando o trabalho é criado.

- Ela é agendada antes de outras tarefas no trabalho.

- A TVM associada a ela é a última a ser removida de um pool quando o pool está sendo reduzido.

- Ela tem a prioridade mais alta quando precisa ser reiniciada. Se não houver um TVM ocioso disponível, o serviço Batch pode encerrar uma das tarefas em execução no pool para liberar espaço para a tarefa em questão ser executada.

- Seu término pode ser vinculado ao término de todas as tarefas existentes no trabalho.

Uma tarefa de gerenciador de trabalho, parte de um determinado trabalho, não tem prioridade sobre tarefas em outros trabalhos. Entre diferentes trabalhos, somente as prioridades de nível de trabalho são observadas. Uma tarefa do gerenciador de trabalho é definida pela adição de uma seção XML ao corpo da solicitação para a operação Adicionar item de trabalho. O exemplo a seguir mostra uma definição básica de uma tarefa de gerenciador de trabalho:

	{
		“name”:”jmTask”,
		“commandLine”:”myapp1.exe”,
		“resourceFiles”:
		[
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp1.exe”
			},
			{
				“blobSource”:”http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d”,
				“filePath”:”myapp2.exe”
			}
		],
		“taskConstraints”:
		{
			“maxWallClockTime”:”PT1H”,
			“maxTaskRetryCount”:0,
			“retentionTime”:”PT1H”
		},
		“killJobOnCompletion”:true,
		“runElevated”:false,
		“runExclusive”:true
	}




## <a name="workflow"></a>Fluxo de trabalho do serviço de Lote

Você precisa de uma conta de Batch para usar esse serviço, e você usa vários recursos do serviço para agendar a computação. Você usa o seguinte fluxo de trabalho básico ao criar um cenário de computação distribuído com o serviço Batch:

1. Carregue os arquivos que você deseja usar em seu cenário de computação distribuído para uma conta de armazenamento do Azure. Esses arquivos devem estar na conta de armazenamento para que o serviço Batch possa acessá-los. O serviço Batch carrega-os em uma TVM quando a tarefa é executada.

2. Carregue os arquivos binários dependentes para a conta de armazenamento. Os arquivos binários incluem o programa executado pela tarefa e os assemblies dependentes. Esses arquivos também devem ser acessados no armazenamento e são carregados na TVM.

3. Crie um pool de TVMs. Você pode, quando o pool é criado, atribuir o tamanho da máquina virtual de tarefa a utilizar. Quando uma tarefa é executada, uma TVM desse pool é atribuída a ela.

4. Crie um item de trabalho. Um trabalho é criado automaticamente quando você cria um item de trabalho. Um item de trabalho permite que você gerencie um trabalho de tarefas.

5. Adicione tarefas ao item de trabalho. Cada tarefa usa o programa que você carregou para processar informações de um arquivo que você carregou.

6. Monitore os resultados da saída.

## <a name="files"></a>Arquivos e diretórios

Cada tarefa tem um diretório de trabalho sob o qual ela cria zero ou mais diretórios e arquivos para armazenar o programa que é executado por uma tarefa, os dados processados por uma tarefa e a saída do processamento realizado por uma tarefa. Esses diretórios e arquivos ficam disponíveis para uso por outras tarefas durante a execução de um trabalho. Todas as tarefas, arquivos e diretórios em um TVM pertencem a uma conta de usuário único.

O serviço Batch expõe uma parte do sistema de arquivos em um TVM, como o diretório raiz. O diretório raiz do TVM está disponível para uma tarefa por meio da variável de ambiente WATASK_TVM_ROOT_DIR. Para obter mais informações sobre como usar variáveis de ambiente, consulte Configurações de ambiente para tarefas.

O diretório raiz contém os seguintes subdiretórios:

- **Tarefas** – Esse local é onde são armazenados todos os arquivos que pertencem a tarefas executadas no TVM. Para cada tarefa, o serviço de Lote cria um diretório de trabalho com o caminho exclusivo na forma de %WATASK_TVM_ROOT_DIR%/tasks/workitemName/jobName/taskName/. Esse diretório fornece acesso de leitura/gravação à tarefa. A tarefa pode criar, ler, atualizar e excluir arquivos nesse diretório, e esse diretório é mantido com base na restrição RetentionTime especificada para a tarefa.

- **Compartilhado** – Esse local é um diretório compartilhado para todas as tarefas na conta. No TVM, o diretório compartilhado é %WATASK_TVM_ROOT_DIR%/shared. Esse diretório fornece acesso de leitura/gravação à tarefa. A tarefa pode criar, ler, atualizar e excluir arquivos contidos nesse diretório.

- **Iniciar** – Esse local é usado por uma tarefa de inicialização assim como o seu diretório de trabalho. Todos os arquivos que são baixados pelo serviço Batch para executar a tarefa de inicialização também são armazenados nesse diretório. Em TVM, o diretório de inicialização é %WATASK_TVM_ROOT_DIR%/start. A tarefa pode criar, ler, atualizar e excluir arquivos contidos nesse diretório, que pode ser usado pelas tarefas de inicialização para configurar o sistema operacional.

Quando uma TVM é removida do pool, todos os arquivos armazenados na TVM também são removidos.

## <a name="lifetime"></a>Pool e a tempo de vida da VM

Uma decisão de design fundamental é quando os pools serão criados e por quanto tempo as VMs estarão disponíveis.

Em um extremo, um pool seria criado para cada trabalho quando o trabalho fosse enviado e as VMs seriam removidas à medida que a execução das tarefas fosse finalizada. Isso maximizaria a utilização, pois a máquina virtual seria alocada somente quando absolutamente necessário e desligaria assim que se tornasse ociosa. Isso significa que o trabalho deve aguardar até que a VM seja alocada, embora seja importante observar que as tarefas serão agendadas para a VM assim que estiverem disponíveis individualmente, alocadas e a tarefa inicial tiver sido concluída; ou seja, o Lote não esperará até que todas as VMs em um pool estejam disponíveis, pois isso poderia levar à subutilização.

Se houver trabalhos, é prioridade iniciar a execução imediatamente, deve ser criado um pool e a VM deve estar disponível antes que o trabalho seja enviado. As tarefas podem ser iniciadas imediatamente, mas a VM pode estar ociosa enquanto aguarda as tarefas de trabalho, dependendo da carga.

Um padrão comum para quando há uma quantidade variável de carga em andamento é ter um pool ao qual vários trabalhos são enviados, mas escalar verticalmente ou reduzir o número de VMs de acordo com a carga. Isso pode ser feito de forma reativa ou proativamente se a carga puder ser prevista.

## <a name="scaling"></a>Escalonamento de aplicativos

O aplicativo pode ser automaticamente dimensionado com facilidade, seja aumentando-o ou reduzindo-o, para acomodar a computação que você precisa. Você pode ajustar dinamicamente o número de TVMs em um pool de acordo com as estatísticas referentes ao uso de recursos e à carga de trabalho atuais. Também é possível otimizar o custo geral de execução do aplicativo, configurando-o para ser dimensionado automaticamente. Você pode especificar as configurações de dimensionamento para um pool quando ele é criado e poderá atualizar essa configuração, a qualquer momento.

Para obter uma redução no número de VMs, pode haver tarefas em execução na VM que precisam ser consideradas. Uma política de desalocação é especificada, o que determina se as tarefas em execução são interrompidas para remover a VM imediatamente ou se tarefas podem terminar antes de a VM ser removida. Definir o número de destino da VM para baixo até zero ao final de um trabalho, mas permitir a execução de tarefas para a conclusão, maximizará a utilização.

Você especifica o dimensionamento automático de um aplicativo usando um conjunto de fórmulas de dimensionamento. As fórmulas que podem ser usadas para determinar o número de TVMs que estão no pool para o próximo intervalo de dimensionamento. Por exemplo, você precisa enviar um grande número de tarefas para que sejam agendadas em um pool. Você pode atribuir ao pool fórmula de dimensionamento que especifica o tamanho desse pool, com base no número atual de tarefas pendentes e na taxa de conclusão das tarefas. O serviço Batch avalia periodicamente a fórmula e redimensiona o pool com base na carga de trabalho.

Uma fórmula pode basear-se nas seguintes métricas:

- **Métricas de tempo** – com base em estatísticas coletadas a cada cinco minutos no número de horas especificado.

- **Métricas de recurso** – com base no número de TVMs e no uso da CPU, largura de banda e memória.

- **Métricas de tarefa** – com base no status das tarefas, como Ativa, Pendente e Concluída.

Para obter mais informações sobre o dimensionamento automático de um aplicativo, consulte Configurar o dimensionamento automático de máquinas virtuais de tarefa.

>Excluir as VMs
>
>Isso geralmente não é necessário, mas é possível especificar VMs individuais para serem removidas de um pool. Se houver uma VM com suspeita de ser menos confiável, por exemplo, ela pode ser removida.

## <a name="cert"></a>Certificados para aplicativos

Você normalmente precisa usar certificados quando criptografa informações secretas. Certificados podem ser instalados em TVMs. Os segredos criptografados são passados para tarefas nos parâmetros de linha de comando ou incorporados em um dos recursos, e certificados instalados podem ser usados para descriptografá-los. Um exemplo de informação secreta é a chave para uma conta de armazenamento.

Você pode usar a operação Adicionar Certificado para adicionar um certificado a uma conta de Batch. Em seguida, você pode associar o certificado a um pool novo ou existente. Quando um certificado está associado um pool, o serviço Batch instala o certificado em cada TVM presente no pool. O serviço Batch instala os certificados apropriados quando a TVM é inicializada, antes que ela execute qualquer tarefa, o que inclui tarefas de inicialização e tarefas de gerenciador de trabalho.

## <a name="scheduling"></a>Prioridade de agendamento

Quando você cria um item de trabalho, pode atribuir a ele uma prioridade. Cada trabalho contido no item de trabalho em questão é criado com essa prioridade. O serviço Batch usa os valores de prioridade do trabalho para determinar a ordem de agendamento dos trabalhos em uma conta. Os valores de prioridade podem variar de -1000 a 1000, em que -1000 é a prioridade mais baixa e 1000 a prioridade mais alta. Você pode atualizar a prioridade de um trabalho usando a operação UpdateJob.

Em uma mesma conta, os trabalhos com prioridade mais alta têm precedência no agendamento sobre aqueles com prioridade mais baixa. Um trabalho com valor de prioridade mais alto em uma conta não tem precedência, no agendamento, sobre outro trabalho com valor de prioridade inferior em uma conta diferente.

Em pools diferentes, o agendamento de trabalhos ocorre de modo independente. Entre pools diferentes, não é garantido que um trabalho com prioridade mais alta seja agendado primeiro, caso faltem TVMs ociosas em seu pool associado. No mesmo pool, trabalhos com o mesmo nível de prioridade têm a mesma chance de ser agendados.

## <a name="environment"></a>Configurações de ambiente para tarefas

Você pode especificar configurações de ambiente que podem ser usadas no contexto de uma tarefa. Configurações de ambiente para uma tarefa de inicialização e tarefas sendo executadas em um trabalho são definidas pela adição de uma seção XML ao corpo da solicitação das operações Adicionar Tarefa ou Atualizar Tarefa.

O exemplo a seguir mostra a definição de uma configuração de ambiente:

Para toda tarefa que está agendada em um trabalho, um conjunto específico de variáveis de ambiente são definidas pelo serviço Batch. A tabela a seguir lista as variáveis de ambiente definidas pelo serviço Batch para todas as tarefas.

<table>
  <tr>
   <th>Nome de variável de ambiente
   </th>
   <th>
   Descrição
   </th>
  </tr>
 <tr>
  <td>
  WATASK_ACCOUNT_NAME
  </td>
  <td>
  O nome da conta à qual a tarefa pertence.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_WORKITEM_NAME
  </td>
  <td >O nome do item de trabalho ao qual a tarefa pertence.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_JOB_NAME
  </td>
  <td >O nome do trabalho ao qual a tarefa pertence.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TASK_NAME
  </td>
  <td >O nome da tarefa atual.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_POOL_NAME
  </td>
  <td >O nome do pool no qual a tarefa está sendo executada.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TVM_NAME
  </td>
  <td >O nome da TVM na qual a tarefa está sendo executada.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_TVM_ROOT_DIR
  </td>
  <td >O caminho completo do diretório raiz da TVM.
  </td>
 </tr>
 <tr>
  <td>
  WATASK_PORTRANGE_LOW WATASK_PORTRANGE_HIGH
</td>
<td>
  O intervalo de portas (inclusive em ambas as extremidades) que a tarefa pode usar para comunicação quando a comunicação dentro do pool está habilitada.
</td>
</table>

**Observação**

Não é possível substituir essas variáveis definidas pelo sistema.

Você pode recuperar o valor de configurações de ambiente usando a operação Obter Tarefa.

## <a name="errorhandling"></a>Tratamento de erro

###Manipulação de falha da tarefa
As falhas de tarefas se enquadram nas categorias a seguir:

- Falhas de agendamento:
	- Se os arquivos são especificados para a tarefa, a cópia de um ou mais dos arquivos pode falhar. Isso pode ocorrer porque os arquivos foram movidos, a conta de armazenamento não está mais disponível, etc.
	- Nesse caso, é definido um "Erro de agendamento" para a tarefa.
- Falhas de aplicativo:
	- O processo de tarefa especificado pela linha de comando também pode falhar. O processo é considerado com falha quando é retornado um código de saída diferente de zero.
	- Para as falhas de aplicativo, é possível configurar o Lote para repetir automaticamente a tarefa até um número especificado de vezes. 
- Falhas de restrição:
	- Uma restrição pode ser especificada para a quantidade máxima de tempo para que possa ser executada por um trabalho ou uma tarefa. Isso pode ser útil para finalizar uma tarefa que foi suspensa.
	- Quando a quantidade máxima de tempo tiver sido excedida e a tarefa estiver marcada como concluída, mas a saída de código estiver marcada como `0xC000013A` e o campo schedulingError estiver marcado como `{ category:“ServerError”, code=“TaskEnded”}`.

###Falhas de depuração de aplicativos

Um aplicativo pode produzir diagnósticos que podem ser usados para solucionar problemas. Muitas vezes os aplicativos gravarão informações para os arquivos stdout e stderr ou a saída para arquivos personalizados. Nesses casos, é fornecida uma API para obter os arquivos, especificando a tarefa ou a VM.

Também é possível fazer logon na máquina virtual do pool. Uma API retorna o arquivo RDP para uma VM, que pode ser usada para fazer logon na VM.

###Catering para problemas e falhas de tarefas

As tarefas podem falhar ou ser interrompidas devido a alguns motivos. O próprio aplicativo de tarefa pode falhar, a VM na qual a tarefa está em execução é reinicializada ou a VM é removida por um redimensionamento de pool com a política de desalocação definida para remover a VM imediatamente sem esperar que a tarefa seja concluída. Em todos os casos, a tarefa pode ser automaticamente colocada de volta na fila por Lote e executada em outra VM.

Também é possível que um problema intermitente faça com que uma tarefa falhe ou demore muito para ser executada. O tempo de execução máximo pode ser definido para uma tarefa e se o Lote excedido interromperá o aplicativo da tarefa. Atualmente, o novo enfileiramento automático de mensagens não é possível para esse caso, mas o caso pode ser detectado pelo cliente que pode enviar uma nova tarefa.

###Catering para VMs "Ruins"

Cada máquina virtual em um pool tem um nome exclusivo e a VM na qual uma tarefa é executada é incluída entre os metadados de tarefa. No caso em que houver uma máquina virtual que está causando falhas nas tarefas por algum motivo, isso pode ser determinado pelo cliente e a VM suspeita pode ser excluída do pool. Se uma tarefa estava em execução na máquina virtual que foi excluída, ela será colocada novamente na fila de forma automática e executada em outra VM.


<!--Image references-->
[1]: ./media/batch-api-basics/batch-api-basics-01.png

[Visão geral do Lote do Azure]: batch-technical-overview.md

<!---HONumber=62-->