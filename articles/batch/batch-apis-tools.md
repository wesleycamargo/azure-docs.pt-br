---
title: "Usar APIs e ferramentas de Lote do Azure para desenvolver soluções de processamento paralelo em larga escala | Microsoft Docs"
description: "Saiba mais sobre as APIs e ferramentas disponíveis para o desenvolvimento de soluções com o serviço de Lote do Azure."
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
ms.date: 03/08/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 9ed3e60679a0c637c99f8019119289c71be81f83
ms.lasthandoff: 04/17/2017

---


# <a name="overview-of-batch-apis-and-tools"></a>Visão geral das ferramentas e APIs de Lote

O processamento de cargas de trabalho paralelas com o Lote do Azure normalmente é feito por meio de programação usando uma das [APIs do Lote](#batch-development-apis). O aplicativo cliente ou serviço pode usar as APIs do Lote para se comunicar com o serviço de Lote. Com as APIs do Lote, você pode criar e gerenciar pools de nós de computação, máquinas virtuais ou serviços de nuvem. Em seguida, você pode agendar trabalhos e tarefas para serem executadas em nós. 

Você pode processar com eficiência cargas de trabalho em grande escala para sua organização ou fornecer um front-end de serviço a seus clientes para que eles possam executar trabalhos e tarefas, sob demanda ou de acordo com uma agenda, em um ou em centenas ou em milhares de nós. Você também pode usar o Lote do Azure como parte de um fluxo de trabalho maior, gerenciado por ferramentas como o [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Quando estiver pronto para se aprofundar na API do Lote e obter uma compreensão mais profunda dos recursos que ele fornece, confira a [Visão geral do recurso Lote para desenvolvedores](batch-api-basics.md).
> 
> 

## <a name="azure-accounts-youll-need"></a>Contas do Azure das quais você precisará
Ao desenvolver soluções de Lote, você usará as contas a seguir no Microsoft Azure.

* **Conta e assinatura do Azure** – se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN][msdn_benefits] ou inscrever-se para uma [conta gratuita do Azure][free_account]. Ao criar uma conta, uma assinatura padrão será criada para você.
* **Conta do Lote** - recursos do Lote do Azure, incluindo pools, nós de computação, trabalhos e tarefas, são associados a uma conta do Lote do Azure. Quando o aplicativo faz uma solicitação ao serviço de Lote, ele autentica a solicitação usando o nome de conta do Lote do Azure, a URL da conta e uma chave de acesso. Você pode [criar a conta do Lote](batch-account-create-portal.md) no portal do Azure.
* **Conta de armazenamento** – o Lote inclui suporte interno para trabalhar com arquivos no [Armazenamento do Azure][azure_storage]. Quase todos os cenários de Lote usam o Armazenamento de Blobs do Azure para preparar os programas que as tarefas executam e os dados que eles processam e para o armazenamento dos dados de saída que eles geram. Para criar uma conta do Armazenamento, veja [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).

## <a name="batch-development-apis"></a>APIs de desenvolvimento em lotes
Os aplicativos e serviços podem emitir chamadas da REST API diretamente ou usar uma ou mais das bibliotecas de cliente a seguir para executar e gerenciar as cargas de trabalho do Lote do Azure.

| API | Referência de API | Baixar | Tutorial | Exemplos de código | Mais informações |
| --- | --- | --- | --- | --- | --- |
| **REST do Lote** |[MSDN][batch_rest] |N/D |- |- | [Versões com suporte](https://docs.microsoft.com/rest/api/batchservice/batch-service-rest-api-versioning) |
| **.NET do Lote** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[Tutorial](batch-dotnet-get-started.md) |[GitHub][api_sample_net] | [Notas de versão](https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/Client/changelog.md) |
| **Python em lotes** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[Tutorial](batch-python-tutorial.md)|[GitHub][api_sample_python] | [Leiame](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Lote do Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- | [Leiame](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Lote Java** |[github.io][api_java] |[Maven][api_java_jar] |- |[Leiame][api_sample_java] | [Leiame](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-command-line-tools"></a>Ferramentas de linha de comando do Lote

A funcionalidade fornecida pelas APIs de desenvolvimento também está disponível usando ferramentas de linha de comando: 

* [Cmdlets do PowerShell do Lote][batch_ps]: os cmdlets do Lote do Azure no módulo do [Azure PowerShell](/powershell/azureps-cmdlets-docs) permitem que você gerencie recursos do Lote com o PowerShell.
* [CLI do Azure](/cli/azure/overview): a CLI do Azure (Interface de linha de comando do Azure) é um conjunto de ferramentas de plataforma cruzada que fornece comandos do shell para interagir com muitos serviços do Azure, incluindo o Lote.

## <a name="batch-resource-management"></a>Gerenciamento de recursos em lotes

As APIs do Azure Resource Manager para o Lote fornecem acesso programático a contas do Lote. Usando essas APIs, você pode gerenciar programaticamente contas do Lote, cotas e pacotes de aplicativos.  

| API | Referência de API | Baixar | Tutorial | Exemplos de código |
| --- | --- | --- | --- | --- |
| **Gerenciador de Recursos do Lote REST** |[docs.microsoft.com][api_rest_mgmt] |N/D |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Gerenciador de Recursos do Lote .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [Tutorial](batch-management-dotnet.md) |[GitHub][api_sample_net] |


## <a name="batch-tools"></a>Ferramentas do Lote
Embora não sejam necessárias para compilar soluções usando o Lote, veja algumas ferramentas essenciais para a compilação e depuração de aplicativos e serviços do Lote.

* [Portal do azure][portal]: você pode criar, monitorar e excluir pools, trabalhos e tarefas do Lote nas folhas do Lote do portal do Azure. Você pode exibir as informações de status para esses e outros recursos ao executar seus trabalhos e até mesmo baixar arquivos de nós de computação em seus pools (baixar um `stderr.txt` de uma tarefa com falha durante a solução de problemas, por exemplo). Você também pode baixar arquivos da área de trabalho remota (RDP) que pode usar para fazer logon em nós de computação.
* [Gerenciador do Lote do Azure][batch_explorer]: o Gerenciador do Lote oferece uma funcionalidade de gerenciamento de recursos do Lote semelhante como no portal do Azure, mas em um aplicativo cliente autônomo do WPF (Windows Presentation Foundation). Um dos aplicativos de exemplo do .NET do Lote disponíveis no [GitHub][github_samples], você pode compilá-lo com o Visual Studio 2015 ou mais recente e usá-lo para procurar e gerenciar os recursos na sua conta do Lote enquanto desenvolver e depurar suas soluções do Lote. Exiba detalhes da tarefa, do pool e do trabalho, baixe arquivos de nós de computação e se conecte a nós remotamente por meio de arquivos de RDP (Área de Trabalho Remota) que você pode baixar com o Gerenciador do Lote.
* [Gerenciador de Armazenamento do Microsoft Azure][storage_explorer]: embora não seja estritamente uma ferramenta do Lote do Azure, o Gerenciador de Armazenamento é outra ferramenta valiosa a ser usada durante o desenvolvimento e a depuração de suas soluções do Lote.

## <a name="next-steps"></a>Próximas etapas

* Leia ae [Visão geral de recursos do Lote para desenvolvedores](batch-api-basics.md), informações essenciais para qualquer pessoa que está se preparando para usar o Lote. O artigo contém informações mais detalhadas sobre os recursos de serviço do Lote, como pools, nós, trabalhos e tarefas, e os muitos recursos da API que você pode usar ao criar o aplicativo do Lote.
* [Introdução à biblioteca do Lote do Azure para .NET](batch-dotnet-get-started.md) para aprender a usar o C# e a biblioteca do .NET do Lote para executar uma carga de trabalho simples usando um fluxo de trabalho comum do Lote. Este artigo deve ser uma de suas primeiras etapas ao aprender a usar o serviço de Lote. Também há uma [Versão do Python](batch-python-tutorial.md) do tutorial.
* Baixe os [exemplos de código no GitHub][github_samples] para ver como o C# e o Python podem interagir com o Lote para agendar e processar cargas de trabalho de exemplo.
* Confira o [Roteiro de Aprendizagem do Lote][learning_path] para ter uma noção dos recursos disponíveis para você ao aprender a trabalhar com o Lote.


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/\rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/resourcemanager/azurerm.batch/v2.7.0/azurerm.batch
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

