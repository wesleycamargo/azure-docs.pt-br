<properties 
	pageTitle="Node.js implantado com Cloud9 - tutorial do Azure" 
	description="Saiba como usar o IDE do Cloud9 para desenvolver, compilar e implantar um aplicativo Node.js no Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# Implantando um aplicativo do Azure no Cloud9

Este tutorial descreve como usar o IDE do Cloud9 para desenvolver, compilar e
implantar um aplicativo Node.js no Azure.

Neste tutorial, você aprenderá a:

-   Criar um projeto IDE do Cloud9
-   Implantar o projeto no Azure
-   Atualizar uma implantação existente do Azure
-   Mover projetos entre as implantações de preparo e de produção

O [IDE do Cloud9][] fornece um ambiente de desenvolvimento entre plataformas, baseado em navegador
. Um dos recursos que o Cloud9 oferece suporte para projetos do Node.js é
que você pode implantar diretamente ao Azure de dentro do IDE.
O Cloud9 também se integra com os serviços do repositório GitHub e BitBucket
, portanto, é fácil compartilhar seu projeto com outras pessoas.

Usando o Cloud9, você pode desenvolver e implantar um aplicativo no Azure
a partir de muitos navegadores modernos e sistemas operacionais, sem a necessidade de
instalar SDKs ou ferramentas de desenvolvimento adicionais localmente. As etapas a seguir
são demonstradas usando o Google Chrome em um Mac.

## Inscrição

Para usar o Cloud9, primeiro você precisa visitar o respectivo site e [registrar-se para
obter uma assinatura do][IDE do Cloud9]. Você pode entrar com uma conta GitHub ou BitBucket existente
, ou criar uma conta do Cloud9. Uma oferta de assinatura gratuita
disponível, bem como uma oferta paga que
fornece mais recursos. Para obter mais informações, consulte [Cloud9 IDE][].

## Criar um projeto Node.js

1.  Entre no Cloud9, clique no símbolo **+** ao lado de **Meus projetos**,
    e, em seguida, selecione **Criar um novo projeto**.

	![create new Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_create_project.png)

2.  Na caixa de diálogo **Criar um novo projeto**, digite um nome de projeto
    acesso e o tipo de projeto. Clique em **Criar** para criar o projeto.

	![create new project dialog Cloud9](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_project.png)

	> [AZURE.NOTE] Algumas opções exigem um plano pago do Cloud9.
	   
	> [AZURE.NOTE] O nome do seu projeto do Cloud9 não é usado ao implantar no Azure.

3.  Depois que o projeto foi criado, clique em **Iniciar Edição**. Se esta for a primeira vez que você usou o IDE do Cloud9, você terá a opção de fazer um tour do serviço. Se desejar ignorar o tour e exibi-lo mais tarde, selecione **Apenas o editor**.

	![start editing the Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_startediting.png)

4.  Para criar um novo aplicativo de nó, selecione **Arquivo** e, em seguida, **Novo
    Arquivo**.

	![create new file in the Cloud9 project](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_filenew.png)

5.  Uma nova guia chamada **Untitled1** será exibida. Insira o
    código a seguir na guia **Untitled1** para criar o aplicativo do nó
    Web:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure\n');
        }).listen(port);
	
	> [AZURE.NOTE] O uso de process.env.PORT garante que o aplicativo selecione a porta correta quer ele seja executado no depurador do Cloud9 ou implantado no Azure.

6.  Para salvar o código, selecione **Arquivo** e, em seguida, **Salvar como**. Na caixa de diálogo
    **Salvar como**, digite **Server.js** como o nome do arquivo e, em seguida,
    clique em **Salvar**.


	> [AZURE.NOTE] Você pode observar um símbolo de aviso indicando que a variável req não é usada. Você pode ignorar esse aviso com segurança.

	![save the server.js file](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_saveas.png)

## Executar o aplicativo

> [AZURE.NOTE] Embora as etapas fornecidas nesta seção sejam suficientes para um aplicativo Hello World, para aplicativos que usam módulos externos você precisará selecionar uma versão específica do Node.js para o ambiente de depuração. Para fazer isso, selecione **Configurar...** no menu suspenso depuração e, em seguida, selecione a versão específica do Node.js. Por exemplo, você poderá receber erros de autenticação ao usar o módulo 'azure', se não tiver o Node.js 0.6.x selecionado.


1.  Clique em **Depurar** para executar o aplicativo no depurador do Cloud9.
	
	![run in the debugger](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug.png)

2.  Será exibida uma janela de saída. Clique na URL listada para
    acessar seu aplicativo por meio de uma janela do navegador.

	![output window](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_output.png)

	O aplicativo resultante será semelhante ao seguinte:

	![application running in browser](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug_browser.png)

3.  Para parar a depuração do aplicativo, clique em **parar**.

## Criar uma conta do Azure

Para implantar seu aplicativo no Azure, você precisa de uma conta. Se você
ainda não tiver uma conta do Azure, você pode se inscrever para uma avaliação gratuita
, seguindo estas etapas:

[AZURE.INCLUDE [create-azure-account](../includes/create-azure-account.md)]


## Criar uma implantação

1.  Para criar uma nova implantação, selecione **Implantar**e, em seguida, clique em **+** para criar um servidor de implantação.

    ![create a new deployment][create a new deployment]

2.  Na caixa de diálogo **Adicionar um destino de implantação**, digite um nome de implantação e, em seguida, selecione **Azure** na lista **Escolher tipo**. O nome da implantação que você especificar será usado para identificar a implantação no Cloud9. O nome corresponderá a um nome de implantação no Azure.

3.  Se esta for a primeira vez que você cria uma implantação do Cloud9 que usa o Azure, você deverá definir as configurações de publicação do Azure. Execute as seguintes etapas para baixar e instalar as configurações no Cloud9:

    1.  Clique em **Baixar as Configurações do Azure**.

        ![download publish settings](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_choosetypeandcert.png)

        Isso abrirá o Portal de Gerenciamento do Azure e solicitará que você baixe as configurações de publicação do Azure. Você deverá fazer logon em sua conta do Azure antes de começar.

    2.  Salve o arquivo de configurações de publicação no disco local.

    3.  Na caixa de diálogo **Adicionar um destino de implantação**, selecione **Escolher arquivo**,
        e, em seguida, selecione o arquivo baixado na etapa anterior.

    4.  Depois de selecionar o arquivo, clique em **Carregar**.

4.  Clique **+ Criar novo** para criar um novo serviço hospedado. Um  *hosted service* é o contêiner no qual seu aplicativo é hospedado quando é implantado no Azure. Para obter mais informações, consulte [Visão geral da criação de um serviço hospedado para o Azure][].

	![create a new deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png)

5.  O nome do novo serviço hospedado e as opções de configuração, como o número de instâncias, o sistema operacional host e o data center, serão solicitados. O nome da implantação especificado será usado como o nome do serviço hospedado no Azure. Esse nome deve ser exclusivo dentro do sistema Azure.
	
	![create a new hosted service](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_hosted_service_settings.png)

	> [AZURE.NOTE] Na caixa de diálogo **Adicionar um destino de implantação**, qualquer serviço hospedado do Azure existente será listado na seção **Escolher implantação existente**. A seleção de um serviço hospedado existente resultará na implantação desse projeto nesse serviço.

	> [AZURE.NOTE] Seleção de **Habilitar RDP** e o fornecimento de um nome do usuário e senha habilitará a área de trabalho remota para a sua implantação.


## Implantar no ambiente de produção do Azure

1.  Selecione a implantação que você criou nas etapas anteriores. Uma caixa de diálogo
    aparecerá, que fornece informações sobre essa implantação. bem como
    a URL de produção que será usada após a implantação do Microsoft
    Azure.

	![select a deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_select_deployment.png)

2.  Selecione **Implantar no Ambiente de Produção**.

3.  Clique em **Implantar** para iniciar a implantação.

4.  Se esta for a primeira vez que você implanta esse projeto no Azure, você receberá um erro de **'Nenhum web.config localizado'**. Selecione **Sim** para criar o arquivo. Isso adicionará um arquivo 'Web.cloud.config' ao seu projeto.
	
	![no web.config file found message](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_no_web_config.png)

5.  Se esta for a primeira vez que você implanta esse projeto no Azure, você receberá um erro de **'Nenhum arquivo  'csdef' presente'**. Selecione **Sim** para criar o arquivo .csdef. Isso adicionará um arquivo 'ServiceDefinition.csdef' ao seu projeto.    ServiceDefinition.csdef é um arquivo específico ao Azure que é necessário para publicar seu aplicativo. Para obter mais informações, consulte [Visão geral da criação de um serviço hospedado para o Azure][].

6.  Será solicitado que você selecione o tamanho de instância para esse aplicativo. Selecione **Pequeno** e, em seguida, clique em **Criar**. Para obter mais detalhes sobre os tamanhos de VM do Azure, consulte [Como configurar tamanhos de máquina virtual][].

	![specify csdef file values](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createcsdef.png)

7.  A entrada da implantação exibirá o status do processo de implantação. Após a conclusão, a implantação será exibida como **Ativa**.

	![deployment status](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deployment_status.png)

	> [AZURE.NOTE] Os projetos implantados por meio do IDE do Cloud9 recebem um GUID como o nome da implantação no Azure.

8.  A caixa de diálogo de implantação inclui um link para a URL de produção. Quando a implantação for concluída, clique na URL para navegar até o aplicativo em execução no Azure.

	![Azure production URL link](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_url.png)

## Atualizar o aplicativo

Ao fazer alterações em seu aplicativo, você pode usar o Cloud9 para implantar o aplicativo atualizado no mesmo serviço hospedado do Azure.

1.  No arquivo server.js, atualize seu código para que "hello azure v2" seja impresso na tela. Você pode substituir o código existente pelo seguinte código atualizado:

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure v2\n');
        }).listen(port);

2.  Para salvar o código, selecione **Arquivo** e, em seguida, **Salvar**.

## Implantar a atualização no ambiente de preparo do Azure

1.  Selecione **Implantar no Preparo**.

2.  Clique em **Implantar** para iniciar a implantação.

	Cada serviço hospedado do Azure oferece suporte a dois ambientes, preparo e produção. O ambiente de preparo é exatamente igual ao ambiente de produção, exceto que você só poderá acessar o aplicativo preparado com uma URL ofuscada, baseada no GUID, que é gerada pelo Azure. Você pode usar o ambiente de preparo para testar o aplicativo e, depois de verificar as alterações, você pode mover a versão de preparo para a produção executando uma troca de VIP (IP virtual), conforme descrito mais adiante neste tutorial.

3.  Quando seu aplicativo for implantado no preparo, a URL de preparo com base no guid será exibida na saída do Console, conforme mostrado na captura de tela a seguir. Clique na URL para abrir o aplicativo preparado em um navegador.

	![console output showing staging URL](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_staging_console_output.png)

## Mover a atualização para a produção usando permuta de VIP

Quando um serviço é implantado em ambientes de produção ou preparo
, um endereço IP virtual (VIP) é atribuído ao serviço
neste ambiente. Quando você deseja mover um serviço a partir do ambiente intermediário
para o ambiente de produção, você pode fazer isso sem
reimplantar, fazendo uma troca de VIP, que troca as implantações de preparo e produção
. Uma troca de VIP coloca seu aplicativo preparado em
produção sem tempo de inatividade no ambiente de produção. Para obter mais
detalhes, consulte [Visão geral do gerenciamento de implantações no Azure.][]

1.  Na caixa de diálogo Implantar, clique no link **Abrir portal** para abrir o
    Portal de Gerenciamento do Azure.

	![Link from deploy dialog to Azure Management Portal][Link from deploy dialog to Azure Management Portal]

2.  Entre no portal com suas credenciais.

3.  À esquerda da página da Web, selecione **Serviços hospedados, contas de armazenamento
    e CDN**e, em seguida, clique em **Serviços hospedados**.

	![Azure Management Portal][Azure Management Portal]

	O painel de resultados mostra o serviço hospedado com o nome especificado no Cloud9 e duas implantações, uma com o    valor de **Ambiente** **preparo**, a segunda **produção**.

4.  Para executar a Permuta de VIP, selecione o serviço hospedado e, em seguida, clique em **Alternar VIP** na faixa de opções.

	![VIP SWAP](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_vipswap.png)

5.  Clique em **OK** na caixa de diálogo Alternar VIPs que é exibida.

6.  Navegue até o aplicativo de produção. Você verá que a versão do aplicativo implantado anteriormente no preparo agora está em produção.

	![Production application running on Azure](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_on_azure.png)

## Usando a área de trabalho remota

Se tiver habilitado o RDP e especificado um nome do usuário e uma senha ao criar sua implantação, você poderá usar a Área de Trabalho Remota para conectar-se ao seu Serviço Hospedado selecionando uma instância específica e, em seguida, selecionando Conectar na
faixa de opções.

![Connect to an instance](./media/cloud-services-nodejs-develop-deploy-cloud9/connect.png)

Quando você clicar em Conectar, será solicitado que você abra ou baixe um arquivo .RDP. Esse arquivo contém as informações necessárias para conectar-se à sua sessão de área de trabalho remota. A execução desse arquivo em um sistema Windows irá solicitar o nome do usuário e a senha que você digitou ao criar sua implantação e, em seguida, conectará você à área de trabalho da instância selecionada.
instância de função.

> [AZURE.NOTE] O arquivo .RDP para conexão à instância hospedada de seu aplicativo só funcionará com o aplicativo de Área de Trabalho Remota no
Windows.

## Parar e excluir o aplicativo

O Azure cobra instâncias de função por hora de servidor consumido, e o tempo do servidor é consumido enquanto seu aplicativo está implantado, mesmo que as instâncias não estejam sendo executadas e estejam em estado de paradas. Além disso,
o tempo do servidor é consumido por implantações de produção e de preparo.

O Cloud9 se concentra em fornecer um IDE e não fornece um método direto de interrupção ou de exclusão de um aplicativo após ele ter sido implantado no Azure. Para excluir um aplicativo hospedado no Azure, execute as seguintes etapas:

1.  Na caixa de diálogo Implantar, clique no link **Abrir portal** para abrir o Portal de Gerenciamento do Azure.

	![Link from deploy dialog to Azure Management Portal](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png)

2.  Entre no portal com suas credenciais.

3.  No lado esquerdo da página da web, selecione **Serviços Hospedados, Contas de Armazenamento e CDN** e, em seguida, clique em **Serviços Hospedados**.

4.  Selecione a implantação de preparo (indicada pelo valor de **Ambiente**). Clique em **Excluir** na faixa de opções para excluir o aplicativo.

	![delete the deployment](./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deletedeployment.png)

5.  Selecione a implantação de produção e, em seguida, clique em **Excluir** para excluir esse aplicativo também.

## Recursos adicionais

-   [Documentação do Cloud9][]


  [Cloud9 IDE]: http://cloud9ide.com/ 
  [Visão geral da criação de um serviço hospedado para o Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
  [Como configurar tamanhos de máquina virtual]: http://msdn.microsoft.com/library/windowsazure/ee814754.aspx
  [Visão geral do gerenciamento de implantações no Azure.]: http://msdn.microsoft.com/library/windowsazure/gg433027.aspx
  [Documentação do Cloud9]: http://go.microsoft.com/fwlink/?LinkId=241421&clcid=0x409

<!--HONumber=45--> 
