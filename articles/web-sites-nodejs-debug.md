<properties urlDisplayName="Debug Websites (Node)" pageTitle="Como depurar sites do Azure no Node.js" metaKeywords="debug website azure, debugging azure, troubleshooting azure web site, troubleshoot azure website node" description="Saiba como depurar um site da Web do Azure no Node.js." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="How to debug a Node.js application in Azure Websites" authors="larryfr" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />





#Como depurar um aplicativo Node.js nos Sites do Azure

O Azure fornece diagnóstico interno para ajudar na depuração de um aplicativo Node.js hospedado em Sites do Azure. Neste artigo, você aprenderá como habilitar o log de stdout e stderr, exibir informações de erro no navegador e como baixar e exibir arquivos de log.

Diagnóstico para aplicativos do Node. js hospedado no Azure é fornecido por [IISNode] Embora este artigo descreve as configurações mais comuns para coletar informações de diagnóstico, ele não fornece uma referência completa para trabalhar com IISNode. Para obter mais informações sobre como trabalhar com IISNode, consulte o [IISNode Readme] no GitHub.

##<a id="enablelogging"></a>Habilitar o registro em log

Por padrão, Sites do Azure apenas capturar informações de diagnóstico sobre implantações, como quando você implanta um site usando git. Essas informações são úteis se você estiver tendo problemas durante a implantação, como uma falha durante a instalação de um módulo referenciado na **package.json**, ou se você estiver usando um script de implantação personalizada.

Para habilitar o log de fluxos stdout e stderr, você deve criar um **IISNode.yml** de arquivos na raiz do seu aplicativo do Node. js e adicione o seguinte:

	loggingEnabled: true

Isso permite que o log de stderr e stdout do seu aplicativo do Node. js.

O **IISNode.yml** arquivo também pode ser usado para controlar se erros amigáveis ou desenvolvedor são retornados ao navegador quando ocorre uma falha. Para permitir que erros de desenvolvedor, adicione a seguinte linha para o **IISNode.yml** arquivo:

	devErrorsEnabled: true

Quando essa opção estiver habilitada, IISNode retornará a última 64K de informações enviadas para stderr em vez de um erro amigáveis, como "Ocorreu um erro interno do servidor".

<div class="dev-callout">
<strong>Observação</strong>
<p>Enquanto devErrorsEnabled é útil para diagnosticar problemas durante o desenvolvimento, ativá-lo em um ambiente de produção pode resultar em erros de desenvolvimento sendo enviados para usuários finais.</p>
</div>

Se o **IISNode.yml** arquivo não existisse já dentro de seu aplicativo, você deve reiniciar o seu site da web depois de publicar o aplicativo atualizado. Se você estiver simplesmente alterando as configurações em um arquivo **IISNode.yml** arquivo que tenha sido publicado anteriormente, não é necessário reiniciar.

<div class="dev-callout">
<strong>Observação</strong>
<p>Se seu site da web foi criado usando as ferramentas de linha de comando do Azure ou Cmdlets do PowerShell do Azure, um arquivo padrão <strong>IISNode.yml</strong> é criado automaticamente.</p>
</div>

Você pode reiniciar o site selecionando o site a partir do [Portal de Gerenciamento Azure]e, em seguida, selecionando o botão **REINICIAR**:

![restart button][restart-button]

Se as ferramentas de linha de comando do Azure são instaladas em seu ambiente de desenvolvimento, você pode usar o seguinte comando para reiniciar o site:

	azure site restart [sitename]

<div class="dev-callout">
<strong>Observação</strong>
<p>Enquanto loggingEnabled e devErrorsEnabled são os mais usados IISNode.yml opções de configuração para a captura de informações de diagnóstico, IISNode.yml pode ser usado para configurar uma variedade de opções para seu ambiente de hospedagem. Para obter uma lista completa das opções de configuração, consulte o arquivo <a href="https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml">iisnode_schema.xml</a> .</p>
</div>

##<a id="viewlogs"></a>Acesse os logs

Logs de diagnóstico podem ser acessado de três maneiras; Usando o protocolo FTP (File Transfer), fazer o download de um arquivo Zip, ou como um live atualizado fluxo do log (também conhecido como um laço). Fazer o download do arquivo Zip dos arquivos de log ou exibição do fluxo ao vivo exigem o Azure Command-line Tools. Elas podem ser instaladas usando o seguinte comando:

	npm install azure-cli -g

Uma vez instalado, as ferramentas podem ser acessadas usando o comando 'azure'. As ferramentas de linha de comando devem primeiro ser configuradas para usar sua assinatura do Azure. Para obter informações sobre como realizar essa tarefa, consulte o **como fazer o download e importar as configurações de publicação** seção a [como uso o Azure Command-line Tools] artigo.

###FTP

Para acessar as informações de diagnóstico por FTP, visite o [Portal Azure], selecione o seu site da web e, em seguida, selecione o **PAINEL**. No **links rápidos** seção, o **FTP LOGS de diagnóstico** e **LOGS de diagnóstico FTPS** links fornecem acesso aos logs usando o protocolo FTP.

<div class="dev-callout">
<strong>Observação</strong>
<p>Se você não tiver configurado anteriormente o nome de usuário e senha de FTP ou implantação, você pode fazer isso a partir da página de gerenciamento <strong>QuickStart</strong> selecionando <strong>Configurar credenciais de implantação</strong>.</p>
</div>

É a URL FTP retornado no painel de controle para o **arquivos de log** diretório, que irá conter as seguintes subpastas:

* [Método de implantação] - Se você usar um método de implantação, como Git, uma pasta de mesmo nome será criada e conterá informações relacionadas às implantações.

* nodejs - Stdout e stderr informações capturadas de todas as instâncias do seu aplicativo (quando loggingEnabled for true.)

###Arquivo Zip

Para fazer o download de um arquivo Zip dos logs de diagnóstico, use o seguinte comando das ferramentas de linha de comando do Azure:

	azure site log download [sitename]

Isso fará o download uma **diagnostics.zip** no diretório atual. Este arquivo contém a seguinte estrutura de diretórios:

* implantações - de um log de informações sobre a implantação do seu aplicativo

* Arquivos de log

	* [Método de implantação] - Se você usar um método de implantação, como Git, uma pasta de mesmo nome será criada e conterá informações relacionadas às implantações.

	* nodejs - Stdout e stderr informações capturadas de todas as instâncias do seu aplicativo (quando loggingEnabled for true.)

###Fluxo ao vivo (final)

Para visualizar um fluxo ao vivo da informações de log de diagnóstico, use o seguinte comando das ferramentas de linha de comando do Azure:

	azure site log tail [sitename]

Isso retornará uma cadeia de eventos de log que são atualizados à medida que ocorrem no servidor. Este fluxo irá retornar informações sobre a implantação, bem como informações de stdout e stderr (quando loggingEnabled for true.)

##<a id="nextsteps"></a>Próximas etapas

Neste artigo, você aprendeu como ativar e acessar as informações de diagnóstico do Azure. Enquanto essas informações são úteis em problemas de compreensão que ocorrem em seu aplicativo, ele pode indicar um problema com um módulo que estiver usando ou a versão do Node. js usados pelos Sites do Azure é diferente daquela usada no seu ambiente de implantação.

Para obter informações no trabalho com módulos no Azure, consulte [usando o Node. js módulos com aplicativos do Azure].

Para obter informações sobre como especificar uma versão do Node. js para seu aplicativo, consulte [especificar uma versão do Node. js em um aplicativo do Azure].

[IISNode]: https://github.com/tjanczuk/iisnode
[Leiame do IISNode]: https://github.com/tjanczuk/iisnode#readme
[Como usar as ferramentas da linha de comando do Azure]: /pt-br/documentation/articles/xplat-cli/
[Usando Módulos no Node.js com aplicativos do Azure]: /pt-br/documentation/articles/nodejs-use-node-modules-azure-apps/
[Especificar uma versão do Node.js em um aplicativo do Azure]: /pt-br/documentation/articles/nodejs-specify-node-version-azure-apps/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png

<!--HONumber=35.2-->
