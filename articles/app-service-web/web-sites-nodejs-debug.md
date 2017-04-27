---
title: "Como depurar um aplicativo Web Node.js no Serviço de Aplicativo do Azure"
description: "Saiba como depurar um aplicativo Web Node.js no Serviço de Aplicativo do Azure"
tags: azure-portal
services: app-service\web
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: a48f906c-1a3e-43bc-ae84-7d2dde175b15
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 29cb2faa2e2d6cb9f45242794d88d3c8f881539d
ms.lasthandoff: 03/15/2017


---
# <a name="how-to-debug-a-nodejs-web-app-in-azure-app-service"></a>Como depurar um aplicativo Web Node.js no Serviço de Aplicativo do Azure
O Azure fornece diagnóstico interno para ajudar na depuração de aplicativos Node.js hospedados em Aplicativos Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) . Neste artigo, você aprenderá como habilitar o log de stdout e stderr, exibir informações de erro no navegador e como baixar e exibir arquivos de log.

O diagnóstico para aplicativos do Node.js hospedados no Azure é fornecido por [IISNode]. Embora este artigo descreve as configurações mais comuns para coletar informações de diagnóstico, ele não fornece uma referência completa para trabalhar com IISNode. Para obter mais informações sobre como trabalhar com IISNode, consulte o [IISNode Readme] no GitHub.

<a id="enablelogging"></a>

## <a name="enable-logging"></a>Habilitar o registro em log
Por padrão, um aplicativo Web do Serviço de Aplicativo captura somente informações de diagnóstico sobre implantações, como quando você implanta um aplicativo Web usando Git. Essas informações são úteis se você estiver tendo problemas durante a implantação, como uma falha durante a instalação de um módulo referenciado na **package.json**, ou se você estiver usando um script de implantação personalizada.

Para habilitar o registro em log de fluxos stdout e stderr, você deve criar um arquivo **IISNode.yml** na raiz do seu aplicativo do Node.js e adicionar o seguinte:

    loggingEnabled: true

Isso permite que o log de stderr e stdout do seu aplicativo do Node. js.

O **IISNode.yml** arquivo também pode ser usado para controlar se erros amigáveis ou desenvolvedor são retornados ao navegador quando ocorre uma falha. Para permitir que erros de desenvolvedor, adicione a seguinte linha para o **IISNode.yml** arquivo:

    devErrorsEnabled: true

Quando essa opção estiver habilitada, IISNode retornará a última 64K de informações enviadas para stderr em vez de um erro amigáveis, como "Ocorreu um erro interno do servidor".

> [!NOTE]
> Enquanto devErrorsEnabled é útil para diagnosticar problemas durante o desenvolvimento, ativá-lo em um ambiente de produção pode resultar em erros de desenvolvimento sendo enviados para usuários finais.
> 
> 

Se o arquivo **iisnode. yml** ainda não existir dentro de seu aplicativo, você deverá reiniciar o aplicativo Web depois de publicar o aplicativo atualizado. Se você estiver simplesmente alterando as configurações em um arquivo **IISNode.yml** arquivo que tenha sido publicado anteriormente, não é necessário reiniciar.

> [!NOTE]
> Se seu aplicativo Web foi criado usando os Cmdlets do PowerShell do Azure ou as ferramentas de linha de comando do Azure, um arquivo **IISNode.yml** padrão é criado automaticamente.
> 
> 

Para reiniciar o aplicativo Web, selecione-o no [Portal do Azure](https://portal.azure.com)e clique no botão **REINICIAR** :

![botão de reinicialização][restart-button]

Se as ferramentas de linha de comando do Azure estiverem instaladas em seu ambiente de desenvolvimento, você poderá usar o seguinte comando para reiniciar o aplicativo Web:

    azure site restart [sitename]

> [!NOTE]
> Enquanto loggingEnabled e devErrorsEnabled são os mais usados IISNode.yml opções de configuração para a captura de informações de diagnóstico, IISNode.yml pode ser usado para configurar uma variedade de opções para seu ambiente de hospedagem. Para obter uma lista completa das opções de configuração, consulte o arquivo [iisnode_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml).
> 
> 

<a id="viewlogs"></a>

## <a name="accessing-logs"></a>Acessando os logs
Logs de diagnóstico podem ser acessado de três maneiras; Usando o protocolo FTP (File Transfer), fazer o download de um arquivo Zip, ou como um live atualizado fluxo do log (também conhecido como um laço). Fazer o download do arquivo Zip dos arquivos de log ou exibição do fluxo ao vivo exigem o Azure Command-line Tools. Elas podem ser instaladas usando o seguinte comando:

    npm install azure-cli -g

Uma vez instalado, as ferramentas podem ser acessadas usando o comando 'azure'. As ferramentas de linha de comando devem primeiro ser configuradas para usar sua assinatura do Azure. Para obter informações sobre como realizar essa tarefa, consulte o **como fazer o download e importar as configurações de publicação** seção a [como uso o Azure Command-line Tools](../xplat-cli-connect.md) artigo.

### <a name="ftp"></a>FTP
Para acessar as informações de diagnóstico por FTP, visite o [Portal do Azure](https://portal.azure.com), selecione o seu aplicativo Web e selecione **PAINEL**. Na seção **links rápidos**, os links **LOGS DE DIAGNÓSTICO FTP** e **LOGS DE DIAGNÓSTICO FTPS** fornecem acesso aos logs usando o protocolo FTP.

> [!NOTE]
> Se ainda não tiver configurado o nome de usuário e a senha para FTP ou para implantação, você poderá fazer isso da página de gerenciamento **Início rápido** selecionando **Configurar credenciais de implantação**.
> 
> 

É a URL FTP retornado no painel de controle para o **arquivos de log** diretório, que irá conter as seguintes subpastas:

* [Método de implantação](web-sites-deploy.md) - se você usar um método de implantação como git, uma pasta com o mesmo nome será criada e conterá informações relacionadas às implantações.
* nodejs - Stdout e stderr informações capturadas de todas as instâncias do seu aplicativo (quando loggingEnabled for true.)

### <a name="zip-archive"></a>Arquivo Zip
Para fazer o download de um arquivo Zip dos logs de diagnóstico, use o seguinte comando das ferramentas de linha de comando do Azure:

    azure site log download [sitename]

Isso fará o download uma **diagnostics.zip** no diretório atual. Este arquivo contém a seguinte estrutura de diretórios:

* implantações - de um log de informações sobre a implantação do seu aplicativo
* arquivos de log
  
  * [Método de implantação](web-sites-deploy.md) - se você usar um método de implantação como Git, uma pasta com o mesmo nome será criada e conterá informações relacionadas às implantações.
  * nodejs - Stdout e stderr informações capturadas de todas as instâncias do seu aplicativo (quando loggingEnabled for true.)

### <a name="live-stream-tail"></a>Fluxo ao vivo (final)
Para visualizar um fluxo ao vivo da informações de log de diagnóstico, use o seguinte comando das ferramentas de linha de comando do Azure:

    azure site log tail [sitename]

Isso retornará uma cadeia de eventos de log que são atualizados à medida que ocorrem no servidor. Este fluxo irá retornar informações sobre a implantação, bem como informações de stdout e stderr (quando loggingEnabled for true.)

<a id="nextsteps"></a>

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como ativar e acessar as informações de diagnóstico do Azure. Embora essas informações sejam úteis em problemas de compreensão que ocorrem em seu aplicativo, podem indicar um problema com um módulo que você esteja usando ou indicar que a versão de Node.js usada pelos Aplicativos Web do Serviço de Aplicativo é diferente daquela usada no seu ambiente de implantação.

Para obter informações no trabalho com módulos no Azure, consulte [usando o Node. js módulos com aplicativos do Azure](../nodejs-use-node-modules-azure-apps.md)

Para obter informações sobre como especificar uma versão do Node.js para seu aplicativo, consulte [Especificar uma versão do Node.js em um aplicativo do Azure].

Para obter mais informações, consulte também o [Centro de desenvolvedores do Node.js](/develop/nodejs/).

## <a name="whats-changed"></a>O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, confira: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Se desejar começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

[IISNode]: https://github.com/tjanczuk/iisnode
[IISNode Readme]: https://github.com/tjanczuk/iisnode#readme
[How to Use The Azure Command-Line Interface]:../cli-install-nodejs.md
[Using Node.js Modules with Azure Applications]: ../nodejs-use-node-modules-azure-apps.md
[Especificar uma versão do Node.js em um aplicativo do Azure]: ../nodejs-specify-node-version-azure-apps.md

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png


