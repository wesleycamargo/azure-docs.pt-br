<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introdução](vs-mobile-services-cordova-getting-started.md)
> - [O que aconteceu](vs-mobile-services-cordova-what-happened.md)

###<span id="whathappened">O que aconteceu com meu projeto?</span>

#####Referências adicionadas

O plug-in do Cliente do Serviço Móvel do Azure está incluído em todos os Aplicativos Híbridos para Vários Dispositivos habilitados.
  
#####Valores de cadeia de conexão para Serviços Móveis

Sob  `services\mobileServices\settings`, um novo arquivo JavaScript (.js) com um **MobileServiceClient** foi gerado contendo o URL do aplicativo do serviço móvel selecionado e uma chave do aplicativo. O arquivo contém a inicialização de um objeto de cliente de serviço móvel, semelhante ao código a seguir.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[Saiba mais sobre serviços móveis](http://azure.microsoft.com/documentation/services/mobile-services/)


<!--HONumber=42-->
