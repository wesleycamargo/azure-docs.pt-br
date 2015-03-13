<properties 
	pageTitle="Introdução aos serviços de nuvem do Azure e ao ASP.NET" 
	description="Saiba como criar um aplicativo de múltiplas camadas usando ASP.NET MVC e o Azure. O aplicativo é executado em um serviço de nuvem, com uma função Web e de trabalho. Ele utiliza Entity Framework, o Banco de Dados SQL e filas e blobs de armazenamento do Azure." 
	services="cloud-services, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="2/19/2015" 
	ms.author="tdykstra"/>

# Introdução aos serviços de nuvem do Azure e ao ASP.NET

## Visão geral

Este tutorial mostra como criar um aplicativo de várias camadas .NET com front-end ASP.NET MVC e implantá-lo no [serviço de nuvem do Azure](/pt-br/documentation/articles/fundamentals-application-models/#CloudServices). O aplicativo usa o [Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/ee336279), o [serviço Blob do Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) e o [serviço Fila do Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern). Você pode [baixar o projeto do Visual Studio](http://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) na Galeria de Códigos do MSDN. 

O tutorial mostra como criar e executar o aplicativo localmente, como implantá-lo no Azure e executá-lo na nuvem e, finalmente, como criá-lo do zero. Você pode começar criando do zero e depois fazer o teste e implantar as etapas posteriormente se preferir.

## O aplicativo Contoso Ads

O aplicativo é um painel de anúncios eletrônico. Os usuários criam um anúncio inserindo texto e carregando uma imagem. Eles podem ver uma lista de anúncios com imagens em miniatura e podem ver a imagem em tamanho total ao selecionar um anúncio para ver os detalhes. Esta é uma captura de tela:

![Ad list](./media/cloud-services-dotnet-get-started/list.png)

O aplicativo usa o [padrão centrado em fila](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) para descarregar o trabalho intensivo de CPU de criação de miniaturas para um processo de back-end. 

## Arquitetura alternativa: Sites e WebJobs

Este tutorial mostra como executar front-end e back-end no serviço de nuvem do Azure. Uma alternativa é executar o front-end em um [site do Azure](/pt-br/services/web-sites/) e usar o recurso [WebJobs](http://go.microsoft.com/fwlink/?LinkId=390226) (atualmente em visualização) para o back-end. Para obter um tutorial que usa WebJobs, consulte [Introdução ao SDK WebJobs do Azure](/pt-br/documentation/articles/websites-dotnet-webjobs-sdk-get-started/). Para obter informações sobre como escolher os serviços que melhor se ajustam ao seu cenário, consulte [Comparação de Sites, Serviços de Nuvem do Azure e Máquinas Virtuais do Azure](http://azure.microsoft.com/ documentation/articles/choose-web-site-cloud-service-vm/).

## O que você vai aprender

* Como habilitar seu computador para desenvolvimento do Azure ao instalar o SDK do Azure.
* Como criar um projeto de serviço de nuvem do Visual Studio com uma função de trabalho e uma função Web MVC do ASP.NET.
* Como testar o projeto de serviço de nuvem localmente, usando o emulador de armazenamento do Azure.
* Como publicar o projeto de nuvem em um serviço de nuvem do Azure e testar usando uma conta de armazenamento do Azure.
* Como carregar arquivos e armazená-los no serviço Blob do Azure.
* Como usar o serviço Fila do Azure para comunicação entre camadas.

## Pré-requisitos

O tutorial presume que você entenda [os conceitos básicos sobre os serviços em nuvem do Azure](http://azure.microsoft.com/ documentation/articles/fundamentals-application-models/#CloudServices) como *web role* e terminologia *worker role*.  Também assumimos que você sabe como trabalhar com projetos [ASP.NET MVC](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) ou de [Web Forms](http://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview) no Visual Studio. O aplicativo função Web usa MVC, mas a maior parte do tutorial também aplica-se a Web Forms. 

Você também pode executar o aplicativo localmente sem uma assinatura do Azure, mas precisará de uma para implantar o aplicativo na nuvem. Se não tiver uma conta, você poderá [ativar os benefícios de assinante MSDN](/pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A55E3C668) ou [inscrever-se para uma avaliação gratuita](/pt-br/pricing/free-trial/?WT.mc_id=A55E3C668).

As instruções do tutorial funcionam com qualquer um dos seguintes produtos:

* Visual Studio 2013
* Visual Studio 2013 Express para Web

Se não tiver nenhum desses produtos, o Visual Studio 2013 Express para Web será instalado automaticamente quando você instalar o SDK do Azure.

## Arquitetura do aplicativo

O aplicativo armazena anúncios em um banco de dados SQL usando Entity Framework Code First para criar as tabelas e acessar os dados. Para cada anúncio o banco de dados armazena duas URLs, uma para a imagem em tamanho total e outra para a miniatura.

![Ad table](./media/cloud-services-dotnet-get-started/adtable.png)

Quando um usuário carrega uma imagem, o front-end sendo executado em uma função Web armazena a imagem em um [Blob do Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) e armazena as informações do anúncio no banco de dados com uma URL que aponta para o blob. Ao mesmo tempo, ele grava uma mensagem em uma fila do Azure. Um processo de back-end sendo executado periodicamente em um função de trabalho consulta a fila para ver se há novas mensagens. Quando uma mensagem é exibida, a função de trabalho cria uma miniatura para essa imagem e atualiza o campo do banco de dados da URL de miniatura desse anúncio. Veja a seguir este diagrama que mostra como as partes de um aplicativo interagem:

![Contoso Ads architecture](./media/cloud-services-dotnet-get-started/apparchitecture.png)

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Baixar e executar a solução completa

1. Baixar e descompactar a [solução concluída](http://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4).

2. Inicie o Visual Studio.

3. No menu **Arquivo**, escolha **Abrir Projeto**, navegue até onde você baixou a solução e, em seguida, abra o arquivo de solução.

3. Pressione CTRL+SHIFT+B para criar a solução.

	Por padrão, o Visual Studio restaura automaticamente o conteúdo do pacote NuGet, que não foi incluído no arquivo *.zip*. Se os pacotes não forem restaurados, instale-os manualmente acessando a caixa de diálogo **Gerenciar Pacotes NuGet para Solução** e clicando no botão **Restaurar** na parte superior direita. 

3. No **Gerenciador de Soluções**, certifique-se de que **ContosoAdsCloudService** foi selecionado como projeto de inicialização.

1. Pressione CTRL+F5 para executar o aplicativo.

	Quando você executa um projeto de serviço de nuvem localmente, o Visual Studio invoca automaticamente o Azure *compute emulator* e o Azure *storage emulator*. O emulador de computação usa os recursos do seu computador para simular os ambientes de função Web e de função de trabalho. O emulador de armazenamento usa um banco de dados [LocalDB do SQL Server Express](http://msdn.microsoft.com/library/hh510202.aspx) para simular o armazenamento em nuvem do Azure. 

	A primeira vez que você executar um projeto de serviço de nuvem, levará por volta de um minuto para que os emuladores sejam inicializados. Quando a inicialização do emulador for finalizada, o navegador padrão abrirá na home page do aplicativo.

	![Contoso Ads architecture](./media/cloud-services-dotnet-get-started/home.png)

2. Clique em **Criar um anúncio**.

2. Insira alguns dados de teste e selecione uma imagem em *.jpg* para carregar e depois clique em **Criar**.

	![Create page](./media/cloud-services-dotnet-get-started/create.png)

	O aplicativo vai para a Página de índice, mas não mostra uma miniatura do anúncio novo porque o processamento ainda não aconteceu.   

3. Aguarde um momento e depois atualize a Página de índice para ver a miniatura.

	![Index page](./media/cloud-services-dotnet-get-started/list.png)

4. Clique em **Detalhes** do anúncio para ver a imagem no tamanho real.

	![Details page](./media/cloud-services-dotnet-get-started/details.png)

Você está executando o aplicativo totalmente em seu computador local sem conexão com a nuvem. O emulador de armazenamento guarda os dados de fila e do Blob em um banco de dados LocalDB do SQL Server Express e o aplicativo armazena os dados do anúncio em outro banco de dados LocalDB. O Entity Framework Code First criou automaticamente o banco de dados de anúncio na primeira vez que o aplicativo Web tentou acessá-lo.

Na seção a seguir você configurará a solução para usar os recursos de nuvem do Azure em filas, blobs, e o banco de dados do aplicativo quando ele for executado na nuvem. Se você desejava continuar a executar localmente, mas usa armazenamento em nuvem e recursos de bancos de dados, pode fazer isso; basta definir as cadeias de conexão, que você verá como fazer. 

## Implantar o aplicativo no Azure

Você seguirá as etapas abaixo para executar o aplicativo na nuvem:

* Criar um serviço de nuvem do Azure 
* Criar um banco de dados SQL do Azure
* Criar uma conta de armazenamento do Azure
* Configurar a solução para usar seu banco de dados SQL do Azure quando ele for executado no Azure
* Configurar a solução para usar sua conta de armazenamento do Azure quando ela for executada no Azure
* Implantar o projeto para seu serviço de nuvem do Azure

### Criar um serviço de nuvem do Azure

Um serviço de nuvem do Azure é o ambiente em que o aplicativo será executado.

1. No seu navegador, abra o [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Clique em **Novo** - **Computação** - **Serviço de Nuvem** - **Criação Rápida**.

4. Na caixa de entrada URL, digite um prefixo de URL. 

	Essa URL precisa ser exclusiva.  Você receberá uma mensagem de erro se o prefixo escolhido já estiver sendo usado por outra pessoa.

5. Selecione a região onde deseja implantar o aplicativo.

	Este campo especifica em qual datacenter seu serviço de nuvem será hospedado. Para um aplicativo de produção, você deve escolher a região mais próxima de seus clientes. Para este tutorial, escolha a região mais próxima de você.

6. Clique em **Criar Serviço de Nuvem**. 

	Na imagem a seguir, um serviço de nuvem é criado com a URL contosoads.cloudapp.net.

	![New Cloud Service](./media/cloud-services-dotnet-get-started/newcs.png)	

### Criar um banco de dados SQL do Azure

Quando o aplicativo é executado na nuvem, ele usa um banco de dados com base na nuvem. 

1. No Portal de Gerenciamento do Azure, clique em **Novo** - **Serviços de Dados** - **Banco de Dados SQL** - **Criação Rápida**.

1. Na caixa **Nome do Banco de Dados**, insira *contosoads*.

1. Na lista suspensa **Servidor**, selecione **Novo Servidor de Banco de Dados SQL**.

	Se a assinatura já tiver um servidor, você poderá selecioná-lo da lista suspensa.

1. Selecione a mesma **Região** que você escolheu para o serviço de nuvem. 

	Quando o banco de dados e o serviço de nuvem estão em datacenters diferentes (regiões diferentes), a latência aumentará e você será cobrado pela largura de banda fora do datacenter. A largura de banda em um data center é gratuita.

1. Insira um **Nome de Logon** e **Senha** de administrador. 

	Se selecionou **Novo servidor do Banco de Dados SQL**, você não irá digitar um nome e uma senha existentes aqui; digite um novo nome e uma senha que você está definindo agora para usar mais tarde ao acessar o banco de dados. Se você selecionou um servidor criado anteriormente, você será solicitado a fornecer a senha da conta do usuário administrador já criada.

1. Clique em **Criar Banco de Dados SQL**.

	![New SQL Database](./media/cloud-services-dotnet-get-started/newdb.png)	

1. Após o Azure terminar de criar o banco de dados, clique na guia **Bancos de dados SQL** no painel à esquerda do portal e depois clique no nome do novo banco de dados.

2. Clique na guia **Painel**.

3. Clique em **Gerenciar endereços IP permitidos**.

4. Em **Serviços permitidos**, altere **Serviços do Azure** para **Sim**.

5. Clique em **Salvar**.

### Criar uma conta de armazenamento do Azure

Uma conta de armazenamento do Azure fornece os recursos para dados de blob e fila de armazenamento na nuvem.

Em um aplicativo do mundo real, geralmente você cria contas separadas para dados de aplicativos e dados de log, e contas separadas para dados de teste e dados de produção. Neste tutorial você usará apenas uma conta.

1. No Portal de Gerenciamento do Azure, clique em **Novo** - **Serviços de Dados** - **Armazenamento** - **Criação Rápida**.

4. Na caixa da **URL**, insira um prefixo de URL. 

	Esse prefixo, além do texto que você vê sob a caixa será a URL exclusiva de sua conta de armazenamento. Se o prefixo inserido já estiver sendo usado por outra pessoa, você terá que escolher um diferente.

5. Defina a lista suspensa **Região** para a mesma região escolhida para o serviço de nuvem.

	Quando a conta de armazenamento do serviço de nuvem estiver em outros datacenters (outras regiões), a latência será maior e você será cobrado pela largura de banda fora do data center. A largura de banda em um data center é gratuita.

	O grupos de afinidade do Azure fornecem um mecanismo para minimizar a distância entre os recursos em um data center, o que pode reduzir a latência. Este tutorial não usa grupos de afinidade. Para obter mais informações, consulte [Como criar um grupo de afinidade no Azure](http://msdn.microsoft.com/library/jj156209.aspx).

6. Defina a lista suspensa **Replicação** para **Localmente redundante**. 

	Quando a replicação geográfica está habilitada para uma conta de armazenamento, o conteúdo armazenado é replicado para um datacenter secundário para habilitar o failover para essa localidade no caso de ocorrer um grande desastre no local principal. A replicação geográfica pode incorrer em custos adicionais. Para contas de teste e desenvolvimento, geralmente, você não deseja pagar pela replicação geográfica. Para obter mais informações, consulte [Criar, gerenciar ou excluir uma conta de armazenamento](../storage-create-storage-account/#replication-options).

5. Clique em **Criar Conta de Armazenamento**. 

	![New storage account](./media/cloud-services-dotnet-get-started/newstorage.png)	

	Na imagem, uma conta de armazenamento é criada com a URL `contosoads.core.windows.net`.

### Configurar a solução para usar seu banco de dados SQL do Azure quando ele for executado no Azure

O projeto Web e o projeto de função de trabalho possuem suas próprias cadeias de conexão de banco de dados, e cada uma precisa apontar para o banco de dados SQL do Azure quando o aplicativo é executado no Azure.

Você usará uma [transformação de Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) para a função de trabalho e uma configuração de ambiente de serviço de nuvem para a função de trabalho.

>[AZURE.NOTE] Nesta seção e na próxima, você armazenará credenciais nos arquivos de projeto. [Não armazene dados confidenciais em repositórios de código-fonte público](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets).

1. No projeto ContosoAdsWeb, abra o arquivo de transformação  *Web.Release.config*Web.Release.config para o arquivo  *Web.config*Web.config do aplicativo, exclua o bloco de comentário que contém um elemento `<connectionStrings>` e cole o código a seguir no lugar. 

		<connectionStrings>
	        <add name="ContosoAdsContext" connectionString="{connectionstring}"
		    providerName="System.Data.SqlClient" xdt:Transform="SetAttributes" xdt:Locator="Match(name)"/>
		</connectionStrings>

	Deixe o arquivo aberto para edição.

2. No Portal de Gerenciamento do Azure, clique em **Bancos de dados SQL** no painel esquerdo, clique no banco de dados criado para este tutorial, clique na guia **Painel** e, em seguida, clique em **Mostrar Cadeias de Conexão**.

	![Show connection strings](./media/cloud-services-dotnet-get-started/showcs.png)	

	O portal exibe as cadeias de conexão com um espaço reservado para a senha.

	![Connection strings](./media/cloud-services-dotnet-get-started/connstrings.png)	

4. No arquivo de transformação  *Web.Release.config*, exclua  `{connectionstring}` e cole no lugar a cadeia de conexão ADO.NET do Portal de Gerenciamento.

5. Na cadeia de conexão que você colou no arquivo de transformação  *Web.Release.config*, substitua  `{your_password_here}` pela senha que você criou para o novo Banco de Dados SQL.

7. Salve o arquivo.  

6. Selecione e copie a cadeia de conexão (sem as aspas) para usar nas etapas a seguir para configurar o projeto de função de trabalho.

5. Em **Gerenciador de Soluções**, em **Funções** no projeto de serviço de nuvem, clique com o botão direito do mouse em**ContosoAdsWorker** e depois clique em **Propriedades**.

	![Role properties](./media/cloud-services-dotnet-get-started/rolepropertiesworker.png)	

6. Clique na guia **Configurações**.

7. Altere **Configuração de Serviço** para **Nuvem**.

7. Selecione o texto na configuração  `ContosoAdsDbConnectionString` e cole a cadeia de conexão que você copiou da seção anterior do tutorial.

	![Database connection string for worker role](./media/cloud-services-dotnet-get-started/workerdbcs.png)	

7. Salve suas alterações.  
 
### Configurar a solução para usar sua conta de armazenamento do Azure quando ela for executada no Azure

As cadeias de conexão da conta de armazenamento do Azure do projeto de função de trabalho e do projeto de função Web são armazenadas nas configurações de ambiente do projeto de serviço de nuvem. Para cada projeto existe um conjunto separado de configurações a ser usado quando o aplicativo é executado localmente e quando é executado na nuvem. Você atualizará as configurações de ambiente de nuvem para os projetos de função de trabalho e da Web. 

4. No **Gerenciador de Soluções**, clique como o botão direito do mouse em **ContosoAdsWeb** em **Funções** no projeto **ContosoAdsCloudService** e depois clique em **Propriedades**.

	![Role properties](./media/cloud-services-dotnet-get-started/roleproperties.png)	

5. Clique na guia **Configurações**. Na caixa suspensa **Configuração de Serviço** selecione **Nuvem**.

	![Cloud configuration](./media/cloud-services-dotnet-get-started/sccloud.png)	

6. Selecione a entrada **StorageConnectionString** e você verá um botão de reticências (**...**) na extremidade direita da linha. Clique no botão para abrir a caixa de diálogo **Criar cadeia de conexão da conta de armazenamento**.

	![Open Connection String Create box](./media/cloud-services-dotnet-get-started/opencscreate.png)	

1. Na caixa de diálogo **Criar Cadeia de conexão de armazenamento**, clique em **Conectar usando assinatura**, selecione a conta de armazenamento e, em seguida, clique em **OK**. Se você não tiver feito logon, suas credenciais da conta do Azure serão solicitadas.

	![Create Storage Connection String](./media/cloud-services-dotnet-get-started/createstoragecs.png)	

1. Salve suas alterações.

2. Siga o mesmo procedimento que usou para a cadeia de conexão `StorageConnectionString` para definir a cadeia de conexão  `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

	Essa cadeia de conexão é usada para o log.

2. Siga o mesmo procedimento que usou para a função **ContosoAdsWeb** definir as duas cadeias de conexão da função **ContosoAdsWorker**. Não se esqueça de definir **Configuração de Serviço** como **Nuvem**.

As configurações do ambiente de função que você configurou usando a interface de usuário do Visual Studio estão armazenadas no seguintes arquivos do projeto ContosoAdsCloudService:

* *ServiceDefinition.csdef* - Define os nomes de configuração.
* *ServiceConfiguration.Cloud.cscfg* - Fornece os valores para quando o aplicativo for executado na nuvem.
* *ServiceConfiguration.Local.cscfg* - Fornece os valores para quando o aplicativo for executado localmente.

Por exemplo, ServiceDefinition.csdef inclui as seguintes definições:

		<ConfigurationSettings>
		  <Setting name="StorageConnectionString" />
		  <Setting name="ContosoAdsDbConnectionString" />
		</ConfigurationSettings>

E o arquivo  *ServiceConfiguration.Cloud.cscfg* inclui os valores inseridos para essas configurações no Visual Studio:

		<Role name="ContosoAdsWorker">
		  <Instances count="1" />
		  <ConfigurationSettings>
		    <Setting name="StorageConnectionString" value="{yourconnectionstring}" />
		    <Setting name="ContosoAdsDbConnectionString" value="{yourconnectionstring}" />
		    <!-- other settings not shown -->
		  </ConfigurationSettings>
		  <!-- other settings not shown -->
		</Role>

A configuração `<Instances>` especifica o número de máquinas virtuais onde o Azure executará o código da função de trabalho. A seção [Próximas etapas](#next-steps) inclui links para mais informações sobre o dimensionamento de um serviço de nuvem,

###  Implantar o projeto no Azure

3.	No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto de nuvem **ContosoAdsCloudService** e selecione **Publicar**.

	![Publish menu](./media/cloud-services-dotnet-get-started/pubmenu.png)	

2. Na etapa **Entrar** do assistente **Publicar aplicativo do Windows Azure** clique em **Avançar**.

	![Sign in step](./media/cloud-services-dotnet-get-started/pubsignin.png)	

3. Na etapa **Configurações** do assistente clique em **Avançar**.

	![Settings step](./media/cloud-services-dotnet-get-started/pubsettings.png)	

	As configurações padrão na guia **Avançado** são adequadas para este tutorial. Para obter mais informações sobre a guia avançada, consulte [Assistente de publicação de Aplicativo do Azure](http://msdn.microsoft.com/library/windowsazure/hh535756.aspx).

2. Na etapa **Resumo**, clique em **Publicar**.

	![Summary step](./media/cloud-services-dotnet-get-started/pubsummary.png)	
   
   A janela **Log de atividade do Azure** será exibida no Visual Studio. 

2. Clique no ícone de seta para a direita para expandir os detalhes da implantação.

	A implantação pode levar cerca de 5 minutos ou mais para ser concluída.

	![Azure Activity Log window](./media/cloud-services-dotnet-get-started/waal.png)	

1. Quando o status da implantação for concluído, clique na **URL do Site** para iniciar o aplicativo.

9. Você pode testar o aplicativo criando, visualizando e editando alguns anúncios, como fez ao executar o aplicativo localmente.

>[AZURE.NOTE] Ao finalizar o teste, exclua ou interrompa o serviço de nuvem. Mesmo que não estiver usando o serviço de nuvem, seus acúmulos serão cobrados porque os recursos do computador virtual serão reservados para o serviço. E se você deixá-lo em execução, qualquer um que encontrar sua URL poderá criar e exibir anúncios. No Portal de Gerenciamento do Azure acesse a guia **Painel** para seu serviço de nuvem e depois clique no botão **Excluir** na parte inferior da página. Se apenas deseja evitar que outros acessem temporariamente o site, em vez disso, clique em **Parar**. Nesse caso, as tarifas continuarão a acumular. Você pode seguir um procedimento semelhante para excluir o banco de dados SQL e a conta de armazenamento quando não precisar mais dela.

## Criar o aplicativo a partir do zero 

Se ainda não fez o download 
[do aplicativo completo](http://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4), faça-o agora. Você copiará os arquivos do projeto baixado para o novo projeto.

A criação do Contoso Ads envolve as seguintes etapas:

* Criar um serviço de nuvem na solução Visual Studio
* Atualizar e adicionar pacotes NuGet
* Definir referências de projeto
* Configurar cadeias de conexão
* Adicionar arquivos de código

Após a solução ser criada, você revisará o código exclusivo dos projetos de serviço de nuvem e dos blobs e filas do Azure.
 
### Criar um serviço de nuvem na solução Visual Studio

1. No Visual Studio, selecione **Novo Projeto** no menu **Arquivo**.

2. No painel esquerdo da caixa de diálogo **Novo Projeto**, expanda **Visual C#** e selecione os modelos de **Nuvem**; em seguida, selecione o modelo de **Serviço de Nuvem do Windows Azure**.

3. Nomeie o projeto e a solução ContosoAdsCloudService e depois clique em **OK**.

	![New Project](./media/cloud-services-dotnet-get-started/newproject.png)	

4. Na caixa de diálogo **Novo projeto de serviço de nuvem**, adicione uma função de trabalho e uma função Web. Nomeie a função Web ContosoAdsWeb e a função de trabalho ContosoAdsWorker. (Use o ícone da caneta no painel direito para alterar os nomes padrão das funções.)

	![New Cloud Service Project](./media/cloud-services-dotnet-get-started/newcsproj.png)	

5. Quando a caixa de diálogo **Novo projeto ASP.NET** da função Web for exibia, selecione o modelo MVC e clique em **Alterar autenticação**.

	![Change Authentication](./media/cloud-services-dotnet-get-started/chgauth.png)	

7. Na caixa de diálogo Alterar Autenticação, escolha **Sem Autenticação** e clique em **OK**.

	![No Authentication](./media/cloud-services-dotnet-get-started/noauth.png)	

8. Na caixa de diálogo **Novo projeto ASP.NET** clique em **OK**. 

9. No **Gerenciador de Soluções**, clique com o botão direito na solução (não aquela de projetos) e selecione **Adicionar - Novo Projeto**.

11. Na caixa de diálogo **Adicionar Novo Projeto**, selecione **Área de Trabalho do Windows** em **Visual C#** no painel esquerdo e, em seguida, clique no modelo **Biblioteca de Classes**.  

10. Nomeie o projeto *ContosoAdsCommon*e, em seguida, clique em **OK**.

	Você precisa usar como referência o contexto e o modelo de dados do Entity Framework para os projetos de função de trabalho e da Web. Como alternativa, você pode definir as classes relacionadas ao EF no projeto de função Web e usar esse projeto como referência para o projeto de função de trabalho. Mas fazendo isso, o seu projeto de função de trabalho teria uma referência para um assembly da Web que não precisa.

### Atualizar e adicionar pacotes NuGet

11. Abra a caixa de diálogo **Gerenciar pacotes NuGet** para a solução.

12. No painel esquerdo selecione **Atualizações**.
   
13. Procure o pacote  *Azure Storage* e, se ele estiver na lista, clique em **Atualizar** para obter a última versão da biblioteca do cliente de armazenamento.

	![Update SCL](./media/cloud-services-dotnet-get-started/updstg.png)	

	A biblioteca de armazenamento do cliente é atualizada com mais frequência que os modelos de projetos do Visual Studio, por isso, você descobrirá com frequência que a versão em um projeto que acabou de ser criado precisa ser atualizada.
 
14. No painel esquerdo, selecione **Online**.

16. Encontre o pacote do NuGet *EntityFramework* e instale-o em todos os três projetos.

### Definir referências de projeto

10. No projeto ContosoAdsWeb, defina uma referência para o projeto ContosoAdsCommon. Clique com o botão direito no projeto ContosoAdsWeb e depois clique em **Referências** - **Adicionar Referências**. Na caixa de diálogo **Gerenciador de referências**, selecione **Solução - Projetos** no painel esquerdo, selecione ContosoAdsCommon e depois clique em **OK**.

11. No projeto ContosoAdsWorker, defina uma referência para o projeto ContosAdsCommon.

	ContosoAdsCommon conterá o modelo de dados e a classe de contexto de Entity Framework, que serão usados por front-end e back-end.

11. No projeto ContosoAdsWorker, defina uma referência para  `System.Drawing`.

	Este assembly é usado pelo back-end para converter imagens em miniaturas.

### Configurar cadeias de conexão

Nesta seção iremos configurar o Armazenamento do Azure e as cadeias de conexão do SQL para o teste local. As instruções de implantação fornecidas anteriormente no tutorial explicam como definir as cadeias de conexão para quando o aplicativo for executado na nuvem.

3. No projeto ContosoAdsWeb, abra o arquivo Web.config do aplicativo e insira o seguinte elemento  `connectionStrings` após o elemento  `configSections`:

		<connectionStrings>
		  <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
		</connectionStrings>

3. Salve suas alterações.

2. No projeto ContosoAdsCloudService, clique com o botão direito do mouse em ContosoAdsWeb abaixo de **Funções** e depois clique em **Propriedades**.

	![Role properties](./media/cloud-services-dotnet-get-started/roleproperties.png)	

3. Na janela de propriedades de **ContosAdsWeb[Role]**, clique na guia **Configurações** e depois clique em **Adicionar configuração**.

	Deixe **Configuração de Serviço** definida como **Todas as configurações**.

4. Adicione uma nova cadeia chamada  *StorageConnectionString*. Defina**Tipo** para *ConnectionString*, e defina **Valor** para *UseDevelopmentStorage=true*.

	![New connection string](./media/cloud-services-dotnet-get-started/scall.png)	

5. Salve suas alterações.

3. Siga o mesmo procedimento para adicionar uma cadeia de conexão nas propriedades da função ContosoAdsWorker.

5. Ainda na janela de propriedades de **ContosoAdsWorker [Role]**, adicione outra cadeia de conexão:

	* Nome: ContosoAdsDbConnectionString
	* Tipo: Cadeia de caracteres
	* Valor: Cole a mesma cadeia de conexão usada para o projeto de função Web:
	
			Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;
	
### Adicionar arquivos de código

Nesta seção você copiará códigos da solução baixada para a nova solução. As seções a seguir mostrarão e explicarão as partes principais desse código.

Para adicionar arquivos a um projeto ou pasta, clique com o botão direito do mouse em **Adicionar** - **Item Existente**. Selecione os arquivos que deseja e clique em **Adicionar**. Se receber uma solicitação para confirmar se deseja substituir os arquivos existentes, clique em **Sim**.

3. No projeto ContosoAdsCommon, exclua o arquivo *Class1.cs* e adicione em seu lugar os arquivos *Ad.cs* e *ContosoAdscontext.cs* do projeto baixado.

3. No projeto ContosoAdsWeb, adicione os seguintes arquivos do projeto baixado.
	- *Global.asax.cs*  
	- Na pasta *Views\Shared*: <em>_Layout.cshtml.</em>.
	- Na pasta *Views\Home*: *Index.cshtml*.
	- Na pasta *Controllers*: *AdController.cs*.
	- Na pasta *Views\Ad* (crie a pasta primeiro): cinco arquivos *.cshtml*.

3. No projeto ContosoAdsWorker, adicione *WorkerRole.cs* a partir do projeto baixado.

Você pode criar e executar os aplicativos conforme explicado anteriormente no tutorial e o aplicativo usará os recursos locais de banco de dados e emulador de armazenamento.

As seções a seguir explicam o código relacionado ao trabalho com os blobs e filas do ambiente do Azure. Neste tutorial não explicaremos como criar os controladores e as visualizações MVC usando scaffolding, como escrever código do Entity Framework que funcione com bancos de dados SQL Server ou noções básicas de programação assíncrona de ASP.NET 4.5. Para obter informações sobre esses tópicos, consulte os seguintes recursos:

* [Introdução ao MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Introdução ao EF 6 e ao MVC 5](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc)
* [Introdução à programação assíncrona no .NET 4.5](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).

### ContosoAdsCommon - Ad.cs

O arquivo Ad.cs file define uma enumeração para categorias de anúncios e uma classe de entidade POCO para as informações de anúncios.

		public enum Category
		{
		    Cars,
		    [Display(Name="Real Estate")]
		    RealEstate,
		    [Display(Name = "Free Stuff")]
		    FreeStuff
		}

		public class Ad
		{
		    public int AdId { get; set; }

		    [StringLength(100)]
		    public string Title { get; set; }

		    public int Price { get; set; }

		    [StringLength(1000)]
		    [DataType(DataType.MultilineText)]
		    public string Description { get; set; }

		    [StringLength(1000)]
		    [DisplayName("Full-size Image")]
		    public string ImageURL { get; set; }

		    [StringLength(1000)]
		    [DisplayName("Thumbnail")]
		    public string ThumbnailURL { get; set; }

		    [DataType(DataType.Date)]
		    [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
		    public DateTime PostedDate { get; set; }

		    public Category? Category { get; set; }
		    [StringLength(12)]
		    public string Phone { get; set; }
		}

### ContosoAdsCommon - ContosoAdsContext.cs

A classe ContosoAdsContext especifica que a classe de anúncio é usada em uma coleção de DbSet e qual Entity Framework será armazenada em um banco de dados SQL.

		public class ContosoAdsContext : DbContext
		{
		    public ContosoAdsContext() : base("name=ContosoAdsContext")
		    {
		    }
		    public ContosoAdsContext(string connString)
		        : base(connString)
		    {
		    }
		    public System.Data.Entity.DbSet<Ad> Ads { get; set; }
		}
 
A classe possui dois construtores. O primeiro deles é usado pelo projeto Web e especifica o nome de uma cadeia de conexão armazenada no arquivo Web.config. O segundo construtor habilita você a transmitir a cadeia de conexão real. Isso é necessário para o projeto de função de trabalho, porque ela não tem um arquivo Web.config. Foi mostrado anteriormente onde essa cadeia de conexão foi armazenada, e você verá mais adiante como o código recupera a cadeia de conexão quando ele cria uma instância de classe DbContext.

### ContosoAdsWeb - Global.asax.cs

Código que é chamado a partir do método `Application_Start` cria um *images* contêiner de blob e ua fila *images* se eles ainda não existirem. Isso garante que sempre que você começar a usar uma nova conta de armazenamento, ou começar a usar o emulador de armazenamento em um novo computador, o contêiner do blob e a fila necessários serão criados automaticamente.

O código obtém acesso à conta de armazenamento usando a cadeia de conexão de armazenamento do arquivo *.cscfg*.

		var storageAccount = CloudStorageAccount.Parse
		    (RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

Em seguida, ele obtém uma referência para o contêiner do blob  *images*, cria o contêiner se ele ainda não existe e define permissões de acesso no novo contêiner. Por padrão, os novos contêineres somente permite que os clientes com credenciais da conta de armazenamento acessem os blobs. O site precisa que os blobs para o público para poder exibir imagens usando URLs que apontam para os blobs de imagem.

		var blobClient = storageAccount.CreateCloudBlobClient();
		var imagesBlobContainer = blobClient.GetContainerReference("images");
		if (imagesBlobContainer.CreateIfNotExists())
		{
		    imagesBlobContainer.SetPermissions(
		        new BlobContainerPermissions
		        {
		            PublicAccess =BlobContainerPublicAccessType.Blob
		        });
		}

Um código semelhante obtém uma referência para a fila  *images* e cria uma nova fila. Nesse caso, nenhuma alteração de permissão é necessária.

		CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
		var imagesQueue = queueClient.GetQueueReference("images");
		imagesQueue.CreateIfNotExists();

### ContosoAdsWeb - _Layout.cshtml

O arquivo *_Layout.cshtml* define o nome do aplicativo no cabeçalho e no rodapé e cria uma entrada de menu "Anúncios".

### ContosoAdsWeb - Views\Home\Index.cshtml

O arquivo *Views\Home\Index.cshtml* exibe links de categoria na home page. Os links passam o valor inteiro da enumeração  `Category` em uma variável querystring para a página Índice de anúncio.
	
		<li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
		<li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
		<li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
		<li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>

### ContosoAdsWeb - AdController.cs

No arquivo  *AdController.cs*, o construtor chama o método  `InitializeStorage` para criar os objetos da Biblioteca do Cliente do Armazenamento do Azure que fornecem uma API para trabalhar com blobs e filas. 

Em seguida, o código obtém uma referência para o *images* contêiner de blob, como você viu anteriormente na *Global.asax.cs*. Ao fazer isso, ele define uma [política de recuperação](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) padrão apropriada para um aplicativo Web. A política de recuperação de retirada exponencial padrão pode fazer com que o aplicativo Web pare de responder por mais de um minuto em tentativas repetidas de uma falha transitória. A política de recuperação especificada aqui aguarda 3 segundos após cada tentativa, até 3 tentativas.

		var blobClient = storageAccount.CreateCloudBlobClient();
		blobClient.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
		imagesBlobContainer = blobClient.GetContainerReference("images");

Um código semelhante obtém uma referência para a fila *images*.

		CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
		queueClient.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
		imagesQueue = queueClient.GetQueueReference("images");

A maior parte do código do controlador é típica para trabalhar com um modelo de dados do Entity Framework utilizando uma classe DbContext. Uma exceção é o método HttpPost  `Create`, que atualiza um arquivo e o salva no armazenamento do blob. O associador de modelo fornece um objeto [HttpPostedFileBase](http://msdn.microsoft.com/library/system.web.httppostedfilebase.aspx) para o método.

		[HttpPost]
		[ValidateAntiForgeryToken]
		public async Task<ActionResult> Create(
		    [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
		    HttpPostedFileBase imageFile)

Se o usuário selecionou um arquivo para carregamento, o código carrega o arquivo, salva o arquivo em um blob e atualiza o registro do banco de dados do anúncio com uma URL que aponta para o blob.

		if (imageFile != null && imageFile.ContentLength != 0)
		{
		    blob = await UploadAndSaveBlobAsync(imageFile);
		    ad.ImageURL = blob.Uri.ToString();
		}

O código que não é carregado está no método  `UploadAndSaveBlobAsync`. Ele cria um nome de GUID para o blob, carrega e salva o arquivo e retorna uma referência para o blob salvo.

		private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
		{
		    string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
		    CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
		    using (var fileStream = imageFile.InputStream)
		    {
		        await imageBlob.UploadFromStreamAsync(fileStream);
		    }
		    return imageBlob;
		}

Depois de o método HttpPost  `Create` carregar um blob e atualizar o banco de dados, ele cria uma mensagem de fila para informar ao processo back-end que uma imagem está pronta para ser convertida para uma miniatura.

		string queueMessageString = ad.AdId.ToString();
		var queueMessage = new CloudQueueMessage(queueMessageString);
		await queue.AddMessageAsync(queueMessage);

O código para o método HttpPost  `Edit` é semelhante, exceto pelo fato de que se o usuário seleciona um novo arquivo de imagem, quaisquer blobs existentes deverão ser excluídos.
 
		if (imageFile != null && imageFile.ContentLength != 0)
		{
		    await DeleteAdBlobsAsync(ad);
		    imageBlob = await UploadAndSaveBlobAsync(imageFile);
		    ad.ImageURL = imageBlob.Uri.ToString();
		}

Este é o código que exclui os blobs quando você exclui um anúncio:

		private async Task DeleteAdBlobsAsync(Ad ad)
		{
		    if (!string.IsNullOrWhiteSpace(ad.ImageURL))
		    {
		        Uri blobUri = new Uri(ad.ImageURL);
		        await DeleteAdBlobAsync(blobUri);
		    }
		    if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
		    {
		        Uri blobUri = new Uri(ad.ThumbnailURL);
		        await DeleteAdBlobAsync(blobUri);
		    }
		}
		private static async Task DeleteAdBlobAsync(Uri blobUri)
		{
		    string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
		    CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
		    await blobToDelete.DeleteAsync();
		}
 
### ContosoAdsWeb - Views\Ad\Index.cshtml e Details.cshtml

O arquivo *Index.cshtml* exibe miniaturas com os outros dados do anúncio:

		<img  src="@Html.Raw(item.ThumbnailURL)" />

O arquivo *Details.cshtml* exibe a imagem em tamanho normal:

		<img src="@Html.Raw(Model.ImageURL)" />

### ContosoAdsWeb - Views\Ad\Create.cshtml e Edit.cshtml

Os arquivos *Create.cshtml* e *Edit.cshtml* especificam a codificação do formulário que permitem que o controlador obtenha o objeto `HttpPostedFileBase`.

		@using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))

Um elemento`<input>` informa o navegador para fornecer uma caixa de diálogo de seleção de arquivo.

		<input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />

### ContosoAdsWorker - WorkerRole.cs - Método OnStart

O ambiente da função de trabalho do Azure chama o método  `OnStart` na classe  `WorkerRole` quando a função de trabalho é iniciada e clama o método  `Run` quando o método  `OnStart` é concluído.

O método  `OnStart` obtém a cadeia de conexão do banco de dados do arquivo *.cscfg* e a transmite para a classe DbContext do Entity Framework. O provedor SQLClient é usado por padrão para que o provedor não precisar ser especificado.

		var dbConnString = CloudConfigurationManager.GetSetting("ContosoAdsDbConnectionString");
		db = new ContosoAdsContext(dbConnString);

Após, o método obtém uma referência para a conta de armazenamento e cria a fila e o contêiner do blob, se eles não existirem. O código para isso é semelhante àquele já visto no método da função web  `Application_Start`.

### ContosoAdsWorker - WorkerRole.cs - Método de execução

O método  `Run` é chamado quando o método  `OnStart` conclui seu trabalho de inicialização. O método executa um loop infinito que procura novas mensagens na fila e as processa quando chegam.

		public override void Run()
		{
		    CloudQueueMessage msg = null;
		
		    while (true)
		    {
		        try
		        {
		            msg = this.imagesQueue.GetMessage();
		            if (msg != null)
		            {
		                ProcessQueueMessage(msg);
		            }
		            else
		            {
		                System.Threading.Thread.Sleep(1000);
		            }
		        }
		        catch (StorageException e)
		        {
		            if (msg != null && msg.DequeueCount > 5)
		            {
		                this.imagesQueue.DeleteMessage(msg);
		            }
		            System.Threading.Thread.Sleep(5000);
		        }
		    }
		}

Após cada interação do loop, se nenhuma mensagem foi encontrada na fila, o programa é suspenso durante um segundo. Isso evita que a função de trabalho utilize tempo excessivo de CPU e incorra em gastos de transação de armazenamento. A equipe de auxílio ao cliente da Microsoft relatou uma história sobre um desenvolvedor que esqueceu de incluir isso, implantou em produção e saiu de férias. Quando retornou, seu esquecimento custou mais que as férias.

Algumas vezes o conteúdo de uma mensagem da fila causa um erro no processamento. Isso é chamado de  *poison message*, e se você acabou de registrar um erro e reiniciou o loop, pode tentar processar essa mensagem infinitamente.  Portanto, o bloco de captura inclui uma instrução que verifica quantas vezes o aplicativo tentou processar a mensagem atual, e se isso aconteceu mais de 5 vezes, a mensagem é excluída da fila.

O `ProcessQueueMessage`é chamado quando uma mensagem é encontrada.

		private void ProcessQueueMessage(CloudQueueMessage msg)
		{
		    var adId = int.Parse(msg.AsString);
		    Ad ad = db.Ads.Find(adId);
		    if (ad == null)
		    {
		        throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", adId.ToString()));
		    }
		
		    CloudBlockBlob inputBlob = this.imagesBlobContainer.GetBlockBlobReference(ad.ImageURL);
		
		    string thumbnailName = Path.GetFileNameWithoutExtension(inputBlob.Name) + "thumb.jpg";
		    CloudBlockBlob outputBlob = this.imagesBlobContainer.GetBlockBlobReference(thumbnailName);
		
		    using (Stream input = inputBlob.OpenRead())
		    using (Stream output = outputBlob.OpenWrite())
		    {
		        ConvertImageToThumbnailJPG(input, output);
		        outputBlob.Properties.ContentType = "image/jpeg";
		    }
		
		    ad.ThumbnailURL = outputBlob.Uri.ToString();
		    db.SaveChanges();
		
		    this.imagesQueue.DeleteMessage(msg);
		}

Esse código lê o banco de dados para obter a URL da imagem, converte a imagem para uma miniatura, salva a miniatura em um blob, atualiza o banco de dados com a URL do blob da miniatura e exclui a mensagem da fila.

>[AZURE.NOTE] O código no método  `ConvertImageToThumbnailJPG` usa classes no namespace System.Drawing para simplificar. Entretanto, as classes nesse namespace foram projetadas para uso nos formulários do Windows. Elas não têm suporte para uso em um serviço Windows ou ASP.NET.

## Solução de problemas

Caso algo não funcione enquanto você estiver seguindo as instruções nesse tutorial, veja a seguir alguns erros comuns e como resolvê-los.

### ServiceRuntime.RoleEnvironmentException

O objeto  `RoleEnvironment` é fornecido pelo Azure quando você executa uma aplicação no Azure ou quando você executa localmente usando o emulador de computação do Azure.  Se você obter esse erro quando estiver executando localmente, certifique-se de que definiu o projeto ContosoAdsCloudService como o projeto de inicialização. Isso define o projeto para ser executado usando o emulador de computação do Azure.

Uma das coisas para as quais o aplicativo usa o RoleEnvironment do Azure é para obter os valores de cadeia de conexão armazenados nos arquivos *.cscfg* e, portanto, uma nova causa dessa exceção é uma cadeia de conexão perdida. Certifique-se de que criou a configuração StorageConnectionString para as configurações local e de nuvem no projeto ContosoAdsWeb, e de que criou as duas cadeias de conexão para as ambas as configurações do projeto ContosoAdsWorker. Se fizer uma pesquisa **Localizar Tudo** para StorageConnectionString na solução inteira, você deverá vê-la 9 vezes em 6 filas.

### Não é possível substituir porta xxx. O valor abaixo do mínimo permitido para a nova porta é de 8080 para o protocolo http

Tente alterar o número da porta usado pelo projeto Web. Clique com o botão direito do mouse no projeto ContosoAdsWeb e depois clique em **Propriedades**. Clique na guia **Web** e depois altere o número da porta na configuração **URL do projeto**.

Para obter uma outra alternativa que possa resolver o problema, consulte a seção a seguir.

### Outros erros que podem ocorrer ao executar localmente

Por padrão, os novos projetos de serviço de nuvem usam o emulador de computação expresso do Azure para simular o ambiente do Azure. Essa é uma versão leve do emulador de computação completo e em algumas condições o emulador completo funcionará quando a versão expressa não funcionar.  

Para alterar o projeto a fim de usar o emulador completo, clique com o botão direito do mouse no projeto ContosoAdsCloudService e depois clique em **Propriedades**. Na janela **Propriedades** clique na guia **Web** e depois clique no botão de opção **Usar Emulador Completo**.

Para executar o aplicativo com o emulador completo, abra o Visual Studio com privilégios de administrador.

## Próximas etapas

Os aplicativo Contoso Ads foi, intencionalmente, mantido simples para este tutorial de introdução. Por exemplo, ele não implementa [injeção de dependência](http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection) ou os [padrões de unidade de trabalho e repositório](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), ele não [usa uma interface para registro em log](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log), não usa [Migrações Iniciais de Código de EF](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) para gerenciar as alterações de modelo de dados ou [Resiliência de Conexão de EF](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) para gerenciar erros de rede transitórios, e assim por diante.

Estes são alguns aplicativos função Web de serviço de nuvem que demonstram práticas mais reais de codificação, listados do menos para o mais complexo:

* [PhluffyFotos](http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31). Em conceito, é semelhante ao Contoso Ads, mas implementa mais recursos e práticas mais reais de codificação.
* [Aplicativo multicamada de serviço de nuvem do Azure com tabelas, filas e blobs](http://code.msdn.microsoft.com/windowsazure/Windows-Azure-Multi-Tier-eadceb36). Introduz as tabelas de armazenamento do Azure, bem como blobs e filas, e vem com uma [série de tutoriais passo a passo](http://azure.microsoft.com/ documentation/articles/cloud-services-dotnet-multi-tier-app-storage-1-overview/).
* [Noções Básicas sobre Serviço de Nuvem no Windows Azure](http://code.msdn.microsoft.com/Cloud-Service-Fundamentals-4ca72649). Um exemplo abrangente que demonstra uma grande variedade de melhores práticas, produzido pelo grupo de Práticas e Padrões da Microsoft.

Para obter informações sobre como desenvolver para a nuvem, consulte [Criando aplicativos de nuvem do mundo real no Windows Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction).

Para assistir a um vídeo de introdução às melhores práticas e padrões de armazenamento do Azure [Armazenamento do Microsoft Azure - O que há de novo, melhores práticas e padrões](http://channel9.msdn.com/Events/Build/2014/3-628).

Para obter mais informações, consulte os recursos a seguir:

* [Serviços de nuvem do Azure Parte 1: Introdução](http://justazure.com/microsoft-azure-cloud-services-part-1-introduction/)
* [Serviços de nuvem do Azure](/pt-br/documentation/services/cloud-services/)
* [Armazenamento do Azure](/pt-br/documentation/services/storage/)
* [Como usar o Armazenamento de Blob no .NET](/pt-br/documentation/articles/storage-dotnet-how-to-use-blobs/)
* [Como usar o Armazenamento de Fila no .NET](/pt-br/documentation/articles/storage-dotnet-how-to-use-queues/)

<!--HONumber=45--> 
