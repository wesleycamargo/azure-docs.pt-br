<properties 
	pageTitle="O que acontece quando você adiciona os Serviços Móveis a um aplicativo do Javascript usando os Serviços Conectados do Visual Studio" 
	description="Descreve o que aconteceu com seu projeto dos Serviços Móveis do Azure no Visual Studio" 
	services="mobile-services" 
	documentationCenter="" 
	authors="TomArcher" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="tarcher"/>

# O que acontece com o meu projeto do Javascript quando adiciono os Serviços Móveis do Azure usando os Serviços Conectados do Visual Studio?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-javascript-getting-started.md)
> - [What Happened](vs-mobile-services-javascript-what-happened.md)

###O que aconteceu com meu projeto?

#####Pacote NuGet adicionado

O pacote NuGet **WindowsAzure.MobileServices.WinJS** foi instalado, incluindo a biblioteca do Serviço Móvel do Azure no arquivo `js\MobileServices.js`.
  
#####Valores de cadeia de conexão para Serviços Móveis 

Na pasta `services\mobileServices\settings`, um novo arquivo JavaScript (.js) com um **MobileServiceClient** foi gerado contendo a URL do aplicativo do serviço móvel selecionado e uma chave do aplicativo.


#####Referências adicionadas ao default.html

As referências a `MobileServices.js` e o arquivo de configurações foram adicionados a `default.html`.


#####Arquivos de serviços conectados adicionados

Na pasta de serviços, arquivos de configuração de Serviços Conectados foram adicionados.



 

<!---HONumber=Nov15_HO3-->