<properties linkid="develop-mobile-tutorials-store-scripts-in-source-control" urlDisplayName="Store server scripts in source control" pageTitle="Store server scripts in source control - Azure Mobile Services" metaKeywords="" description="Learn how to store your server script files and modules in a local Git repo on your computer." metaCanonical="" services="" documentationCenter="Mobile" title="Store server scripts in source control" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Scripts de servidor de armazenamento no controle do código-fonte

Este tópico mostra como configurar o controle do código-fonte pela primeira vez nos Serviços Móveis do Azure para armazenar seus scripts de servidor em um repositório Git. Os scripts e outros arquivos de código JavaScript podem ser promovidos do seu repositório local para o seu serviço móvel de produção. Ele também mostra como definir o código compartilhado que pode ser exigido por vários scripts e como carregar módulos do Node.js.

O tutorial o orientará por estas etapas:

1.  [Habilitar o controle do código-fonte no seu serviço móvel][Habilitar o controle do código-fonte no seu serviço móvel].
2.  [Instalar o Git e criar o repositório local][Instalar o Git e criar o repositório local].
3.  [Implantar arquivos de script atualizados no seu serviço móvel][Implantar arquivos de script atualizados no seu serviço móvel].
4.  [Aproveitar o código compartilhado e os módulos do Node.js nos seus scripts de servidor][Aproveitar o código compartilhado e os módulos do Node.js nos seus scripts de servidor].

Para concluir este tutorial, você já deverá ter criado um serviço móvel, concluindo o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou [Introdução aos dados][Introdução aos dados].

## <a name="enable-source-control"></a><span class="short-header">Habilitar o controle do código-fonte</span>Habilitar o controle do código-fonte no seu serviço móvel

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e, em seguida, clique em seu serviço móvel.

    ![][]

2.  Clique na guia **Painel** e, em seguida, em **Visão rápida**, clique em **Configurar controle de origem** e em **Sim** para confirmar.

    ![][1]

    > [WACOM.NOTE]
    > O controle do código-fonte é um recurso de visualização. É recomendável que você faça backup dos seus arquivos de script regularmente, mesmo que eles estejam armazenados nos Serviços Móveis.

3.  Forneça um **Nome do usuário**, uma **Nova senha**, confirme a senha e clique no botão de seleção.

    ![][2]

    O repositório Git é criado no seu serviço móvel. Anote as credenciais que acabou de fornecer, pois você as utilizará para acessar esse repositório.

4.  Clique na guia Configurar e observe o novo campo **Controle do código-fonte**.

    ![][3]

    A URL do repositório Git é exibida. Você usará essa URL para clonar o repositório para o computador local.

Agora que você habilitou o controle do código-fonte no seu serviço móvel, é hora de usar Git para clonar o repositório para o seu computador local.

## <a name="clone-repo"></a><span class="short-header">Clonar o repositório</span>Instalar o Git e criar o repositório local

1.  Instale o Git no seu computador local.

    As etapas necessárias para instalar o Git variam de acordo com o sistema operacional. Consulte [Instalando o Git][Instalando o Git] para distribuições específicas de sistemas operacionais e orientações de instalação.

    > [WACOM.NOTE]
    > Em alguns sistemas operacionais, uma versão de linha de comando e uma versão da GUI do Git estão disponíveis. As instruções fornecidas neste artigo usam a versão de linha de comando.

2.  Abra uma linha de comando, como **GitBash** (Windows) ou **Bash** (shell Unix). Nos sistemas OS X, você pode acessar a linha de comando por meio do aplicativo **Terminal**.

3.  Na linha de comando, altere para o diretório no qual você armazenará seus scripts. Por exemplo, `cd SourceControl`.

4.  Use o comando a seguir para criar uma cópia local do seu novo repositório Git, substituindo `<your_git_URL>` pela URL do repositório Git para seu serviço móvel:

        git clone <your_git_URL>

5.  Quando solicitado, digite o nome de usuário e a senha definidos no momento em que habilitou o controle do código-fonte no seu serviço móvel. Após a autenticação bem-sucedida, você verá uma série de respostas, como esta:

        remote: Counting objects: 8, done.
        remote: Compressing objects: 100% (4/4), done.
        remote: Total 8 (delta 1), reused 0 (delta 0)
        Unpacking objects: 100% (8/8), done.

6.  Navegue até o diretório do qual executou o comando `git clone` e observe a seguinte estrutura de diretórios:

    ![4][4]

    Nesse caso, um novo diretório é criado com o nome do serviço móvel, que é o repositório local para o serviço de dados.

7.  Abra a subpasta .\\service\\table e observe se ela contém um arquivo TodoItem.json, que é uma representação JSON das permissões de operação na tabela TodoItem.

    Quando os scripts de servidor foram definidos nessa tabela, você também terá um ou mais arquivos denominados `TodoItem.<operation>.js` que contêm os scripts para a operação de tabela específica. O Agendador e os scripts de APIs personalizadas serão mantidos em pastas separadas com esses respectivos nomes. Para obter mais informações, consulte [Controle do código-fonte][Controle do código-fonte].

Agora que criou seu repositório local, você pode fazer alterações nos scripts de servidor e enviar por push as alterações de volta para o serviço móvel.

## <a name="deploy-scripts"></a><span class="short-header">Implantar scripts</span>Implantar arquivos de script atualizados no seu serviço móvel

1.  Navegue até a subpasta .\\service\\table e, se um arquivo de todoitem.insert.js ainda não existir, crie-o agora.

2.  Abra o novo arquivo todoitem.insert.js em um editor de texto, cole o código a seguir e salve suas alterações:

        function insert(item, user, request) {
            request.execute();
            console.log(JSON.stringify(item, null, 4));
        }

    Esse código simplesmente grava o item inserido no log. Se esse arquivo já contiver código, basta adicionar um código JavaScript válido a ele, por exemplo, uma chamada para `console.log()` e salvar as alterações.

3.  No prompt de comando do Git, digite o comando a seguir para iniciar o acompanhamento do novo arquivo de script:

        $ git add .

4.  Digite o comando a seguir para confirmar as alterações:

        $ git commit -m "updated the insert script"

5.  Digite o comando a seguir para carregar as alterações para o repositório remoto:

        $ git push origin master

    Uma série de comandos deverão ser exibidos indicando que a confirmação foi implantada no serviço móvel.

6.  De volta ao Portal de Gerenciamento, clique na guia **Dados** e, em seguida, clique na tabela **TodoItem**.

    ![][5]

7.  Clique em **Script** e selecione a operação **Inserir**.

    ![][6]

    Observe que o script da operação de inserção exibido é o mesmo do código JavaScript que você acabou de carregar para o repositório.

## <a name="use-npm"></a><span class="short-header">Código compartilhado e módulos</span>Aproveitar o código compartilhado e os módulos do Node.js nos seus scripts de servidor

Os Serviços Móveis fornecem acesso ao conjunto completo de módulos do Node.js do núcleo, que você pode usar no seu código por meio da função **require**. O serviço móvel também pode usar módulos do Node.js que não fazem parte do pacote do Node.js do núcleo, e você pode até mesmo definir o seu próprio código compartilhado como módulos do Node.js. Para obter mais informações sobre como criar módulos, consulte [Módulos][Módulos] na documentação de referência da API do Node.js.

Em seguida, você adicionará o módulo [node-uuid][node-uuid] do Node.js ao seu serviço móvel usando o controle do código-fonte e o gerenciador de pacotes do Node.js (NPM). Esse módulo será usado, então, para gerar um novo valor de GUID para a propriedade **uuid** nos itens inseridos.

1.  Se ainda não tiver instalado o Node.js no seu computador local, faça isso seguindo as etapas do [site do Node.js][site do Node.js].

2.  Navegue até a pasta`.\service` do seu repositório Git local e, em seguida, no prompt de comando, execute o comando a seguir:

        npm install node-uuid

    NPM cria o diretório `node_modules` no local atual e instala o módulo [node-uuid][node-uuid] no subdiretório `\node-uuid`.

    <div class="dev-callout">
<strong>Observa&ccedil;&atilde;o</strong>
<p>Quando <code data-inline="1">node_modules</code> j&aacute; existir na hierarquia de diret&oacute;rio, NPM criar&aacute; o subdiret&oacute;rio <code data-inline="1">\node-uuid</code> nela em vez de criar um novo <code data-inline="1">node_modules</code> no reposit&oacute;rio. Nesse caso, simplesmente exclua o diret&oacute;rio <code data-inline="1">node_modules</code> existente.</p>
</div>

3.  Agora, navegue até a subpasta .\\service\\table, abra o arquivo todoitem.insert.js e modifique-o da seguinte maneira:

        function insert(item, user, request) {
            var uuid = require('node-uuid');
            item.uuid = uuid.v1();
            request.execute();
            console.log(JSON.stringify(item, null, 4));
        }

    Esse código adiciona uma coluna uuid à tabela, preenchendo-a com identificadores GUID exclusivos.

4.  Assim como na seção anterior, digite o comando a seguir no prompt de comando do Git:

        $ git add .
        $ git commit -m "added node-uuid module"
        $ git push origin master

    Isso adicionará o novo arquivo, confirmará suas alterações e enviará o novo módulo node-uuid e as alterações por push ao script todoitem.insert.js para o seu serviço móvel.

## <a name="next-steps"> </a>Próximas etapas

Agora que concluiu este tutorial, você sabe como armazenar seus scripts no controle do código-fonte. Para saber mais sobre como trabalhar com scripts de servidor e APIs personalizadas, consulte:

-   [Trabalhar com scripts de servidor em Serviços Móveis][Trabalhar com scripts de servidor em Serviços Móveis]
    
	Mostra como trabalhar com scripts de servidor, o agendador de trabalhos e APIs personalizadas.

-   [Definir uma API personalizada que ofereça suporte a notificações pull][Definir uma API personalizada que ofereça suporte a notificações pull]
     
	Mostra como usar APIs personalizadas para oferecer suporte a notificações periódicas que atualizam blocos ao vivo em um aplicativo da Windows Store.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Habilitar o controle do código-fonte no seu serviço móvel]: #enable-source-control
  [Instalar o Git e criar o repositório local]: #clone-repo
  [Implantar arquivos de script atualizados no seu serviço móvel]: #deploy-scripts
  [Aproveitar o código compartilhado e os módulos do Node.js nos seus scripts de servidor]: #use-npm
  [Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started
  [Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-store-scripts-source-control/mobile-services-selection.png
  [1]: ./media/mobile-services-store-scripts-source-control/mobile-setup-source-control.png
  [2]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-credentials.png
  [3]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-configure.png
  [Instalando o Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
  [Controle do código-fonte]: http://msdn.microsoft.com/pt-br/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
  [5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
  [6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png
  [Módulos]: http://nodejs.org/api/modules.html
  [node-uuid]: https://npmjs.org/package/node-uuid
  [site do Node.js]: http://nodejs.org/
  [Trabalhar com scripts de servidor em Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-server-scripts
  [Definir uma API personalizada que ofereça suporte a notificações pull]: /pt-br/develop/mobile/tutorials/create-pull-notifications-dotnet
