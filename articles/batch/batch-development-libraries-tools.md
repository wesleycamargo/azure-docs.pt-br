<properties 
	pageTitle="Ferramentas e bibliotecas de desenvolvimento do Azure Batch" 
	description="Obtenha as bibliotecas e ferramentas de que você precisa para desenvolver os aplicativos Azure Batch e Batch Apps" 
	services="batch" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"
	editor="yidingz"/>

<tags 
	ms.service="batch" 
	ms.workload="big-compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="danlep"/>


# Ferramentas e bibliotecas de desenvolvimento do Azure Batch 
<p> Obtenha essas bibliotecas e ferramentas para desenvolver aplicativos do Azure Batch e do Batch Apps.

## Batch

+ [Biblioteca de cliente em lotes para .NET](http://www.nuget.org/packages/Azure.Batch/) (NuGet) – desenvolver aplicativos de cliente para executar trabalhos com o serviço de Lote
+ [Biblioteca de gerenciamento de lotes](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – gerenciar contas de serviço de Lote
+ [Gerenciador de lotes](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN) – aplicativo GUI e exemplo para navegar, acessar e atualizar os principais elementos em uma conta de lote, incluindo trabalhos e tarefas, TVMs (máquinas virtuais de tarefa) e pools, além de arquivos em uma TVM.

> [AZURE.TIP]O Gerenciador de lotes é uma excelente ferramenta se você for novo no Batch ou deseja monitorar ou solucionar problemas de atividade do Batch. Como esse é um exemplo de código, o código-fonte mostra que os recursos são implementados usando a API do .NET de Lote. Consulte a [postagem de blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).

<p>
## Batch Apps

+ [SDK de nuvem no Batch Apps](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) (NuGet) – permite que os aplicativos descarreguem trabalhos no serviço Lote
+ [Extensão do Visual Studio no Batch Apps](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (Galeria do Visual Studio) – aplicativos habilitados para nuvem no Visual Studio usando o SDK de nuvem no Batch Apps
+ [SDK de cliente no Batch Apps](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) (NuGet) – interagir com aplicativos habilitados a descarregar trabalhos no serviço Batch
+ [Cliente Python do Batch Apps](https://github.com/Azure/azure-batch-apps-python) (GitHub) - módulo de cliente Python para interagir com aplicativos configurados em um serviço Batch Apps
+ [Exemplo do Batch Apps Blender](https://github.com/Azure/azure-batch-apps-blender) (GitHub) - complemento para o software livre de renderização Blender, que usa o cliente Python e SDK do Batch Apps para configurar uma plataforma de renderização baseada em nuvem


## Recursos adicionais

+ [Exemplos de código](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [Fórum do Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [Introdução à Biblioteca do Azure Batch para .NET](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=July15_HO4-->