<properties 
	pageTitle="Adicionar um aplicativo Java a Aplicativos Web do Serviço de Aplicativo do Azure"
	description="Este tutorial mostra como adicionar uma página ou um aplicativo à sua instância de Aplicativos Web do Serviço de Aplicativo de Azure que já está configurada para usar o Java."
	services="app-service\web"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>

<tags 
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="robmcm"/>

# Adicionar um aplicativo Java a Aplicativos Web do Serviço de Aplicativo do Azure

Após inicializar seu aplicativo Web Java no [Serviço de Aplicativo do Azure][] conforme documentado em [Criar um aplicativo Web Java no Serviço de Aplicativo do Azure](web-sites-java-get-started.md), você poderá carregar o aplicativo colocando o WAR na pasta **webapps**.

O caminho de navegação para a pasta **webapps** varia dependendo de como você configurou sua instância dos Aplicativos Web.

- Se você configurar o aplicativo Web usando o Azure Marketplace, o caminho para a pasta **webapps** estará no formato **d:\\home\\site\\wwwroot\\bin\\application\_server\\webapps**, em que **application\_server** é o nome do servidor de aplicativos em vigor para a instância dos Aplicativos Web. 
- Se você configurar o aplicativo Web usando a interface do usuário de configuração do Azure, o caminho para a pasta **webapps** estará no formato **d:\\home\\site\\wwwroot\\webapps**. 

Observe que você pode usar o controle do código-fonte para carregar o aplicativo ou páginas da web, incluindo cenários de integração contínua. Instruções para usar o controle de origem com o aplicativo Web estão disponíveis em [Implantação contínua usando GIT no Serviço de Aplicativo do Azure](web-sites-publish-source-control.md). O FTP também é uma opção para carregar seu aplicativo ou suas páginas web.

Observação para aplicativos Web Tomcat: assim que você tiver carregado seu arquivo WAR na pasta **webapps**, o servidor do aplicativo Tomcat detectará que você o adicionou e o carregará automaticamente. Observe que se você copiar arquivos (diferentes de arquivos WAR) no diretório raiz, o servidor de aplicativos precisará ser reiniciado para que esses arquivos sejam usados. A funcionalidade de carregamento automático dos aplicativos Web Tomcat Java em execução no Azure baseia-se na adição de um novo arquivo WAR ou da adição de novos arquivos ou diretórios à pasta **webapps**.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Serviço de Aplicativo do Azure]: http://go.microsoft.com/fwlink/?LinkId=529714

<!---HONumber=September15_HO1-->