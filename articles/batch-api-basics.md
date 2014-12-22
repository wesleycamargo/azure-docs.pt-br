
<properties title="API basics for Azure Batch" pageTitle="Noções básicas de API para Azure Batch" description="Concepts to introduce developers to the Azure Batch APIs and Batch service" metaKeywords="" services="batch" solutions="" documentationCenter=".NET" authors="yidingz, karran.batta" videoId="" scriptId="" manager="timlt" />

<tags ms.service="batch" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="big-compute" ms.date="10/27/2014" ms.author="yidingz, karran.batta" />


<!--The next line, with one pound sign at the beginning, is the page title--> 
# Noções básicas de API para Azure Batch

O serviço Azure Batch fornece uma estrutura de agendamento de trabalhos para a computação distribuída e dimensionável. O serviço Batch mantém um conjunto de máquinas virtuais que estão localizados em diferentes clusters e data centers no Azure. O serviço Batch realiza a computação distribuída executando um ou mais programas, seja sob demanda ou então agendando-os para serem executados em um momento específico, em uma coleção especificada dessas VMs. O serviço Batch gerencia essas VMs para executar suas tarefas de computação de acordo com as especificações, restrições e requisitos de recursos fornecidos por você.

Ao usar o serviço Batch, você pode eliminar a necessidade de escrever código para enfileiramento, agendamento, alocação e gerenciamento de recursos de computação. Isso permite que você se concentre no aplicativo específico e não precise se preocupar com a complexidade do gerenciamento de recursos e agendamento de trabalho na plataforma subjacente. Isso também permite ao serviço Batch otimizar a localização desses trabalhos, assim como o acesso que eles têm aos dados que precisam processar.

A seguir, alguns dos cenários que podem ser habilitados pelo uso do serviço Batch:

- Processamento paralelo computacionalmente intenso

- Limpeza diária de arquivos

- Processamento em lotes

Você pode aprender mais sobre o serviço Batch lendo as seções a seguir:

- [Recursos do serviço Batch ](#resource)

- [Fluxo de trabalho do serviço Batch](#workflow)

- [Arquivos e diretórios](#file)

- [Dimensionamento de aplicativos](#scaling)

- [Certificados para aplicativos](#cert)

- [Prioridade de agendamento](#scheduling)

- [Configurações de ambiente para tarefas](#environment)

## <a name="resource"></a> Recursos do serviço Batch

Quando usa o serviço Batch, você tira proveito dos seguintes recursos:

- [Conta](#account)

- [Máquina virtual de tarefa](#taskvm)

- [Pool](#pool)

- [Item de trabalho](#workitem)

- [Trabalho](#job)

- [Tarefa](#task)

### <a name="account"></a>Conta

Uma conta do Batch é uma entidade identificada exclusivamente no serviço Batch. Todo o processamento é feito através de uma conta do Batch. Quando você executar operações com o serviço Batch, precisará do nome da conta e da chave para essa conta. No momento, você deve contatar a equipe do Azure Batch para criar uma nova conta. Após a conta ser criada, uma chave é fornecida a você.

### <a name="taskvm"></a>Máquina virtual de tarefa

Uma máquina virtual de tarefa (TVM) é uma VM do Azure que é dedicada a uma carga de trabalho específica para o aplicativo. O tamanho de uma TVM determina o número de núcleos de CPU, a capacidade de memória e o tamanho do sistema de arquivos local alocados para a TVM. Uma TVM pode ser uma máquina virtual pequena, grande ou extragrande, conforme descrito em [Máquina Virtual e tamanhos de serviços de nuvem do Azure](http://msdn.microsoft.com/pt-br/library/dn197896.aspx).

Os tipos de programas que podem ser executados por uma TVM incluem arquivos executáveis (.exe), arquivos de comando (.cmd), arquivos de lote (.bat) e arquivos de script. Uma TVM também tem os seguintes atributos:

- Pastas do sistema de arquivos que são tanto específicas de determinadas tarefas quanto compartilhadas

- Arquivos stdout.txt e stderr.txt que são gravados em uma pasta específica de uma determinada tarefa

- Variáveis de ambiente para processamento

- Configurações de firewall que são realizadas para controlar o acesso

### <a name="pool"></a>Pool

Um pool é uma coleção de TVMs em que o aplicativo é executado. O pool pode ser criado por você ou o serviço Batch cria automaticamente o pool quando você especifica o trabalho a ser feito. Você pode criar e gerenciar um pool que atenda às necessidades do aplicativo. Um pool pode ser usado somente pela conta do Batch na qual ele foi criado. Uma conta do Batch pode ter mais de um pool.

A toda TVM que é adicionada a um pool, é atribuído um nome exclusivo e um endereço IP associado. Quando uma TVM é removida de um pool, ela perde as alterações feitas no sistema operacional, todos os seus arquivos locais, seu nome e seu endereço IP. Quando uma TVM deixa um pool, seu tempo de vida terminou.

Você pode configurar um pool para permitir a comunicação entre TVMs contidas nele. Se for solicitada comunicação entre TVMs em um determinado pool, o serviço Batch habilita portas com número superior a 1100 em cada TVM contida no pool. Cada TVM no pool está configurada para permitir e restringir conexões em entrada exclusivamente para esse intervalo de portas e somente por meio de outras TVMs no pool. Se o aplicativo não precisa de comunicação entre TVMs, o serviço Batch tem potencial para alocar um grande número de TVMs em diferentes clusters ou em data centers, para que o pool habilite mais processamento paralelo.

Ao criar um pool, você pode especificar os seguintes atributos:

- O tamanho de TVMs no pool.

- A família do sistema operacional e a versão que é executada nas TVMs.

- O número de desejado de TVMs que devem estar disponíveis para o pool.

- A política de dimensionamento para o pool.

- O status de comunicação das TVMs no pool.

- A tarefa inicial para as TVMs no pool.

Ao criar um pool, você pode especificar a conta de armazenamento à qual o pool deve ser associado. O serviço Batch aloca TVMs por meio dos data centers com melhor conectividade de rede e capacidade de largura de banda para a conta de armazenamento especificada. Isso permite que as cargas de trabalho acessem os dados com mais eficiência.

### <a name="workitem"></a>Item de trabalho

Um item de trabalho especifica como a computação é executada nas TVMs contidas em um pool. Um item de trabalho inclui os seguintes itens de configuração:

1.O programa que executa o processamento na TVM.

2.Os parâmetros de linha de comando para o programa.

3.A prioridade da computação.

4.A agenda da computação, que inclui definir se a computação deve ocorrer novamente.

Um item de trabalho é sempre atribuído a um pool, e o pool pode já existir ou pode ser criado automaticamente quando o item de trabalho é criado.

### <a name="job"></a>Trabalho

Um trabalho é uma instância em execução de um item de trabalho e consiste em um conjunto de tarefas. O serviço Batch instancia um trabalho com base na configuração do item de trabalho. O trabalho usa TVMs do pool que está associado ao item de trabalho.

### <a name="task"></a>Tarefa

Uma tarefa é uma unidade de computação que está associada a um trabalho e é executada em uma TVM. Uma tarefa usa os seguintes recursos:

- O programa que foi especificado no item de trabalho.

- Os arquivos de recursos que contêm os dados a serem processados. Esses arquivos são copiados automaticamente para o TVM do armazenamento de blob. Para obter mais informações, consulte Arquivos e diretórios.

- As configurações de ambiente que são requeridas pelo programa. Para obter mais informações, consulte as Configurações de ambiente para tarefas.

- As restrições sob as quais a computação deve ocorrer. Por exemplo, o tempo máximo pelo qual a tarefa pode ser executada, o número máximo de vezes que se deve tentar novamente executar uma tarefa se ela não funcionar e o tempo máximo pelo qual os arquivos no diretório de trabalho são mantidos.

Além das tarefas que você pode definir para realizar computação em uma TVM, você pode usar as seguintes tarefas especiais fornecidas pelo serviço Batch:

- [Tarefa de inicialização](#starttask)

- [Tarefa do gerenciador de trabalho](#jobmanagertask)

#### <a name="starttask"></a>Tarefa de inicialização

Você pode configurar o sistema operacional de TVMs em um pool, associando uma tarefa de inicialização ao pool. Instalar software e iniciar processos em segundo plano são algumas das ações que uma tarefa de inicialização pode executar. A tarefa de inicialização será executada sempre que uma TVM for iniciada, sem interrupção enquanto essa TVM permanecer no pool.

Uma tarefa de inicialização é definida pela adição de uma seção XML ao corpo da solicitação para a operação de Adicionar Pool. O exemplo a seguir mostra uma definição básica de uma tarefa de inicialização:

	{
		"commandLine":"mypoolsetup.exe",
		"resourceFiles":
		[
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"mypoolsetup.exe"
			},
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp2.exe"
			}
		],
		"maxTaskRetryCount":0
	}


#### <a name="jobmanagertask"></a>Tarefa do gerenciador de trabalho

Uma tarefa do gerenciador de trabalho é iniciada antes de todas as outras tarefas. Uma tarefa de gerenciador de trabalho oferece os seguintes benefícios:

- É criada automaticamente pelo serviço Batch quando o trabalho é criado.

- Ela é agendada antes de outras tarefas no trabalho.

- A TVM associada a ela é a última a ser removida de um pool quando o pool está sendo reduzido.

- Ela tem a prioridade mais alta quando precisa ser reiniciada. Se não houver um TVM ocioso disponível, o serviço Batch pode encerrar uma das tarefas em execução no pool para liberar espaço para a tarefa em questão ser executada.

- Seu término pode ser vinculado ao término de todas as tarefas existentes no trabalho.

Uma tarefa de gerenciador de trabalho, parte de um determinado trabalho, não tem prioridade sobre tarefas em outros trabalhos. Entre diferentes trabalhos, somente as prioridades de nível de trabalho são observadas. Uma tarefa do gerenciador de trabalho é definida pela adição de uma seção XML ao corpo da solicitação para a operação Adicionar item de trabalho. O exemplo a seguir mostra uma definição básica de uma tarefa de gerenciador de trabalho:

	{
		"name":"jmTask",
		"commandLine":"myapp1.exe",
		"resourceFiles":
		[
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp1.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp1.exe"
			},
			{
				"blobSource":"http://account.blob.core.windows.net/container/myapp2.exe?st=2013-08-09T08%3a49%3a37.0000000Z&se=2013-08-10T08%3a49%3a37.0000000Z&sr=c&sp=d&si=YWJjZGTVMZw%3d%3d&sig= %2bSzBm0wi8xECuGkKw97wnkSZ%2f62sxU%2b6Hq6a7qojIVE%3d",
				"filePath":"myapp2.exe"
			}
		],
		"taskConstraints":
		{
			"maxWallClockTime":"PT1H",
			"maxTaskRetryCount":0,
			"retentionTime":"PT1H"
		},
		"killJobOnCompletion":true,
		"runElevated":false,
		"runExclusive":true
	}




## <a name="workflow"></a>Fluxo de trabalho do serviço Batch

Você precisa de uma conta de Batch para usar esse serviço, e você usa vários recursos do serviço para agendar a computação. Você usa o seguinte fluxo de trabalho básico ao criar um cenário de computação distribuído com o serviço Batch:

1.Carregue em uma conta de armazenamento do Azure os arquivos que você deseja usar no cenário computacional distribuído. Esses arquivos devem estar na conta de armazenamento para que o serviço Batch possa acessá-los. O serviço Batch carrega-os em uma TVM quando a tarefa é executada.

2.Carregue na conta de armazenamento os arquivos binários dependentes. Os arquivos binários incluem o programa executado pela tarefa e os assemblies dependentes. Esses arquivos também devem ser acessados no armazenamento e são carregados na TVM.

3.Crie um pool de TVMs. Você pode, quando o pool é criado, atribuir o tamanho da máquina virtual de tarefa a utilizar. Quando uma tarefa é executada, uma TVM desse pool é atribuída a ela.

4.Crie um item de trabalho. Um trabalho é criado automaticamente quando você cria um item de trabalho. Um item de trabalho permite que você gerencie um trabalho de tarefas.

5.Adicione tarefas ao item de trabalho. Cada tarefa usa o programa que você carregou para processar informações de um arquivo que você carregou.

6.Monitore os resultados da saída.

## <a name="files"></a>Arquivos e diretórios

Cada tarefa tem um diretório de trabalho sob o qual ela cria zero ou mais diretórios e arquivos para armazenar o programa que é executado por uma tarefa, os dados processados por uma tarefa e a saída do processamento realizado por uma tarefa. Esses diretórios e arquivos ficam disponíveis para uso por outras tarefas durante a execução de um trabalho. Todas as tarefas, arquivos e diretórios em um TVM pertencem a uma conta de usuário único.

O serviço Batch expõe uma parte do sistema de arquivos em um TVM, como o diretório raiz. O diretório raiz do TVM está disponível para uma tarefa por meio da variável de ambiente WATASK\_TVM\_ROOT\_DIR. Para obter mais informações sobre como usar variáveis de ambiente, consulte Configurações de ambiente para tarefas.

O diretório raiz contém os seguintes subdiretórios:

- **Tarefas** - esse local é onde são armazenados todos os arquivos que pertencem a tarefas executadas na TVM. Para cada tarefa, o serviço Batch cria um diretório de trabalho com caminho exclusivo no formato %WATASK\_TVM\_ROOT\_DIR%/tasks/Nome_do_item_de_trabalho/nome_do_trabalho/nome_da_tarefa/. Esse diretório fornece acesso de leitura/gravação à tarefa. A tarefa pode criar, ler, atualizar e excluir arquivos nesse diretório, e esse diretório é mantido com base na restrição RetentionTime especificada para a tarefa.

- **Compartilhado** - esse local é um diretório compartilhado para todas as tarefas contidas na conta. Na TVM, o diretório compartilhado fica em %WATASK\_TVM\_ROOT\_DIR%/shared. Esse diretório fornece acesso de leitura/gravação à tarefa. A tarefa pode criar, ler, atualizar e excluir arquivos contidos nesse diretório.

- **Start** - esse local é usado por uma tarefa de inicialização como seu diretório de trabalho. Todos os arquivos que são baixados pelo serviço Batch para executar a tarefa de inicialização também são armazenados nesse diretório. Na TVM, o diretório de inicialização é %WATASK\_TVM\_ROOT\_DIR%/start. A tarefa pode criar, ler, atualizar e excluir arquivos contidos nesse diretório, que pode ser usado pelas tarefas de inicialização para configurar o sistema operacional.

Quando uma TVM é removida do pool, todos os arquivos armazenados na TVM também são removidos.

## <a name="scaling"></a>Dimensionamento de aplicativos

O aplicativo pode ser automaticamente dimensionado com facilidade, seja aumentando-o ou reduzindo-o, para acomodar a computação que você precisa. Você pode ajustar dinamicamente o número de TVMs em um pool de acordo com as estatísticas referentes ao uso de recursos e à carga de trabalho atuais. Também é possível otimizar o custo geral de execução do aplicativo, configurando-o para ser dimensionado automaticamente. Você pode especificar as configurações de dimensionamento para um pool quando ele é criado e poderá atualizar essa configuração, a qualquer momento.

Você especifica o dimensionamento automático de um aplicativo usando um conjunto de fórmulas de dimensionamento. As fórmulas que podem ser usadas para determinar o número de TVMs que estão no pool para o próximo intervalo de dimensionamento. Por exemplo, você precisa enviar um grande número de tarefas para que sejam agendadas em um pool. Você pode atribuir ao pool fórmula de dimensionamento que especifica o tamanho desse pool, com base no número atual de tarefas pendentes e na taxa de conclusão das tarefas. O serviço Batch avalia periodicamente a fórmula e redimensiona o pool com base na carga de trabalho.

Uma fórmula pode basear-se nas seguintes métricas:

- **Métricas de tempo** - com base em estatísticas coletadas a cada cinco minutos, durante o número de horas especificado.

- **Métricas de recurso** - com base no número de TVMs e no uso da CPU, largura de banda e memória.

- **Métricas de tarefa** - com base no status das tarefas, como Ativa, Pendente e Concluída.

Para obter mais informações sobre o dimensionamento automático de um aplicativo, consulte Configurar o dimensionamento automático de máquinas virtuais de tarefa.

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
  WATASK_PORTRANGE_LOW
  WATASK_PORTRANGE_HIGH
</td>
<td>
  O intervalo de portas (inclusive em ambas as extremidades) que a tarefa pode usar para comunicação quando a comunicação dentro do pool está habilitada.
</td>
</table>


**Observação** 

Não é possível substituir essas variáveis definidas pelo sistema.

Você pode recuperar o valor de configurações de ambiente usando a operação Obter Tarefa.
