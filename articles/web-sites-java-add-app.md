<properties linkid="develop-java-tutorials-web-site-add-app" urlDisplayName="Add an application to your Java website" pageTitle="Add an application to your Java website" metaKeywords="" description="This tutorial shows you how to add a page or application to your Java website on Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Add an application to your Java website" videoId="" scriptId="" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Adicionar um aplicativo a seu site Java no Azure

Assim que você tiver inicializado seu site Java como documentado em [Introdução aos sites di Microsoft Azure e Java][Introdução aos sites di Microsoft Azure e Java], você pode carregar seu aplicativo colocando seu WAR na pasta **webapps**.

O caminho de navegação para a pasta **webapps** varia dependendo de como seu site está configurado.

-   Se você tiver configurado seu site usando a galeria de aplicativos do Azure, o caminho para a pasta **webapps** está no formulário **d:\\home\\site\\wwwroot\\bin\\application\_server\\webapps**, onde **application\_server** é o nome do servidor do aplicativo em vigor para seu site.
-   Se você configurar seu site usando a configuração UI do Azure, o caminho para a pasta **webapps** está no formulário **d:\\home\\site\\wwwroot\\webapps**.

Observe que você pode usar o controle de origem para carregar o aplicativo ou páginas da web, incluindo cenários de integração contínua. As instruções de uso do controle do código-fonte com seu site estão disponíveis em [Publicando do controle do código-fonte para sites do Azure][Publicando do controle do código-fonte para sites do Azure]. O FTP também é uma opção para carregar seu aplicativo ou suas páginas web.

Observação para sites Tomcat: Assim que você tiver carregado seu arquivo WAR na pasta **webapps**, o servidor do aplicativo Tomcat detectará que você o adicionou e o carregará automaticamente. Observe que se você copiar arquivos (diferentes de arquivos WAR) no diretório raiz, o servidor de aplicativos precisará ser reiniciado para que esses arquivos sejam usados. A funcionalidade de carregamento automático dos sites Tomcat Java em execução no Azure baseia-se na adição de um novo arquivo WAR ou da adição de novos arquivos ou diretórios à pasta **webapps**.

  [Introdução aos sites di Microsoft Azure e Java]: ../web-sites-java-get-started
  [Publicando do controle do código-fonte para sites do Azure]: ../web-sites-publish-source-control
