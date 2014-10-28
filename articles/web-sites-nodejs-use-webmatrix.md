<properties linkid="web-site-with-webmatrix" urlDisplayName="Website with WebMatrix" pageTitle="Node.js website with WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that teaches you how to WebMatrix to develop and deploy a Node.js application to an Azure website." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure using WebMatrix" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Criar e implantar um site do Node.js para o Azure usando WebMatrix

Este tutorial mostra como usar o WebMatrix para desenvolver e implantar um aplicativo Node.js para um Site do Azure. O WebMatrix é uma ferramenta gratuita de desenvolvimento da Web da Microsoft que inclui tudo que você precisa para o desenvolvimento de sites. O WebMatrix inclui vários recursos que o tornam fácil de usar Node. js como autocompletar de código, modelos predefinidos e suporte a editor Jade, menos e CoffeeScript. Saiba mais sobre o [WebMatrix for Azure][WebMatrix for Azure].

Após a conclusão deste guia, você terá um site do Node.js em execução no Azure.

A seguinte é uma captura de tela do aplicativo concluído:

![Site do nó Azure][Site do nó Azure]

[WACOM.INCLUDE [create-account-and-websites-note][create-account-and-websites-note]]

## Entrar no Azure

Siga estas etapas para criar um site do Azure.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong>
<p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure com o recurso Sites do Azure habilitado.</p>
<p>Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p>
</div>

1.  Inicie o WebMatrix
2.  Se essa for a primeira vez em que tiver usado o WebMatrix, você receberá uma solicitação iniciar sessão no Azure. Caso contrário, você pode clicar na **Sign In** botão e escolha **adicionar conta** Selecionar **Entrar** usando sua conta da Microsoft.

    ![Adicionar conta][Adicionar conta]

3.  Se você se inscrever para uma conta do Azure, você pode efetuar login usando sua conta da Microsoft:

    ![Entrar no Azure][Entrar no Azure]

## Criar um site usando um modelo criado no Azure

1.  Na tela inicial, clique o **novo** botão e escolha **Galeria de modelos de** para criar um novo site da Galeria de modelos:

    ![Novo site da Galeria de modelos][Novo site da Galeria de modelos]

2.  No **Site de modelo** caixa de diálogo, selecione **nó** e, em seguida, selecione **Express Site**. Finalmente, clique em **Próximo**. Se estiver faltando algum pré-requisito para o **Site Express** modelo, você será solicitado a instalá-los.

    ![Selecione o modelo express][Selecione o modelo express]

3.  Se você tiver entrado no Azure, agora você tem a opção de criar um Site do Azure para seu site local. Escolha um nome exclusivo e selecione o Center dados onde deseja que o seu site a ser criado:

    ![Criar um site no Azure][Criar um site no Azure]

4.  Após o WebMatrix termina de criar o site, o WebMatrix IDE é exibido.

    ![WebMatrix ide][WebMatrix ide]

## Publicar seu aplicativo no Azure

1.  No WebMatrix, clique em **Publicar** da faixa de opções **Início** para exibir a caixa de diálogo **Publicar visualização** para o site.

    ![Publicar visualização][Publicar visualização]

2.  Clique em **Continuar**. Quando a publicação for concluída, o URL para o site no Azure é exibido na parte inferior do IDE WebMatrix.

    ![Publicação concluída][Publicação concluída]

3.  Clique no link para abrir o site no seu navegador.

    ![Site Express][Site Express]

## Modificar e republicar seu aplicativo

É possível modificar e republicar seu aplicativo. Aqui você fará uma simples alteração no cabeçalho do arquivo **index.jade** e republicará o aplicativo.

1.  No WebMatrix, selecione **arquivos** e, em seguida, expanda a pasta **Visualização**. Abra o arquivo **index.jade** clicando nele duas vezes.

    ![o WebMatrix exibindo Jade][o WebMatrix exibindo Jade]

2.  Altere segunda linha para o seguinte:

        p Welcome to #{title} with WebMatrix on Azure!

3.  Salve suas alterações e, em seguida, clique no ícone publish. Por fim, clique em **continuar** no **Publicar visualização** caixa de diálogo e espere até que a atualização a ser publicado.

    ![Publicar visualização][1]

4.  Quando a publicação estiver concluída, use o link retornado quando o processo de publicação for concluído para ver o site atualizado.

    ![Site do nó Azure][Site do nó Azure]

## Próximas etapas

Para saber mais sobre as versões do Node.js que são fornecidas com o Azure e como especificar a versão a ser usada com seu aplicativo, consulte [Especificando uma versão do Node.js em um aplicativo do Azure][Especificando uma versão do Node.js em um aplicativo do Azure].

Se você tiver problemas com seu aplicativo após ele ter sido implantado no Azure, consulte [Como depurar um aplicativo Node.js em Sites do Azure][Como depurar um aplicativo Node.js em Sites do Azure] para obter informações sobre como diagnosticar o problema.

  [WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Site do nó Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A7171371E
  [Adicionar conta]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
  [Entrar no Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
  [Novo site da Galeria de modelos]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
  [Selecione o modelo express]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png
  [Criar um site no Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
  [WebMatrix ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
  [Publicar visualização]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png
  [Publicação concluída]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
  [Site Express]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
  [o WebMatrix exibindo Jade]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
  [1]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
  [Especificando uma versão do Node.js em um aplicativo do Azure]: /pt-br/documentation/articles/nodejs-specify-node-version-azure-apps/
  [Como depurar um aplicativo Node.js em Sites do Azure]: http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/Debug-Website/
