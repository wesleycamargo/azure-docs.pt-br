<properties linkid="batch-technical-overview" urlDisplayName="" pageTitle="Visão técnica geral do Azure Batch" metaKeywords="" description="Saiba mais sobre os conceitos, fluxos de trabalho e cenários do serviço de Lote do Azure" metaCanonical="" services="batch" documentationCenter="" title="Azure Batch technical overview" authors="danlep" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="batch" ms.workload="big-compute" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/08/2014" ms.author="danlep" />


#Visão técnica geral do Azure Batch
O Azure Batch ajuda você a executar aplicativos HPC (computação de alto desempenho) em larga escala e paralelos, na nuvem, fornecendo agendamento dos trabalhos e dimensionamento automático dos recursos de computação como um serviço de plataforma. Usando o SDK do Batch e o serviço Batch, você pode configurar as cargas de trabalho em lotes para executar sob demanda ou em uma agenda, em uma coleção gerenciada de máquinas virtuais, sem precisar se preocupar com a complexidade de agendamento de trabalhos e do gerenciamento dos recursos na plataforma subjacente.
 
>[WACOM.NOTE]O Batch está em Visualizar. Para usar o Batch, você precisa de uma conta do Azure; além disso, precisa habilitar a Visualização do Batch em sua assinatura. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure](http://www.windowsazure.com/pt-br/develop/php/tutorials/create-a-windows-azure-account/). 


Este artigo fornece a você uma visão geral de:
 
* [Casos de uso](#BKMK_Scenarios)
* [Cenários do desenvolvedor](#BKMK_Approaches)
* [Conceitos do Batch](#BKMK_Entities)
* [Fluxo de trabalho por item de trabalho](#BKMK_Workflow_workitems)
* [Fluxo de trabalho para publicar e executar aplicativos](#BKMK_Workflow_cloudappss)
* [Recursos adicionais](#BKMK_Resources)

<h2 id="BKMK_Scenarios">Casos de uso</h2>

O Batch usa a elasticidade e a escala da nuvem para ajudá-lo com o *processamento em lotes* ou *computação em lotes* - executando programaticamente grandes volumes de tarefas similares. Um programa ou script de linha de comando usa um conjunto de arquivos de dados como entrada, processa os dados em uma série de tarefas e produz um conjunto de arquivos de saída. Os arquivos de saída podem ser os resultados finais ou uma etapa intermediária em um fluxo de trabalho maior.       
 
Computação de lote é um padrão comum para as organizações que processam, transformam e analisam grandes volumes de dados, seja em um agendamento ou sob demanda. Ela inclui o processamento de final do ciclo, como o relatório de risco diário dos bancos ou uma folha de pagamento que deve ser feita segundo uma agenda. Ela também inclui os aplicativos em grande escala de negócios, ciência e engenharia, que normalmente precisam de ferramentas e recursos de uma grade ou cluster de computação. Os aplicativos incluem aplicativos HPC tradicionais como simulações de dinâmica de fluidos, bem como cargas de trabalho especializadas em campos como design automotivo, exploração de petróleo e gás, pesquisas de ciências biológicas e criação de conteúdo digital. 
 
O Batch funciona bem com aplicativos ou cargas de trabalho intrinsecamente paralelos (às vezes chamados de "excessivamente paralelos") que se prestam para execução como tarefas paralelas em vários computadores, como as VMs de computação gerenciadas pelo serviço Batch. Consulte a Figura 1.  

![Parallel tasks][parallel]

**Figura 1. Tarefas paralelas sendo executadas em vários computadores**

Os exemplos incluem:

* Modelagem de riscos financeiros 
* Renderização e processamento de imagens
* Codificação e transcodificação de mídia
* Análise de sequência genética
* Teste de software

Você também pode usar o Batch para realizar cálculos paralelos com uma etapa de redução ao término, além de outras cargas de trabalho paralelas não acíclicas mais complexas. 

>[WACOM.NOTE]A Visualização do Batch atualmente não oferece suporte a aplicativos de interface de troca de mensagens (MPI).

<h2 id="BKMK_Approaches">cenários do desenvolvedor</h2>

As APIs do Batch baseadas em REST oferecem suporte a dois cenários de desenvolvedor para ajudá-lo a configurar e executar suas cargas de trabalho em lotes com o serviço Batch:
 
1. **Distribua computações como itens de trabalho** - utilize as APIs do *Batch* para criar e gerenciar um pool flexível de VMs para computação e especificar os itens de trabalho executados nessas VMs. Isso lhe dá total controle sobre os recursos e exige que o cliente gerencie o pipeline de execução de tarefas - por exemplo, com um gerenciador de fluxo de trabalho ou um meta-agendador, que pode ser implementado usando as APIs REST do Batch ou, opcionalmente, um recurso de gerenciador de trabalho do item de trabalho. Em vez de configurar um cluster de computação ou escrever código para colocar em fila e agendar os trabalhos, você pode automatizar o agendamento de trabalhos de computação e dimensionar, para mais ou menos, um pool de VMs de computação para executar esses trabalhos. Como parte da especificação de itens de trabalho, você define todas as dependências e também a movimentação de arquivos de entrada e saída. 

2. **Publique e execute aplicativos com o serviço Batch** - as APIs do *Batch Apps* oferecem um maior nível de abstração e uma pipeline para execução de trabalhos, hospedada pelo serviço Batch. Com o Batch Apps, você pode criar uma carga de trabalho em lotes como um serviço na nuvem, por meio de um aplicativo executado atualmente em estações de trabalho clientes ou em um cluster de computação. O Batch Apps ajuda você a encapsular executáveis e binários existentes e publicá-los para execução por VMs em pool, as quais o serviço de lote cria e gerencia em segundo plano. A estrutura do Batch Apps cuida da movimentação de arquivos de saída e entrada, da execução do trabalho, do gerenciamento de trabalhos e da persistência de dados. O Batch Apps também permite a você modelar tarefas segundo o modo como os dados são particionados e para múltiplas etapas em um trabalho. Estão incluídos uma API baseada em REST e o portal do Batch Apps, o qual pode ser acessado pelo Portal de Gerenciamento do Azure e ajuda a monitorar os trabalhos enviados por você.


<h2 id="BKMK_Entities">Conceitos Batch</h2>

As seções a seguir resumem os conceitos principais para trabalhar com o serviço Batch e APIs. Para obter mais informações, consulte [Noções básicas de API para o Azure Batch](http://azure.microsoft.com/pt-br/documentation/articles/batch-api-basics). 

* [Conta do Batch](#BKMK_Account)
* [Máquinas virtuais de tarefa e pools](#BKMK_TVM)
* [Itens de trabalho, trabalhos e tarefas](#BKMK_Workitem)
* [Arquivos e diretórios](#BKMK_Files)

<h3 id="BKMK_Account">Conta do Batch</h3>
Você precisa de uma **conta do Batch** exclusiva para trabalhar com o serviço Batch. Ao executar operações de API com o serviço Batch, você autentica suas solicitações com a URL da conta e uma chave de acesso associada. Você também precisa de uma conta de lote para acessar o portal do Batch Apps.
 
Você pode criar uma conta do Batch e gerenciar chaves de acesso para a conta no Portal de Gerenciamento do Azure.

Para criar uma conta do Batch:

1. Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. Clique em **Novo**, em **Computar**, em **Serviço Batch** e, por fim, clique em **Criação Rápida**.
![Create a Batch account][account_portal]

3. Insira as seguintes informações:

	* Em **Nome da Conta**, digite um nome exclusivo para usar na URL da conta do Batch.
	* Em **Região**, selecione uma região na qual o Batch está disponível.
	* Se você tiver mais de uma assinatura, em **Assinatura**, selecione uma assinatura disponível que será cobrada pelo seu uso do Batch.

 
<h3 id="BKMK_TVM">Máquinas virtuais de tarefa e pools</h3>

Uma **máquina virtual de tarefa** (TVM) é uma VM do Azure que o serviço Batch dedica à execução de uma carga de trabalho específica (tarefa) para o seu aplicativo - como um arquivo executável (.exe) ou, no caso de Batch Apps, um ou mais programas por meio de uma imagem do aplicativo. Ao contrário do que ocorre em uma VM típica do Azure, você não provisiona nem gerencia uma TVM diretamente; em vez disso, o serviço de lote cria e gerencia TVMs como um **pool** de recursos de computação configurados da mesma forma, conforme mostrado na Figura 2. 

![Pool and TVMs][TVM_pool]

**Figura 2. Um pool de TVMs**

Se você usar as APIs do Batch, você pode criar um pool diretamente ou configurar o serviço Batch para criá-lo automaticamente assim que você especifica o trabalho a ser feito. Se você usar as APIs de Batch Apps, um pool será criado automaticamente quando você executar o aplicativo do Batch habilitado para a nuvem.


Os atributos de um pool incluem:

* Um []tamanho(http://msdn.microsoft.com/library/azure/dn197896.aspx) para as TVMs 
* O sistema operacional executado nas TVMs
* O número máximo de TVMs
* Uma política de escalonamento para o pool - uma fórmula com base no uso de recursos e na carga de trabalho atuais, que ajusta dinamicamente o número de TVMs que processam tarefas
* Se as portas de firewall estão habilitadas nas TVMs para permitir a comunicação dentro do pool
* Um certificado que é instalado nas TVMs - por exemplo, para autenticar o acesso a uma conta de armazenamento
* Uma tarefa de inicialização para as TVMs, para operações de execução única como instalação de aplicativos ou download de dados usados por tarefas

>[WACOM.NOTE]Atualmente uma TVM só pode executar os sistemas operacionais Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 SP1.

Um pool pode ser usado somente pela conta do Batch na qual ele foi criado. Uma conta do Batch pode ter mais de um pool.

A toda TVM que é adicionada a um pool, é atribuído um nome exclusivo e um endereço IP associado. Quando uma TVM é removida de um pool, ela perde as alterações feitas ao sistema operacional, seus arquivos locais, seu nome e endereço IP. Quando uma TVM deixa um pool, seu tempo de vida terminou.


<h3 id="BKMK_Workitem">Itens de trabalho, trabalhos e tarefas</h3>

Um **item de trabalho** é um modelo que especifica como um aplicativo será executado nas TVMs de um pool. Um **trabalho** é uma instância agendada de um item de trabalho, e pode ocorrer somente uma vez ou ocorrer mais vezes. Um trabalho consiste em um conjunto de **tarefas**. A Figura 3 mostra as relações básicas.    
 
![Work item, job, and tasks][job_task]

**Figura 3. Um item de trabalho, trabalho e tarefas**

Dependendo das APIs que você usa para desenvolvimento com o Batch, você precisará gerenciar mais ou menos detalhes sobre itens de trabalho, trabalhos e tarefas.

* Se você desenvolver um aplicativo com as APIs de nível inferior do Batch, você precisará definir todos os itens de trabalho, trabalhos e tarefas que o serviço Batch executa e configurar os pools de TVMs que executam as tarefas.

* Se você integrar um aplicativo cliente usando as ferramentas e APIs do Batch Apps, você pode usar componentes que automaticamente dividem um trabalho em tarefas, processam as tarefas e mesclam os resultados das tarefas individuais aos resultados do trabalho final. Quando você envia a carga de trabalho para o serviço Batch, a estrutura do Batch Apps gerencia os trabalhos e executa as tarefas nos recursos de computação subjacentes. 



<h3 id="BKMK_Files">Arquivos e diretórios</h3>

Um arquivo é uma unidade de dados usada como entrada para uma tarefa de trabalho. Uma tarefa pode ter zero, um ou mais arquivos de entrada associados a ela. O mesmo arquivo pode ser usado em várias tarefas, por exemplo, para as tarefas em um trabalho de renderização de filme, podem existir modelos e texturas comumente usadas. Para tarefas em um trabalho de análise de dados, os arquivos podem ser um conjunto de observações ou medidas.

Cada tarefa tem um diretório de trabalho sob o qual ele cria diretórios e arquivos necessários para armazenar o programa que é executado por uma tarefa, os dados de entrada que são processados por uma tarefa e a saída do processamento realizado por uma tarefa. Esses arquivos e diretórios estão disponíveis para uso por outras tarefas enquanto um trabalho é executado. Todas as tarefas, arquivos e diretórios em um TVM pertencem a uma conta de usuário único.

Novamente, dependendo das APIs que você usa com o Batch, você precisará gerenciar mais ou menos detalhes sobre os locais e a movimentação de arquivos de entrada e saída para seus trabalhos e tarefas. Se você está desenvolvendo com APIs de Batch de nível inferior, você especifica essas dependências e movimentos de arquivo explicitamente. Com Batch Apps, a estrutura cuida da maioria desses detalhes para você. 

<h2 id="BKMK_Workflow_workitems"> Fluxo de trabalho de itens de trabalho</h2>
A Figura 4 mostra um fluxo de trabalho típico para distribuir computações a um pool de TVMs, pelo uso da API de Batch.

![Work items workflow][work_item_workflow]

**Figura 4. Fluxo de trabalho para distribuir itens de trabalho a VMs contidas em pool**

1.	Carregue arquivos de entrada (como dados de origem ou imagens) necessários para um trabalho em uma conta de armazenamento do Azure. Esses arquivos devem estar na conta de armazenamento para que o serviço Batch possa acessá-los. O serviço Batch carrega-os em uma TVM quando a tarefa é executada.
2.	Carregue na conta de armazenamento os arquivos binários dependentes. Os arquivos binários incluem o programa executado pela tarefa e os assemblies dependentes. Esses arquivos também devem ser acessados no armazenamento e são carregados na TVM.
3.	Crie um pool de TVMs, especificando o tamanho dos TVMs no pool, o sistema operacional executado nelas e outras propriedades. Quando uma tarefa é executada, uma TVM desse pool é atribuída a ela.
4.	Crie um item de trabalho. Um trabalho será criado automaticamente quando você criar um item de trabalho. Um item de trabalho permite que você gerencie um trabalho de tarefas.
5.	Adicione tarefas ao trabalho. Cada tarefa usa o programa que você carregou para processar informações de um arquivo carregado por você.
6.	Execute o aplicativo e monitore os resultados da saída.

<h2 id="BKMK_Workflow_cloudapps">Fluxo de trabalho para publicar e executar aplicativos </h2>
A Figura 5 mostra um fluxo de trabalho básico para publicar um aplicativo usando a API do Batch Apps e, em seguida, enviar trabalhos para o aplicativo habilitado pelo Batch.

![Application publishing workflow][app_pub_workflow]

**Figura 5. Fluxo de trabalho para publicar e executar um aplicativo com o Batch Apps**

1.	Prepare uma **imagem de aplicativo** - um arquivo zip contendo os executáveis de seus aplicativos existentes e quaisquer arquivos de suporte requeridos por eles. Estes executáveis podem ser os mesmos executados por você em um cluster ou farm de servidores tradicional.
2.	Crie um arquivo zip do **assembly em nuvem** que invocará e expedirá suas cargas de trabalho para o serviço Batch. Ele contém dois componentes que estão disponíveis via SDK:

	a. **Divisor de trabalho** - divide um trabalho em tarefas que podem ser processadas independentemente. Por exemplo, em um cenário de animação, o divisor de trabalho faria com que um trabalho de renderização de filme fosse dividido em quadros individuais.

	b. **Processador de tarefa** - chama o executável do aplicativo para uma determinada tarefa. Por exemplo, em um cenário de animação, o processador de tarefa invocaria um programa de renderização para renderizar o único quadro especificado pela tarefa. 

3.	Use as APIs do Batch Apps ou ferramentas de desenvolvedor para carregar os arquivos zip preparados nas duas etapas anteriores para uma conta de armazenamento do Azure. Esses arquivos devem estar na conta de armazenamento para que o serviço Batch possa acessá-los. Isso geralmente é feito uma vez por aplicativo, por um administrador de serviços.
4.	Forneça um modo de enviar trabalhos para o serviço de aplicativo habilitado no Azure. Isso pode ser um plug-in no aplicativo da interface do usuário, um portal da Web ou um serviço autônomo como parte do seu sistema back-end. Há exemplos disponíveis com o SDK para demonstrar diversas opções. 

	Para executar um trabalho:

	a. Carregue os arquivos de entrada (como dados de origem ou imagens) específicos do trabalho do usuário. Esses arquivos devem estar na conta de armazenamento para que o serviço Batch possa acessá-los.

	b. Envie um trabalho com os parâmetros e lista de arquivos necessários.
	
	c. Monitore o andamento do trabalho usando as APIs ou o portal do Batch Apps.
	
	d. Baixe as saídas.
	
<h2 id="BKMK_Resources">Recursos adicionais</h2>

* [Introdução à Biblioteca do Azure Batch para .NET](http://azure.microsoft.com/pt-br/documentation/articles/batch-dotnet-get-started/)
* [Ferramentas e bibliotecas de desenvolvimento do Azure Batch](http://azure.microsoft.com/pt-br/documentation/articles/batch-development-libraries-tools/)
* [Referência da API REST do Azure Batch](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [Referência da API REST do Azure Batch Apps](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[TVM_pool]: ./media/batch-technical-overview/TVM_pool.png
[job_task]: ./media/batch-technical-overview/job_task.png
[account_portal]: ./media/batch-technical-overview/account_portal.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png

<!--HONumber=35.2-->
