---
title: "O Lote do Azure executa soluções de computação paralela em larga escala na nuvem | Microsoft Docs"
description: "Saiba mais sobre o uso do serviço Lote do Azure para cargas de trabalho paralelas e HPC em larga escala."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6b6c548ca1001587e2b40bbe9ee2fcb298f40d72
ms.openlocfilehash: e204a3c79768ddbd433f3b00a3db456ef3258173
ms.lasthandoff: 02/28/2017


---
# <a name="run-intrinsically-parallel-workloads-with-batch"></a>Executar cargas de trabalho intrinsecamente paralelas com o Lote

O Lote do Azure é um serviço de plataforma para execução de aplicativos paralelos em grande escala e aplicativos HPC (computação de alto desempenho) com eficiência na nuvem. O Lote do Azure agenda o trabalho de computação intensiva para executar em uma coleção gerenciada de máquinas virtuais e que pode dimensionar automaticamente os recursos de computação para atender às necessidades dos trabalhos.

Com o Lote do Azure, você pode definir facilmente os recursos de computação do Azure para executar os aplicativos em paralelo e em escala. Não é necessário criar, configurar e gerenciar manualmente um cluster HPC, máquinas virtuais, redes virtuais ou um trabalho e infraestrutura de agendamento de tarefas complexos. O Lote do Azure automatiza ou simplifica essas tarefas para você.

## <a name="use-cases-for-batch"></a>Casos de uso para o Lote
O Lote é um serviço do Azure gerenciado que é usado para *processamento em lotes* ou *computação em lotes*, executando grandes volumes de tarefas semelhantes para obter o resultado desejado. A computação em lote é mais comumente usada por organizações que processam, transformam e analisam grandes volumes de dados regularmente.

O Lote funciona bem com cargas de trabalho e aplicativos intrinsecamente paralelos (também conhecidos como "excessivamente paralelos"). Cargas de trabalho intrinsecamente paralelas são aquelas divididas com facilidade em várias tarefas que executam o trabalho simultaneamente em vários computadores.

![Tarefas paralelas][1]<br/>

Alguns exemplos de cargas de trabalho que normalmente são processadas usando essa técnica são:

* Modelagem de riscos financeiros
* Análise de dados de clima e hidrologia
* Processamento, análise e renderização de imagens
* Codificação e transcodificação de mídia
* Análise de sequência genética
* Análise de estresse de engenharia
* Teste de software

O lote também pode realizar cálculos paralelos com uma etapa de redução no final, além de executar cargas de trabalho HPC mais complexas, como aplicativos [MPI (nterface de Transmissão de Mensagens)](batch-mpi.md) .

Para obter uma comparação entre o Lote e outras opções de solução HPC no Azure, confira [Soluções do Lote e HPC](batch-hpc-solutions.md).

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="developing-with-batch"></a>Desenvolvendo com o Lote
O processamento de cargas de trabalho paralelas com o Lote do Azure normalmente é feito por meio de programação usando uma das [APIs do Lote](#batch-development-apis). O aplicativo cliente ou serviço pode usar as APIs do Lote para se comunicar com o serviço de Lote. Com as APIs do Lote, você pode criar e gerenciar pools de nós de computação, máquinas virtuais ou serviços de nuvem. Em seguida, você pode agendar trabalhos e tarefas para serem executadas em nós. 

Você pode processar com eficiência cargas de trabalho em grande escala para sua organização ou fornecer um front-end de serviço a seus clientes para que eles possam executar trabalhos e tarefas, sob demanda ou de acordo com uma agenda, em um ou em centenas ou em milhares de nós. Você também pode usar o Lote do Azure como parte de um fluxo de trabalho maior, gerenciado por ferramentas como o [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md).

> [!TIP]
> Quando estiver pronto para se aprofundar na API do Lote e obter uma compreensão mais profunda dos recursos que ele fornece, confira a [Visão geral do recurso Lote para desenvolvedores](batch-api-basics.md).
> 
> 

### <a name="azure-accounts-youll-need"></a>Contas do Azure das quais você precisará
Ao desenvolver soluções de Lote, você usará as contas a seguir no Microsoft Azure.

* **Conta e assinatura do Azure** – se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN][msdn_benefits] ou inscrever-se para uma [conta gratuita do Azure][free_account]. Ao criar uma conta, uma assinatura padrão será criada para você.
* **Conta do Lote** - recursos do Lote do Azure, incluindo pools, nós de computação, trabalhos e tarefas, são associados a uma conta do Lote do Azure. Quando o aplicativo faz uma solicitação ao serviço de Lote, ele autentica a solicitação usando o nome de conta do Lote do Azure, a URL da conta e uma chave de acesso. Você pode [criar a conta do Lote](batch-account-create-portal.md) no portal do Azure.
* **Conta de armazenamento** – o Lote inclui suporte interno para trabalhar com arquivos no [Armazenamento do Azure][azure_storage]. Quase todos os cenários de Lote usam o Armazenamento de Blobs do Azure para preparar os programas que as tarefas executam e os dados que eles processam e para o armazenamento dos dados de saída que eles geram. Para criar uma conta do Armazenamento, veja [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).

### <a name="batch-development-apis"></a>APIs de desenvolvimento em lotes
Os aplicativos e serviços podem emitir chamadas da REST API diretamente ou usar uma ou mais das bibliotecas de cliente a seguir para executar e gerenciar as cargas de trabalho do Lote do Azure.

| API | Referência de API | Baixar | Tutorial | Exemplos de código |
| --- | --- | --- | --- | --- |
| **REST do Lote** |[MSDN][batch_rest] |N/D |- |- |
| **.NET do Lote** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[Tutorial](batch-dotnet-get-started.md) |[GitHub][api_sample_net] |
| **Python em lotes** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[Tutorial](batch-python-tutorial.md)|[GitHub][api_sample_python] |
| **Lote do Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- |
| **Lote do Java** (versão prévia) |[github.io][api_java] |[Maven][api_java_jar] |- |[GitHub][api_sample_java] |

### <a name="batch-command-line-tools"></a>Ferramentas de linha de comando do Lote

A funcionalidade fornecida pelas APIs de desenvolvimento também está disponível usando ferramentas de linha de comando: 

* [Cmdlets do PowerShell do Lote][batch_ps]: os cmdlets do Lote do Azure no módulo do [Azure PowerShell](/powershell/azureps-cmdlets-docs) permitem que você gerencie recursos do Lote com o PowerShell.
* [CLI do Azure](../xplat-cli-install.md): a CLI do Azure (Interface de linha de comando do Azure) é um conjunto de ferramentas de plataforma cruzada que fornece comandos do shell para interagir com muitos serviços do Azure, incluindo o Lote.

### <a name="batch-resource-management"></a>Gerenciamento de recursos em lotes

As APIs do Azure Resource Manager para o Lote fornecem acesso programático a contas do Lote. Usando essas APIs, você pode gerenciar programaticamente contas do Lote, cotas e pacotes de aplicativos.  

| API | Referência de API | Baixar | Tutorial | Exemplos de código |
| --- | --- | --- | --- | --- |
| **Gerenciador de Recursos do Lote REST** |[docs.microsoft.com][api_rest_mgmt] |N/D |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Gerenciador de Recursos do Lote .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [Tutorial](batch-management-dotnet.md) |[GitHub][api_sample_net] |


### <a name="batch-tools"></a>Ferramentas do Lote
Embora não sejam necessárias para compilar soluções usando o Lote, veja algumas ferramentas essenciais para a compilação e depuração de aplicativos e serviços do Lote.

* [Portal do azure][portal]: você pode criar, monitorar e excluir pools, trabalhos e tarefas do Lote nas folhas do Lote do portal do Azure. Você pode exibir as informações de status para esses e outros recursos ao executar seus trabalhos e até mesmo baixar arquivos de nós de computação em seus pools (baixar um `stderr.txt` de uma tarefa com falha durante a solução de problemas, por exemplo). Você também pode baixar arquivos da área de trabalho remota (RDP) que pode usar para fazer logon em nós de computação.
* [Gerenciador do Lote do Azure][batch_explorer]: o Gerenciador do Lote oferece uma funcionalidade de gerenciamento de recursos do Lote semelhante como no portal do Azure, mas em um aplicativo cliente autônomo do WPF (Windows Presentation Foundation). Um dos aplicativos de exemplo do .NET do Lote disponíveis no [GitHub][github_samples], você pode compilá-lo com o Visual Studio 2015 ou acima e usá-lo para procurar e gerenciar os recursos na sua conta do Lote enquanto desenvolver e depurar suas soluções do Lote. Exiba detalhes da tarefa, do pool e do trabalho, baixe arquivos de nós de computação e se conecte a nós remotamente por meio de arquivos de RDP (Área de Trabalho Remota) que você pode baixar com o Gerenciador do Lote.
* [Gerenciador de Armazenamento do Microsoft Azure][storage_explorer]: embora não seja estritamente uma ferramenta do Lote do Azure, o Gerenciador de Armazenamento é outra ferramenta valiosa a ser usada durante o desenvolvimento e a depuração de suas soluções do Lote.

## <a name="scenario-scale-out-a-parallel-workload"></a>Cenário: escalar horizontalmente uma carga de trabalho paralela
Uma solução comum que usa as APIs de Lote para interagir com o serviço de Lote envolve a expansão de trabalho intrinsecamente paralelo (como a renderização de imagens para cenas 3D) em um pool de nós de computação. Esse pool de nós de computação pode ser seu "farm de renderização" que oferece dezenas, centenas ou até milhares de núcleos para o trabalho de renderização, por exemplo.

O diagrama a seguir mostra um fluxo de trabalho do Lote comum, com um aplicativo cliente ou serviço hospedado usando o Lote para executar uma carga de trabalho paralela.

![Fluxo de trabalho da solução do Lote][2]

Nesse cenário comum, o aplicativo ou serviço processa uma carga de trabalho computacional no Lote do Azure, executando as seguintes etapas:

1. Carregue os **arquivos de entrada** e o **aplicativo** que processará esses arquivos em sua conta do Armazenamento do Azure. Os arquivos de entrada podem ser quaisquer dados que o aplicativo processará, como dados de modelagem financeira ou arquivos de vídeo a serem transcodificados. Os arquivos do aplicativo podem ser qualquer aplicativo que seja usado para processar os dados, como um aplicativo de renderização 3D ou um transcodificador de mídia.
2. Crie um **pool** do Lote de nós de computação em sua conta do Lote. Esses nós são as máquinas virtuais que executarão suas tarefas. Especifique propriedades, como o [tamanho de nó](../cloud-services/cloud-services-sizes-specs.md), o sistema operacional e o local no Armazenamento do Azure do aplicativo para instalação quando os nós ingressarem no pool (o aplicativo que você carregou na etapa 1). Você também pode configurar o pool para ser [dimensionado automaticamente](batch-automatic-scaling.md) em resposta à carga de trabalho gerada pelas tarefas. O dimensionamento automático ajusta dinamicamente o número de nós de computação no pool.
3. Crie um **trabalho** do Lote para executar a carga de trabalho no pool de nós de computação. Ao criar um trabalho, você pode associá-lo a um pool do Lote.
4. Adicione **Tarefas** ao trabalho. Quando você adiciona tarefas a um trabalho, o serviço de Lote agenda automaticamente as tarefas para execução em nós de computação no pool. Cada tarefa usa o aplicativo que você carregou para processar os arquivos de entrada.
   
   * 4a. Antes da execução de uma tarefa, ela pode baixar os dados (os arquivos de entrada) que deve processar para o nó de computação ao qual está atribuída. Se o aplicativo ainda não tiver sido instalado no nó (confira a etapa nº 2), ele poderá ser baixado aqui em vez disso. Quando os downloads forem concluídos, as tarefas serão executadas em seus nós atribuídos.
5. À medida que as tarefas são executadas, você pode consultar o Lote para monitorar o andamento do trabalho e suas tarefas. O aplicativo cliente ou serviço se comunica com o serviço de Lote por HTTPS. Como você pode monitorar milhares de tarefas em execução em milhares de nós de computação, não se esqueça de [consultar o serviço do Lote com eficiência](batch-efficient-list-queries.md).
6. À medida que as tarefas são concluídas,elas podem carregar seus dados de resultados para o Armazenamento do Azure. Você também pode recuperar arquivos diretamente do sistema de arquivos em um nó de computação.
7. Quando o monitoramento detecta que as tarefas em seu trabalho foram concluídas, o aplicativo cliente ou serviço pode baixar os dados de saída para processamento adicional ou avaliação.

Lembre-se de que essa é apenas uma maneira de usar o Lote, e este cenário descreve apenas alguns de seus recursos disponíveis. Por exemplo, você pode executar [várias tarefas em paralelo](batch-parallel-node-tasks.md) em cada nó de computação e pode usar [tarefas de preparo e conclusão de trabalhos](batch-job-prep-release.md) para preparar os nós para os trabalhos e realizar a limpeza posteriormente.

## <a name="next-steps"></a>Próximas etapas
Agora que você já viu uma visão geral de alto nível do serviço Lote, é hora de se aprofundar para saber como pode usá-lo para processar suas cargas de trabalho paralelas com uso intensivo de computação.

* Leia ae [Visão geral de recursos do Lote para desenvolvedores](batch-api-basics.md), informações essenciais para qualquer pessoa que está se preparando para usar o Lote. O artigo contém informações mais detalhadas sobre os recursos de serviço do Lote, como pools, nós, trabalhos e tarefas, e os muitos recursos da API que você pode usar ao criar o aplicativo do Lote.
* [Introdução à biblioteca do Lote do Azure para .NET](batch-dotnet-get-started.md) para aprender a usar o C# e a biblioteca do .NET do Lote para executar uma carga de trabalho simples usando um fluxo de trabalho comum do Lote. Este artigo deve ser uma de suas primeiras etapas ao aprender a usar o serviço de Lote. Também há uma [Versão do Python](batch-python-tutorial.md) do tutorial.
* Baixe os [exemplos de código no GitHub][github_samples] para ver como o C# e o Python podem interagir com o Lote para agendar e processar cargas de trabalho de exemplo.
* Confira o [Roteiro de Aprendizagem do Lote][learning_path] para ter uma noção dos recursos disponíveis para você ao aprender a trabalhar com o Lote.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/en-us/rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png

