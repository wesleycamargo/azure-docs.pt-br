---
title: Criar e implantar um aplicativo Web Node.js no Azure usando o WebMatrix
description: "Um tutorial que mostra como usar o WebMatrix para desenvolver um aplicativo Node.js e implantá-lo em Aplicativos Web do Serviço de Aplicativo do Azure."
services: app-service\web
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 42986058-57b8-42ea-af76-d6c6ba508608
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a551edf6aeaa77798173709deaf78e1a46431ad7


---
# <a name="build-and-deploy-a-nodejs-web-app-to-azure-using-webmatrix"></a>Criar e implantar um aplicativo Web Node.js no Azure usando o WebMatrix
Este tutorial mostra como usar o WebMatrix para desenvolver um aplicativo Node.js e implantá-lo em Aplicativos Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). O WebMatrix é uma ferramenta gratuita de desenvolvimento da Web da Microsoft que inclui tudo que você precisa para o desenvolvimento de sites ou aplicativos Web. O WebMatrix inclui vários recursos que o tornam fácil de usar Node. js como autocompletar de código, modelos predefinidos e suporte a editor Jade, menos e CoffeeScript. Saiba mais sobre [WebMatrix](https://www.microsoft.com/web/webmatrix/).

Após a conclusão deste guia, você terá um aplicativo Web Node.js em execução no Serviço de Aplicativo do Azure.

Abaixo, uma captura de tela do aplicativo concluído:

![Site do nó Azure][webmatrix-node-completed]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a name="sign-into-azure"></a>Entrar no Azure
Siga estas etapas para criar um aplicativo Web no Serviço de Aplicativo do Azure.

1. Inicie o WebMatrix
2. Se essa for a primeira vez em que tiver usado o WebMatrix, você receberá uma solicitação iniciar sessão no Azure.  Caso contrário, você pode clicar no botão **Entrar** e escolher **Adicionar Conta**.  Selecione **Entrar** usando sua conta da Microsoft.
   
    ![Adicionar conta][addaccount]
3. Se você se inscrever para uma conta do Azure, você pode efetuar login usando sua conta da Microsoft:
   
    ![Entrar no Azure][signin]    

## <a name="create-a-site-using-a-built-in-template-for-azure"></a>Criar um site usando um modelo criado no Azure
1. Na tela inicial, clique no botão **Novo** e escolha **Galeria de Modelos** de para criar um novo site:
   
    ![Novo site da Galeria de modelos][sitefromtemplate]
2. Na caixa de diálogo **Site do Modelo**, escolha **Nó** e **Site Express**. Finalmente, clique em **Próximo**. Se estiver faltando algum pré-requisito para o **Site Express** modelo, você será solicitado a instalá-los.
   
    ![Selecione o modelo express][webmatrix-templates]
3. Se tiver entrado no Azure, agora você terá a opção de criar um aplicativo Web do Serviço de Aplicativo para seu site local.  Escolha um nome exclusivo e selecione o data center em que deseja que seu Aplicativo Web do Serviço do Aplicativo seja criado: 
   
    ![Criar um site no Azure][nodesitefromtemplateazure]
4. Depois que o WebMatrix terminar de criar o site local e o aplicativo Web do Serviço de Aplicativo, o WebMatrix IDE será exibido.
   
    ![WebMatrix ide][webmatrix-ide]

## <a name="publish-your-application-to-azure"></a>Publicar seu aplicativo no Azure
1. No WebMatrix, clique em **Publicar** na faixa de opções **Página Inicial** para exibir a caixa de diálogo **Publicar Visualização** para o site.
   
    ![Publicar Visualização][webmatrix-node-publishpreview]
2. Clique em **Continuar**. Quando a publicação for concluída, a URL para o aplicativo Web do Serviço de Aplicativo será exibida na parte inferior do WebMatrix IDE
   
    ![Publicação concluída][webmatrix-publish-complete]
3. Clique no link para abrir o aplicativo Web do Serviço de Aplicativo no navegador.
   
    ![Aplicativo Web Express][webmatrix-node-express-site]

## <a name="modify-and-republish-your-application"></a>Modificar e republicar seu aplicativo
É possível modificar e republicar seu aplicativo. Aqui você fará uma simples alteração no cabeçalho do arquivo **index.jade** e republicará o aplicativo.

1. No WebMatrix, escolha **Arquivos** e, em seguida, expanda a pasta de **exibições**. Abra o arquivo **index.jade** clicando nele duas vezes.
   
    ![o WebMatrix exibindo Jade][webmatrix-modify-index]
2. Altere a linha de parágrafo para o seguinte:
   
        p Welcome to #{title} with WebMatrix on Azure!
3. Salve suas alterações e, em seguida, clique no ícone publish. Finalmente, clique em **Continuar** in the **Publicar Visualização** caixa de diálogo e espere até que a atualização a ser publicado.
   
    ![Publicar Visualização][webmatrix-republish]
4. Quando a publicação for concluída, use o link retornado quando o processo de publicação for concluído para ver o aplicativo Web do Serviço de Aplicativo atualizado.
   
    ![Aplicativo Web de nó do Azure][webmatrix-node-completed]

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as versões do Node.js que são fornecidas com o Azure e como especificar a versão a ser usada com seu aplicativo, consulte [Especificando uma versão do Node.js em um aplicativo do Azure](../nodejs-specify-node-version-azure-apps.md).

Se você tiver problemas com seu aplicativo após ele ter sido implantado no Azure, consulte [Como depurar um aplicativo Web Node.js no Serviço de Aplicativo do Azure](web-sites-nodejs-debug.md) para obter informações sobre como diagnosticar o problema.

## <a name="whats-changed"></a>O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[Site do WebMatrix]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix para Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

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



<!--HONumber=Nov16_HO3-->


