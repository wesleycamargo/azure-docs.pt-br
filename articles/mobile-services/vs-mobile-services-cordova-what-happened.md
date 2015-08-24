<properties 
	pageTitle="" 
	description="Descreve o que aconteceu com seu projeto de Serviços Móveis do Azure no Cordova" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/12/2015" 
	ms.author="patshea"/>

# O que aconteceu com meu projeto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-cordova-getting-started.md)
> - [What Happened](vs-mobile-services-cordova-what-happened.md)

##Referências adicionadas

O plug-in do Cliente do Serviço Móvel do Azure está incluído em todos os Aplicativos Híbridos para Vários Dispositivos habilitados.
  
##Valores de cadeia de conexão para Serviços Móveis

Em `services\mobileServices\settings`, um novo arquivo JavaScript (.js) com um **MobileServiceClient** foi gerado contendo o URL do aplicativo do serviço móvel selecionado e uma chave do aplicativo. O arquivo contém a inicialização de um objeto de cliente de serviço móvel, semelhante ao código a seguir.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[Saiba mais sobre serviços móveis](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=August15_HO7-->