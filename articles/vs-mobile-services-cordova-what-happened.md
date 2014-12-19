<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Introdução](/documentation/articles/vs-mobile-services-cordova-getting-started/)
> - [O que aconteceu](/documentation/articles/vs-mobile-services-cordova-what-happened/)

###<span id="whathappened">O que aconteceu com meu projeto?</span>

#####Referências adicionadas

O plug-in do Cliente do Serviço Móvel do Azure está incluído em todos os Aplicativos Híbridos para Vários Dispositivos habilitados.
  
#####Valores de cadeia de conexão para Serviços Móveis

Em `services\mobileServices\settings`, foi gerado um novo arquivo JavaScript (.js) com um **MobileServiceClient** contendo o URL do aplicativo do serviço móvel selecionado e uma chave do aplicativo. O arquivo contém a inicialização de um objeto de cliente de serviço móvel, semelhante ao código a seguir.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[Saiba mais sobre serviços móveis](http://azure.microsoft.com/documentation/services/mobile-services/)
