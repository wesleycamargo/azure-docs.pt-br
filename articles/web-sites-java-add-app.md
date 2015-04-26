<properties 
	pageTitle="Adicionar um aplicativo a seu site Java" 
	description="Este tutorial mostra como adicionar uma página ou aplicativo a seu site Java no Microsoft Azure." 
	services="web-sites" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

# Adicionar um aplicativo a seu site Java no Azure

Assim que você tiver inicializado seu site Java como documentado em [Introdução aos Sites do Microsoft Azure e Java](web-sites-java-get-started.md), você pode carregar seu aplicativo colocando seu WAR na pasta **webapps**.

O caminho de navegação para a pasta **webapps** varia dependendo de como seu site está configurado.

- Se você tiver configurado seu site usando a galeria de aplicativos do Azure, o caminho para a pasta **webapps** está no formulário **d:\home\site\wwwroot\bin\application\_server\webapps**, em que **application\_server** é o nome do servidor de aplicativos em vigor para seu site. 
- Se você configurar seu site usando a UI de configuração do Azure, o caminho para a pasta **webapps** está no formulário **d:\home\site\wwwroot\webapps**. 

Observe que você pode usar o controle do código-fonte para carregar o aplicativo ou páginas da web, incluindo cenários de integração contínua. As instruções de uso do controle do código-fonte com seu site estão disponíveis em [Publicando do controle do código-fonte para sites do Azure](web-sites-publish-source-control.md). O FTP também é uma opção para carregar seu aplicativo ou suas páginas web.

Observação para sites Tomcat: Assim que você tiver carregado seu arquivo WAR na pasta **webapps**, o servidor do aplicativo Tomcat detectará que você o adicionou e o carregará automaticamente. Observe que se você copiar arquivos (diferentes de arquivos WAR) no diretório raiz, o servidor de aplicativos precisará ser reiniciado para que esses arquivos sejam usados. A funcionalidade de carregamento automático dos sites Tomcat Java em execução no Azure baseia-se na adição de um novo arquivo WAR ou da adição de novos arquivos ou diretórios à pasta **webapps**. 



<!--HONumber=42-->
