<properties urlDisplayName="How to create" pageTitle="Como criar sites - gerenciamento de serviços do Azure" metaKeywords="Azure creating website, Azure deleting website" description="Learn how to create a website using the Azure Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Website" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />

#Como criar um site

Este tópico mostra como criar um site na galeria ou usando o portal de gerenciamento.

Para obter informações sobre como implantar o conteúdo em um Site no Azure que você criou, consulte a seção **Implantar** em [Sites do Azure](/pt-br/documentation/services/web-sites/).

## Sumário ##

- [Como: Criar um site usando o Portal de Gerenciamento](#createawebsiteportal)
- [Como: Criar um site na galeria](#howtocreatefromgallery)
- [Como: Excluir um site](#deleteawebsite)
- [Próximas etapas](#nextsteps)

##<a name="createawebsiteportal"></a>Como: criar um site usando o Portal de Gerenciamento

Siga essas etapas para criar um site no Azure.
	
1. Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).

2. Clique no ícone **Criar Novo** na parte inferior esquerda do Portal de Gerenciamento.

3. Clique no ícone **Site**, clique no ícone **Criação Rápida**, digite um valor para a URL e clique na marca de verificação ao lado de **Criar Site** no canto inferior direito da página.

4. Quando o website tiver sido criado, você verá o texto **Criando Website <*nome do site*> com sucesso**. Você pode navegar até o site clicando em **Procurar** na parte inferior da página do portal.

5. No portal, clique no nome do site exibido na lista de sites para abrir a página de gerenciamento **Início Rápido** do site.

6. Na página **Início Rápido**, são fornecidas opções para você obter as ferramentas de desenvolvimento de site, configurar a publicação de seu site ou configurar a implantação de um provedor de controle do código-fonte, como TFS ou Git. Por padrão, a publicação FTP é configurada para sites, e o nome do Host FTP é exibido na seção **Visão Rápida** da página **Painel** em **Nome do Host FTP**. Antes da publicação com FTP ou Git, escolha a opção para **Redefinir credenciais de implantação** na página **Painel** para que você possa autenticar no Host FTP ou no Repositório Git ao implantar conteúdo em seu site.

7. A página de gerenciamento **Configurar** expõe as configurações para o seu site, como:

	- A versão do .NET Framework ou PHP exigido por seu aplicativo Web
	- Associações SSL
	- nomes de domínio personalizados
	- opções de registro em log
	- configurações do aplicativo para o ambiente do Azure (substituindo <appSettings> na Web.config do seu ambiente de desenvolvimento, por exemplo)
	- configurações de conexão (substituindo <connectionStrings> na Web.config do seu ambiente de desenvolvimento, por exemplo)
	- processadores de script para extensões de arquivo específicas, como *.php

##<a name="howtocreatefromgallery"></a>Como: criar um site na galeria

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>Como: excluir um site
Os sites são excluídos usando o ícone **Excluir** no Portal de Gerenciamento do Azure. O ícone **Excluir** está disponível no Portal do Azure, quando você clica em **Sites** para listar todos os seus sites e, na parte inferior de cada uma das páginas de gerenciamento de site.

##<a name="nextsteps"></a>Próximas etapas

Para obter mais informações, consulte [Sites do Azure](/pt-br/documentation/services/web-sites/).

<!--HONumber=35_1-->
