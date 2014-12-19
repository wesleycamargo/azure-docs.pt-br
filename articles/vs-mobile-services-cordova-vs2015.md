<properties title="Using Mobile Services with Cordova Projects" pageTitle="" metaKeywords="Azure, Cordova, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/11/2014" ms.author="ghogen" />

### Usando os Serviços Móveis com projetos Cordova na Visualização do Visual Studio 2015

Para usar Serviços Móveis do Azure em projetos Cordova na Visualização do Visual Studio 2015, você precisa aplicar a solução a seguir.

1. Em seu projeto Cordova de Visualização do Visual Studio 2015, abra o config. XML e na guia **Plug-ins**, habilite o plug-in **Serviços Móveis do Microsoft Azure**.<br/>
![][1]

2. Em index.html, exclua as linhas que fazem referência ao **MobileServices.Web-1.2.2.min.js**.<br/>

<PRE style="prettyprint">
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>MyCordovaApp>/title>

    <!--MyCordovaApp references -->
    <link href="css/index.css" rel="stylesheet" />
</head>
<body>
    <p>Hello world!&lt/p>

    <!--Cordova reference, this is added to your app when it's build. -->
    <script src="cordova.js"></script>
    <script src="scripts/platformOverrides.js"></script>

    <script src="scripts/index.js"></script>

    <!-- yourservicename references -->
    <span style="background-color:yellow"><script src="//ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.2.min.js">&lt/script></span>
    <script src="/services/mobileservices/settings/yourservicename.js"></script>
</body>
</html>
</PRE>

3. Abra {nomedoseuserviço}.js em serviços -> mobileServices -> pastas de configurações e substitua o trecho de código existente pelo seguinte:

		// http://go.microsoft.com/fwlink/?LinkID=290993&clcid=0x409
		var {yourservicename}Client;
		document.addEventListener("deviceready", function () {    
            mhadetestserviceClient = new WindowsAzure.MobileServiceClient(
                    "https://{yourservicename}.azure-mobile.net/",
                    "<your application key>");
		});

[1]: ./media/vs-mobile-services-cordova-vs2015/AzureMobileServicesPlugIn.png 
