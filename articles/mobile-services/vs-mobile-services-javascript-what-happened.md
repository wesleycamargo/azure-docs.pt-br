<properties 
	pageTitle="" 
	description="Descreve o que aconteceu com seu projeto dos Serviços Móveis do Azure no Visual Studio" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="patshea123"/>

# O que aconteceu com meu projeto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-javascript-getting-started.md)
> - [What Happened](vs-mobile-services-javascript-what-happened.md)

###<span id="whathappened">O que aconteceu com meu projeto?</id>

#####Pacote NuGet adicionado

O pacote NuGet **WindowsAzure.MobileServices.WinJS** foi instalado, incluindo a biblioteca do Serviço Móvel do Azure no arquivo `js\MobileServices.js`.
  
#####Valores de cadeia de conexão para Serviços Móveis 

Na pasta `services\mobileServices\settings`, um novo arquivo JavaScript (.js) com um **MobileServiceClient** foi gerado contendo a URL do aplicativo do serviço móvel selecionado e uma chave do aplicativo.


#####Referências adicionadas ao default.html

As referências a `MobileServices.js` e o arquivo de configurações foram adicionados a `default.html`.


#####Arquivos de serviços conectados adicionados

Na pasta de serviços, arquivos de configuração de Serviços Conectados foram adicionados.



 

<!---HONumber=July15_HO4-->