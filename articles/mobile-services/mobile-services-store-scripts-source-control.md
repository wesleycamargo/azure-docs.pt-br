<properties
	pageTitle="Armazenar o código do projeto back-end JavaScript no controle de origem | Serviços Móveis do Azure"
	description="Saiba como armazenar seus arquivos de script de servidor e módulos em um repositório Git local no computador."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="ggailey777"/>

# Armazenar o código de seu projeto de serviço móvel no controle do código-fonte

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
- [.NET backend](mobile-services-dotnet-backend-store-code-source-control.md)
- [Javascript backend](mobile-services-store-scripts-source-control.md)

Este tópico mostra como usar o controle do código-fonte dos Serviços Móveis do Azure para armazenar scripts do servidor. Scripts e outros arquivos de código de back-end de JavaScript podem ser promovidos do repositório de Git local para o serviço móvel de produção. Ele também mostra como definir o código compartilhado que pode ser exigido por vários scripts e como usar o arquivo package.json para adicionar módulos Node.js ao serviço móvel.

Para concluir este tutorial, você já deve ter criado um serviço móvel por meio da conclusão do tutorial [Guia de introdução aos serviços móveis].

##<a name="enable-source-control"></a>Habilitar o controle do código-fonte no seu serviço móvel

[AZURE.INCLUDE [mobile-services-enable-source-control](../../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Instalar o Git e criar o repositório local

1. Instale o Git no seu computador local.

	As etapas necessárias para instalar o Git variam de acordo com o sistema operacional. Consulte [Instalando o Git] para distribuições específicas de sistemas operacionais e orientações de instalação.

	> [AZURE.NOTE]Em alguns sistemas operacionais, uma versão da linha de comando e uma versão da GUI do Git estão disponíveis. As instruções fornecidas neste artigo usam a versão de linha de comando.

2. Abra uma linha de comando, como **GitBash** (Windows) ou **Bash** (shell Unix). Nos sistemas OS X, você pode acessar a linha de comando por meio do aplicativo **Terminal**.

3. Na linha de comando, altere para o diretório no qual você armazenará seus scripts. Por exemplo: `cd SourceControl`.

4. Use o comando a seguir para criar uma cópia local do seu novo repositório Git, substituindo `<your_git_URL>` pela URL do repositório Git para seu serviço móvel:

		git clone <your_git_URL>

5. Quando solicitado, digite o nome de usuário e a senha definidos no momento em que habilitou o controle do código-fonte no seu serviço móvel. Após a autenticação bem-sucedida, você verá uma série de respostas, como esta:

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Navegue até o diretório do qual executou o comando `git clone` e observe a seguinte estrutura de diretórios:

	![4][4]

	Nesse caso, um novo diretório é criado com o nome do serviço móvel, que é o repositório local para o serviço de dados.

7. Abra a subpasta .\\service\\table e observe se ela contém um arquivo TodoItem.json, que é uma representação JSON das permissões de operação na tabela TodoItem.

	Quando os scripts de servidor tiverem sido definidos nessa tabela, você também terá um ou mais arquivos denominados <code>TodoItem._&lt;operation&gt;_.js</code> contendo os scripts para a operação da tabela específica. O Agendador e os scripts de APIs personalizadas serão mantidos em pastas separadas com esses respectivos nomes. Para obter mais informações, consulte [Controle do código-fonte].

Agora que criou seu repositório local, você pode fazer alterações nos scripts de servidor e enviar por push as alterações de volta para o serviço móvel.

##<a name="deploy-scripts"></a>Implantar arquivos de script atualizados no seu serviço móvel

1. Navegue até a subpasta .\\service\\table e, se um arquivo de todoitem.insert.js ainda não existir, crie-o agora.

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

6. De volta ao [Portal clássico do Azure], clique na guia **Dados**. Em seguida, clique na tabela **TodoItem**, em **Script** e selecione a operação **Inserir**. Observe que o script da operação de inserção exibido é o mesmo do código JavaScript que você acabou de carregar para o repositório.

##<a name="use-npm"></a>Aproveitar o código compartilhado e os módulos do Node.js nos seus scripts de servidor

Os Serviços Móveis fornecem acesso ao conjunto completo de módulos do Node.js do núcleo, que você pode usar no seu código por meio da função **require**. O serviço móvel também pode usar módulos do Node.js que não fazem parte do pacote do Node.js do núcleo, e você pode até mesmo definir o seu próprio código compartilhado como módulos do Node.js. Para obter mais informações sobre como criar módulos, consulte [Módulos] na documentação de referência da API do Node.js.

A maneira recomendada para adicionar módulos Node.js ao seu serviço móvel é adicionando referências ao arquivo package.json do serviço. Em seguida, você adiciona o módulo Node.js [node-uuid] ao seu serviço móvel atualizando o arquivo package.json. Quando a atualização é empurrado para o Azure, o serviço móvel é reiniciado e o módulo é instalado. Esse módulo será usado, então, para gerar um novo valor de GUID para a propriedade **uuid** nos itens inseridos.

2. Navegue até a pasta `.\service` do seu repositório Git local, abra o arquivo package.json em um editor de texto e adicione o campo a seguir ao objeto **dependencies**:

		"node-uuid": "~1.4.3"

	>[AZURE.NOTE]Essa atualização no arquivo package.json causará uma reinicialização no seu serviço móvel depois que a confirmação for enviada por push.

4. Agora, navegue até a subpasta .\\service\\table, abra o arquivo todoitem.insert.js e modifique-o da seguinte maneira:

		function insert(item, user, request) {
		    var uuid = require('node-uuid');
		    item.uuid = uuid.v1();
		    request.execute();
		    console.log(item);
		}

	Esse código adiciona uma coluna uuid à tabela, preenchendo-a com identificadores GUID exclusivos.

5. Assim como na seção anterior, digite o comando a seguir no prompt de comando do Git:

		$ git add .
		$ git commit -m "added node-uuid module"
		$ git push origin master

	Isso adicionará o novo arquivo, confirmará suas alterações e enviará o novo módulo node-uuid e as alterações por push ao script todoitem.insert.js para o seu serviço móvel.

## <a name="next-steps"> </a>Próximas etapas

Agora que concluiu este tutorial, você sabe como armazenar seus scripts no controle do código-fonte. Para saber mais sobre como trabalhar com scripts de servidor e APIs personalizadas, consulte:

+ [Trabalhar com scripts de servidor em Serviços Móveis] <br/>Mostra como trabalhar com scripts de servidor, o agendador de trabalhos e APIs personalizadas.

<!-- Anchors. -->
[Enable source control in your mobile service]: #enable-source-control
[Install Git and create the local repository]: #clone-repo
[Deploy updated script files to your mobile service]: #deploy-scripts
[Leverage shared code and Node.js modules in your server scripts]: #use-npm

<!-- Images. -->
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Git website]: http://git-scm.com
[Controle do código-fonte]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Instalando o Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Guia de introdução aos serviços móveis]: mobile-services-ios-get-started.md
[Trabalhar com scripts de servidor em Serviços Móveis]: mobile-services-how-to-use-server-scripts.md
[Portal clássico do Azure]: https://manage.windowsazure.com/
[Módulos]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid

<!---HONumber=AcomDC_1203_2015-->