<properties
	pageTitle="Como criar um aplicativo Web - gerenciamento de serviços do Azure"
	description="Aprenda a criar um aplicativo Web usando o Portal do Azure."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>

#Como criar um aplicativo Web

Este tópico mostra como criar um aplicativo Web na galeria ou usando o Portal do Azure.

Para obter informações sobre como implantar o conteúdo em um aplicativo Web que você criou, consulte a seção **Implantar** em [aplicativos Web do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

##<a name="createawebsiteportal"></a>Como: Criar um aplicativo Web usando o Portal do Azure

Siga estas etapas para criar um aplicativo Web no Azure.

1. Faça logon no [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

2. Clique no ícone **Novo**, na parte inferior esquerda do Portal do Azure.

3. Clique no ícone **Web + Móvel**, clique no ícone **Aplicativo Web** ícone, insira um valor de URL e, em seguida, clique em **criar** no canto inferior direito da lâmina criar.

4. Quando o aplicativo Web tiver sido criado, você verá o texto **A implantação no grupo de recursos <nome do grupo de recursos> foi bem-sucedida**.

5. No portal, clique no nome do aplicativo Web exibido na lista de aplicativos Web para abrir a lâmina do aplicativo Web.

6. Na lâmina, são fornecidas opções para você obter as ferramentas de desenvolvimento de aplicativo Web, configurar a publicação de seu aplicativo Web ou configurar a implantação de um provedor de controle do código-fonte, como TFS ou Git. A publicação de FTP é configurada por padrão para aplicativos Web e o nome do host de FTP é exibido na seção **Informações Gerais** da lâmina do aplicativo Web. Antes da publicação com FTP ou Git, escolha a opção para **Redefinir perfil de publicação** na lâmina do aplicativo Web, para que você possa autenticar no Host FTP ou no Repositório Git ao implantar conteúdo em seu aplicativo Web.

7. A lâmina **Configurações** expõe as configurações de seu aplicativo Web, tais como:

	- versão do .NET, PHP, Java ou Python para seu aplicativo Web
	- editar no Visual Studio Online
	- associações SSL
	- nomes de domínio personalizados
	- autenticação/autorização
	- diagnóstico de aplicativo e site
	- pontos de extremidade de monitoramento
	- opções de registro em log
	- configurações do aplicativo para o ambiente do Azure (substituindo <appSettings> na Web.config do seu ambiente de desenvolvimento, por exemplo)
	- cadeias de conexão (substituindo <connectionStrings> na Web.config do seu ambiente de desenvolvimento, por exemplo)
	- processadores de script para extensões de arquivo específicas, como *.php

##<a name="howtocreatefromgallery"></a> Como: criar um aplicativo Web na Galeria

[AZURE.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a> Como: excluir um aplicativo Web
Aplicativos Web são excluídos usando o ícone **Excluir** no Portal do Azure. O ícone **Excluir** está disponível na parte superior a lâmina do aplicativo Web.

##<a name="nextsteps"></a> Próximas Etapas

Para obter mais informações, consulte [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

## O que mudou
* Para obter um guia para a mudança de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a mudança do portal antigo para o novo portal, consulte: [Referência para navegação no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->