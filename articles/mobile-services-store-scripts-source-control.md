<properties urlDisplayName="Store server scripts in source control" pageTitle="Armazenar scripts de servidor no controle do código-fonte - Serviços Móveis do Azure" metaKeywords="" description="Learn how to store your server script files and modules in a local Git repo on your computer." metaCanonical="" services="" documentationCenter="Mobile" title="Store server scripts in source control" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />


# Armazenar scripts de servidor no controle do código-fonte

Este tópico mostra como configurar o controle do código-fonte pela primeira vez nos Serviços Móveis do Azure para armazenar seus scripts de servidor em um repositório Git. Os scripts e outros arquivos de código JavaScript podem ser promovidos do seu repositório local para o seu serviço móvel de produção. Ele também mostra como definir o código compartilhado que pode ser exigido por vários scripts e como carregar módulos do Node.js. 

O tutorial o orientará por estas etapas:

1. [Habilitar o controle do código-fonte no serviço móvel].
2. [Instalar o Git e criar o repositório local].
3. [Implantar arquivos de script atualizados no serviço móvel].
4. [Aproveitar o código compartilhado e os módulos do Node.js nos seus scripts de servidor].

Para concluir este tutorial, você já deverá ter criado um serviço móvel, concluindo o tutorial [Introdução aos Serviços Móveis] ou [Introdução aos dados].

<h2><a name="enable-source-control"></a>Habilitar o controle do código-fonte no seu serviço móvel</h2>

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e depois no serviço móvel.

	![][0]

2. Clique na guia **Painel** e, em seguida, em **Visão rápida**, clique em **Configurar controle do código-fonte** e em **Sim** para confirmar.

	![][1]

	> [WACOM.NOTE]
	> O controle do código-fonte é um recurso de visualização. É recomendável que você faça backup dos seus arquivos de script regularmente, mesmo que eles estejam armazenados nos Serviços Móveis.

3. Forneça um **Nome do usuário**, uma **Nova senha**, confirme a senha e clique no botão de seleção. 

	![][2]

	O repositório Git é criado no seu serviço móvel. Anote as credenciais que acabou de fornecer, pois você as utilizará para acessar esse repositório.

4. Clique na guia Configurar e observe o novo campo **Controle do código-fonte**.

	![][3]

	A URL do repositório Git é exibida. Você usará essa URL para clonar o repositório para o computador local.

Agora que você habilitou o controle do código-fonte no seu serviço móvel, é hora de usar Git para clonar o repositório para o seu computador local.

<h2><a name="clone-repo"></a>Instalar o Git e criar o repositório local</h2>

1. Instale o Git no seu computador local. 

	As etapas necessárias para instalar o Git variam de acordo com o sistema operacional. Consulte [Instalando o Git] para distribuições específicas de sistemas operacionais e orientações de instalação.

	> [WACOM.NOTE]
	> Em alguns sistemas operacionais, uma versão de linha de comando e uma versão da GUI do Git estão disponíveis. As instruções fornecidas neste artigo usam a versão de linha de comando.

2. Abra uma linha de comando, como **GitBash** (Windows) or **Bash** (shell do Unix). Nos sistemas OS X, você pode acessar a linha de comando por meio do aplicativo **Terminal**.

3. Na linha de comando, altere para o diretório no qual você armazenará seus scripts. Por exemplo, `cd SourceControl`.

4. Use o comando a seguir para criar uma cópia local do seu novo repositório Git, substituindo `<URL_do_seu_git>` pela URL do repositório Git para seu serviço móvel:

		git clone <your_git_URL>

5. Quando solicitado, digite o nome de usuário e a senha definidos no momento em que habilitou o controle do código-fonte no seu serviço móvel. Após a autenticação bem-sucedida, você verá uma série de respostas, como esta:

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Navegue até o diretório do qual executou o comando "git clone" e observe a seguinte estrutura de diretórios:

	![4][4]

	Nesse caso, um novo diretório é criado com o nome do serviço móvel, que é o repositório local para o serviço de dados. 

7. Abra a subpasta .\service\table e observe se ela contém um arquivo TodoItem.json, que é uma representação JSON das permissões de operação na tabela TodoItem. 

	Quando os scripts de servidor foram definidos nessa tabela, você também terá um ou mais arquivos denominados <code>TodoItem._&lt;operation&gt;_.js</code> contendo os scripts para a operação de tabela específica. O Agendador e os scripts de APIs personalizadas serão mantidos em pastas separadas com esses respectivos nomes. Para obter mais informações, consulte [Controle do código-fonte].

Agora que criou seu repositório local, você pode fazer alterações nos scripts de servidor e enviar por push as alterações de volta para o serviço móvel.

<h2><a name="deploy-scripts"></a>Implantar arquivos de script atualizados no seu serviço móvel</h2>

1. Navegue até a subpasta .\service\table e, se um arquivo de todoitem.insert.js ainda não existir, crie-o agora.

2. Abra o novo arquivo todoitem.insert.js em um editor de texto, cole o código a seguir e salve suas alterações:

		function insert(item, user, request) {
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}
	
	Esse código simplesmente grava o item inserido no log. Se esse arquivo já contiver código, basta adicionar um código JavaScript válido a ele, por exemplo, uma chamada para `console.log()`, e salvar as alterações. 

3. No prompt de comando do Git, digite o comando a seguir para iniciar o acompanhamento do novo arquivo de script:

		$ git add .
	

4. Digite o comando a seguir para confirmar as alterações:

		$ git commit -m "updated the insert script"

5. Digite o comando a seguir para carregar as alterações para o repositório remoto:

		$ git push origin master
	
	Uma série de comandos deverão ser exibidos indicando que a confirmação foi implantada no serviço móvel.

6. De volta ao Portal de Gerenciamento, clique na guia **Dados** e na tabela **TodoItem**.

	![][5]

3. Clique em **Script** e selecione a operação **Insert**.

	![][6]

	Observe que o script da operação de inserção exibido é o mesmo do código JavaScript que você acabou de carregar para o repositório.

<h2><a name="use-npm"></a>Aproveitar o código compartilhado e módulos do Node. js em seus scripts de servidor</h2>
Os Serviços Móveis fornecem acesso ao conjunto completo de módulos do Node.js do núcleo, que você pode usar no seu código por meio da função **require**. O serviço móvel também pode usar módulos do Node.js que não fazem parte do pacote do Node.js do núcleo, e você pode até mesmo definir o seu próprio código compartilhado como módulos do Node.js. Para obter mais informações sobre como criar módulos, consulte a documentação da API de Node.js de [Módulos][: Módulos] na documentação de referência da API do Node.js.

Em seguida, você adicionará o módulo [node-uuid] do Node.js ao seu serviço móvel usando o controle do código-fonte e o gerenciador de pacotes do Node.js (NPM). Esse módulo será usado, então, para gerar um novo valor de GUID para a propriedade **uuid** nos itens inseridos. 

1. Se ainda não tiver instalado o Node.js no seu computador local, faça isso seguindo as etapas do <a href="http://nodejs.org/" target="_blank">site do Node.js</a>. 

2. Navegue até a pasta `.\service` do seu repositório Git local e, em seguida, no prompt de comando, execute o comando a seguir:

		npm install node-uuid

	O NPM cria o diretório `node_modules` no local atual e instala o módulo [node-uuid] no subdiretório `\node-uuid`. 

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Quando <code>node_modules</code> já existir na hierarquia do diretório, o NPM criará o subdiretório <code>\node-uuid</code> lá, em vez de criar um novo <code>node_modules</code> no repositório. Nesse caso, simplesmente exclua o diretório <code>node_modules</code> existente.</p>
	</div>

4. Agora, navegue até a subpasta .\service\table, abra o arquivo todoitem.insert.js e modifique-o da seguinte maneira:

		function insert(item, user, request) {
		    var uuid = require('node-uuid');
		    item.uuid = uuid.v1();
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}

	Esse código adiciona uma coluna uuid à tabela, preenchendo-a com identificadores GUID exclusivos.

5. Assim como na seção anterior, digite o comando a seguir no prompt de comando do Git: 

		$ git add .
		$ git commit -m "added node-uuid module"
		$ git push origin master
		
	Isso adicionará o novo arquivo, confirmará suas alterações e enviará o novo módulo node-uuid e as alterações por push ao script todoitem.insert.js para o seu serviço móvel.

## <a name="next-steps"> </a>Próximas etapas

Agora que concluiu este tutorial, você sabe como armazenar seus scripts no controle do código-fonte. Para saber mais sobre como trabalhar com scripts de servidor e APIs personalizadas, consulte: 

+ [Trabalhar com scripts de servidor em Serviços Móveis]
	<br/>Mostra como trabalhar com scripts de servidor, o agendador de trabalhos e APIs personalizadas.

+ [Definir uma API personalizada que ofereça suporte a notificações pull] 
	<br/> Mostra como usar APIs personalizadas para dar suporte a notificações periódicas que atualizam blocos ao vivo em um aplicativo da Windows Store.

<!-- Anchors. -->
[Habilitar o controle do código-fonte no seu serviço móvel]: #enable-source-control
[Instalar o Git e criar o repositório local]: #clone-repo
[Implantar arquivos de script atualizados no seu serviço móvel]: #deploy-scripts
[Aproveitar o código compartilhado e módulos do Node. js em seus scripts de servidor]: #use-npm

<!-- Images. -->
[0]: ./media/mobile-services-store-scripts-source-control/mobile-services-selection.png
[1]: ./media/mobile-services-store-scripts-source-control/mobile-setup-source-control.png
[2]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-credentials.png
[3]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-configure.png
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Site do Git]: http://git-scm.com
[Controle do código-fonte]: http://msdn.microsoft.com/pt-br/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Instalando o Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[Trabalhar com scripts de servidor em Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-server-scripts
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-users-js
[WindowsAzure.com]: http://www.windowsazure.com/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Definir uma API personalizada que ofereça suporte a notificações pull]: /pt-br/develop/mobile/tutorials/create-pull-notifications-dotnet
[Documentação da API do Node.js: Módulos]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid
