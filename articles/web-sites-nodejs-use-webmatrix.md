<properties 
	pageTitle="Site do Node.js com o WebMatrix - tutorial do Azure" 
	description="Um tutorial que ensina como realizar WebMatrix para desenvolver e implantar um aplicativo Node.js em um site do Azure." 
	services="web-sites" 
	documentationCenter="nodejs" 
	authors="blackmist" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>


# Criar e implantar um site do Node.js para o Azure usando WebMatrix

Este tutorial mostra como usar o WebMatrix para desenvolver e implantar um aplicativo Node.js para um site do Azure. O WebMatrix é uma ferramenta gratuita de desenvolvimento da Web da Microsoft que inclui tudo que você precisa para o desenvolvimento de sites. O WebMatrix inclui vários recursos que o tornam fácil de usar Node. js como autocompletar de código, modelos predefinidos e suporte a editor Jade, menos e CoffeeScript. Saiba mais sobre o [WebMatrix para Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409).

Após a conclusão deste guia, você terá um site do Node.js em execução no Azure.
 
A seguinte é uma captura de tela do aplicativo concluído:

![Azure node Web site][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Entrar no Azure

Siga estas etapas para criar um site do Azure.

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure com o recurso Sites do Azure habilitado.  <br /> Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A7171371E"%20target="_blank").
<br />

1. Inicie o WebMatrix
2. Se essa for a primeira vez em que tiver usado o WebMatrix, você receberá uma solicitação iniciar sessão no Azure.  Caso contrário, você pode clicar na **Entrar** botão e escolha **Adicionar conta**.  Selecionar **Entrar** usando sua conta da Microsoft.

	![Add Account][addaccount]

3. Se você se inscrever para uma conta do Azure, você pode efetuar login usando sua conta da Microsoft:

	![Sign into Azure][signin]	


## Criar um site usando um modelo criado no Azure

1. Na tela inicial, clique no botão **novo** e escolha **Galeria de modelos** para criar um novo site a partir da Galeria de Modelos:

	![New site from Template Gallery][sitefromtemplate]

2. No **Site de modelo** caixa de diálogo, selecione **nó** e, em seguida, selecione **Express Site**. Finalmente, clique em **Próximo**. Se estiver faltando algum pré-requisito para o **Site Express** modelo, você será solicitado a instalá-los.

	![select express template][webmatrix-templates]

3. Se você tiver entrado no Azure, agora você tem a opção de criar um Site do Azure para seu site local.  Escolha um nome exclusivo e selecione o Center dados onde deseja que o seu site a ser criado: 

	![Create site on Azure][nodesitefromtemplateazure]
	
4. Após o WebMatrix termina de criar o site, o WebMatrix IDE é exibido.

	![webmatrix ide][webmatrix-ide]

##Publicar seu aplicativo no Azure

1. No WebMatrix, clique em **Publicar** da faixa de opções **Início** para exibir a caixa de diálogo **Publicar visualização** para o site.

	![publish preview][webmatrix-node-publishpreview]

2. Clique em **continuar**. Quando a publicação for concluída, o URL para o site no Azure é exibido na parte inferior do IDE WebMatrix

	![publish complete][webmatrix-publish-complete]

3. Clique no link para abrir o site no seu navegador.

	![Express web site][webmatrix-node-express-site]

##Modificar e republicar seu aplicativo

É possível modificar e republicar seu aplicativo. Aqui, você fará uma simples alteração no cabeçalho do arquivo **index.jade** e republicará o aplicativo.

1. No WebMatrix, selecione **arquivos de** e, em seguida, faz o **modos de exibição** pasta. Abra o arquivo **index.jade** clicando nele duas vezes.

	![webmatrix viewing index.jade][webmatrix-modify-index]

2. Altere segunda linha para o seguinte:

		p Welcome to #{title} with WebMatrix on Azure!

3. Salve suas alterações e, em seguida, clique no ícone publish. Por fim, clique em **continuar** no **Publicar visualização** caixa de diálogo e espere até que a atualização a ser publicado.

	![publish preview][webmatrix-republish]

4. Quando a publicação estiver concluída, use o link retornado quando o processo de publicação for concluído para ver o site atualizado.

	![Azure node Web site][webmatrix-node-completed]

##Próximas etapas

Para saber mais sobre as versões do Node.js que são fornecidas com o Azure e como especificar a versão a ser usada com seu aplicativo, consulte [Especificando uma versão do Node.js em um aplicativo do Azure](/pt-br/documentation/articles/nodejs-specify-node-version-azure-apps/).

Se você tiver problemas com seu aplicativo após ele ter sido implantado no Azure, consulte [Como depurar um aplicativo Node.js em sites do Azure](http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/Debug-Website/) para obter informações sobre como diagnosticar o problema.


[Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
[Site do WebMatrix]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix para Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[Publicar um site do Azure usando Git]: /pt-br/develop/nodejs/common-tasks/publishing-with-git/
[grátis]: /pt-br/pricing/free-trial
[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png



[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png







[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png



[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png


<!--HONumber=42-->
