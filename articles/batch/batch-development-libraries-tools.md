<properties 
	pageTitle="Ferramentas e bibliotecas de desenvolvimento do Azure Batch" 
	description="As bibliotecas e ferramentas que necessárias para desenvolver aplicativos do Azure lote e aplicativos de lote" 
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
<p> Obtenha essas bibliotecas e ferramentas para desenvolver aplicativos do Azure lote e aplicativos de lote.

## Batch

+ [Biblioteca de cliente em lotes para .NET](http://www.nuget.org/packages/Azure.Batch/) (NuGet) – desenvolver aplicativos de cliente para executar trabalhos com o serviço de lote
+ [Biblioteca de gerenciamento de lote](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) – contas de serviço do lote de gerenciar
+ [Explorer lote](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN) – exemplo e aplicação de GUI para navegar, acessar e atualizar os principais elementos em uma conta de lote, incluindo trabalhos e tarefas, máquinas virtuais de tarefa (TVMs) e pools e arquivos em um TVM.

> [AZURE.TIP]Gerenciador de lote é uma excelente ferramenta se você for novo no lote ou deseja monitorar ou solucionar problemas de atividade de lote. Como esse é um exemplo de código, o código-fonte mostra que os recursos são implementados usando a API do .NET em lotes. Consulte o [postagem de blog](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx).

<p>
## Batch Apps

+ [Lote aplicativos nuvem SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) (NuGet) – permitem que os aplicativos descarregar trabalhos para o serviço de lote
+ [Extensão do Visual Studio em lotes aplicativos](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) (Galeria do Visual Studio) – nuvem habilitar os aplicativos no Visual Studio usando o SDK do lote aplicativos de nuvem
+ [Lote aplicativos cliente SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) (NuGet) – interagir com aplicativos habilitados para descarregar trabalhos para o serviço de lote
+ [Lote aplicativos Python cliente](https://github.com/Azure/azure-batch-apps-python) (GitHub) - módulo de cliente de Python para interagir com aplicativos configurado em um serviço de aplicativos de lote
+ [Lote aplicativos Misturador exemplo](https://github.com/Azure/azure-batch-apps-blender) (GitHub) - Addon para Misturador abrir software de processamento de código-fonte que usa o cliente em lotes aplicativos SDK e Python para configurar uma plataforma de processamento baseado em nuvem


## Recursos adicionais

+ [Amostras de código](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [Fórum de lote do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch)
+ [Começar com a biblioteca de lote do Azure para .NET](batch-dotnet-get-started.md)  

<!--Anchors-->
[Batch]: #batch
[Batch Apps]: #batch-apps
[Additional resources]: #additional-resources

<!---HONumber=GIT-SubDir-->