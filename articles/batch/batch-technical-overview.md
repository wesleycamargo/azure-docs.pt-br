<properties
	pageTitle="Noções básicas do serviço Lote do Azure | Microsoft Azure"
	description="Saiba mais sobre o uso do serviço Lote do Azure para cargas de trabalho paralelas e HPC em larga escala."
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/16/2016"
	ms.author="marsma"/>

# Noções básicas de Lote do Azure

O Lote do Azure o habilita a executar aplicativos paralelos em larga escala e de HPC (computação de alto desempenho) na nuvem. É um serviço de plataforma que agenda o trabalho de computação intensiva para executar em uma coleção gerenciada de máquinas virtuais e que pode dimensionar automaticamente os recursos de computação para atender às necessidades dos trabalhos.

Com o serviço em Lotes, você define os recursos de computação do Azure para executar os aplicativos em paralelo e em escala. Você pode executar trabalhos sob demanda ou agendados e não precisa criar, configurar e gerenciar manualmente um cluster HPC, máquinas virtuais individuais, redes virtuais ou uma infraestrutura complexa de agendamento de trabalhos e tarefas.

## Casos de uso para o Lote

O Lote é um serviço do Azure gerenciado que é usado para *processamento em lotes* ou *computação em lotes*, executando grandes volumes de tarefas semelhantes para obter o resultado desejado. A computação em lote é mais comumente usada por organizações que processam, transformam e analisam grandes volumes de dados regularmente.

O Lote funciona bem com cargas de trabalho e aplicativos intrinsecamente paralelos (também conhecidos como "excessivamente paralelos"). Cargas de trabalho intrinsecamente paralelas são divididas com facilidade em várias tarefas que executam o trabalho simultaneamente em vários computadores.

![Tarefas paralelas][1]<br/>

Alguns exemplos de cargas de trabalho que normalmente são processadas usando essa técnica são:

* Modelagem de riscos financeiros
* Análise de dados de clima e hidrologia
* Processamento, análise e renderização de imagens
* Codificação e transcodificação de mídia
* Análise de sequência genética
* Análise de estresse de engenharia
* Teste de software

O Lote também pode realizar cálculos paralelos com uma etapa de redução no final, além de executar cargas de trabalho HPC mais complexas, como aplicativos de [MPI (Interface de Transmissão de Mensagens)](batch-mpi.md).

Para obter uma comparação entre o Lote e outras opções de solução HPC no Azure, confira [Soluções do Lote e HPC](batch-hpc-solutions.md).

## Desenvolvendo com o Lote

Ao criar soluções que usam o Lote do Azure para processamento paralelo de carga de trabalho, você faz isso programaticamente usando as [APIs do Lote](#batch-development-apis). Com as APIs de Lote, você criar e gerenciar pools de nós de computação (máquinas virtuais) e agendar trabalhos e tarefas para serem executadas nesses nós. Um aplicativo cliente ou serviço que você cria usa as APIs de Lote para se comunicar com o serviço de Lote.

Você pode processar com eficiência cargas de trabalho em grande escala para sua organização ou fornecer um front-end de serviço a seus clientes para que eles possam executar trabalhos e tarefas, sob demanda ou de acordo com uma agenda, em um ou em centenas ou em milhares de nós. Você também pode usar o Lote como parte de um fluxo de trabalho maior, gerenciado por ferramentas como o [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md).

> [AZURE.TIP] Quando estiver pronto para se aprofundar na API do Lote e obter uma compreensão mais profunda dos recursos que ele fornece, confira a [Visão geral do recurso Lote para desenvolvedores](batch-api-basics.md).

### Contas do Azure das quais você precisará

Ao desenvolver soluções de Lote, você usará as contas a seguir no Microsoft Azure.

- [Conta e assinatura do Azure][free_account] - se ainda não tiver uma assinatura do Azure, você poderá ativar o **benefício de assinante do MSDN** ou inscrever-se para uma [conta gratuita do Azure][msdn_benefits]. Ao criar uma conta, uma assinatura padrão será criada para você.

- **Conta do Lote** - quando os aplicativos interagem com o serviço de Lote, o nome da conta, a URL da conta, e uma chave de acesso são usados como credenciais. Todos os recursos do Lote, como pools, nós de computação, trabalhos e tarefas estão associados a uma conta do Lote. Você pode [criar e gerenciar uma conta do Lote](batch-account-create-portal.md) no portal do Azure.

- **Conta de armazenamento** - o Lote inclui suporte interno para trabalhar com arquivos no [Armazenamento do Azure][azure_storage]. Quase todos os cenários de Lote usarão o Armazenamento do Azure para preparar os programas que as tarefas executam e os dados que eles processam e para o armazenamento dos dados de saída que eles geram. Para criar uma conta de Armazenamento, confira [Sobre as contas de armazenamento do Azure](./../storage/storage-create-storage-account.md).

### APIs de desenvolvimento em lotes

Os aplicativos e serviços podem emitir chamadas à API REST diretamente, usar uma ou mais das bibliotecas de cliente a seguir ou uma combinação de ambos para gerenciar recursos de computação e executar cargas de trabalho paralelas em escala usando o serviço em Lotes.

| API | Referência de API | Baixar | Exemplos de código |
| ----------------- | ------------- | -------- | ------------ |
| **REST do Lote** | [MSDN][batch_rest] | N/D | [MSDN][batch_rest] |
| **.NET do Lote** | [MSDN][api_net] | [NuGet][api_net_nuget] | [GitHub][api_sample_net] |
| **Python em lotes** | [readthedocs.io][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Node.js do Lote** | [github.io][api_nodejs] | [npm][api_nodejs_npm] | - | | **Java do Lote** (visualização) | [github.io][api_java] | [Repositório de instantâneos do Maven][api_java_jar] | - |

### Gerenciamento de recursos em lotes

Além das APIs de cliente, você também pode usar os itens a seguir para gerenciar recursos em sua conta do Lote.

- [Cmdlets do PowerShell do Lote][batch_ps]\: os cmdlets do Lote do Azure no módulo do [Azure PowerShell](../powershell-install-configure.md) permitem que você gerencie recursos do Lote com o PowerShell.

- [CLI do Azure](../xplat-cli-install.md): a CLI do Azure (Interface de linha de comando do Azure) é um conjunto de ferramentas de plataforma cruzada que fornece comandos para interagir com muitos serviços do Azure, incluindo o Lote.

- Biblioteca de cliente do [.NET de Gerenciamento do Lote](batch-management-dotnet.md): também disponível via [NuGet][api_net_mgmt_nuget]. Use a biblioteca de cliente .NET de Gerenciamento do Lote para gerenciar programaticamente contas, cotas e pacotes de aplicativos do Lote. A referência da biblioteca de gerenciamento está no [MSDN][api_net_mgmt].

## Cenário: escalar horizontalmente uma carga de trabalho paralela

Uma solução comum que usa as APIs de Lote para interagir com o serviço de Lote envolve a expansão de trabalho intrinsecamente paralelo (como a renderização de imagens para cenas 3D) em um pool de nós de computação. Esse pool de nós de computação pode ser seu "farm de renderização" que oferece dezenas, centenas ou até milhares de núcleos para o trabalho de renderização, por exemplo.

O diagrama a seguir mostra um fluxo de trabalho do Lote comum, com um aplicativo cliente ou serviço hospedado usando o Lote para executar uma carga de trabalho paralela.

![Fluxo de trabalho da solução do Lote][2]

Nesse cenário comum, o aplicativo ou serviço processa uma carga de trabalho computacional no Lote do Azure, executando as seguintes etapas:

1. Carregue os **arquivos de entrada** e o **aplicativo** que processará esses arquivos em sua conta de Armazenamento do Azure. Os arquivos de entrada podem ser quaisquer dados que o aplicativo processará, como dados de modelagem financeira ou arquivos de vídeo a serem transcodificados. Os arquivos do aplicativo podem ser qualquer aplicativo que seja usado para processar os dados, como um aplicativo de renderização 3D ou um transcodificador de mídia.

2. Crie um **pool** do Lote de nós de computação em sua conta do Lote. Essas serão as máquinas virtuais que executarão suas tarefas. Especifique propriedades como o [tamanho de nó](./../cloud-services/cloud-services-sizes-specs.md), seu sistema operacional e o local no Armazenamento do Azure do aplicativo para instalação quando os nós ingressarem no pool (o aplicativo que você carregou na etapa 1). Você também pode configurar o pool para [dimensionamento automático](batch-automatic-scaling.md) (ajustar dinamicamente o número de nós de computação no pool) em resposta à carga de trabalho gerada por suas tarefas.

3. Crie um **trabalho** do Lote para executar a carga de trabalho no pool de nós de computação. Ao criar um trabalho, você pode associá-lo a um pool do Lote.

4. Adicione **tarefas** ao trabalho. Quando você adiciona tarefas a um trabalho, o serviço de Lote agenda automaticamente as tarefas para execução em nós de computação no pool. Cada tarefa usa o aplicativo que você carregou para processar os arquivos de entrada.

    - 4a. Antes da execução de uma tarefa, ela pode baixar os dados (os arquivos de entrada) que deve processar para o nó de computação ao qual está atribuída. Se o aplicativo ainda não tiver sido instalado no nó (confira a etapa nº 2), ele poderá ser baixado aqui em vez disso. Quando os downloads forem concluídos, as tarefas serão executadas em seus nós atribuídos.

5. À medida que as tarefas são executadas, você pode consultar o Lote para monitorar o andamento do trabalho e suas tarefas. O serviço ou aplicativo cliente se comunica com o serviço de Lote via HTTPS. Como você pode estar monitorando milhares de tarefas em execução em milhares de nós de computação, não deixe de [conferir o serviço do Lote de forma eficiente](batch-efficient-list-queries.md).

6. À medida que as tarefas são concluídas,elas podem carregar seus dados de resultados para o Armazenamento do Azure. Você também pode recuperar arquivos diretamente de nós de computação.

7. Quando o monitoramento detecta que as tarefas em seu trabalho foram concluídas, o aplicativo cliente ou serviço pode baixar os dados de saída para processamento adicional ou avaliação.

Lembre-se de que essa é apenas uma maneira de usar o Lote, e este cenário descreve apenas alguns de seus recursos disponíveis. Por exemplo, você pode executar [várias tarefas em paralelo](batch-parallel-node-tasks.md) em cada nó de computação e pode usar [tarefas de preparo e conclusão de trabalhos](batch-job-prep-release.md) para preparar os nós para os trabalhos e realizar a limpeza posteriormente.

## Próximas etapas

Agora que você já viu uma visão geral de alto nível do serviço Lote, é hora de se aprofundar para saber como pode usá-lo para processar suas cargas de trabalho paralelas com uso intensivo de computação.

- Leia a [visão geral do recurso do Lote para desenvolvedores](batch-api-basics.md) para obter informações mais detalhadas sobre os recursos da API que o Lote fornece para o processamento de suas cargas de trabalho. Esta é uma leitura essencial para qualquer pessoa que esteja se preparando para usar o Lote.

- [Introdução à biblioteca do Lote do Azure para .NET](batch-dotnet-get-started.md) para aprender a usar o C# e a biblioteca do .NET do Lote para executar uma carga de trabalho simples usando um fluxo de trabalho comum do Lote. Essa deve ser uma de suas primeiras etapas ao aprender a usar o serviço de Lote. Também há uma [Versão do Python](batch-python-tutorial.md) do tutorial.

- Baixe os [exemplos de código no GitHub][github_samples] para ver como o C# e o Python pode interagir com o Lote para agendar e processar cargas de trabalho de exemplo.

- Confira o [Roteiro de Aprendizagem do Lote][learning_path] para ter uma noção dos recursos disponíveis para você ao aprender a trabalhar com o Lote.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://adxsnapshots.azurewebsites.net/?dir=com%5cmicrosoft%5cazure%5cazure-batch
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png

<!---HONumber=AcomDC_0622_2016-->