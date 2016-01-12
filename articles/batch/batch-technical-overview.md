<properties
	pageTitle="Noções básicas do serviço Lote do Azure | Microsoft Azure"
	description="Saiba mais sobre o uso do serviço Lote do Azure para cargas de trabalho paralelas e HPC em larga escala."
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
	ms.date="11/19/2015"
	ms.author="danlep"/>

# Noções básicas de Lote do Azure

O Lote do Azure ajuda você a executar aplicativos paralelos em larga escala e de computação de alto desempenho (HPC) na nuvem. É um serviço de plataforma que agenda o trabalho de computação intensiva para executar em uma coleção gerenciada de máquinas virtuais (nós de computação) e que pode dimensionar os recursos de computação para atender às necessidades do trabalho. Com o serviço em Lotes, você define de forma programática recursos de computação do Azure e trabalhos em lotes em larga escala que são executados sob demanda ou em um agendamento, e não precisa configurar e gerenciar manualmente um cluster HPC, VMs individuais, redes virtuais ou um agendador de trabalhos.

## Casos de uso

O Lote é um serviço gerenciado para *processamento em lotes* ou *computação em lotes*, executando grandes volumes de tarefas semelhantes para obter o resultado desejado. A computação em lote é um padrão comum para as organizações que processam, transformam e analisam grandes volumes de dados, seja em um agendamento ou sob demanda, com exemplos em campos desde serviços financeiros até engenharia.

O Lote funciona bem com aplicativos ou cargas de trabalho intrinsecamente paralelos (às vezes chamados de "excessivamente paralelos") que se prestam para execução como tarefas paralelas em vários computadores. Consulte a Figura 1.

![Tarefas paralelas][parallel]

**Figura 1. Tarefas paralelas sendo executadas em vários computadores**

Os exemplos incluem:

* Modelagem de riscos financeiros
* Renderização e processamento de imagens
* Codificação e transcodificação de mídia
* Análise de sequência genética
* Teste de software

O lote também pode realizar cálculos paralelos com uma etapa de redução no final, além de cargas de trabalho HPC mais complexas, como aplicativos de interface de transmissão de mensagens (MPI).

>[AZURE.NOTE]Neste momento, o Lote só oferece suporte a cargas de trabalho executadas em máquinas virtuais baseadas no Windows Server.

Para obter uma comparação do Lote com outras opções de solução HPC no Azure, confira [Soluções do Lote e HPC](batch-hpc-solutions.md).

## Desenvolvendo com o Lote

Desenvolva com as APIs do Lote para criar e gerenciar pools de VMs e para agendar os trabalhos e as tarefas executadas neles. Escreva aplicativos clientes ou front-ends para executar trabalhos e tarefas sob demanda, em uma agenda, ou como parte de um fluxo de trabalho maior gerenciado por ferramentas como o [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

Consulte [Visão geral do recurso Lote do Azure](batch-api-basics.md) para saber mais sobre os conceitos do Lote.

### Contas necessárias

+ [Conta e assinatura do Azure](http://azure.microsoft.com/pricing/free-trial/) - Se não tiver uma conta, você poderá ativar os **benefícios de assinante MSDN** ou inscrever-se para uma [avaliação gratuita](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

+ **Conta do Lote** -Use o nome e a URL de uma conta do Lote e uma chave de acesso como credenciais quando fizer chamadas de API do Lote. Todos os recursos do Lote, como nós de computação, pools, trabalhos e tarefas estão associados a uma conta do Lote. Uma maneira de criar uma conta do Lote e de gerenciar chaves de acesso para a conta é usar o [portal do Azure](batch-account-create-portal.md).

+ **Conta de armazenamento** - Para a maioria dos cenários do Lote, você precisará de uma conta de armazenamento do Azure para armazenar suas entradas e saídas de dados e os scripts ou executáveis executados nos nós de computação. Para criar uma conta de armazenamento, confira [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).

### Bibliotecas e ferramentas de desenvolvimento do Lote

Use essas ferramentas e bibliotecas do .NET com o Visual Studio para desenvolver e gerenciar aplicativos do Lote do Azure.

+ [Biblioteca .NET de cliente do Lote](http://www.nuget.org/packages/Azure.Batch/) (NuGet) – Desenvolva aplicativos de cliente para executar trabalhos com o serviço em Lotes
+ [Biblioteca .NET de gerenciamento do Lote](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – Gerenciar contas do Lote
+ [Gerenciador de lotes](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) (GitHub) – Crie esse aplicativo GUI para navegar, acessar e atualizar recursos em uma conta do Lote, incluindo trabalhos e tarefas, nós de computação e pools, além de arquivos em nó de computação. Consulte a [postagem de blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).


## Cenário: escalar horizontalmente uma carga de trabalho paralela

Um cenário comum com as APIs do Lote envolve a expansão do trabalho intrinsecamente paralelo, como a renderização de imagem em um conjunto de nós de computação, fornecendo a milhares de núcleos de computação.

A Figura 2 mostra um fluxo de trabalho que usa um aplicativo cliente .NET do Lote para executar uma carga de trabalho paralela com o Lote.


![Fluxo de trabalho de itens de trabalho][work_item_workflow]

**Figura 2. Escalar horizontalmente uma carga de trabalho paralela no Lote**

1.	Carregue arquivos de entrada (como dados de origem ou imagens) necessários para um trabalho em uma conta de armazenamento do Azure. O serviço em Lotes carregará arquivos em nós de computação quando as tarefas de trabalho forem executadas.

2.	Carregue arquivos de programas ou scripts que serão executados em nós de computação na conta de armazenamento. Eles podem incluir arquivos binários e os assemblies dependentes. O serviço Lote também carregará esses arquivos nos nós de computação quando as tarefas forem executadas.

3.	Crie programaticamente um conjunto de nós de computação do Lote em sua conta do Lote, especificando propriedades como o tamanho e o sistema operacional executado. Você também pode definir como o número de nós no pool será [escalado ou reduzido verticalmente](batch-automatic-scaling.md) em resposta à carga de trabalho. Quando uma tarefa for executada, receberá um nó desse pool.

4.	Defina programaticamente um trabalho do Lote para executar a carga de trabalho no pool de nós.

5.	Adicione tarefas ao trabalho. Cada tarefa usa o programa que você carregou para processar informações de um arquivo carregado por você. Dependendo da carga de trabalho, talvez você queira executar [várias tarefas ao mesmo tempo](batch-parallel-node-tasks.md) em cada nó de computação para maximizar o uso de recursos. O Lote também oferece suporte especializado a [tarefas de preparação e tarefas de conclusão](batch-job-prep-release.md) para preparar os nós de computação antes que as tarefas agendadas sejam executadas e limpas posteriormente.

6.	Execute a carga de trabalho do Lote e monitore o andamento e os resultados. O aplicativo se comunica por HTTPS com o serviço em Lotes. Para melhorar o desempenho do aplicativo ao monitorar grandes números de pools, trabalhos e tarefas, aproveite as [técnicas eficientes para consultar](batch-efficient-list-queries.md) o serviço Lote.






## Próximas etapas

* Comece a desenvolver seu primeiro aplicativo com a [biblioteca .NET cliente do Lote](batch-dotnet-get-started.md)
* Procure os exemplos de código do Lote no [GitHub](https://github.com/Azure/azure-batch-samples)

[parallel]: ./media/batch-technical-overview/parallel.png
[work_item_workflow]: ./media/batch-technical-overview/work_item_workflow.png

<!---HONumber=AcomDC_0107_2016-->