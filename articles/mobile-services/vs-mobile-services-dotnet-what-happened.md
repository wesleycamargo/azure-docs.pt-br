<properties 
	pageTitle="O que aconteceu com o meu projeto .NET depois de adicionar Serviços Móveis usando os Serviços Conectados do Visual Studio | Microsoft Azure" 
	description="Descreve o que aconteceu em seu projeto do Visual Studio .NET depois da adição de Serviços Móveis do Azure usando Serviços Conectados" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/17/2015" 
	ms.author="patshea"/>

# O que aconteceu com o meu projeto do Visual Studio .NET depois da adição de Serviços Móveis do Azure usando Serviços Conectados?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-dotnet-getting-started.md)
> - [What Happened](vs-mobile-services-dotnet-what-happened.md)

## Referências adicionadas

O pacote NuGet de Serviços Móveis do Azure foi adicionado ao seu projeto. Por isso, as referências .NET a seguir foram adicionadas ao projeto:

- **Microsoft.WindowsAzure.Mobile**
- **Microsoft.WindowsAzure.Mobile.Ext**
- **Newtonsoft.Json**
- **System.Net.Http.Extensions**
- **System.Net.Http.Primitives** 

## Valores de cadeia de conexão para Serviços Móveis

Em seu arquivo App.xaml.cs, um objeto **MobileServiceClient** foi criado com o URL do aplicativo do serviço móvel selecionado e a chave do aplicativo.

## Projeto de Serviços Móveis adicionado

Se um serviço móvel .NET foi criado no Provedor de Serviço Conectado, um projeto de serviços móveis foi criado e adicionado à solução.


[Saiba mais sobre serviços móveis](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=Sept15_HO4-->