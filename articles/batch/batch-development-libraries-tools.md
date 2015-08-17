<properties
	pageTitle="Ferramentas e bibliotecas de desenvolvimento em lote do Azure | Microsoft Azure"
	description="Obtenha as bibliotecas e ferramentas de que você precisa para desenvolver os aplicativos em lote do Azure"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="danlep"/>


# Ferramentas e bibliotecas de desenvolvimento do Azure Batch
<p> Obtenha essas bibliotecas e ferramentas para desenvolver aplicativos em lote do Azure.

## Batch

+ [Biblioteca de cliente em lotes para .NET](http://www.nuget.org/packages/Azure.Batch/) (NuGet) – desenvolver aplicativos de cliente para executar trabalhos com o serviço de Lote
+ [Biblioteca de gerenciamento de lotes](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – gerenciar contas em lote
+ [Gerenciador de lotes](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) (GitHub) – aplicativo GUI e exemplo para navegar, acessar e atualizar os principais elementos em uma conta do Lote, incluindo trabalhos e tarefas, nós de computação e pools, além de arquivos em nó de computação. Consulte a [postagem de blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).


## Batch Apps

+ [SDK de nuvem no Batch Apps](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/1.1.1-preview) (NuGet) – permite que os aplicativos descarreguem trabalhos no serviço Lote
+ [Extensão do Visual Studio no Batch Apps](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (Galeria do Visual Studio) – aplicativos habilitados para nuvem no Visual Studio usando o SDK de nuvem no Batch Apps
+ [SDK de cliente no Batch Apps](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/2.3.0-preview) (NuGet) – interagir com aplicativos habilitados a descarregar trabalhos no serviço Batch
+ [Cliente Python do Batch Apps](https://github.com/Azure/azure-batch-apps-python) (GitHub) - módulo de cliente Python para interagir com aplicativos configurados em um serviço Batch Apps

>[AZURE.IMPORTANT]O Azure oferecerá apenas a API de aplicativos em lote como pré-visualização. Você só deve desenvolver com ele para projetos de teste ou prova de conceito. Recursos dos aplicativos essenciais do Lote serão integrados à API do Lote em futuras versões do serviço.

## Recursos adicionais

+ [Exemplos de código](https://github.com/Azure/azure-batch-samples) (GitHub)
+ [Fórum do Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=August15_HO6-->