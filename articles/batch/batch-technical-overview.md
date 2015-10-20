<properties
	pageTitle="Visão geral técnica do Azure Batch | Microsoft Azure"
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
	ms.topic="get-started-article"
	ms.date="07/13/2015"
	ms.author="danlep"/>


# Visão técnica geral do Lote do Azure
O Lote do Azure ajuda você a executar aplicativos paralelos em larga escala e de computação de alto desempenho (HPC) na nuvem. É um serviço de plataforma que fornece o plano de trabalho e o dimensionamento automático de uma coleção gerenciada de máquinas virtuais (VMs) para executar os trabalhos. Usando o serviço Lote, você pode configurar as cargas de trabalho em lotes para executar no Azure sob demanda ou de acordo com um planejamento, sem precisar se preocupar com a complexidade da configuração e gerenciamento de um cluster HPC, de máquinas virtuais ou de um agendador de trabalhos.

>[AZURE.NOTE]Para usar o Lote, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/).


## Casos de uso

O Batch usa a elasticidade e a escala da nuvem para ajudá-lo com o *processamento em lotes* ou *computação em lotes* - executando programaticamente grandes volumes de tarefas similares. Um programa ou script de linha de comando usa um conjunto de arquivos de dados como entrada, processa os dados em uma série de tarefas e produz um conjunto de arquivos de saída. Os arquivos de saída podem ser o resultado final ou uma etapa intermediária em um fluxo de trabalho maior.

Computação de lote é um padrão comum para as organizações que processam, transformam e analisam grandes volumes de dados, seja em um agendamento ou sob demanda. Ela inclui o processamento de final do ciclo, como o relatório de risco diário dos bancos ou uma folha de pagamento que deve ser feita segundo uma agenda. Ela também inclui os aplicativos em grande escala de negócios, ciência e engenharia, que normalmente precisam de ferramentas e recursos de uma grade ou cluster de computação. Os aplicativos incluem aplicativos HPC tradicionais como simulações de dinâmica de fluidos, bem como cargas de trabalho especializadas nos campos, desde a criação de conteúdo digital para serviços financeiros a pesquisas de ciências da vida.

O Batch funciona bem com aplicativos ou cargas de trabalho intrinsecamente paralelos (às vezes chamados de "excessivamente paralelos") que se prestam para execução como tarefas paralelas em vários computadores, como as VMs de computação gerenciadas pelo serviço Batch.

![Tarefas paralelas][parallel]

**Figura 1. Tarefas paralelas sendo executadas em vários computadores**

Os exemplos incluem:

* Modelagem de riscos financeiros
* Renderização e processamento de imagens
* Codificação e transcodificação de mídia
* Análise de sequência genética
* Teste de software

Você também pode usar o Lote para realizar cálculos paralelos com uma etapa de redução ao término, além de outras cargas de trabalho paralelas mais complexas.

>[AZURE.NOTE]Neste momento, você só pode executar cargas de trabalho do Windows Server no Lote. Além disso, o Lote atualmente não oferece suporte a aplicativos de interface de troca de mensagens (MPI).

## Cenários do desenvolvedor

O Lote dá suporte a cenários de desenvolvedor diferentes para ajudá-lo a configurar e executar suas cargas de trabalho paralelas em larga escala com o serviço de Lot. Esses cenários usam APIs para criar e gerenciar pools de VMs (nós de computação) e agendar os trabalhos e tarefas executadas neles. Consulte [Noções básicas de API do Lote do Azure](batch-api-basics.md) para obter mais informações sobre os conceitos do Lote.

Cenários típicos de desenvolvedor do Lote são apresentados nas seções a seguir.

### Escalar horizontalmente uma carga de trabalho paralela

Use a API do Lote para escalar horizontalmente trabalhos intrinsecamente paralelos, como renderização de imagem em um pool de até milhares de núcleos de computação. Em vez de configurar um cluster de cálculo ou escrever códigos para colocar em fila e agendar os trabalhos e mover os dados de entrada e saída necessários, você pode automatizar o agendamento de grandes trabalhos de computação e dimensionar um pool de VMs de computação para executar esses trabalhos. Você pode escrever aplicativos clientes ou front-ends para executar trabalhos e tarefas sob demanda, em uma agenda, ou como parte de um fluxo de trabalho maior gerenciado por ferramentas como [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

A Figura 2 mostra um fluxo de trabalho simplificado para enviar um aplicativo para um pool do Lote em que ele é distribuído para processamento.

![Fluxo de trabalho de itens de trabalho][work_item_workflow]

**Figura 2. Escalar horizontalmente uma carga de trabalho paralela no Lote**

1.	Carregue arquivos de entrada (como dados de origem ou imagens) necessários para um trabalho em uma conta de armazenamento do Azure. Esses arquivos devem estar na conta de armazenamento para que o serviço Batch possa acessá-los. O serviço do Lote carrega arquivos em nós de computação quando as tarefas são executadas.
2.	Carregue na conta de armazenamento os arquivos binários dependentes. Os arquivos binários incluem o programa executado pela tarefa e os assemblies dependentes. Esses arquivos também devem ser acessados no armazenamento e são carregados nos nós de computação.
3.	Crie um pool de nós de computação, especificando propriedades como o tamanho da VM e o sistema operacional executado. Você também pode definir como o número de nós no pool é dimensionado para cima ou para baixo em resposta à carga de trabalho. Quando uma tarefa é executada, um nó desse pool é atribuído a ela.
4.	Defina um trabalho para execução no pool.
5.	Adicione tarefas ao trabalho. Cada tarefa usa o programa que você carregou para processar informações de um arquivo carregado por você.
6.	Execute o aplicativo e monitore os resultados da saída.


### Habilite pela nuvem um aplicativo de computação intensiva

Você pode usar a API de aplicativos de visualização do Lote para encapsular um aplicativo existente para que seja executado como um serviço em um pool de nós de computação que o Lote gerencia em segundo plano. O aplicativo poderia ser aquele executado hoje em estações de trabalho cliente ou em um cluster de computação. Você pode desenvolver o serviço para que os usuários possam descarregar o trabalho de pico para a nuvem ou executar seu trabalho totalmente na nuvem. A estrutura dos aplicativos do Lote manipula a movimentação de arquivos de entrada e saída, a divisão de trabalhos em tarefas, trabalho e processamento de tarefas e persistência de dados.

>[AZURE.IMPORTANT]O Azure oferece apenas a API de aplicativos do Lote no formulário de visualização. Você só deve desenvolver com ele para projetos de teste ou prova de conceito. Recursos dos aplicativos essenciais do Lote são integrados à API do Lote em futuras versões do serviço.

A Figura 3 mostra um fluxo de trabalho básico para publicar um aplicativo usando a API de aplicativos do Lote e, em seguida, permitir que um usuário envie trabalhos para o aplicativo.

![Fluxo de trabalho de publicação do aplicativo][app_pub_workflow]

**Figura 3. Fluxo de trabalho para publicar e executar um aplicativo com o Batch Apps**

1.	Prepare uma **imagem de aplicativo** - um arquivo zip contendo os executáveis de seus aplicativos existentes e quaisquer arquivos de suporte de que eles precisem. Estes executáveis podem ser os mesmos executados por você em um cluster ou farm de servidores tradicional.
2.	Crie um arquivo zip do **assembly em nuvem** que invoca e expede suas cargas de trabalho para o serviço Batch. Ele contém dois componentes:

	a. **Divisor de trabalho** - divide um trabalho em tarefas que podem ser processadas independentemente. Por exemplo, em um cenário de animação, o divisor de trabalho faria com que um trabalho de renderização de filme fosse dividido em quadros individuais.

	b. **Processador de tarefa** - chama o executável do aplicativo para uma determinada tarefa. Por exemplo, em um cenário de animação, o processador de tarefa invocaria um programa de renderização para renderizar o único quadro especificado pela tarefa.

3.	Use a API de aplicativos do Lote ou ferramentas de desenvolvedor para carregar os arquivos zip preparados nas duas etapas anteriores para uma conta de armazenamento do Azure. Esses arquivos devem estar na conta de armazenamento para que o serviço Batch possa acessá-los. Isso geralmente é feito uma vez por aplicativo, por um administrador de serviços.
4.	Forneça um modo de enviar trabalhos para o serviço de aplicativo habilitado no Azure. Isso pode ser um plug-in no aplicativo da interface do usuário, um portal da Web ou um serviço autônomo como parte do seu sistema back-end.

	Para executar um trabalho:

	a. Carregue os arquivos de entrada (como dados de origem ou imagens) específicos do trabalho do usuário. Esses arquivos devem estar na conta de armazenamento para que o serviço Batch possa acessá-los.

	b. Envie um trabalho com os parâmetros e lista de arquivos necessários.

	c. Monitore o andamento do trabalho usando as APIs ou o portal do Batch Apps.



## <a id="BKMK_Account">Conta do Batch</a>
Você precisa criar uma ou mais **contas do Lote** exclusivas para usar e desenvolver com o serviço do Lote. Todas as solicitações feitas ao serviço do Lote devem ser autenticadas usando o nome da conta e sua chave de acesso.

Você pode criar uma conta do Lote e gerenciar as chaves de acesso para a conta no portal de Visualização do Azure ou com os [cmdlets do PowerShell do Lote](batch-powershell-cmdlets-get-started.md).

Para criar uma conta do Batch no portal:

1. Entre no [Portal de Visualização do Azure](https://portal.azure.com).

2. Clique em **Novo** > **Computação** > **Marketplace** > **Tudo** e, em seguida, digite *Lote* na caixa de pesquisa.

	![Lote no Marketplace][marketplace_portal]

3. Clique em **Serviço do Lote** nos resultados da pesquisa e em **Criar**.

4. Na folha **Nova Conta em Lotes**, insira as seguintes informações:

	a. Em **Nome da Conta**, insira um nome exclusivo a ser usado na URL da conta do Batch.

	>[AZURE.NOTE]O nome da conta do Batch deve ser exclusivo no Azure, conter entre 3 e 24 caracteres e usar somente números e letras minúsculas.

	b. Clique em **grupo de recursos** para selecionar um grupo de recursos existente para a conta ou criar um novo.

	c. Se você tiver mais de uma assinatura, clique em **Assinatura** para selecionar uma assinatura disponível onde a conta será criada.

	d. Em **Local**, selecione uma região do Azure na qual o Lote está disponível.

	![Criar uma conta do Batch][account_portal]

5. Clique em **Criar** para concluir a criação da conta.


Depois que a conta é criada, você pode achá-la no portal para gerenciar chaves de acesso e outras configurações. Por exemplo, clique no ícone de chave para gerenciar as chaves de acesso.

![Chaves da conta do Lote][account_keys]

## Recursos adicionais

* [Introdução à Biblioteca do Azure Batch para .NET](batch-dotnet-get-started.md)
* [Ferramentas e bibliotecas de desenvolvimento do Azure Batch](batch-development-libraries-tools.md)
* [Referência da API REST do Azure Batch](http://go.microsoft.com/fwlink/p/?LinkId=517803)
* [Referência da API REST do Azure Batch Apps](http://go.microsoft.com/fwlink/p/?LinkId=517804)

[parallel]: ./media/batch-technical-overview/parallel.png
[marketplace_portal]: ./media/batch-technical-overview/marketplace_batch.PNG
[account_portal]: ./media/batch-technical-overview/batch_acct_portal.png
[account_keys]: ./media/batch-technical-overview/account_keys.PNG
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png
[app_pub_workflow]: ./media/batch-technical-overview/app_pub_workflow.png

<!---HONumber=Oct15_HO3-->