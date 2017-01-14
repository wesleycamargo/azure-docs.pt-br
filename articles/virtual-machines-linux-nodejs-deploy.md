---
title: "Implantar um aplicativo Node.js para máquinas virtuais Linux no Azure"
description: "Saiba como implantar um aplicativo Node.js em máquinas virtuais Linux no Azure."
services: 
documentationcenter: nodejs
author: stepro
manager: dmitryr
editor: 
ms.assetid: 857a812d-c73e-4af7-a985-2d0baf8b6f71
ms.service: multiple
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2016
ms.author: stephpr
translationtype: Human Translation
ms.sourcegitcommit: 4fc33ba185122496661f7bc49d14f7522d6ee522
ms.openlocfilehash: 0de0314902805a2bdb37ce3c6f79ec221f3aed31


---
# <a name="deploy-a-nodejs-application-to-linux-virtual-machines-in-azure"></a>Implantar um aplicativo Node.js para máquinas virtuais Linux no Azure
Este tutorial mostra como usar um aplicativo Node.js e como implantá-lo em máquinas virtuais Linux em execução no Azure. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional que seja capaz de executar o Node.js.

Você aprenderá a:

* Bifurcar e clonar um repositório GitHub contendo um aplicativo TODO simples;
* Criar e configurar duas máquinas virtuais Linux no Azure para executar o aplicativo;
* Iterar no aplicativo pelo envio de atualizações por push para a máquina virtual de front-end da Web.

> [!NOTE]
> Para concluir este tutorial, você precisará de uma conta do GitHub e de uma conta do Microsoft Azure, além da capacidade de usar o Git em um computador de desenvolvimento.
> 
> Se você não tiver uma conta do GitHub, poderá se inscrever [aqui](https://github.com/join).
> 
> Se você não tiver uma conta do [Microsoft Azure](https://azure.microsoft.com/), poderá se inscrever para uma avaliação GRATUITA [aqui](https://azure.microsoft.com/pricing/free-trial/). Isso também o guiará pelo processo de inscrição em uma [Conta da Microsoft](http://account.microsoft.com) se você já não tiver uma. Como alternativa, se você for um assinante do Visual Studio, poderá [ativar os benefícios do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
> 
> Se você não tiver o git na máquina de desenvolvimento e estiver usando um computador Windows ou Macintosh, instale o git [aqui](http://www.git-scm.com). Se você estiver usando o Linux, instale o git usando o mecanismo mais apropriado, como `sudo apt-get install git`.
> 
> 

## <a name="forking-and-cloning-the-todo-application"></a>Bifurcação e clonagem de aplicativo TODO
O aplicativo TODO usado por este tutorial implementa um front-end da Web simples em uma instância do MongoDB que rastreia uma lista de tarefas pendentes. Depois de entrar no GitHub, acesse [aqui](https://github.com/stepro/node-todo) para localizar o aplicativo e bifurcá-lo usando o link no canto superior direito. Isso deve criar um repositório em sua conta denominado *nomedaconta*/node-todo.

Agora, em sua máquina de desenvolvimento, clone este repositório:

    git clone https://github.com/accountname/node-todo.git

Usaremos esse clone local do repositório mais tarde ao fazer alterações no código-fonte.

## <a name="creating-and-configuring-the-linux-virtual-machines"></a>Criando e configurando máquinas virtuais Linux
O Azure tem excelente suporte para computação bruta usando máquinas virtuais Linux. Esta parte do tutorial mostra como é fácil criar duas máquinas virtuais Linux e implantar o aplicativo TODO, executando o front-end da Web em uma e a instância do MongoDB em outra.

### <a name="creating-virtual-machines"></a>Criando máquinas virtuais
A maneira mais fácil de criar uma nova máquina virtual no Azure é usando o Portal do Azure. Clique [aqui](https://portal.azure.com) para entrar e iniciar o Portal do Azure no navegador da Web. Depois que o Portal do Azure tiver sido carregado, conclua as seguintes etapas:

* Clique no link "+ Novo";
* Selecione a categoria "Computação" e escolha "Ubuntu Server 14.04 LTS";
* Selecione o modelo de implantação do "Gerenciador de Recursos" e clique em "Criar";
* Preencha o básico de acordo com estas diretrizes:
  * Especifique um nome que você pode identificar facilmente mais tarde;
  * Neste tutorial, escolha Autenticação de senha;
  * Crie um novo grupo de recursos com um nome identificável.
* Para o tamanho da máquina Virtual, o "Padrão A1"é uma opção razoável para este tutorial.
* Para configurações adicionais, verifique se o tipo de disco é "Padrão" e aceite os padrões restantes.
* Inicie a criação na página Resumo.

Execute o processo acima duas vezes para criar duas máquinas virtuais Linux, uma para o front-end da Web e outra para a instância do MongoDB. A criação das máquinas virtuais levará cerca de 5 a 10 minutos.

### <a name="assigning-a-dns-entry-for-virtual-machines"></a>Atribuindo uma entrada DNS para máquinas virtuais
As máquinas virtuais criadas no Azure, por padrão, somente podem ser acessadas com endereço IP público, como 1.2.3.4. Vamos facilitar a identificação das máquinas atribuindo-lhes entradas DNS.

Depois que o portal indicar a criação das máquinas virtuais, clique no link "Máquinas virtuais" na barra de navegação à esquerda e localize suas máquinas. Para cada máquina:

* Localize a guia Essentials e clique no endereço IP público;
* Na configuração de endereço IP público, atribua um rótulo de nome de DNS e salve-o.

O portal fará com que o nome especificado esteja disponível. Depois de salvar a configuração, as máquinas virtuais terão nomes de host semelhantes a `machinename.region.cloudapp.azure.com`.

### <a name="connecting-to-the-virtual-machines"></a>Conectando-se às máquinas virtuais
Quando as máquinas virtuais foram provisionadas, eles foram pré-configuradas para permitir conexões remotas via SSH. Esse é o mecanismo que usaremos para configurar as máquinas virtuais. Se você estiver usando o Windows para desenvolvimento e não tiver um cliente SSH, obtenha um. Uma opção comum é o PuTTY, que pode ser baixado [aqui](http://www.chiark.greenend.org.uk/~sgtatham/putty/). Os sistemas operacionais Macintosh e Linux vêm com uma versão do SSH pré-instalada.

### <a name="configuring-the-web-frontend-virtual-machine"></a>Configurando a máquina virtual front-end da Web
SSH para a máquina de front-end da Web criada usando PuTTY, linha de comando SSH ou outra ferramenta de SSH favorita. Você deve ver uma mensagem de boas-vindas seguida de um prompt de comando.

Primeiro, vamos garantir que tanto o Git quanto o Node estão instalados:

    sudo apt-get install -y git
    curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
    sudo apt-get install -y nodejs

Como o front-end do aplicativo Web depende de alguns módulos nativos do Node.js, também precisamos instalar o conjunto essencial de ferramentas de compilação:

    sudo apt-get install -y build-essential

Por fim, vamos instalar um aplicativo Node.js chamado *forever*, que ajuda a executar aplicativos de servidor do Node.js:

    sudo npm install -g forever

Essas são todas as dependências necessárias nesta máquina virtual para poder executar o front-end do aplicativo Web; portanto, vamos executá-las. Para isso, primeiro vamos criar um clone básico do repositório GitHub que você bifurcou anteriormente para poder publicar facilmente atualizações na máquina virtual (abordaremos esse cenário de atualização mais adiante) e depois clonar o clone básico para fornecer uma versão do repositório que pode realmente ser executada.

No diretório home (~), execute os comandos abaixo (substituindo *nomedaconta* pelo nome da conta de usuário do GitHub):

    git clone --bare https://github.com/accountname/node-todo.git
    git clone node-todo.git

Agora, entre no diretório node-todo e execute estes comandos:

    npm install
    forever start server.js

O front-end do aplicativo Web está em execução; no entanto, há mais uma etapa antes de se poder acessar o aplicativo em um navegador da Web. A máquina virtual que você criou é protegida por um recurso do Azure chamado *grupo de segurança de rede*, que foi criado quando você provisionou a máquina virtual. Atualmente, esse recurso permite que somente as solicitações externas para a porta 22 sejam roteadas para a máquina virtual, o que permite a comunicação SSH com a máquina, mas nada além disso. Então, para exibir o aplicativo TODO, que é configurado para ser executado na porta 8080, essa porta também precisa ser aberta.

Volte para o portal do Azure e execute as seguintes etapas:

* Clique em "Grupos de recursos" na barra de navegação à esquerda;
* Selecione o grupo de recursos que contém sua máquina virtual;
* Na lista de recursos resultante, selecione o grupo de segurança de rede (aquele com um ícone de escudo);
* Nas propriedades, selecione "Regras de segurança de entrada";
* Na barra de ferramentas, clique em "Adicionar";
* Forneça um nome como "default-allow-todo";
* Defina o protocolo como "TCP";
* Defina o intervalo de porta de destino como "8080";
* Clique em OK e aguarde a criação da regra de segurança.

Depois de criar essa regra de segurança, o aplicativo TODO fica visível publicamente na Internet e você pode navegar até ele usando uma URL como:

    http://machinename.region.cloudapp.azure.com:8080

Você observará que, embora ainda não tenhamos configurado a máquina virtual de MongoDB, o aplicativo TODO parece estar funcionando bem. Isso ocorre porque o repositório de origem é codificado para usar uma instância do MongoDB pré-instalada. Depois de configurarmos a máquina virtual de MongoDB, vamos voltar e alterar o código-fonte para utilizar nossa instância privada do MongoDB.

### <a name="configuring-the-mongodb-virtual-machine"></a>Configurando a máquina virtual MongoDB
SSH para o segundo computador que você criou usando PuTTY, linha de comando SSH ou outra ferramenta SSH favorita. Após ver a mensagem de boas-vindas e o prompt de comando, instale o MongoDB (estas instruções foram tiradas [daqui](https://docs.mongodb.org/master/tutorial/install-mongodb-on-ubuntu/)):

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
    echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

Por padrão, o MongoDB é configurado para só poder ser acessado localmente. Neste tutorial, configuraremos MongoDB para que ele possa ser acessado da máquina de virtual do aplicativo. Em um contexto sudo, abra o arquivo /etc/mongod.conf e localize a seção `# network interfaces` . Altere o valor de configuração `net.bindIp` para `0.0.0.0`.

> [!NOTE]
> Essa configuração serve apenas para este tutorial. Ela **NÃO** é uma prática de segurança recomendada e não deve ser usada em ambientes de produção.
> 
> 

Agora, verifique se o serviço MongoDB foi iniciado:

    sudo service mongod restart

O MongoDB opera na porta 27017 por padrão. Assim, da mesma forma que precisávamos abrir a porta 8080 na máquina virtual front-end da Web, precisamos abrir a porta 27017 na máquina virtual de MongoDB.

Volte para o portal do Azure e execute as seguintes etapas:

* Clique em "Grupos de recursos" na barra de navegação à esquerda;
* Selecione o grupo de recursos que contém a máquina virtual de MongoDB;
* Na lista de recursos resultante, selecione o grupo de segurança de rede (aquele com um ícone de escudo) com o mesmo nome que você atribuiu à máquina virtual de MongoDB;
* Nas propriedades, selecione "Regras de segurança de entrada";
* Na barra de ferramentas, clique em "Adicionar";
* Forneça um nome como "default-allow-mongo";
* Defina o protocolo como "TCP";
* Defina o intervalo de porta de destino como "27017";
* Clique em OK e aguarde a criação da regra de segurança.

## <a name="iterating-on-the-todo-application"></a>Iterando o aplicativo TODO
Até agora, provisionamos duas máquinas virtuais Linux: aquela que está executando o aplicativo front-end da Web e outra que está executando uma instância do MongoDB. Mas há um problema: o front-end da Web, na verdade, ainda não está usando a instância do MongoDB provisionada. Vamos corrigir isso atualizando o código de front-end da Web para usar uma variável de ambiente em vez de uma instância embutida em código.

### <a name="changing-the-todo-application"></a>Alterar o aplicativo TODO
Na máquina de desenvolvimento onde você clonou o repositório node-todo pela primeira vez, abra o `node-todo/config/database.js` no seu editor favorito e altere o valor de URL do valor embutido em código, como `mongodb://...`, para `process.env.MONGODB`.

Confirme as alterações e envie por push ao mestre do GitHub:

    git commit -am "Get MongoDB instance from env"
    git push origin master

Infelizmente, isso não publica a alteração para a máquina virtual de front-end da Web. Vamos fazer mais algumas alterações na máquina virtual para criar um mecanismo simples, mas eficaz, de publicação de atualizações, para que você possa ver rapidamente os efeitos das alterações no ambiente em funcionamento.

### <a name="configuring-the-web-frontend-virtual-machine"></a>Configurando a máquina virtual front-end da Web
Lembre-se de que criamos anteriormente um clone básico do repositório node-todo na máquina virtual de front-end da Web. Acontece que essa ação criou um novo Git remoto para onde as alterações podem ser enviadas por push. No entanto, o simples envio por push para esse Git remoto não fornece o modelo de iteração rápida que os desenvolvedores buscam ao trabalhar em seus códigos.

O que queremos fazer é garantir que quando ocorrer um envio por push para o repositório remoto na máquina virtual, o aplicativo TODO em execução será atualizado automaticamente. Felizmente, isso é fácil de conseguir com o git.

O Git expõe um número de ganchos que são chamados em momentos específicos para reagir às ações realizadas no repositório. Eles são especificados usando scripts do shell na pasta `hooks` do repositório. O gancho que mais se aplica ao cenário de atualização automática é o evento `post-update` .

Em uma sessão SSH para a máquina virtual de front-end da Web, altere para o `~/node-todo.git/hooks` diretório e adicione o seguinte conteúdo em um arquivo chamado `post-update` (substituindo `machinename` e `region` com as informações de máquina virtual do MongoDB):

    #!/bin/bash

    forever stopall
    unset 'GIT_DIR'
    export MONGODB="mongodb://machinename.region.cloudapp.azure.com:27017/tododb"
    cd ~/node-todo && git fetch origin && git pull origin master && npm install && forever start ~/node-todo/server.js
    exec git update-server-info

Verifique se o arquivo é executável executando o seguinte comando:

    chmod 755 post-update

Esse script garante que se o aplicativo de servidor atual for interrompido, o código no repositório clonado será atualizado com o mais recente, as dependências atualizadas serão atendidas e o servidor será reiniciado. Ela também garante que o ambiente foi configurado em preparação para o recebimento de nossa primeira atualização de aplicativo para obter a instância do MongoDB de uma variável de ambiente.

### <a name="configuring-your-development-machine"></a>Configurando seu computador de desenvolvimento
Agora, vejamos sua máquina de desenvolvimento conectada à máquina virtual de front-end da Web. Isso é tão simples quanto adicionar o repositório vazio na máquina virtual como um repositório remoto. Execute o seguinte comando para fazer isso (substituindo *usuário* pelo nome de logon de máquina virtual de front-end da Web, e *nomedamáquina* e *região* conforme apropriado):

    git remote add azure user@machinename.region.cloudapp.azure.com:node-todo.git

Isso é tudo o que é necessário para habilitar o envio por push, ou a devida publicação, das alterações para a máquina virtual de front-end da Web.

### <a name="publishing-updates"></a>Publicação de atualizações
Vamos publicar uma alteração feita até o momento para que o aplicativo use nossa própria instância do MongoDB:

    git push azure master

Você deve ver saídas semelhantes às seguintes:

    Counting objects: 4, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (4/4), 406 bytes | 0 bytes/s, done.
    Total 4 (delta 1), reused 0 (delta 0)
    remote: info:    Forever stopped processes:
    remote: data:        uid  command         script    forever pid  id logfile                         uptime
    remote: data:    [0] 0Lyh /usr/bin/nodejs server.js 9064    9301    /home/username/.forever/0Lyh.log 0:0:3:17.487
    remote: From /home/username/node-todo
    remote:    5f31fd7..5bc7be5  master     -> origin/master
    remote: From /home/username/node-todo
    remote:  * branch            master     -> FETCH_HEAD
    remote: Updating 5f31fd7..5bc7be5
    remote: Fast-forward
    remote:  config/database.js | 2 +-
    remote:  1 file changed, 1 insertion(+), 1 deletion(-)
    remote: npm WARN package.json node-todo@0.0.0 No repository field.
    remote: npm WARN package.json node-todo@0.0.0 No license field.
    remote: warn:    --minUptime not set. Defaulting to: 1000ms
    remote: warn:    --spinSleepTime not set. Your script will exit if it does not stay up for at least 1000ms
    remote: info:    Forever processing file: /home/username/node-todo/server.js
    To username@machinename.region.cloudapp.azure.com:node-todo.git
    5f31fd7..5bc7be5  master -> master

Após a conclusão do comando, tente atualizar o aplicativo em um navegador da Web. Você deve ser capaz de ver que a lista de tarefas pendentes apresentada aqui está vazia e não mais associada à instância do MongoDB implantada e compartilhada.

Para concluir o tutorial, vamos fazer outra alteração mais visível. Na máquina de desenvolvimento, abra o arquivo node-todo/public/index.html usando seu editor favorito. Localize o cabeçalho jumbotron e altere o título de "I’m a Todo-aholilc" para "I’m a Todo-aholic on Azure!".

Agora, vamos confirmar:

    git commit -am "Azurify the title"

Dessa vez, vamos publicar a alteração no Azure antes de enviá-la de volta por push para o repositório GitHub:

    git push azure master

Quando esse comando for concluído, atualize a página da Web para ver as alterações. Se estiverem boas, envie a alteração por push de volta à origem remota: 

    git push origin master

## <a name="next-steps"></a>Próximas etapas
Este artigo mostrou como utilizar um aplicativo Node.js e implantá-lo em máquinas virtuais Linux em execução no Azure. Para saber mais sobre máquinas virtuais Linux no Azure, confira [Introdução ao Linux no Azure](/documentation/articles/virtual-machines-linux-introduction/).

Para obter mais informações sobre como desenvolver aplicativos do Node.js no Azure, consulte o [Centro de desenvolvedores do Node.js](/develop/nodejs/).




<!--HONumber=Dec16_HO1-->


