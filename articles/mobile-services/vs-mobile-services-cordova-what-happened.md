<properties 
	pageTitle="O que aconteceu com o meu projeto Cordova (Serviços Conectados do Visual Studio) | Microsoft Azure" 
	description="Descreve o que aconteceu com nosso projeto do Azure Cordova após a adição dos Serviços Móveis do Azure usando os Serviços Conectados do Visual Studio" 
	services="mobile-services" 
	documentationCenter="na" 
	authors="mlhoop" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="mlearned"/>

# O que aconteceu com o meu projeto do Azure Cordova após a adição dos Serviços Móveis do Azure usando os Serviços Conectados do Visual Studio?

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

[Saiba mais sobre serviços móveis](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0128_2016-->