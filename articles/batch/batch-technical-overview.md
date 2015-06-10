<properties
	pageTitle="Visão técnica geral do Lote do Azure"
	description="Saiba mais sobre os conceitos, os fluxos de trabalho e cenários do serviço Lote do Azure"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="danlep"/>


#Visão técnica geral do Lote do Azure
Lote Azure ajuda a executar em larga escala paralelo e alto desempenho aplicativos HPC (computação) na nuvem. É um serviço de plataforma que fornece o plano de trabalho e o dimensionamento automático de uma coleção gerenciada de máquinas virtuais (VMs) para executar os trabalhos. Usando o serviço de lote, você pode configurar as cargas de trabalho em lotes para ser executado no Azure sob demanda ou em um agendamento e não se preocupar com a complexidade de agendamento de trabalhos e gerenciar VMs na plataforma subjacente.

>[AZURE.NOTE]O Batch está em Visualizar. Para usar em lotes, você precisa de uma conta do Azure e você precisa inscrever-se para a visualização de lote. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/).


## Casos de uso

Lote usa a elasticidade e a escala da nuvem para ajudá-lo com *processamento em lote* ou *lote computação* -executar um grande volume de tarefas semelhantes para obter alguns desejado de resultados. Um programa ou script de linha de comando usa um conjunto de arquivos de dados como entrada, processa os dados em uma série de tarefas e produz um conjunto de arquivos de saída. Os arquivos de saída podem ser o resultado final ou uma etapa intermediária em um fluxo de trabalho maior.

Computação de lote é um padrão comum para as organizações que processam, transformam e analisam grandes volumes de dados, seja em um agendamento ou sob demanda. Ela inclui o processamento de final do ciclo, como o relatório de risco diário dos bancos ou uma folha de pagamento que deve ser feita segundo uma agenda. Ela também inclui os aplicativos em grande escala de negócios, ciência e engenharia, que normalmente precisam de ferramentas e recursos de uma grade ou cluster de computação. Os aplicativos incluem aplicativos HPC tradicionais como simulações de dinâmica de fluidos, bem como cargas de trabalho especializadas em campos como design automotivo, exploração de petróleo e gás, pesquisas de ciências biológicas e criação de conteúdo digital.

O Batch funciona bem com aplicativos ou cargas de trabalho intrinsecamente paralelos (às vezes chamados de "excessivamente paralelos") que se prestam para execução como tarefas paralelas em vários computadores, como as VMs de computação gerenciadas pelo serviço Batch. Consulte a Figura 1.

![Tarefas paralelas][parallel]

**Figura 1. Tarefas paralelas executados em vários computadores**

Os exemplos incluem:

* Modelagem de riscos financeiros
* Renderização e processamento de imagens
* Codificação e transcodificação de mídia
* Análise de sequência genética
* Teste de software

Você também pode usar o Batch para realizar cálculos paralelos com uma etapa de redução ao término, além de outras cargas de trabalho paralelas não acíclicas mais complexas.

>[AZURE.NOTE]A Visualização do Batch atualmente não oferece suporte a aplicativos de interface de troca de mensagens (MPI).

## Cenários do desenvolvedor

As APIs do Batch baseadas em REST oferecem suporte a dois cenários de desenvolvedor para ajudá-lo a configurar e executar suas cargas de trabalho em lotes com o serviço Batch:

1. **Distribuir computações como workitems** -usar o *lote* APIs para criar e gerenciar um conjunto flexível de computação VMs e especificar o trabalho será realizado. Isso lhe dá controle total sobre os recursos e requer que o cliente gerenciar o pipeline de execução tarefa - por exemplo, com um Gerenciador de fluxo de trabalho ou um meta-Agendador, que pode ser implementado usando as APIs de REST do lote ou, opcionalmente, um recurso de Gerenciador de trabalho do item de trabalho. Em vez de configurar um cluster de computação ou escrever código para colocar em fila e agendar os trabalhos, você pode automatizar o agendamento de trabalhos de computação e dimensionar, para mais ou menos, um pool de VMs de computação para executar esses trabalhos. Como parte da especificação de itens de trabalho, defina todas as dependências e definir a movimentação de arquivos de entrada e saídas.

2. **Publicar e executar aplicativos com o serviço de lote** - o *lote aplicativos* APIs funcionam em um nível superior, ajudando você a dispor de um aplicativo existente para que seja executado como um serviço em um pool de VMs gerenciadas em segundo plano pelo lote. O aplicativo pode ser aquele executado hoje em estações de trabalho cliente ou em um cluster de computação. A estrutura do Batch Apps cuida da movimentação de arquivos de saída e entrada, da execução do trabalho, do gerenciamento de trabalhos e da persistência de dados. O Batch Apps também permite a você modelar tarefas segundo o modo como os dados são particionados e para múltiplas etapas em um trabalho. Estão incluídos uma API baseada em REST e o portal do Batch Apps, o qual pode ser acessado pelo Portal de Gerenciamento do Azure e ajuda a monitorar os trabalhos enviados por você.


## Conceitos de lote

As seções a seguir resumem os conceitos principais para trabalhar com o serviço Batch e APIs. Para obter mais informações, consulte [Noções básicas de API para Azure lote](batch-api-basics.md).

* [Conta de lote](#BKMK_Account)
* [Máquinas virtuais de tarefa e pools](#BKMK_TVM)
* [Itens de trabalho, trabalhos e tarefas](#BKMK_Workitem)
* [Arquivos e diretórios](#BKMK_Files)

### <a id="BKMK_Account">Conta de lote</a>
Você precisa de um único **conta de lote** para trabalhar com o serviço de lote. Todas as solicitações feitas ao serviço de lote devem ser autenticadas usando o nome da conta e sua chave de acesso.

Você pode criar uma conta de lote e gerenciar chaves de acesso para a conta no portal do Azure ou com o [cmdlets do PowerShell do lote](batch-powershell-cmdlets-get-started.md).

Para criar uma conta de lote no portal:

1. Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em **novo**, clique em **de computação**, clique em **serviço de lote**, e, em seguida, clique em **criação rápida**. ![Criar uma conta de lote][account_portal]

3. Insira as seguintes informações:

	* Em **nome da conta**, insira um nome exclusivo para usar na URL da conta de lote.
	* Em **região**, selecione uma região na qual o lote está disponível.
	* Se você tiver mais de uma assinatura, na **assinatura**, selecione uma assinatura disponível que será cobrada pelo uso do lote.


### <a id="BKMK_TVM">Máquinas virtuais de tarefa e pools</a>

Um **máquina virtual de tarefa** (TVM) é uma VM do Azure que o serviço de lote se dedica à execução de uma determinada tarefa para seu aplicativo - o como um arquivo executável (.exe) ou, no caso de aplicativos de lote, um ou mais programas a partir de uma imagem do aplicativo. Ao contrário de uma VM do Azure típico, você não provisionar ou gerenciar um TVM diretamente; em vez disso, o serviço de lote cria e gerencia TVMs como um **pool** de VMs configuradas da mesma forma, como mostrado na Figura 2.

![Pool e TVMs][TVM_pool]

**Figura 2. Um pool de TVMs**

Se você usar as APIs de lote, você pode criar um pool diretamente ou criá-lo automaticamente quando você especifica o trabalho a ser feito. Se você usar as APIs de Batch Apps, um pool será criado automaticamente quando você executar o aplicativo do Batch habilitado para a nuvem.


Os atributos de um pool incluem:

* Um [tamanho](http://msdn.microsoft.com/library/azure/dn197896.aspx) para os TVMs
* O sistema operacional executado nas TVMs
* O número máximo de TVMs
* Uma política de escalonamento para o pool - uma fórmula com base no uso de recursos e na carga de trabalho atuais, que ajusta dinamicamente o número de TVMs que processam tarefas
* Se as portas de firewall estão habilitadas nas TVMs para permitir a comunicação dentro do pool
* Um certificado que é instalado nas TVMs - por exemplo, para autenticar o acesso a uma conta de armazenamento
* Uma tarefa de inicialização para as TVMs, para operações de execução única como instalação de aplicativos ou download de dados usados por tarefas

>[AZURE.NOTE]Atualmente uma TVM só pode executar os sistemas operacionais Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 SP1.

Um pool pode ser usado somente pela conta do Batch na qual ele foi criado. Uma conta do Batch pode ter mais de um pool.

Cada TVM em um pool é atribuído um nome exclusivo e um endereço IP associado. Quando uma TVM é removida de um pool, ela perde as alterações feitas ao sistema operacional, seus arquivos locais, seu nome e endereço IP. Quando uma TVM deixa um pool, seu tempo de vida terminou.


### <a id="BKMK_Workitem">Itens de trabalho, trabalhos e tarefas</a>

Um **workitem** é um modelo que especifica como um aplicativo será executado em TVMs em um pool. Um **trabalho** é um item de trabalho agendado e poderá ocorrer uma vez ou ocorrer novamente. Um trabalho consiste em uma coleção de **tarefas**. A Figura 3 mostra as relações básicas.

![Item de trabalho, trabalho e tarefas][job_task]

**Figura 3. Um item de trabalho, trabalho e tarefas**

Dependendo das APIs que você usa para desenvolver com o lote, você precisará gerenciar mais ou menos detalhes sobre itens de trabalho, trabalhos e tarefas.

* Se você desenvolver um aplicativo com as APIs de lote, você precisa definir os itens de trabalho, trabalhos e tarefas e configurar os pools TVM que executam as tarefas programaticamente.

* Se você integrar um aplicativo cliente usando as ferramentas e APIs do Batch Apps, você pode usar componentes que automaticamente dividem um trabalho em tarefas, processam as tarefas e mesclam os resultados das tarefas individuais aos resultados do trabalho final. Quando você envia a carga de trabalho para o serviço de lote, a estrutura de aplicativos de lote gerencia os trabalhos e executa as tarefas nos TVMs.



### <a id="BKMK_Files">Arquivos e diretórios</a>

Um arquivo é uma unidade de dados usada como entrada para uma tarefa de trabalho. Uma tarefa pode ter zero, um ou mais arquivos de entrada associados a ela. O mesmo arquivo pode ser usado em várias tarefas, por exemplo, para as tarefas em um trabalho de renderização de filme, podem existir modelos e texturas comumente usadas. Para tarefas em um trabalho de análise de dados, os arquivos podem ser um conjunto de observações ou medidas.

Existe uma pasta de trabalho para cada tarefa que contém os dados de entrada que processa e os dados de saída que ele cria. Esses arquivos e diretórios estão disponíveis para uso por outras tarefas enquanto um trabalho é executado. Todas as tarefas, arquivos e diretórios em um TVM pertencem a uma conta de usuário único.

Novamente, dependendo das APIs usam com lote, você precisará gerenciar mais ou menos detalhes sobre os arquivos de entrada e saídos para seus trabalhos e tarefas. Se você estiver desenvolvendo com as APIs de lote, especifique essas dependências e movimentos de arquivo explicitamente. Com Batch Apps, a estrutura cuida da maioria desses detalhes para você.

## Fluxo de trabalho de itens de trabalho
Figura 4 mostra um como enviar uma solicitação para um pool de onde ele é distribuído para processamento. Isso usa a API de lote.

![Fluxo de trabalho de itens de trabalho][work_item_workflow]

**Figura 4. Fluxo de trabalho para distribuir itens de trabalho em VMs em pool**

1.	Carregue arquivos de entrada (como dados de origem ou imagens) necessários para um trabalho em uma conta de armazenamento do Azure. Esses arquivos devem estar na conta de armazenamento para que o serviço Batch possa acessá-los. O serviço Batch carrega-os em uma TVM quando a tarefa é executada.
2.	Carregue na conta de armazenamento os arquivos binários dependentes. Os arquivos binários incluem o programa executado pela tarefa e os assemblies dependentes. Esses arquivos também devem ser acessados no armazenamento e são carregados na TVM.
3.	Crie um pool de TVMs, especificando o tamanho dos TVMs no pool, o sistema operacional executado nelas e outras propriedades. Quando uma tarefa é executada, uma TVM desse pool é atribuída a ela.
4.	Crie um item de trabalho. Um trabalho será criado automaticamente quando você cria um item de trabalho. Um item de trabalho permite que você gerencie um trabalho de tarefas.
5.	Adicione tarefas ao trabalho. Cada tarefa usa o programa que você carregou para processar informações de um arquivo carregado por você.
6.	Execute o aplicativo e monitore os resultados da saída.

## Fluxo de trabalho para publicar e executar aplicativos
A Figura 5 mostra um fluxo de trabalho básico para publicar um aplicativo usando a API do Batch Apps e, em seguida, enviar trabalhos para o aplicativo habilitado pelo Batch.

![Fluxo de trabalho de publicação do aplicativo][app_pub_workflow]

**Figura 5. Fluxo de trabalho para publicar e executar um aplicativo com aplicativos de lote**

1.	Preparar um **imagem do aplicativo** -um arquivo zip de seus executáveis de aplicativos existentes e quaisquer arquivos de suporte que eles precisam. Estes executáveis podem ser os mesmos executados por você em um cluster ou farm de servidores tradicional.
2.	Criar um arquivo zip do **nuvem assembly** que invocam e expedir suas cargas de trabalho para o serviço de lote. Ele contém dois componentes que estão disponíveis via SDK:

	a. **Divisão de trabalho** – um trabalho é dividido em tarefas que podem ser processadas de forma independente. Por exemplo, em um cenário de animação, o divisor de trabalho faria com que um trabalho de renderização de filme fosse dividido em quadros individuais.

	b. **o processador de tarefa** – invoca o aplicativo executável para uma determinada tarefa. Por exemplo, em um cenário de animação, o processador de tarefa invocaria um programa de renderização para renderizar o único quadro especificado pela tarefa.

3.	Use as APIs do Batch Apps ou ferramentas de desenvolvedor para carregar os arquivos zip preparados nas duas etapas anteriores para uma conta de armazenamento do Azure. Esses arquivos devem estar na conta de armazenamento para que o serviço Batch possa acessá-los. Isso geralmente é feito uma vez por aplicativo, por um administrador de serviços.
4.	Forneça um modo de enviar trabalhos para o serviço de aplicativo habilitado no Azure. Isso pode ser um plug-in no aplicativo da interface do usuário, um portal da Web ou um serviço autônomo como parte do seu sistema back-end. Há exemplos disponíveis com o SDK para demonstrar diversas opções.

	Para executar um trabalho:

	a. Carregue os arquivos de entrada (como dados de origem ou imagens) específicos do trabalho do usuário. Esses arquivos devem estar na conta de armazenamento para que o serviço Batch possa acessá-los.

	b. Envie um trabalho com os parâmetros e lista de arquivos necessários.

	c. Monitore o andamento do trabalho usando as APIs ou o portal do Batch Apps.

	d. Baixe as saídas.

## Recursos adicionais

* [Começar com a biblioteca de lote do Azure para .NET](batch-dotnet-get-started.md)
* [Ferramentas e bibliotecas de desenvolvimento de lote do azure](batch-development-libraries-tools.md)
* [Referência da API REST em lotes do Azure](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [Referência da API REST de aplicativos de lote do Azure](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[TVM_pool]: ./media/batch-technical-overview/TVM_pool.png
[job_task]: ./media/batch-technical-overview/job_task.png
[account_portal]: ./media/batch-technical-overview/account_portal.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png

<!---HONumber=GIT-SubDir-->