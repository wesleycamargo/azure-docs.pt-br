<properties 
	pageTitle="Introdução ao SDK de Trabalhos Web do Azure" 
	description="Saiba como criar um aplicativo de múltiplas camadas usando ASP.NET MVC e o Azure. O front-end é executado em um site e o back-end é executado como um trabalho de Web. O aplicativo usa o Entity Framework, banco de dados SQL e filas do armazenamento do Azure e blobs." 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/12/2014" 
	ms.author="tdykstra"/>

# Introdução ao SDK de Trabalhos Web do Azure

Este tutorial mostra como criar um aplicativo ASP.NET MVC de várias camadas que utiliza o SDK de Trabalhos Web para trabalhar com [filas do Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) e [blobs do Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) em um [Site do Azure](/pt-br/documentation/services/websites/). O aplicativo também usa o [Banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/ee336279). 

O aplicativo de exemplo é um boletim informativo de anúncio. Os usuários criam um anúncio inserindo texto e carregando uma imagem. Eles podem ver uma lista de anúncios com imagens em miniatura e podem ver a imagem em tamanho total ao selecionar um anúncio para ver os detalhes. Esta é uma captura de tela:

![Ad list](./media/websites-dotnet-webjobs-sdk-get-started/list.png)

É possível [baixar o projeto do Visual Studio][na]Galeria de Códigos do MSDN. 

[baixar]: http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb

## Sumário

- [Pré-requisitos](#prerequisites)
- [O que você vai aprender](#learn)
- [Arquitetura do aplicativo](#contosoads)
- [Configurar o ambiente de desenvolvimento](#setupdevenv)
- [Compilar, executar e implantar o aplicativo](#storage)
- [Criar o aplicativo do zero](#create)
- [Examinar o código do aplicativo](#code)
- [Próximas etapas](#next-steps)

## <a id="prerequisites"></a>Pré-requisitos

O tutorial pressupõe que você saiba como trabalhar com projetos [ASP.NET MVC](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) ou [Web Forms](http://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview) no Visual Studio. O aplicativo função Web usa MVC, mas a maior parte do tutorial também aplica-se a Web Forms. 

As instruções do tutorial funcionam com os seguintes produtos:

* Visual Studio 2013
* Comunidade do Visual Studio 2013
* Visual Studio 2013 Express para Web

Se não tiver nenhum desses produtos, o Visual Studio 2013 Express para Web será instalado automaticamente quando você instalar o SDK do Azure.

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

## <a id="learn"></a>O que você vai aprender

O tutorial mostra como executar as seguintes tarefas:

* Habilitar o computador para desenvolvimento do Azure instalando o SDK do Azure.
* Criar um projeto Aplicativo do Console implantado automaticamente como um Trabalho Web do Azure ao implantar o projeto Web associado.
* Testar um back-end do SDK de Trabalhos Web localmente no computador de desenvolvimento.
* Publicar um aplicativo com um back-end de Trabalhos Web em um site do Azure.
* Carregar arquivos e armazená-los no serviço Azure Blob.
* Usar o SDK de Trabalhos Web do Azure para trabalhar com filas e blobs do Armazenamento do Azure.

## <a id="contosoads"></a>Arquitetura do aplicativo

O aplicativo de exemplo usa o [queue-centric work pattern](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) para aliviar o trabalho de uso intensivo de CPU de criação de miniaturas para um processo de back-end. 

O aplicativo armazena anúncios em um banco de dados SQL utilizando o Entity Framework Code First para criar as tabelas e acessar os dados. Para cada anúncio o banco de dados armazena duas URLs, uma para a imagem em tamanho total e outra para a miniatura.

![Ad table](./media/websites-dotnet-webjobs-sdk-get-started/adtable.png)

Quando um usuário carrega uma imagem, o site front-end armazena a imagem em um [blob do Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) e armazena as informações do anúncio no banco de dados com uma URL apontada para o blob. Ao mesmo tempo, ele grava uma mensagem em uma fila do Azure. Um processo back-end em execução como um Trabalho Web do Azure usa o SDK de Trabalhos Web para sondar a fila a procura de novas mensagens. Quando uma nova mensagem é exibida, o Trabalho Web cria uma miniatura dessa imagem e atualiza o campo do banco de dados da URL de miniatura desse anúncio. Veja a seguir este diagrama que mostra como as partes de um aplicativo interagem:

![Contoso Ads architecture](./media/websites-dotnet-webjobs-sdk-get-started/apparchitecture.png)

### Alternative architecture

Trabalhos Web são executados no contexto de um site e não são escalonáveis separadamente. Por exemplo, se tiver uma instância de site Padrão, você só pode ter 1 instância do processo em segundo plano em execução e ela está usando parte dos recursos do servidor (CPU, memória etc.) que, de outra forma, estariam disponíveis para atender ao conteúdo da Web. 

Se o tráfego variar de acordo com a hora do dia ou o dia da semana e se for possível esperar o processamento back-end necessário, é possível agendar os Trabalhos Web para serem executados nos horários de menos tráfego. Se ainda assim a carga for muito grande para essa solução, você pode levar em consideração ambientes alternativos para o programa back-end, como os seguintes:

* Executar o programa como um Trabalho Web em um site à parte dedicado a essa finalidade. Assim, é possível dimensionar o site back-end independentemente do site front-end.
* Executar o programa em uma função de trabalho Serviço de Nuvem do Azure. Se escolher essa opção, você pode executar o front-end em uma função web Serviço de Nuvem ou Site.

Este tutorial mostra como executar o front-end em um site e o back-end como um Trabalho Web no mesmo site. Para obter informações sobre como escolher o melhor ambiente para o cenário, consulte [Comparação entre Sites do Azure, Serviços de Nuvem e Máquinas Virtuais](/pt-br/documentation/articles/choose-web-site-cloud-service-vm/).

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

As instruções do tutorial foram gravadas usando a próxima versão de teste do [Visual Studio 2013 Atualização 4](http://go.microsoft.com/fwlink/?LinkID=510328). A única diferença com o Visual Studio 2013 Atualização 3 está na seção create-from-scratch em que você cria o projeto de trabalho Web: com a Atualização 4, os pacotes SDK de trabalhos Web são incluídos automaticamente no projeto; sem a Atualização 4, você precisa instalar os pacotes manualmente.

## <a id="storage"></a>Criar uma conta de Armazenamento do Azure

Uma conta de armazenamento do Azure fornece os recursos para dados de blob e fila de armazenamento na nuvem. Ela também é usada pelo SDK de Trabalhos Web para armazenar dados de registro no painel.

Em um aplicativo real, você normalmente cria contas à parte para dados de aplicativo em comparação com dados de registro em log e separa contas para dados de teste em comparação com dados de produção. Neste tutorial você usará apenas uma conta.

1. Abra a janela **Gerenciador de Servidores** no Visual Studio.

2. Clique com o botão direito no nó do **Azure** e depois clique em **Conectar-se ao Microsoft Azure**.

![Connect to Azure](./media/websites-dotnet-webjobs-sdk-get-started/connaz.png)

3. Entre usando suas credenciais do Azure.

5. Clique com o botão direito em**Armazenamento**, localizado abaixo do nó do Azure, em seguida, clique em **Criar conta de armazenamento**.

![Create Storage Account](./media/websites-dotnet-webjobs-sdk-get-started/createstor.png)

3. Na caixa de diálogo **Criar conta de armazenamento**, digite um nome para a conta de armazenamento. 

	O nome deve ser exclusivo (nenhuma outra conta de armazenamento do Azure pode ter o mesmo nome). Se o nome digitado já estiver sendo utilizado, você terá a oportunidade de alterá-lo.

	A URL para acessar sua conta de armazenamento será *{name}*.core.windows.net. 

5. Defina a lista suspensa **Região ou grupo de afinidade** como a região mais próxima de você.

	Essa configuração especifica qual datacenter do Azure hospedará a conta de armazenamento. Para este tutorial, a escolha não fará grande diferença, mas para um site em produção convém que o servidor Web e a conta de armazenamento estejam na mesma região para minimizar os encargos de dados egressos e com latência. O site (que você criará mais tarde) deve estar o mais próximo possível dos navegadores que acessam o site para minimizar a latência.

6. Defina a lista suspensa **Replicação** para **Localmente redundante**. 

	Quando a replicação geográfica está habilitada para uma conta de armazenamento, o conteúdo armazenado é replicado para um datacenter secundário para habilitar o failover para essa localidade no caso de ocorrer um grande desastre no local principal. A replicação geográfica pode incorrer em custos adicionais. Para contas de teste e desenvolvimento, geralmente, você não deseja pagar pela replicação geográfica. Para obter mais informações, consulte [Criar, gerenciar ou excluir uma conta de armazenamento](../storage-create-storage-account/#replication-options).

5. Clique em **Criar**. 

	![New storage account](./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png)	

## <a id="download"></a>Baixar o aplicativo
 
1. Baixar e descompactar a [solução concluída][download].

2. Inicie o Visual Studio.

3. No menu **Arquivo**, escolha **Abrir** > **Projeto/Solução**, navegue até onde você baixou a solução e abra o arquivo de solução.

3. Pressione CTRL+SHIFT+B para criar a solução.

	Por padrão, o Visual Studio restaura automaticamente o conteúdo do pacote NuGet, que não foi incluído no arquivo *.zip*. Se os pacotes não forem restaurados, instale-os manualmente acessando a caixa de diálogo **Gerenciar Pacotes NuGet para Solução** e clicando no botão **Restaurar** na parte superior direita. 

3. No **Gerenciador de Soluções**, verifique se **ContosoAdsWeb** está selecionado como o projeto inicial.

## <a id="configurestorage"></a>Configurar o aplicativo para utilizar a conta de armazenamento

2. Abra o arquivo do aplicativo *Web.config* no projeto ContosoAdsWeb.
 
	O arquivo contém uma cadeia de conexão SQL e uma cadeia de caracteres de conexão de armazenamento do Azure para trabalhar com blobs e filas. 

	A cadeia de conexão SQL aponta para um banco de dados [SQL Server Express LocalDB](http://msdn.microsoft.com/library/hh510202.aspx).
 
	A cadeia de conexão de armazenamento é um exemplo que tem espaços reservados para a chave de acesso e para o nome da conta de armazenamento. Isso será substituído com uma cadeia de conexão que tem o nome e a chave da sua conta de armazenamento.  

	<pre class="prettyprint">&lt;connectionStrings&gt;
	  &lt;add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" /&gt;
	  &lt;add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname]</mark>;AccountKey=<mark>[accesskey]</mark>"/&gt;
	&lt;/connectionStrings&gt;</pre>

	A cadeia de conexão de armazenamento se chama AzureWebJobsStorage porque esse é o nome usado pelo SDK de Trabalhos Web por padrão. O mesmo nome é usado aqui de forma que você só precise definir um valor da cadeia de conexão no ambiente do Azure.
 
2. No **Gerenciador de Servidores**, clique com o botão direito do mouse na sua conta de armazenamento, localizada abaixo do nó **Armazenamento**, em seguida, clique em **Propriedades**.

	![Click Storage Account Properties](./media/websites-dotnet-webjobs-sdk-get-started/storppty.png)	

4. Na janela **Propriedades**, clique em **Chaves da conta de armazenamento**e depois clique nas reticências.

	![New storage account](./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png)	

7. Copie a **Cadeia de conexão**.

	![Storage Account Keys dialog](./media/websites-dotnet-webjobs-sdk-get-started/cpak.png)	

8. Substitua a cadeia de conexão de armazenamento no arquivo  *Web.config* com a cadeia de conexão que você acabou de copiar. Certifique-se de selecionar tudo dentro das aspas, mas não inclua as aspas antes de colar.

4. Abra o arquivo *App.config* no projeto ContosoAdsWebJob.

	Esse arquivo tem duas cadeias de caracteres de conexão de armazenamento, uma para dados do aplicativo e outra para registro em log. Para este tutorial você vai usar a mesma conta em ambas. As cadeias de conexão têm espaços reservados para as chaves da conta de armazenamento.
  	<pre class="prettyprint">&lt;configuration&gt;
    &lt;connectionStrings&gt;
        &lt;add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname]</mark>;AccountKey=<mark>[accesskey]</mark>"/&gt;
        &lt;add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=<mark>[accountname]</mark>;AccountKey=<mark>[accesskey]</mark>"/&gt;
        &lt;add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;"/&gt;
    &lt;/connectionStrings&gt;
        &lt;startup&gt; 
            &lt;supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" /&gt;
    &lt;/startup&gt;
&lt;/configuration&gt;</pre>

	Por padrão, o SDK de Trabalhos Web procura cadeias de conexão chamadas AzureWebJobsStorage e AzureWebJobsDashboard. Como alternativa, é possível [armazenar a cadeia de conexão, por mais que você queira passá-la explicitamente para o `JobHost` objeto](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#config).

1. Substitua ambas as cadeias de conexão de armazenamento com a cadeia de conexão que você copiou anteriormente.

5. Salve suas alterações.

## <a id="run"></a>Executar o aplicativo localmente

1. Para iniciar o front-end Web do aplicativo, pressione CTRL+F5. 

	O navegador padrão é aberto na home page. (O projeto Web é executado porque você o tornou o projeto inicial.)

	![Contoso Ads home page](./media/websites-dotnet-webjobs-sdk-get-started/home.png)

2. Para iniciar o back-end do Trabalho Web do aplicativo, clique com o botão direito do mouse no projeto ContosoAdsWebJob no **Gerenciador de Soluções** e clique em **Depurar** > **Iniciar nova instância**.

	Uma janela de aplicativo de console é aberta e exibe mensagens de log indicando que o objeto JobHost do SDK de trabalhos Web começou a ser executado.

	![Console application window showing that the backend is running](./media/websites-dotnet-webjobs-sdk-get-started/backendrunning.png)

2. No navegador, clique em **Criar um anúncio**.

2. Insira alguns dados de teste e selecione uma imagem para carregar e depois clique em **Criar**.

	![Create page](./media/websites-dotnet-webjobs-sdk-get-started/create.png)

	O aplicativo vai para a Página de índice, mas não mostra uma miniatura do anúncio novo porque o processamento ainda não aconteceu.

	Enquanto isso, depois de esperar um pouco, uma mensagem de registro em log na janela do aplicativo de console mostra que uma mensagem da fila foi recebida e processada.   

	![Console application window showing that a queue message has been processed](./media/websites-dotnet-webjobs-sdk-get-started/backendlogs.png)

3. Depois de ver as mensagens de registro em log na janela do aplicativo de console, atualize a página Índice para ver a miniatura.

	![Index page](./media/websites-dotnet-webjobs-sdk-get-started/list.png)

4. Clique em **Detalhes** do anúncio para ver a imagem em tamanho total.

	![Details page](./media/websites-dotnet-webjobs-sdk-get-started/details.png)

Você esteve executando o aplicativo no computador local, e ele estava usando um banco de dados do SQL Server localizado no computador, mas funcionando com filas e blobs na nuvem. Na seção a seguir, você vai executar o aplicativo na nuvem usando um banco de dados de nuvem, bem como blobs e filas da nuvem.  

## <a id="runincloud"></a>Executar o aplicativo na nuvem

Você seguirá as etapas abaixo para executar o aplicativo na nuvem:

* Implantar em um Site do Azure. O Visual Studio criará automaticamente um novo Site do Azure e uma instância do Banco de Dados SQL.
* Configure o site para usar o banco de dados SQL do Azure e a conta de armazenamento.

Depois de criar alguns anúncios ainda em execução na nuvem, você exibirá o painel do SDK de Trabalhos Web para ver os recursos de monitoramento sofisticados que ele tem a oferecer.

### Implantar em um Site do Azure

1. Feche o navegador e a janela do aplicativo de console.

3. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ContosoAdsWeb e clique em **Publicar**.

3. Na etapa **Perfil** do assistente **Publicar Web**, clique em **Sites do Microsoft Azure**.

	![Select Azure Website publish target](./media/websites-dotnet-webjobs-sdk-get-started/pubweb.png)	

2. Na caixa **Selecionar Site Existente**, clique em **Entrar**.
 
	![Click Sign In](./media/websites-dotnet-webjobs-sdk-get-started/signin.png)	

5. Depois de entrar, clique em Novo.

	![Click New](./media/websites-dotnet-webjobs-sdk-get-started/clicknew.png)

9. Na caixa de diálogo **Criar site no Microsoft Azure**, insira um nome exclusivo na caixa **Nome do site**.

	A URL completa consistirá no que você digitar aqui mais o domínio .azurewebsites.net (conforme exibido ao lado da caixa de texto **Nome do Site**). Por exemplo, se o nome do site for ContosoAds, a URL será ContosoAds.azurewebsites.net.

9. Na lista suspensa **Região**, escolha a mesma região selecionada para a conta de armazenamento.

	Essa configuração especifica em qual datacenter do Azure o site será executado. A manutenção do site e da conta de armazenamento no mesmo datacenter minimiza os encargos de dados egressos e com latência.

9. Na lista suspensa **Servidor de banco de dados**, escolha **Criar novo servidor**.

	Se a assinatura já tiver um servidor, você poderá selecioná-lo da lista suspensa.

1. Insira um **Nome de usuário do banco de dados** e uma **Senha do banco de dados**. 

	Se selecionou **Novo servidor do Banco de Dados SQL**, você não irá digitar um nome e uma senha existentes aqui; digite um novo nome e uma senha que você está definindo agora para usar mais tarde ao acessar o banco de dados. Se você selecionou um servidor criado anteriormente, você será solicitado a fornecer a senha da conta do usuário administrador já criada.

1. Clique em **Criar**.

	![Create site on Microsoft Azure dialog](./media/websites-dotnet-webjobs-sdk-get-started/newdb.png)	

	O Visual Studio cria a solução, o projeto Web, o site do Azure e a instância do banco de dados SQL do Azure.

2. Na etapa **Conexão** do assistente **Publicar Web**, clique em **Avançar**.

	![Connection step](./media/websites-dotnet-webjobs-sdk-get-started/connstep.png)	

3. Na etapa **Configurações**, desmarque a caixa de seleção **Usar esta cadeia de conexão no tempo de execução** e clique em **Avançar**.

	![Settings step](./media/websites-dotnet-webjobs-sdk-get-started/settingsstep.png)	
	
	Não é necessário usar a caixa de diálogo para definir a cadeia de conexão SQL pois você definirá esse valor mais tarde no ambiente do Azure.

	É possível ignorar os avisos desta página. 

	* Normalmente, a conta de armazenamento usada na execução no Azure seria diferente daquela usada na execução local, mas para este tutorial você está usando a mesma em ambos os ambientes. Assim, a cadeia de conexão AzureWebJobsStorage não precisa ser transformada. Mesmo que quisesse usar uma conta de armazenamento diferente na nuvem, você não precisaria transformar a cadeia de conexão porque o aplicativo usará uma configuração de ambiente do Azure ao ser executado no Azure. Você verá isso posteriormente no tutorial.

	* Para este tutorial, como você não fará alterações no modelo de dados usado para o banco de dados ContosoAdsContext, não há necessidade de usar Entity Framework Code First Migrations na implantação. O Code First criará automaticamente um novo banco de dados na primeira vez em que o aplicativo tentar acessar os dados SQL.

	Para este tutorial bastam os valores padrão das opções em **Opções de Publicação do Arquivo**. 

4. Na etapa **Visualização**, clique em **Iniciar Visualização**.

	![Click Start Preview](./media/websites-dotnet-webjobs-sdk-get-started/previewstep.png)	

	É possível ignorar o aviso sobre a não publicação de nenhum banco de dados. O Entity Framework Code First criará o banco de dados. Ele não precisa ser publicado.

	a janela de visualização mostra que os binários e os arquivos de configuração do projeto WebJob serão copiados para a pasta  *app_data\jobs\continuous* do site.

	![WebJobs files in preview window](./media/websites-dotnet-webjobs-sdk-get-started/previewwjfiles.png)	

5. Clique em **Publicar**.

	O Visual Studio implanta o aplicativo e abre a URL da home page no navegador. 

	Você não vai conseguir usar o site até definir as cadeias de conexão no ambiente do Azure, na próxima seção. Será exibida uma página de erro ou a home page, dependendo das opções de criação do site e do banco de dados escolhidas anteriormente. 

### Configure o site para usar o banco de dados SQL do Azure e a conta de armazenamento.

É uma prática recomendada de segurança para [evitar colocar informações confidenciais, como cadeias de conexão em arquivos armazenados em repositórios de código fonte](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets). O Azure fornece uma maneira de fazer isso: é possível definir a cadeia de conexão e outros valores de configuração no ambiente do Azure, as APIs de configuração do ASP.NET separam automaticamente esses valores quando o aplicativo é executado no Azure. Nesta seção, você definirá os valores da cadeia de conexão no Azure.

7. No **Gerenciador de Servidores**, clique com o botão direito do mouse no seu site, localizado abaixo do nó **Sites**, em seguida, clique em **Exibir Configurações**.

	A janela **Site do Azure** é aberta na guia **Configuração**.

9. Altere o nome da cadeia de conexão DefaultConnection para ContosoAdsContext.

	O Azure criou essa cadeia de conexão automaticamente quando você criou o site com um banco de dados associado. Assim, ele já tem o valor da cadeia de conexão certo. Você está apenas alterando o nome para aquilo que o código está procurando.

9. Adicione duas novas cadeias de conexão, chamadas AzureWebJobsStorage e AzureWebJobsDashboard. Defina o tipo personalizado e defina o valor de cadeia de caracteres de conexão com o mesmo valor que você usou anteriormente para os arquivos *Web.config* e *App.config*. (Não se esqueça de incluir toda a cadeia de conexão, não apenas a chave de acesso, e as aspas.)

	Essas cadeias de conexão são usadas pelo SDK de Trabalhos Web, uma para dados do aplicativo e outra para registro em log. Como visto anteriormente, a dos dados do aplicativo também é usada pelo código front-end Web.
	
9. Clique em **Salvar**.

	![Connection strings in management portal](./media/websites-dotnet-webjobs-sdk-get-started/azconnstr.png)

10. No **Gerenciador de Servidores**, clique com o botão direito do mouse no site e, em seguida, clique em **Parar Site**. 

12. Após a interrupção do site, clique novamente com o botão direito no site e, em seguida, clique em **Iniciar Site**.

	O Trabalho Web é iniciado automaticamente na publicação, mas para quando você faz uma alteração na configuração. Para reiniciá-lo, você pode reiniciar o site ou reiniciar o trabalho Web no portal de gerenciamento do Azure. Normalmente é recomendável reiniciar o site depois de uma alteração na configuração. 

9. Atualize a janela do navegador com a URL do site na barra de endereços.

	A home page é exibida.

10. Crie um anúncio, como você fez ao executar o aplicativo localmente.

	A página Índice é exibida, inicialmente, sem uma miniatura.

11.	A miniatura será exibida alguns segundos após a atualização da página.

	Se a miniatura aparecer, o Trabalho Web pode não ter sido iniciado automaticamente. Nesse caso, vá até a guia Trabalhos Web 
 

### Veja o painel SDK de Trabalhos Web

1. No [Portal de gerenciamento do Azure](http://manage.windowsazure.com/), selecione o site.

2. Clique na guia **Trabalhos Web**.

3. Clique na URL da coluna Logs do Trabalho Web.

	![WebJobs tab](./media/websites-dotnet-webjobs-sdk-get-started/wjtab.png)

	Uma nova guia do navegador é aberta no painel do SDK de Trabalhos Web. O painel mostra que o Trabalho Web está em execução e exibe uma lista de funções no seu código, disparadas pelo SDK de Trabalhos Web.

4. Clique em uma das funções para ver detalhes sobre a execução 
 
	![WebJobs SDK dashboard](./media/websites-dotnet-webjobs-sdk-get-started/wjdashboardhome.png)	

	![WebJobs SDK dashboard](./media/websites-dotnet-webjobs-sdk-get-started/wjfunctiondetails.png)	

	O botão **Função de Repetição**, nesta página, faz a estrutura do SDK de Trabalhos Web chamar a função novamente e dá uma chance de primeiro alterar os dados passados para a função.

>[AZURE.NOTE] Quando terminar os testes, exclua o site e a instância do Banco de Dados SQL. O site é gratuito, mas a instância do Banco de Dados SQL e a conta de armazenamento acumulam encargos (mínimos, devido ao tamanho reduzido). Além disso, se deixar o site em execução, qualquer pessoa que encontrar a URL poderá criar e exibir anúncios. No portal de gerenciamento do Azure, vá até a guia **Painel** do site e clique no botão **Excluir** na parte inferior da página. Em seguida, é possível marcar uma caixa de seleção para excluir a instância do Banco de Dados SQL ao mesmo tempo. Se apenas deseja evitar que outros acessem temporariamente o site, em vez disso clique em **Parar**. Nesse caso, os encargos continuarão acumulando para o Banco de Dados SQL e a conta de armazenamento. Você pode seguir um procedimento semelhante para excluir o banco de dados SQL e a conta de armazenamento quando não precisar mais dela.

### Habilite o AlwaysOn para processos de longa duração

Para este aplicativo de exemplo, a atividade do site sempre precede a criação de uma mensagem de fila, portanto, não há nenhum problema se o site entrar em suspensão e finalizar o trabalho Web devido a um longo período de inatividade. Quando uma solicitação chega, o site se ativado e o trabalho Web é reiniciado.

Para trabalhos Web que você desejar manter em execução mesmo quando o site estiver inativo por um longo período de tempo, você pode usar o recurso [AlwaysOn](http://weblogs.asp.net/scottgu/archive/2014/01/16/windows-azure-staging-publishing-support-for-web-sites-monitoring-improvements-hyper-v-recovery-manager-ga-and-pci-compliance.aspx) dos Sites do Azure.

## <a id="create"></a>Criar o aplicativo do zero 

Nesta seção, você executará as seguintes tarefas:

* Criar uma solução do Visual Studio com um projeto Web.
* Adicionar um projeto de biblioteca de classes para a camada de acesso a dados compartilhada entre o front-end e o back-end.
* Adicionar um projeto Aplicativo do Console para um back-end com a implantação de Trabalhos Web habilitada.
* Adicionar pacotes NuGet.
* Definir referências de projeto.
* Copiar o código do aplicativo e os arquivos de configuração do aplicativo baixado, com o qual você trabalhou na seção anterior do tutorial.
* Examinar as partes do código que funcionam com blobs e filas do Azure e o SDK de Trabalhos Web.
 
### Criar uma solução do Visual Studio com um projeto Web e um projeto de biblioteca de classes

1. No Visual Studio, escolha **Novo** > **Projeto** no menu **Arquivo**.

2. Na caixa de diálogo **Novo Projeto**, escolha **Visual C#** > **Web** > **Aplicativo Web ASP.NET**.

3. Nomeie o projeto como ContosoAdsWeb, nomeie a solução como ContosoAdsWebJobsSDK (altere o nome da solução caso esteja colocando-a na mesma pasta da solução baixada) e clique em **OK**.

	![New Project](./media/websites-dotnet-webjobs-sdk-get-started/newproject.png)	

5. Na caixa de diálogo **Novo Projeto do ASP.NET**, escolha o modelo MVC e desmarque a caixa de seleção **Hospedar na nuvem** em **Microsoft Azure**.

	A seleção de **Hospedar na nuvem** permite que o Visual Studio crie automaticamente um novo Site do Azure e um novo Banco de Dados SQL. Como já os criou anteriormente, você não precisa fazer isso agora, enquanto cria o projeto. Se quiser criar um novo, marque a caixa de seleção. Em seguida, é possível configurar o novo site e o banco de dados SQL da mesma forma como fez anteriormente ao implantar o aplicativo.

5. Clique em **Alterar Autenticação**.

	![Change Authentication](./media/websites-dotnet-webjobs-sdk-get-started/chgauth.png)	

7. Na caixa de diálogo **Alterar Autenticação**, escolha **Sem Autenticação** e clique em **OK**.

	![No Authentication](./media/websites-dotnet-webjobs-sdk-get-started/noauth.png)	

8. Na caixa de diálogo **Novo projeto ASP.NET** clique em **OK**. 

	O Visual Studio cria a solução e o projeto Web.

9. No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução (não no projeto) e escolha **Adicionar** > **Novo Projeto**.

11. Na caixa de diálogo **Adicionar Novo Projeto**, escolha o modelo **Visual C#** > **Área de Trabalho do Windows** > **Biblioteca de Classes**.  

10. Nomeie o projeto *ContosoAdsCommon*e, em seguida, clique em **OK**.

	Esse projeto conterá o contexto do Entity Framework e o modelo de dados que o front-end e o back-end utilizarão. Como alternativa, você pode definir as classes relacionadas ao EF no projeto Web e referenciar esse projeto pelo projeto Trabalho Web. Assim porém, o projeto WebJob teria uma referência desnecessária a assemblies da Web.

### Adicionar um projeto de Aplicativo do Console com a implantação de Trabalhos Web habilitada

11. Clique com o botão direito do mouse no projeto Web (e não na solução ou no projeto da biblioteca de classes) e em **Adicionar** > **Novo Projeto de Trabalho Web do Azure**.

	![New Azure WebJob Project menu selection](./media/websites-dotnet-webjobs-sdk-get-started/newawjp.png)	

1. Na caixa de diálogo **Adicionar Trabalho Web do Azure**, insira ContosoAdsWebJob tanto como **Nome do projeto** quanto como **Nome do Trabalho Web**. Deixe o **Modo de execução do Trabalho Web** definido como **Executar Continuamente**.

2.  Clique em **OK**.
  
	O Visual Studio cria um aplicativo de console configurado para ser implantado como um Trabalho Web sempre que você implantar o projeto Web. Para isso, ele realizou as seguintes tarefas após a criação do projeto:

	* Adicionou um arquivo *webjob-publish-settings.json* na pasta de propriedades do projeto WebJob.
	* Adicionou um arquivo *webjobs-list.json* na pasta de propriedades do projeto da web.
	* Instalou o pacote Microsoft.Web.WebJobs.Publish NuGet no projeto Trabalho Web.
	 
	Para obter mais informações sobre essas alterações, consulte [Como implantar Trabalhos Web usando o Visual Studio](/pt-br/documentation/articles/websites-dotnet-deploy-webjobs/).

### Adicionar pacotes NuGet

O modelo de novo projeto para um projeto de Trabalho Web instala automaticamente o pacote NuGet do SDK de Trabalhos Web [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) e suas dependências. 

Uma das dependências do SDK de Trabalhos Web que é instalada automaticamente no projeto do Trabalho Web é o Azure Storage Client Library (SCL). No entanto, é necessário adicioná-lo ao projeto da web para trabalhar com blobs e filas.

11. Abra a caixa de diálogo **Gerenciar pacotes NuGet** para a solução.

12. No painel à esquerda, selecione **Pacotes instalados**.
   
13. Encontre o pacote *Azure Storage*, em seguida, clique em **Gerenciar**.

13. Na caixa **Projetos Selecionados**, marque a caixa de seleção **ContosoAdsWeb** e clique em **OK**. 

Todos os três projetos usam o Entity Framework para trabalhar com dados no Banco de Dados SQL.

12. No painel esquerdo, selecione **Online**.
   
16. Encontre o pacote do NuGet *EntityFramework* e instale-o em todos os três projetos.


### Definir referências de projeto

Ambos os projetos Web e Trabalho Web funcionarão com o banco de dados SQL, logo ambos precisam de uma referência para o projeto ContosoAdsCommon.

10. No projeto ContosoAdsWeb, defina uma referência para o projeto ContosoAdsCommon. (Clique com o botão direito do mouse no projeto ContosoAdsWeb e clique em **Adicionar** > **Referência**. Na caixa de diálogo **Gerenciador de Referências**, selecione **Solução** > **Projetos** > **ContosoAdsCommon** e clique em **OK**.)

11. No projeto ContosoAdsWebJob, defina uma referência para o projeto ContosAdsCommon.

O projeto Trabalho Web precisa de referências para trabalhar com imagens e acessar cadeias de conexão.

11. No projeto ContosoAdsWebJob, defina uma referência como  `System.Drawing` e  `System.Configuration`.

### Adicionar código e arquivos de configuração

Este tutorial não mostra como [criar controladores e exibições MVC usando scaffolding](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started), como [escrever código do Entity Framework que funciona com bancos de dados do SQL Server](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc) ou [as noções básicas de programação assíncrona no ASP.NET 4.5](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async). Assim, tudo o que resta a fazer é copiar o código e os arquivos de configuração da solução baixada para a nova solução. Depois que fizer isso, as seções a seguir irão mostrar e explicar as partes-chave do código.

Para adicionar arquivos a um projeto ou a uma pasta, clique com o botão direito do mouse no projeto ou na pasta e clique em **Adicionar** > **Item Existente**. Selecione os arquivos que deseja e clique em **Adicionar**. Se receber uma solicitação para confirmar se deseja substituir os arquivos existentes, clique em **Sim**.

3. No projeto ContosoAdsCommon, exclua o arquivo *Class1.cs* e adicione em seu lugar os arquivos a seguir do projeto baixado.

	- *Ad.cs*
	- *ContosoAdscontext.cs*
	- *BlobInformation.cs*<br/><br/>

3. No projeto ContosoAdsWeb, adicione os seguintes arquivos do projeto baixado.

	- *Web.config*
	- *Global.asax.cs*  
	- In the *Controllers* folder: *AdController.cs* 
	- In the *Views\Shared* folder: <em>_Layout.cshtml</em> file. 
	- In the *Views\Home* folder: *Index.cshtml*. 
	- In the *Views\Ad* folder (create the folder first): five *.cshtml* files.<br/><br/>

3. No projeto ContosoAdsWebJob, adicione os seguintes arquivos do projeto baixado.

	- *App.config* (change the file type filter to **All Files**)
	- *Program.cs*
	- *Functions.cs*

Agora é possível compilar, executar e implantar o aplicativo conforme instruído anteriormente no tutorial. Porém, antes de fazer isso, pare o Trabalho Web em execução no primeiro site em que o implantou. Do contrário, esse Trabalho Web processará as mensagens da fila criadas localmente ou pelo aplicativo em execução no novo site, porque todas estão usando a mesma conta de armazenamento.

## <a id="code"></a>Examinar o código do aplicativo

As seções a seguir explicam o código relacionado ao trabalho com os blobs e as filas do SDK de Trabalhos Web e do Armazenamento do Azure. Para o código específico do SDK de Trabalhos Web, consulte a [seção Program.cs](#programcs).

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
 
A classe possui dois construtores. O primeiro deles é usado pelo projeto Web e especifica o nome de uma cadeia de conexão armazenada no arquivo Web.config ou no ambiente de tempo de execução do Azure. O segundo construtor habilita você a transmitir a cadeia de conexão real. Isso é exigido pelo projeto Trabalho Web, já que não possui um arquivo Web.config. Foi mostrado anteriormente onde essa cadeia de conexão foi armazenada, e você verá mais adiante como o código recupera a cadeia de conexão quando ele cria uma instância de classe DbContext.

### ContosoAdsCommon - BlobInformation.cs

A classe  `BlobInformation` é usada para armazenar informações sobre um blob de imagem em uma mensagem da fila.

		public class BlobInformation
		{
		    public Uri BlobUri { get; set; }
		    
		    public string BlobName
		    {
		        get
		        {
		            return BlobUri.Segments[BlobUri.Segments.Length - 1];
		        }
		    }
		    public string BlobNameWithoutExtension
		    {
		        get
		        {
		            return Path.GetFileNameWithoutExtension(BlobName);
		        }
		    }
		    public int AdId { get; set; }
		}


### ContosoAdsWeb - Global.asax.cs

Código que é chamado a partir do método `Application_Start` cria um *images* contêiner de blob e ua fila *images* se eles ainda não existirem. Isso garante que, sempre que você começar a usar uma nova conta de armazenamento, o contêiner do blob e a fila obrigatórios serão criados automaticamente.

O código obtém acesso à conta de armazenamento usando a cadeia de conexão de armazenamento do arquivo *Web.config* ou do ambiente de execução do Azure.

		var storageAccount = CloudStorageAccount.Parse
		    (ConfigurationManager.ConnectionStrings["AzureWebJobsStorage"].ToString());

Em seguida, ele obtém uma referência para o contêiner do blob  *images*, cria o contêiner se ele ainda não existe e define permissões de acesso no novo contêiner. Por padrão, os novos contêineres somente permite que os clientes com credenciais da conta de armazenamento acessem os blobs. O site precisa que os blobs para o público para poder exibir imagens usando URLs que apontam para os blobs de imagem.

		var blobClient = storageAccount.CreateCloudBlobClient();
		var imagesBlobContainer = blobClient.GetContainerReference("images");
		if (imagesBlobContainer.CreateIfNotExists())
		{
		    imagesBlobContainer.SetPermissions(
		        new BlobContainerPermissions
		        {
		            PublicAccess = BlobContainerPublicAccessType.Blob
		        });
		}

Um código semelhante obtém uma referência para a fila  *blobnamerequest* e cria uma nova fila. Nesse caso, nenhuma alteração de permissão é necessária. A seção [ResolveBlobName](#resolveblobname) , mais adiante no tutorial, explica por que a fila na qual o aplicativo Web escreve é usada apenas para obter nomes de blob e não para gerar miniaturas.

		CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
		var imagesQueue = queueClient.GetQueueReference("blobnamerequest");
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
		blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
		imagesBlobContainer = blobClient.GetContainerReference("images");

Um código semelhante obtém uma referência para a fila *images*.

		CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
		queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
		imagesQueue = queueClient.GetQueueReference("blobnamerequest");

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

Depois de carregar um blob e atualizar o banco de dados, o método HttpPost  `Create` criará uma mensagem da fila para informar o processo back-end que uma imagem está pronta para conversão em miniatura.

		BlobInformation blobInfo = new BlobInformation() { AdId = ad.AdId, BlobUri = new Uri(ad.ImageURL) };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		await thumbnailRequestQueue.AddMessageAsync(queueMessage);

O código do método HttpPost  `Edit` é parecido, exceto pelo fato de que se o usuário selecionar um novo arquivo de imagem, qualquer blob já existente para esse anúncio deverá ser excluído.
 
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
 
### ContosoAdsWeb - Views\Ad\Index.cshtml and Details.cshtml

O arquivo *Index.cshtml* exibe miniaturas com os outros dados do anúncio:

		<img  src="@Html.Raw(item.ThumbnailURL)" />

O arquivo *Details.cshtml* exibe a imagem em tamanho normal:

		<img src="@Html.Raw(Model.ImageURL)" />

### ContosoAdsWeb - Views\Ad\Create.cshtml and Edit.cshtml

Os arquivos *Create.cshtml* e *Edit.cshtml* especificam a codificação do formulário que permitem que o controlador obtenha o objeto `HttpPostedFileBase`.

		@using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))

Um elemento `<input>` informa o navegador para fornecer uma caixa de diálogo de seleção de arquivo.

		<input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />

### <a id="programcs"></a>ContosoAdsWebJob - Program.cs

Quando o Trabalho Web começa, o método  `Main` chama  `Initialize` para criar uma instância do contexto do banco de dados do Entity Framework. Em seguida, ele chama o método  `JobHost.RunAndBlock` do SDK de Trabalhos Web para começar a execução do thread atual.

		static void Main(string[] args)
		{
		    Initialize();
		
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
		private static void Initialize()
		{
		    db = new ContosoAdsContext();
		}

### <a id="generatethumbnail"></a>ContosoAdsWebJob - Functions.cs - GenerateThumbnail method

O SDK de Trabalhos Web chama esse método quando uma mensagem de fila é recebida. O método cria uma miniatura e coloca a URL da miniatura no banco de dados.

		public static void GenerateThumbnail(
		[QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,
		[Blob("images/{BlobName}", FileAccess.Read)] Stream input,
		[Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg"), FileAccess.Write] CloudBlockBlob outputBlob)
		{
		    using (Stream output = outputBlob.OpenWrite())
		    {
		        ConvertImageToThumbnailJPG(input, output);
		        outputBlob.Properties.ContentType = "image/jpeg";
		    }
		
		    var id = blobInfo.AdId;
		    Ad ad = Program.db.Ads.Find(id);
		    if (ad == null)
		    {
		        throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", id.ToString()));
		    }
		    ad.ThumbnailURL = outputBlob.Uri.ToString();
		    Program.db.SaveChanges();
		}

* O atributo  `QueueTrigger` direciona o SDK de Trabalhos Web para chamar esse método quando uma nova mensagem é recebida na fila thumbnailrequest.

		[QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,

	O objeto  `BlobInformation` na mensagem da fila é desserializado automaticamente no parâmetro  `blobInfo`. Quando o método é concluído, a mensagem da fila é excluída. Se o método falhar antes da conclusão, a mensagem da fila não será excluída. Depois de uma concessão de 10 minutos, a mensagem será liberada para ser novamente retirada e processada. Essa sequência não será repetida indefinidamente se uma mensagem sempre causar uma exceção. Depois de 5 tentativas malsucedidas de processar uma mensagem, a mensagem será movida para uma fila chamada {queuename}-poison. O número máximo de tentativas é configurável. 

* Os dois atributos `Blob` fornecem objetos vinculados a blobs: um para o blob de imagem existente e outro para um novo blob de miniatura que o método cria. 

		[Blob("images/{BlobName}", FileAccess.Read)] Stream input,
		[Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)

	Os nomes dos blobs vêm de propriedades do objeto  `BlobInformation` recebidas na mensagem da fila (`BlobName` e  `BlobNameWithoutExtension`). Para obter toda a funcionalidade da SCL, é possível usar a classe  `CloudBlockBlob` para trabalhar com blobs. Se quiser reutilizar código escrito para funcionar com objetos  `Stream`, você pode usar a classe  `Stream`. 

Para obter mais informações sobre como escrever funções que usam atributos SDK do WebJobs, consulte os seguintes recursos:

* [Como usar o armazenamento de fila do Azure com o SDK do WebJobs](../websites-dotnet-webjobs-sdk-storage-queues-how-to)
* [Como usar o armazenamento de blobs do Azure com o SDK do WebJobs](../websites-dotnet-webjobs-sdk-storage-blobs-how-to)
* [Como usar o armazenamento de tabela do Azure com o SDK do WebJobs](../websites-dotnet-webjobs-sdk-storage-tables-how-to)
* [Como usar o barramento de serviço do Azure com o SDK do WebJobs](../websites-dotnet-webjobs-sdk-service-bus)

>[AZURE.NOTE] 
>* Se o site for executado em várias VMs, esse programa será executado em todos os computadores e cada computador aguardará os gatilhos e tentará executar as funções. Em alguns cenários, isso pode fazer com que algumas funções processem os mesmos dados duas vezes. Assim, as funções devem ser idempotentes (escritas de forma que chamá-las repetidamente com os mesmos dados de entrada não produza resultados duplicados).
>* Para obter informações sobre como implementar o desligamento normal, consulte [Desligamento normal](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#graceful).   
>* O código no método  `ConvertImageToThumbnailJPG` (não mostrado) usa classes no namespace  `System.Drawing` por uma questão de simplicidade. Entretanto, as classes nesse namespace foram projetadas para uso nos formulários do Windows. Elas não têm suporte para uso em um serviço Windows ou ASP.NET.

### SDK de Trabalhos Web em comparação com a função de trabalho de Serviço de Nuvem sem o SDK de Trabalhos Web

Ao comparar a quantidade de código no método  `GenerateThumbnails` desse aplicativo de exemplo com o código da função de trabalho na [versão de Serviço de Nuvem do aplicativo](/pt-br/documentation/articles/cloud-services-dotnet-get-started/), você pode ver quanto trabalho o SDK de Trabalhos Web está fazendo por você. A vantagem é maior do que parece, porque o aplicativo de exemplo de Serviço de Nuvem não faz todas as coisas (como a manipulação de mensagens suspeitas) que você faria em um aplicativo em produção, algo que o SDK de Trabalhos Web faz para você.

Na versão de Serviço de Nuvem do aplicativo, a ID de registro é a única informação na mensagem da fila e o processo em segundo plano obtém a URL da imagem do banco de dados. Na versão do SDK de Trabalhos Web do aplicativo, a mensagem da fila inclui a URL da imagem de forma que ela possa ser fornecida aos atributos  `Blob`. Se a mensagem da fila não tivesse a URL de blob, você poderia [usar o atributo de Blob no corpo do método em vez da assinatura do método](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#blobbody).

### Usando o SDK de Trabalhos Web fora de Trabalhos Web

Um programa que usa o SDK de Trabalhos Web não precisa ser executado no Azure em um Trabalho Web. Ele pode ser executado localmente, e também pode ser executado em outros ambientes, como numa função de trabalho do Serviço de Nuvem ou num serviço do Windows. No entanto, só é possível acessar o painel do SDK de Trabalhos Web por meio de um Site do Azure. Para usar o painel, é preciso conectar o site à conta de armazenamento que você está usando ao definir a cadeia de conexão AzureWebJobsDashboard na guia **Configurar** do portal de gerenciamento. Em seguida, você pode chegar ao Painel usando a URL https://{websitename}.scm.azurewebsites.net/azurejobs/#/functions. Para obter mais informações, consulte [Obtendo um painel para desenvolvimento local com o SDK de Trabalhos Web](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), mas observe que ele mostra um nome da cadeia de conexão antigo. 

## Próximas etapas

Neste tutorial, você viu um aplicativo multicamadas simples que utiliza o SDK de Trabalhos Web no processamento back-end. O aplicativo foi mantido simples para um tutorial de introdução. Por exemplo, ele não implementa [injeção de dependência](http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection) ou os [padrões de unidade de trabalho e repositório](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), ele não [usa uma interface para registro em log](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log), não usa [Migrações Iniciais de Código de EF](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) para gerenciar as alterações de modelo de dados ou [Resiliência de Conexão de EF](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) para gerenciar erros de rede transitórios, e assim por diante.

Para obter mais informações, consulte [Recursos recomendados para Trabalhos Web do Azure](http://go.microsoft.com/fwlink/?LinkId=390226).


<!--HONumber=42-->
