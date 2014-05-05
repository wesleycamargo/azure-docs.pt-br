<properties linkid="web-site-with-webmatrix" urlDisplayName="Site da Web com o WebMatrix" pageTitle="site do Node. js com o WebMatrix - tutorial Azure" metaKeywords="" description="um tutorial que ensina como ao WebMatrix para desenvolver e implantar um aplicativo do Node. js para um Azure na web site." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Criar e implantar um site do Node. js no Azure usando o WebMatrix" authors="" solutions="" manager="" editor="" />





# Criar e implantar um site do Node.js para o Azure usando WebMatrix

Este tutorial mostra como usar o WebMatrix para desenvolver e implantar um aplicativo Node.js para um Site do Azure. O WebMatrix é uma ferramenta gratuita de desenvolvimento da Web da Microsoft que inclui tudo que você precisa para o desenvolvimento de sites. O WebMatrix inclui vários recursos que o tornam fácil de usar Node. js como autocompletar de código, modelos predefinidos e suporte a editor Jade, menos e CoffeeScript. Saiba mais sobre o [WebMatrix for Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409).

Após a conclusão deste guia, você terá um site do Node.js em execução no Azure.
 
A seguinte é uma captura de tela do aplicativo concluído:

![Site do nó Azure][webmatrix-node-completed]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Entrar no Azure

Siga estas etapas para criar um site no Azure.

<div class="dev-callout"><strong>Observação</strong>
<p>Para concluir este tutorial, você precisa de uma conta do Azure com o recurso Sites do Azure habilitado.</p>
<p>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Avaliação gratuita do Azure</a>.</p>
</div>
<br />

1. Inicie o WebMatrix
2. Se essa for a primeira vez em que tiver usado o WebMatrix, você receberá uma solicitação iniciar sessão no Azure.  Caso contrário, você pode clicar na **Sign In** botão e escolha **adicionar conta**  Selecionar **Entrar** usando sua conta da Microsoft.

	![Adicionar conta][addaccount]

3. Se você se inscrever para uma conta do Azure, você pode efetuar login usando sua conta da Microsoft:

	![Entrar no Azure][signin]	


## Criar um site usando um modelo criado no Azure

1. Na tela inicial, clique o **novo** botão e escolha **Galeria de modelos de** para criar um novo site da Galeria de modelos:

	![Novo site da Galeria de modelos][sitefromtemplate]

2. No **Site de modelo** caixa de diálogo, selecione **nó** e, em seguida, selecione **Express Site** Finalmente, clique em **Avançar**. Se estiver faltando algum pré-requisito para o **Site Express** modelo, você será solicitado a instalá-los.

	![Selecione o modelo express][webmatrix-templates]

3. Se você tiver entrado no Azure, agora você tem a opção de criar um Site do Azure para seu site local.  Escolha um nome exclusivo e selecione o Center dados onde deseja que o seu site a ser criado: 

	![Criar um site no Azure][nodesitefromtemplateazure]
	
4. Após o WebMatrix terminar de criar o site, o WebMatrix IDE é exibido.

	![WebMatrix ide][webmatrix-ide]

##Publicar seu aplicativo no Azure

1. No WebMatrix, clique em **Publicar** da faixa de opções **Início** para exibir a caixa de diálogo **Publicar visualização** para o site.

	![Publicar visualização][webmatrix-node-publishpreview]

2. Clique em **continuar** Quando a publicação for concluída, o URL para o site no Azure é exibido na parte inferior do IDE WebMatrix.

	![Publicação concluída][webmatrix-publish-complete]

3. Clique no link para abrir o site no seu navegador.

	![Site Express][webmatrix-node-express-site]

##Modificar e republicar seu aplicativo

É possível modificar e republicar seu aplicativo. Aqui você fará uma simples alteração no cabeçalho do arquivo **index.jade** e republicará o aplicativo.

1. No WebMatrix, selecione **arquivos de**e, em seguida, faz o **modos de exibição** pasta. Abra o arquivo **index.jade** clicando nele duas vezes.

	![o WebMatrix exibindo Jade][webmatrix-modify-index]

2. Altere segunda linha para o seguinte:

		p Welcome to #{title} with WebMatrix on Azure!

3. Salve suas alterações e, em seguida, clique no ícone publish. Por fim, clique em **continuar** no **Publicar visualização** caixa de diálogo e espere até que a atualização a ser publicado.

	![Publicar visualização][webmatrix-republish]

4. Quando a publicação estiver concluída, use o link retornado quando o processo de publicação for concluído para ver o site atualizado.

	![Site do nó Azure][webmatrix-node-completed]

##Próximas etapas

Para saber mais sobre as versões do Node.js que são fornecidas com o Azure e como especificar a versão a ser usada com seu aplicativo, consulte [Especificando uma versão do Node.js em um aplicativo do Azure](http://www.windowsazure.com/pt-br/develop/nodejs/common-tasks/specifying-a-node-version/).

Se você tiver problemas com seu aplicativo após ele ter sido implantado no Azure, consulte [Como depurar um aplicativo Node.js em Sites do Azure](http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/Debug-Website/) para obter informações sobre como diagnosticar o problema.


[Azure Management Portal]: http://manage.windowsazure.com
[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[Publishing an Azure Web site using Git]: /pt-br/develop/nodejs/common-tasks/publishing-with-git/
[for free]: /pt-br/pricing/free-trial
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


