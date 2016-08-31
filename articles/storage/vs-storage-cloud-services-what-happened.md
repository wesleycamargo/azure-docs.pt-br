<properties
    pageTitle="O que aconteceu ao meu projeto do serviço de nuvem? | Microsoft Azure | Serviços conectados do Visual Studio"
	description="Descreve o que acontece em um projeto de serviços de nuvem após a conexão a uma conta de armazenamento do Azure usando os serviços conectados do Visual Studio"
    services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/15/2016"
	ms.author="tarcher"/>

# O que aconteceu ao meu projeto dos serviços de nuvem (serviço conectado do Armazenamento do Azure do Visual Studio)?

## Referências adicionadas

O pacote do NuGet do armazenamento do Azure foi adicionado ao seu projeto do Visual Studio. Esse pacote adiciona as referências de .NET a seguir:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## Cadeia de conexão do Armazenamento do Azure adicionada
Elementos foram criados com a cadeia de conexão e chave da conta de armazenamento selecionada. Foram realizadas modificações nos seguintes arquivos:

- **ServiceDefinition.csdef**
- **ServiceConfiguration.Cloud.cscfg**
- **ServiceConfiguration.Local.cscfg**

<!---HONumber=AcomDC_0817_2016-->