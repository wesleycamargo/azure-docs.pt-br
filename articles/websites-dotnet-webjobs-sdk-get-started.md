<properties pageTitle="Get Started with the Azure WebJobs SDK" metaKeywords="Azure tutorial, Azure WebJobs tutorial, Azure multi-tier tutorial, MVC tutorial, Azure blobs tutorial, Azure queues tutorial, Azure storage tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The frontend runs in a website, and the backend runs as a WebJob. The app uses Entity Framework, SQL Database, and Azure storage queues and blobs." metaCanonical="" services="web-sites,storage" documentationCenter=".NET" title="Get Started with the Azure WebJobs SDK" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

# Introdução ao SDK de Trabalhos Web do Azure

Este tutorial apresenta o SDK de Trabalhos Web e mostra como criar um aplicativo multicamadas ASP.NET MVC que usa o SDK de Trabalhos Web em um [site do Azure][site do Azure]. O aplicativo usa o [Banco de Dados SQL do Azure][Banco de Dados SQL do Azure], o [serviço Blob do Azure][serviço Blob do Azure] e o [serviço Fila do Azure][serviço Fila do Azure].

O aplicativo de exemplo é um boletim informativo de anúncio. Os usuários criam um anúncio inserindo texto e carregando uma imagem. Eles podem ver uma lista de anúncios com imagens em miniatura e podem ver a imagem em tamanho total ao selecionar um anúncio para ver os detalhes. Esta é uma captura de tela:

![Lista de anúncios][Lista de anúncios]

Você pode [baixar o projeto do Visual Studio][baixar o projeto do Visual Studio] na Galeria de Códigos do MSDN.

## Sumário

O tutorial inclui as seguintes seções:

-   [Pré-requisitos][Pré-requisitos]
-   [O que você vai aprender][O que você vai aprender]
-   [O SDK de Trabalhos Web][O SDK de Trabalhos Web]
-   [Arquitetura do aplicativo][Arquitetura do aplicativo]
-   [Configurar o ambiente de desenvolvimento][Configurar o ambiente de desenvolvimento]
-   [Compilar, executar e implantar o aplicativo][Compilar, executar e implantar o aplicativo]
-   [Criar o aplicativo a partir do zero][Criar o aplicativo a partir do zero]
-   [Examinar o código do aplicativo][Examinar o código do aplicativo]
-   [Solucionar problemas][Solucionar problemas]
-   [Próximas etapas][Próximas etapas]

## <span id="prerequisites"></span></a>Pré-requisitos

O tutorial pressupõe que você saiba como trabalhar com projetos [ASP.NET MVC][ASP.NET MVC] ou [Web Forms][Web Forms] no Visual Studio. O aplicativo função Web usa MVC, mas a maior parte do tutorial também aplica-se a Web Forms.

As instruções do tutorial funcionam com qualquer um dos seguintes produtos:

-   Visual Studio 2013
-   Visual Studio 2013 Express para Web

Se não tiver nenhum desses produtos, o Visual Studio 2013 Express para Web será instalado automaticamente quando você instalar o SDK do Azure.

[WACOM.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

## <span id="learn"></span></a>O que você vai aprender

O tutorial mostra como executar as seguintes tarefas:

-   Habilitar o computador para desenvolvimento do Azure instalando o SDK do Azure.
-   Criar um projeto Aplicativo do Console implantado automaticamente como um Trabalho Web do Azure ao implantar o projeto Web associado.
-   Testar um back-end do SDK de Trabalhos Web localmente no computador de desenvolvimento.
-   Publicar um aplicativo com um back-end de Trabalhos Web em um site do Azure.
-   Carregar arquivos e armazená-los no serviço Azure Blob.
-   Usar o SDK de Trabalhos Web do Azure para trabalhar com filas e blobs do Armazenamento do Azure.

## <span id="webjobssdk"></span></a>Introdução ao SDK de Trabalhos Web

[Trabalhos Web][Trabalhos Web] é um recurso dos Sites do Azure que permite executar um programa ou um script no mesmo contexto do site. Você não precisa usar o SDK de Trabalhos Web para executar um programa como um Trabalho Web. A finalidade do SDK de Trabalhos Web é simplificar a tarefa de escrever um código que funcione com filas, blobs e tabelas do Armazenamento do Azure, além de filas do Barramento de Serviço.

O SDK de Trabalhos Web inclui os seguintes componentes:

-   **Pacotes NuGet**. Os pacotes NuGet adicionados a um projeto do Visual Studio fornecem uma estrutura usada pelo código para funcionar com o serviço de Armazenamento do Azure ou as filas do Barramento de Serviço.
-   **Painel**. Parte do SDK de Trabalhos Web está incluído em Sites do Azure e oferece monitoramento e diagnóstico sofisticados para os programas escritos por você usando os pacotes NuGet. Você não precisa escrever o código para usar esses recursos de monitoramento e diagnóstico.

É possível executar um programa que use os pacotes NuGet do SDK de Trabalhos Web em qualquer lugar. Ele não precisa ser executado como um Trabalho Web do Azure. Porém, o painel só permanece disponível como uma extensão de site para um Site do Azure. Por exemplo, é possível executar um programa que use o SDK de Trabalhos Web localmente no computador de desenvolvimento ou em uma função de trabalho Serviço de Nuvem do Azure. Assim, você poderia obter informações de monitoramento configurando o painel do SDK de Trabalhos Web de um Site do Azure para usar a mesma conta de armazenamento usada pelo programa do SDK de Trabalhos Web.

No momento da redação deste tutorial, o SDK de Trabalhos Web é uma versão beta. Ele não é compatível com o uso em produção.

### Cenários típicos

Aqui estão alguns cenários típicos que é possível processar mais facilmente com o SDK de Trabalhos Web do Azure:

-   Processamento de imagens ou outro trabalho com uso intensivo da CPU. Uma característica comum de sites é a capacidade de carregar imagens ou vídeos. Você pode querer manipular o conteúdo após o upload, mas sem fazer o usuário esperar enquanto faz isso.
-   Processamento de filas. Uma maneira comum de comunicação de um front-end da Web com um serviço back-end é usar filas. Quando precisa fazer o trabalho, o site envia uma mensagem para uma fila. Um serviço back-end efetua pull das mensagens da fila e faz o trabalho. Por exemplo, você poderia usar filas no processamento de imagens: depois que o usuário carregar vários arquivos, coloque os nomes dos arquivos em uma mensagem na fila a ser retirada pelo back-end para processamento. Ou você poderia usar filas para melhorar a resposta do site. Por exemplo, em vez de escrever diretamente para um banco de dados SQL, escreva para uma fila, informe ao usuário quando terminar e deixe o serviço back-end processar o trabalho do banco de dados relacional de alta latência.
-   Agregação de RSS. Se tivesse um site que mantivesse uma lista de RSS feeds, você poderia efetuar pull em todos os artigos dos feeds em um processo em segundo plano.
-   Manutenção de arquivos, como agregar ou limpar arquivos de log. Você pode ter arquivos de log criados por diversos sites ou para períodos de tempo separados que deseja combinar para executar trabalhos de análise neles. Ou, talvez, você queira agendar uma tarefa a ser executada semanalmente para limpar arquivos de log antigos.
-   Outras tarefas de execução longa que deseja executar em um thread em segundo plano, como enviar emails.

Para evitar que o site entre em suspensão após um período de inatividade, o que interromperia uma tarefa de execução longa em segundo plano, é possível usar o recurso [AlwaysOn][AlwaysOn] de Sites do Azure.

### Exemplos de código

O código para processar tarefas típicas que funcionam com o Armazenamento do Azure é simples. Em um Aplicativo do Console, você escreve métodos para as tarefas em segundo plano que deseja executar e as decora com atributos do SDK de Trabalhos Web. O método `Main` cria um objeto `JobHost` que coordena as chamadas para métodos escritos por você. A estrutura do SDK de Trabalhos Web sabe quando chamar os métodos com base nos atributos do SDK de Trabalhos Web que você usou neles. Por exemplo:

        static void Main()
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

        public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
        {
            writer.WriteLine(inputText);
        }

O objeto `JobHost` é um contêiner de um conjunto de funções em segundo plano. O objeto `JobHost` monitora as funções, observa eventos que as disparam e executa as funções quando ocorrem os eventos de gatilho. É possível chamar um método `JobHost` para indicar se deseja que o processo de contêiner seja executado no thread atual ou em um thread em segundo plano. No exemplo, o método `RunAndBlock` executa continuamente o processo no thread atual.

Como o método `ProcessQueueMessage` neste exemplo tem um atributo `QueueTrigger`, o gatilho dessa função é a recepção de uma nova mensagem da fila. O objeto `JobHost` observa novas mensagens na fila especificada ("webjobsqueue” neste exemplo) e quando uma é encontrada, ele chama `ProcessQueueMessage`. O atributo `QueueTrigger` também notifica a estrutura para associar o parâmetro `inputText` ao valor da mensagem da fila:

<pre class="prettyprint">
public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] string inputText, 
    [Blob(&quot;containername/blobname&quot;)]TextWriter writer)
</pre>

A estrutura também associa um objeto `TextWriter` a um blob chamado "blobname" em um contêiner chamado "containername":

<pre class="prettyprint">
public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] string inputText, 
    [Blob(&quot;containername/blobname&quot;)]TextWriter writer)
</pre>

Em seguida, a função usa esses parâmetros para gravar o valor da mensagem da fila no blob:

        writer.WriteLine(inputText);

Como é possível ver, os recursos de gatilho e de associador do SDK de Trabalhos Web simplificam muito o código que você precisa escrever com objetos do Armazenamento do Azure. O código de nível inferior obrigatório para processar a fila e o blob é feito por você pela estrutura do SDK de Trabalhos Web. A estrutura que cria filas que ainda não existem, abre a fila, lê as mensagens da fila, exclui mensagens da fila quando o processamento é concluído, cria contêineres de blob que ainda não existem, grava em blobs, etc.

O SDK de Trabalhos Web oferece muitas maneiras de trabalhar com o Armazenamento do Azure. Por exemplo, se o parâmetro decorado por você com o atributo `QueueTrigger` for uma matriz de bytes ou um tipo personalizado, ele será desserializado automaticamente de JSON. É possível usar um atributo `BlobTrigger` para disparar um processo sempre que um novo blob é criado na conta do Armazenamento do Azure. (Embora `QueueTrigger` encontre novas mensagens da fila em questão de segundos, `BlobTrigger` pode demorar até 20 minutos para detectar um novo blob. `BlobTrigger` examina se há blobs sempre que o `JobHost` é iniciado e verifica periodicamente os logs do Armazenamento do Azure para detectar novos blobs.)

## <span id="contosoads"></span></a>Arquitetura do aplicativo

O aplicativo de exemplo usa o [padrão de trabalho centrado em fila][serviço Fila do Azure] para descarregar o trabalho com uso intensivo da CPU de criar miniaturas para um processo back-end.

O aplicativo armazena anúncios em um banco de dados SQL usando Entity Framework Code First para criar as tabelas e acessar os dados. Para cada anúncio o banco de dados armazena duas URLs, uma para a imagem em tamanho total e outra para a miniatura.

![Tabela de anúncios][Tabela de anúncios]

Quando um usuário carrega uma imagem, o site front-end armazena a imagem em um [blob do Azure][serviço Blob do Azure] e armazena as informações do anúncio no banco de dados com uma URL apontando para o blob. Ao mesmo tempo, ele grava uma mensagem em uma fila do Azure. Um processo back-end em execução como um Trabalho Web do Azure usa o SDK de Trabalhos Web para sondar a fila a procura de novas mensagens. Quando uma nova mensagem é exibida, o Trabalho Web cria uma miniatura dessa imagem e atualiza o campo do banco de dados da URL de miniatura desse anúncio. Veja a seguir este diagrama que mostra como as partes de um aplicativo interagem:

![Arquitetura do Contoso Ads][Arquitetura do Contoso Ads]

### Arquitetura alternativa

Trabalhos Web são executados no contexto de um site e não são escalonáveis separadamente. Por exemplo, se tiver uma instância de site Padrão, você só pode ter 1 instância do processo em segundo plano em execução e ela está usando parte dos recursos do servidor (CPU, memória etc.) que, de outra forma, estariam disponíveis para exibir conteúdo da Web.

Se o tráfego variar de acordo com a hora do dia ou o dia da semana e se for possível esperar o processamento back-end necessário, você pode agendar os Trabalhos Web para serem executados nos horários de menos tráfego. Se ainda assim a carga for muito grande para essa solução, você pode levar em consideração ambientes alternativos para o programa back-end, como os seguintes:

-   Executar o programa como um Trabalho Web em um site à parte dedicado a essa finalidade. Assim, é possível dimensionar o site back-end independentemente do site front-end.
-   Executar o programa em uma função de trabalho Serviço de Nuvem do Azure. Se escolher essa opção, você pode executar o front-end em uma função web Serviço de Nuvem ou Site.

Este tutorial mostra como executar o front-end em um site e o back-end como um Trabalho Web no mesmo site. Para obter informações sobre como escolher o melhor ambiente para o cenário, consulte [Comparação entre Sites do Azure, Serviços de Nuvem e Máquinas Virtuais][Comparação entre Sites do Azure, Serviços de Nuvem e Máquinas Virtuais].

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## <span id="storage"></span></a>Criar uma conta de Armazenamento do Azure

Uma conta de armazenamento do Azure fornece os recursos para dados de blob e fila de armazenamento na nuvem. Ela também é usada pelo SDK de Trabalhos Web para armazenar dados de registro no painel.

Em um aplicativo real, você normalmente cria contas à parte para dados de aplicativo em comparação com dados de registro em log e separa contas para dados de teste em comparação com dados de produção. Neste tutorial você usará apenas uma conta.

1.  No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Novo** - **Serviços de Dados** - **Armazenamento** - **Criação Rápida**.

2.  Na caixa da **URL**, insira um prefixo de URL.

    Esse prefixo, além do texto que você vê sob a caixa será a URL exclusiva de sua conta de armazenamento. Se o prefixo inserido já tiver sido usado por outra pessoa, escolha um prefixo diferente.

    Na imagem ao final desta seção, uma conta de armazenamento é criada com a URL `contosoads.core.windows.net`.

3.  Defina a lista suspensa **Região** como a região mais próxima de você.

    Essa configuração especifica qual datacenter do Azure hospedará a conta de armazenamento. Para este tutorial, a escolha não fará grande diferença, mas para um site em produção convém que o servidor Web e a conta de armazenamento estejam na mesma região para minimizar os encargos de dados egressos e com latência. O site (que você criará mais tarde) deve estar o mais próximo possível dos navegadores que acessam o site para minimizar a latência.

4.  Defina a lista suspensa **Replicação** para **Localmente redundante**.

    Quando a replicação geográfica está habilitada para uma conta de armazenamento, o conteúdo armazenado é replicado para um datacenter secundário para habilitar o failover para essa localidade no caso de ocorrer um grande desastre no local principal. A replicação geográfica pode incorrer em custos adicionais. Para contas de teste e desenvolvimento, geralmente, você não deseja pagar pela replicação geográfica. Para obter mais informações, consulte [Como gerenciar contas de armazenamento][Como gerenciar contas de armazenamento].

5.  Clique em **Criar Conta de Armazenamento**.

    ![Nova conta de armazenamento][Nova conta de armazenamento]

## <span id="download"></span></a>Baixar o aplicativo

1.  Baixar e descompactar a [solução concluída][baixar o projeto do Visual Studio].

2.  Inicie o Visual Studio.

3.  No menu **Arquivo**, escolha **Abrir** \> **Projeto/Solução**, navegue até onde você baixou a solução e abra o arquivo de solução.

4.  Pressione CTRL+SHIFT+B para criar a solução.

    Por padrão, o Visual Studio restaura automaticamente o conteúdo do pacote NuGet, que não foi incluído no arquivo *.zip*. Se os pacotes não forem restaurados, instale-os manualmente acessando a caixa de diálogo **Gerenciar Pacotes NuGet para Solução** e clicando no botão **Restaurar** na parte superior direita.

5.  No **Gerenciador de Soluções**, verifique se **ContosoAdsWeb** está selecionado como o projeto inicial.

## <span id="configurestorage"></span></a>Configurar o aplicativo para usar a conta de armazenamento

1.  Abra o arquivo *Web.config* do aplicativo no projeto ContosoAdsWeb.

    O arquivo contém uma cadeia de conexão SQL e uma cadeia de conexão de armazenamento do Azure para trabalhar com blobs e filas.

    A cadeia de conexão SQL aponta para um banco de dados [SQL Server Express LocalDB][SQL Server Express LocalDB].

    A cadeia de conexão de armazenamento tem espaços reservados onde você vai inserir o nome da conta de armazenamento e a chave de acesso nas etapas a seguir.

    ``` prettyprint
    <connectionStrings>
      <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
      <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
    </connectionStrings>
    ```

    A cadeia de conexão de armazenamento se chama AzureWebJobsStorage porque esse é o nome usado pelo SDK de Trabalhos Web por padrão. O mesmo nome é usado aqui de forma que você só precise definir um valor da cadeia de conexão no ambiente do Azure.

2.  No [Portal de Gerenciamento do Azure][1], selecione a conta de armazenamento e clique em **Gerenciar Chaves de Acesso** na parte inferior da página.

    ![Botão Gerenciar Chaves de Acesso][Botão Gerenciar Chaves de Acesso]

    ![Caixa de diálogo Gerenciar Chaves de Acesso][Caixa de diálogo Gerenciar Chaves de Acesso]

3.  Substitua *[accountname]* na cadeia de conexão pelo valor na caixa **Nome da Conta de Armazenamento**.

4.  Substitua *[accesskey]* na cadeia de conexão pelo valor na caixa **Chave de Acesso Primária**.

5.  Abra o arquivo *App.config* no projeto ContosoAdsWebJob.

    Esse arquivo tem duas cadeias de conexão de armazenamento: uma para dados do aplicativo e outra para registro em log. Para este tutorial você vai usar a mesma conta em ambas. As cadeias de conexão têm os mesmos espaços reservados que você viu anteriormente.

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

    Por padrão, o SDK de Trabalhos Web procura cadeias de conexão chamadas AzureWebJobsStorage e AzureWebJobsDashboard. Como alternativa, é possível armazenar a cadeia de conexão, por mais que você queira passá-la explicitamente para o objeto `JobHost`.

6.  Substitua todas as ocorrências de *[accountname]* e *[accesskey]* pelos valores da conta de armazenamento, como fez para o projeto Web. (Ou é possível copiar a cadeia de conexão concluída do arquivo *Web.config* para ambas as cadeias de conexão do arquivo *App.config*.)

7.  Salve suas alterações.

## <span id="run"></span></a> Executar o aplicativo localmente

1.  Para iniciar o front-end Web do aplicativo, pressione CTRL+F5.

    O navegador padrão é aberto na home page. (O projeto Web é executado porque você o tornou o projeto inicial.)

    ![Home page dos Anúncios da Contoso][Home page dos Anúncios da Contoso]

2.  Para iniciar o back-end do Trabalho Web do aplicativo, clique com o botão direito do mouse no projeto ContosoAdsWebJob em **Gerenciador de Soluções** e clique em **Depurar** \> **Iniciar nova instância**.

    Uma janela do aplicativo de console é aberta e exibe “Host de trabalho iniciado" para indicar que ele está em execução.

    ![Janela do aplicativo de console mostrando que o back-end está em execução][Janela do aplicativo de console mostrando que o back-end está em execução]

3.  No navegador, clique em **Criar um anúncio**.

4.  Insira alguns dados de teste e selecione uma imagem para carregar e depois clique em **Criar**.

    ![Criar página][Criar página]

    O aplicativo vai para a Página de índice, mas não mostra uma miniatura do anúncio novo porque o processamento ainda não aconteceu.

    Enquanto isso, depois de esperar um pouco, uma mensagem de registro em log na janela do aplicativo de console mostra que uma mensagem da fila foi recebida e processada.

    ![Janela do aplicativo de console mostrando que uma mensagem da fila foi processada][Janela do aplicativo de console mostrando que uma mensagem da fila foi processada]

5.  Depois de ver as mensagens de registro em log na janela do aplicativo de console, atualize a página Índice para ver a miniatura.

    ![Página de índice][Lista de anúncios]

6.  Clique em **Detalhes** do anúncio para ver a imagem em tamanho total.

    ![Página de detalhes][Página de detalhes]

Você esteve executando o aplicativo no computador local, e ele estava usando um banco de dados do SQL Server localizado no computador, mas funcionando com filas e blobs na nuvem. Na seção a seguir, você vai executar o aplicativo na nuvem usando um banco de dados de nuvem, bem como blobs e filas da nuvem.

## <span id="runincloud"></span></a>Executar o aplicativo na nuvem

Você seguirá as etapas abaixo para executar o aplicativo na nuvem:

-   Implantar em um Site do Azure. O Visual Studio criará automaticamente um novo Site do Azure e uma instância do Banco de Dados SQL.
-   Configure o site para usar o banco de dados SQL do Azure e a conta de armazenamento.

Depois de criar alguns anúncios ainda em execução na nuvem, você exibirá o painel do SDK de Trabalhos Web para ver os recursos de monitoramento sofisticados que ele tem a oferecer.

### Implantar em um Site do Azure

1.  Feche o navegador e a janela do aplicativo de console.

2.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto ContosoAdsWeb e clique em **Publicar**.

3.  Na etapa **Perfil** do assistente **Publicar Web**, clique em **Sites do Microsoft Azure**.

    ![Selecionar destino de publicação Site do Azure][Selecionar destino de publicação Site do Azure]

4.  Na caixa **Selecionar Site Existente**, clique em **Entrar**.

    ![Clique em Entrar][Clique em Entrar]

5.  Depois de entrar, clique em Novo.

    ![Clique em Novo][Clique em Novo]

6.  Na caixa de diálogo **Criar site no Microsoft Azure**, insira um nome exclusivo na caixa **Nome do site**.

    A URL completa será composta pelo que você inserir aqui mais .azurewebsites.net (conforme mostrado ao lado da caixa de texto **Nome do site**). Por exemplo, se o nome do site for ContosoAds, a URL será ContosoAds.azurewebsites.net.

7.  Na lista suspensa **Região**, escolha a mesma região selecionada para a conta de armazenamento.

    Essa configuração especifica em qual datacenter do Azure o site será executado. A manutenção do site e da conta de armazenamento no mesmo datacenter minimiza os encargos de dados egressos e com latência.

8.  Na lista suspensa **Servidor de banco de dados**, escolha **Criar novo servidor**.

    Se a assinatura já tiver um servidor, você poderá selecioná-lo da lista suspensa.

9.  Insira um **Nome de usuário do banco de dados** e uma **Senha do banco de dados**.

    Se selecionou **Novo servidor do Banco de Dados SQL**, você não irá digitar um nome e uma senha existentes aqui; digite um novo nome e uma senha que você está definindo agora para usar mais tarde ao acessar o banco de dados. Se você selecionou um servidor criado anteriormente, você será solicitado a fornecer a senha da conta do usuário administrador já criada.

10. Clique em **Criar**.

    ![Caixa de diálogo Criar site no Microsoft Azure][Caixa de diálogo Criar site no Microsoft Azure]

    O Visual Studio cria a solução, o projeto Web, o site do Azure e a instância do banco de dados SQL do Azure.

11. Na etapa **Conexão** do assistente **Publicar Web**, clique em **Avançar**.

    ![Etapa de Conexão][Etapa de Conexão]

12. Na etapa **Configurações**, desmarque a caixa de seleção **Usar esta cadeia de conexão no tempo de execução** e clique em **Avançar**.

    ![Etapa configurações][Etapa configurações]

    Não é necessário usar a caixa de diálogo para definir a cadeia de conexão SQL pois você definirá esse valor mais tarde no ambiente do Azure.

    É possível ignorar os avisos desta página.

    -   Normalmente, a conta de armazenamento usada na execução no Azure seria diferente daquela usada na execução local, mas para este tutorial você está usando a mesma em ambos os ambientes. Assim, a cadeia de conexão AzureWebJobsStorage não precisa ser transformada. Mesmo que quisesse usar uma conta de armazenamento diferente na nuvem, você não precisaria transformar a cadeia de conexão porque o aplicativo usará uma configuração de ambiente do Azure ao ser executado no Azure. Você verá isso posteriormente no tutorial.

    -   Para este tutorial, como você não fará alterações no modelo de dados usado para o banco de dados ContosoAdsContext, não há necessidade de usar Entity Framework Code First Migrations na implantação. O Code First criará automaticamente um novo banco de dados na primeira vez em que o aplicativo tentar acessar os dados SQL.

    Para este tutorial bastam os valores padrão das opções em **Opções de Publicação do Arquivo**.

13. Na etapa **Visualização**, clique em **Iniciar Visualização**.

    ![Clique em Iniciar Visualização][Clique em Iniciar Visualização]

    É possível ignorar o aviso sobre a não publicação de nenhum banco de dados. O Entity Framework Code First criará o banco de dados. Ele não precisa ser publicado.

    A janela Visualizar mostra que os binários e os arquivos de configuração do projeto Trabalho Web serão copiados para a pasta *app\_data\\jobs\\continuous* do site.

    ![Arquivos de Trabalhos Web na janela de visualização][Arquivos de Trabalhos Web na janela de visualização]

14. Clique em **Publicar**.

    O Visual Studio implanta o aplicativo e abre a URL da home page no navegador.

    Você não vai conseguir usar o site até definir as cadeias de conexão no ambiente do Azure, na próxima seção. Será exibida uma página de erro ou a home page, dependendo das opções de criação do site e do banco de dados escolhidas anteriormente.

### Configure o site para usar o banco de dados SQL do Azure e a conta de armazenamento.

Trata-se de uma melhor prática de segurança [evitar colocar informações confidenciais como cadeias de conexão em arquivos armazenados em repositórios de código-fonte][evitar colocar informações confidenciais como cadeias de conexão em arquivos armazenados em repositórios de código-fonte]. O Azure oferece uma maneira de fazer isso: É possível definir a cadeia de conexão e outros valores de configuração no ambiente do Azure, as APIs de configuração do ASP.NET separam automaticamente esses valores quando o aplicativo é executado no Azure. Nesta seção, você definirá os valores da cadeia de conexão no Azure.

1.  No navegador, vá até o portal de gerenciamento do Azure e selecione o site onde implantou o aplicativo Contoso Ads.

2.  Clique na guia **Configurar** e role a tela para baixo até a seção **Cadeias de conexão**.

3.  Altere o nome da cadeia de conexão DefaultConnection para ContosoAdsContext.

    O Azure criou essa cadeia de conexão automaticamente quando você criou o site com um banco de dados associado. Assim, ele já tem o valor da cadeia de conexão certo. Você está apenas alterando o nome para aquilo que o código está procurando.

4.  Adicione duas novas cadeias de conexão, chamadas AzureWebJobsStorage e AzureWebJobsDashboard. Defina o tipo como Personalizado e o valor da cadeia de conexão com o mesmo valor usado anteriormente por você para os arquivos *Web.config* e *App.config*. (Não se esqueça de incluir toda a cadeia de conexão, não apenas a chave de acesso, e as aspas.)

    Essas cadeias de conexão são usadas pelo SDK de Trabalhos Web, uma para dados do aplicativo e outra para registro em log. Como visto anteriormente, a dos dados do aplicativo também é usada pelo código front-end Web.

5.  Clique em **Salvar**.

    ![Cadeias de conexão no portal de gerenciamento][Cadeias de conexão no portal de gerenciamento]

6.  Clique na guia **Painel** e em **Reiniciar**.

    O Trabalho Web é iniciado automaticamente na publicação, mas para quando você faz uma alteração na configuração. Para reiniciá-lo, você pode ou reiniciar o site ou o Trabalho Web. Normalmente é recomendável reiniciar o site depois de uma alteração na configuração.

7.  Atualize a janela do navegador com a URL do site na barra de endereços.

    A home page é exibida.

8.  Crie um anúncio, como você fez ao executar o aplicativo localmente.

    A página Índice é exibida, inicialmente, sem uma miniatura.

9.  A miniatura será exibida alguns segundos após a atualização da página.

    Se a miniatura aparecer, o Trabalho Web pode não ter sido iniciado automaticamente. Nesse caso, vá até a guia Trabalhos Web

### Veja o painel SDK de Trabalhos Web

1.  No portal de gerenciamento do Azure, selecione o site.

2.  Clique na guia **Trabalhos Web**.

3.  Clique na URL da coluna Logs do Trabalho Web.

    ![Guia Trabalhos Web][Guia Trabalhos Web]
    Uma nova guia do navegador é aberta no painel do SDK de Trabalhos Web. O painel mostra que o Trabalho Web está em execução e exibe uma lista de funções no seu código, disparadas pelo SDK de Trabalhos Web.

4.  Clique em uma das funções para ver detalhes sobre a execução

    ![Painel do SDK de Trabalhos Web][Painel do SDK de Trabalhos Web]

    ![Painel do SDK de Trabalhos Web][2]

    O botão **Função de Repetição**, nesta página, faz a estrutura do SDK de Trabalhos Web chamar a função novamente e dá uma chance de primeiro alterar os dados passados para a função.

> [WACOM.NOTE] Quando terminar os testes, exclua o site e a instância do Banco de Dados SQL. O site é gratuito, mas a instância do Banco de Dados SQL e a conta de armazenamento acumulam encargos (mínimos, devido ao tamanho reduzido). Além disso, se deixar o site em execução, qualquer pessoa que encontrar a URL poderá criar e exibir anúncios. No portal de gerenciamento do Azure, vá até a guia **Painel** do site e clique no botão **Excluir** na parte inferior da página. Em seguida, é possível marcar uma caixa de seleção para excluir a instância do Banco de Dados SQL ao mesmo tempo. Se apenas deseja evitar que outros acessem temporariamente o site, em vez disso clique em **Parar**. Nesse caso, os encargos continuarão acumulando para o Banco de Dados SQL e a conta de armazenamento. Você pode seguir um procedimento semelhante para excluir o banco de dados SQL e a conta de armazenamento quando não precisar mais dela.

## <span id="create"></span></a>Criar o aplicativo do zero

Nesta seção, você executará as seguintes tarefas:

-   Criar uma solução do Visual Studio com um projeto Web.
-   Adicionar um projeto de biblioteca de classes para a camada de acesso a dados compartilhada entre o front-end e o back-end.
-   Adicionar um projeto Aplicativo do Console para um back-end com a implantação de Trabalhos Web habilitada.
-   Adicionar pacotes NuGet.
-   Definir referências de projeto.
-   Copiar o código do aplicativo e os arquivos de configuração do aplicativo baixado, com o qual você trabalhou na seção anterior do tutorial.
-   Examinar as partes do código que funcionam com blobs e filas do Azure e o SDK de Trabalhos Web.

### Criar uma solução do Visual Studio com um projeto Web e um projeto de biblioteca de classes

1.  No Visual Studio, escolha **Novo** \> **Projeto** no menu **Arquivo**.

2.  Na caixa de diálogo **Novo Projeto**, escolha **Visual C#** \> **Web** \> **Aplicativo Web ASP .NET**.

3.  Nomeie o projeto como ContosoAdsWeb, nomeie a solução como ContosoAdsWebJobsSDK (altere o nome da solução caso esteja colocando-a na mesma pasta da solução baixada) e clique em **OK**.

    ![Novo Projeto][Novo Projeto]

4.  Na caixa de diálogo **Novo Projeto do ASP.NET**, escolha o modelo MVC e desmarque a caixa de seleção **Hospedar na nuvem** em **Microsoft Azure**.

    A seleção de **Hospedar na nuvem** permite que o Visual Studio crie automaticamente um novo Site do Azure e um novo Banco de Dados SQL. Como já os criou anteriormente, você não precisa fazer isso agora, enquanto cria o projeto. Se quiser criar um novo, marque a caixa de seleção. Em seguida, é possível configurar o novo site e o banco de dados SQL da mesma forma como fez anteriormente ao implantar o aplicativo.

5.  Clique em **Alterar Autenticação**.

    ![Alterar autenticação][Alterar autenticação]

6.  Na caixa de diálogo **Alterar Autenticação**, escolha **Sem Autenticação** e clique em **OK**.

    ![Sem Autenticação][Sem Autenticação]

7.  Na caixa de diálogo **Novo projeto ASP.NET** clique em **OK**.

    O Visual Studio cria a solução e o projeto Web.

8.  No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução (e não no projeto) e escolha **Adicionar** \> **Novo Projeto**.

9.  Na caixa de diálogo **Adicionar Novo Projeto**, escolha o modelo **Visual C#** \> **Área de Trabalho do Windows** \> **Biblioteca de Classes**.

10. Nomeie o projeto *ContosoAdsCommon*, e depois clique em **OK**.

    Esse projeto conterá o contexto do Entity Framework e o modelo de dados que o front-end e o back-end vão usar. Como alternativa, você pode definir as classes relacionadas ao EF no projeto Web e referenciar esse projeto pelo projeto Trabalho Web. Assim porém, o projeto WebJob teria uma referência desnecessária a assemblies da Web.

### Adicionar um projeto de Aplicativo do Console com a implantação de Trabalhos Web habilitada

1.  Clique com o botão direito do mouse no projeto Web (e não na solução ou no projeto da biblioteca de classes) e em **Adicionar** \> **Novo Projeto de Trabalho Web do Azure**.

    ![Seleção de menu de Novo Projeto de Trabalho Web do Azure][Seleção de menu de Novo Projeto de Trabalho Web do Azure]

2.  Na caixa de diálogo **Adicionar Trabalho Web do Azure**, insira ContosoAdsWebJob anto como **Nome do projeto** quanto como **Nome do Trabalho Web**. Deixe o **Modo de execução do Trabalho Web** definido como **Executar Continuamente**.

3.  Clique em **OK**.

    O Visual Studio cria um aplicativo de console configurado para ser implantado como um Trabalho Web sempre que você implantar o projeto Web. Para isso, ele realizou as seguintes tarefas após a criação do projeto:

    -   Adicionou um arquivo *webjob-publish-settings.json* na pasta Propriedades do projeto Trabalho Web.
    -   Adicionou um arquivo *webjobs-list.json* na pasta Propriedades do projeto Web.
    -   Instalou o pacote Microsoft.Web.WebJobs.Publish NuGet no projeto Trabalho Web.

    Para obter mais informações sobre essas alterações, consulte [Como implantar Trabalhos Web usando o Visual Studio][Como implantar Trabalhos Web usando o Visual Studio].

### Adicionar pacotes NuGet

Primeiramente, instale o SDK de Trabalhos Web no projeto Trabalho Web.

1.  Abra a caixa de diálogo **Gerenciar pacotes NuGet** para a solução.

2.  No painel esquerdo, selecione **Online**.

3.  Altere **Apenas Estável** para **Incluir Pré-lançamento**.

4.  Encontre o pacote NuGet *Microsoft.Azure.WebJobs* e instale-o no projeto ContosoAdsWebJob.

    ![Encontrar o pacote do SDK de Trabalhos Web][Encontrar o pacote do SDK de Trabalhos Web]

    ![Instalar o pacote do SDK de Trabalhos Web apenas no projeto Trabalho Web][Instalar o pacote do SDK de Trabalhos Web apenas no projeto Trabalho Web]

    Isso também instala pacotes dependentes, inclusive outro pacote do SDK de Trabalhos Web, *Microsoft.Jobs.Core*. (Você só usa o outro pacote do SDK de Trabalhos Web separadamente ao criar as funções do usuário em uma DLL à parte; para este tutorial você está escrevendo todo o código no Aplicativo do Console). Você precisará da SCL (Biblioteca de Clientes de Armazenamento) do Azure para trabalhar com filas e blobs no projeto Web e no projeto Trabalho Web.

O pacote NuGet da SCL do Azure é instalado automaticamente no projeto Trabalho Web como uma dependência do SDK de Trabalhos Web. Mas você também precisa usá-lo para trabalhar com blobs e filas no projeto Web.

1.  No painel à esquerda, selecione **Pacotes instalados**.

2.  Encontre o pacote *Armazenamento do Azure* e clique em **Gerenciar**.

3.  Na caixa **Projetos Selecionados**, marque a caixa de seleção **ContosoAdsWeb** e clique em **OK**.

Todos os três projetos usam o Entity Framework para trabalhar com dados no Banco de Dados SQL.

1.  No painel esquerdo, selecione **Online**.

2.  Altere **Incluir Pré-lançamento** para **Apenas Estável**.

3.  Encontre o pacote NuGet do *EntityFramework* e instale-o em todos os três projetos.

### Definir referências de projeto

Ambos os projetos Web e Trabalho Web funcionarão com o banco de dados SQL, logo ambos precisam de uma referência para o projeto ContosoAdsCommon.

1.  No projeto ContosoAdsWeb, defina uma referência para o projeto ContosoAdsCommon. (Clique com o botão direito do mouse no projeto ContosoAdsWeb e clique em **Adicionar** \> **Referência**. Na caixa de diálogo **Gerenciador de Referências**, selecione **Solução** \> **Projetos** \> **ContosoAdsCommon** e clique em **OK**.)

2.  No projeto ContosoAdsWebJob, defina uma referência para o projeto ContosAdsCommon.

O projeto Trabalho Web precisa de referências para trabalhar com imagens e acessar cadeias de conexão.

1.  No projeto ContosoAdsWebJob, defina uma referência como `System.Drawing` e `System.Configuration`.

### Adicionar código e arquivos de configuração

Este tutorial não mostra como [criar controladores e exibições MVC usando scaffolding][ASP.NET MVC], como [escrever código do Entity Framework que funciona com bancos de dados do SQL Server][escrever código do Entity Framework que funciona com bancos de dados do SQL Server] ou [as noções básicas da programação assíncrona no ASP.NET 4.5][as noções básicas da programação assíncrona no ASP.NET 4.5]. Assim, tudo o que resta a fazer é copiar o código e os arquivos de configuração da solução baixada para a nova solução. Depois que fizer isso, as seções a seguir irão mostrar e explicar as partes-chave do código.

Para adicionar arquivos a um projeto ou a uma pasta, clique com o botão direito do mouse no projeto ou na pasta e clique em **Adicionar** \> **Item Existente**. Selecione os arquivos que deseja e clique em **Adicionar**. Se receber uma solicitação para confirmar se deseja substituir os arquivos existentes, clique em **Sim**.

1.  No projeto ContosoAdsCommon, exclua o arquivo *Class1.cs* e adicione em seu lugar os arquivos a seguir do projeto baixado.

    -   *Ad.cs*
    -   *ContosoAdscontext.cs*
    -   *BlobInformation.cs*<br /><br />

2.  No projeto ContosoAdsWeb, adicione os seguintes arquivos do projeto baixado.

    -   *Web.config*
    -   *Global.asax.cs*
    -   Na pasta *Controllers*: *AdController.cs*
    -   Na pasta *Views\\Shared*: arquivo *\_Layout.cshtml*.
    -   Na pasta *Views\\Home*: *Index.cshtml*.
    -   Na pasta *Views\\Ad* (crie a pasta primeiro): cinco arquivos *.cshtml*.<br /><br />

3.  No projeto ContosoAdsWebJob, adicione os seguintes arquivos do projeto baixado.

    -   *App.config* (altere o filtro do tipo de arquivo para **Todos os Arquivos**)
    -   *Program.cs*

Agora é possível compilar, executar e implantar o aplicativo conforme instruído anteriormente no tutorial. Porém, antes de fazer isso, pare o Trabalho Web em execução no primeiro site em que o implantou. Do contrário, esse Trabalho Web processará as mensagens da fila criadas localmente ou pelo aplicativo em execução no novo site, porque todas estão usando a mesma conta de armazenamento.

## <span id="code"></span></a>Examinar o código do aplicativo

As seções a seguir explicam o código relacionado ao trabalho com os blobs e as filas do SDK de Trabalhos Web e do Armazenamento do Azure. Para o código específico do SDK de Trabalhos Web, consulte a seção [Program.cs][Program.cs].

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

A classe `BlobInformation` é usada para armazenar informações sobre um blob de imagem em uma mensagem da fila.

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

O código que é chamado do método `Application_Start` cria um contêiner de blog *images* e uma fila *images*, se ainda não existir. Isso garante que, sempre que você começar a usar uma nova conta de armazenamento, o contêiner do blob e a fila obrigatórios serão criados automaticamente.

O código obtém acesso à conta de armazenamento usando a cadeia de conexão de armazenamento do arquivo *Web.config* ou do ambiente de execução do Azure.

        var storageAccount = CloudStorageAccount.Parse
            (ConfigurationManager.ConnectionStrings["AzureWebJobsStorage"].ToString());

Em seguida, ele obtém uma referência para o contêiner do blob de *imagens*, cria o contêiner se ele ainda não existe e define permissões de acesso no novo contêiner. Por padrão, os novos contêineres somente permite que os clientes com credenciais da conta de armazenamento acessem os blobs. O site precisa que os blobs para o público para poder exibir imagens usando URLs que apontam para os blobs de imagem.

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

Um código semelhante obtém uma referência para a fila *blobnamerequest* e cria uma nova fila. Nesse caso, nenhuma alteração de permissão é necessária. A seção [ResolveBlobName][ResolveBlobName], mais adiante no tutorial, explica por que a fila na qual o aplicativo Web escreve é usada apenas para obter nomes de blob e não para gerar miniaturas.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        var imagesQueue = queueClient.GetQueueReference("blobnamerequest");
        imagesQueue.CreateIfNotExists();

### ContosoAdsWeb - \_Layout.cshtml

O arquivo *\_Layout.cshtml* define o nome do aplicativo no cabeçalho e no rodapé e cria uma entrada de menu “Anúncios".

### ContosoAdsWeb - Views\\Home\\Index.cshtml

O arquivo *Views\\Home\\Index.cshtml* exibe links de categoria na home page. Os links passam o valor inteiro da enumeração `Category` em uma variável querystring para a página Índice de anúncio.

        <li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
        <li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
        <li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
        <li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>

### ContosoAdsWeb - AdController.cs

No arquivo *AdController.cs*, o construtor chama o método `InitializeStorage` para criar os objetos da Biblioteca do Cliente do Armazenamento do Azure que fornecem uma API para trabalhar com blobs e filas.

Em seguida, o código obtém uma referência para o contêiner do blob de *imagens* como visto anteriormente em *Global.asax.cs*. Enquanto faz isso ele define uma [política de recuperação][política de recuperação] padrão apropriada para um aplicativo Web. A política de recuperação de retirada exponencial padrão pode fazer com que o aplicativo Web pare de responder por mais de um minuto em tentativas repetidas de uma falha transitória. A política de recuperação especificada aqui aguarda 3 segundos após cada tentativa, até 3 tentativas.

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesBlobContainer = blobClient.GetContainerReference("images");

Código similar obtém uma referência para a fila de *imagens*.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesQueue = queueClient.GetQueueReference("blobnamerequest");

A maior parte do código do controlador é típica para trabalhar com um modelo de dados do Entity Framework usando uma classe DbContext. Uma exceção é o método HttpPost `Create`, que atualiza um arquivo e o salva no armazenamento do blob. O associador de modelo fornece um objeto [HttpPostedFileBase][HttpPostedFileBase] para o método.

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

O código que não é carregado está no método `UploadAndSaveBlobAsync`. Ele cria um nome de GUID para o blob, carrega e salva o arquivo e retorna uma referência para o blob salvo.

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

Depois de carregar um blob e atualizar o banco de dados, o método HttpPost `Create` criará uma mensagem da fila para informar o processo back-end que uma imagem está pronta para conversão em miniatura.

        BlobInformation blobInfo = new BlobInformation() { AdId = ad.AdId, BlobUri = new Uri(ad.ImageURL) };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        await thumbnailRequestQueue.AddMessageAsync(queueMessage);

O código do método HttpPost `Edit` é parecido, exceto pelo fato de que se o usuário selecionar um novo arquivo de imagem, qualquer blob já existente para esse anúncio deverá ser excluído.

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

### ContosoAdsWeb - Views\\Ad\\Index.cshtml e Details.cshtml

O arquivo *Index.cshtml* exibe miniaturas com os outros dados de anúncio:

        <img  src="@Html.Raw(item.ThumbnailURL)" />

O arquivo *Details.cshtml* exibe a imagem em tamanho real:

        <img src="@Html.Raw(Model.ImageURL)" />

### ContosoAdsWeb - Views\\Ad\\Create.cshtml e Edit.cshtml

Os arquivos *Create.cshtml* e *Edit.cshtml* especificam a codificação de formulário que habilita o controlador a obter o objeto `HttpPostedFileBase`.

        @using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))

Um elemento `<input>` informa o navegador para fornecer uma caixa de diálogo de seleção de arquivo.

        <input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />

### <span id="programcs"></span></a>ContosoAdsWebJob - Program.cs - Métodos Main e Initialize

Quando o Trabalho Web começa, o método `Main` chama `Initialize` para criar uma instância do contexto do banco de dados do Entity Framework. Em seguida, ele chama o método `JobHost.RunAndBlock` do SDK de Trabalhos Web para começar a execução do thread atual.

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

### <span id="generatethumbnail"></span></a>ContosoAdsWebJob - Program.cs - Método GenerateThumbnail

O SDK de Trabalhos Web chama esse método quando uma mensagem da fila é recebida. O método cria uma miniatura e coloca a URL da miniatura no banco de dados.

        public static void GenerateThumbnail(
        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,
        [Blob("images/{BlobName}")] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)
        {
            using (Stream output = outputBlob.OpenWrite())
            {
                ConvertImageToThumbnailJPG(input, output);
                outputBlob.Properties.ContentType = "image/jpeg";
            }
        
            var id = blobInfo.AdId;
            Ad ad = db.Ads.Find(id);
            if (ad == null)
            {
                throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", id.ToString()));
            }
            ad.ThumbnailURL = outputBlob.Uri.ToString();
            db.SaveChanges();
        }

-   O atributo `QueueTrigger` direciona o SDK de Trabalhos Web para chamar esse método quando uma nova mensagem é recebida na fila thumbnailrequest.

        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,

    O objeto `BlobInformation` na mensagem da fila é desserializado automaticamente no parâmetro `blobInfo`. Quando o método é concluído, a mensagem da fila é excluída. Se o método falhar antes da conclusão, a mensagem da fila não será excluída. Depois de uma concessão de 10 minutos, a mensagem será liberada para ser novamente retirada e processada. Essa sequência não será repetida indefinidamente se uma mensagem sempre causar uma exceção. Depois de 5 tentativas malsucedidas de processar uma mensagem, a mensagem será movida para uma fila chamada {queuename}-poison. O número máximo de tentativas é configurável.

-   Os dois atributos `Blob` fornecem objetos associados a blobs: um ao blob da imagem existente e outro ao blob da nova miniatura criado pelo método.

        [Blob("images/{BlobName}")] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)

    Os nomes dos blobs vêm de propriedades do objeto `BlobInformation` recebidas na mensagem da fila (`BlobName` e `BlobNameWithoutExtension`). Para obter toda a funcionalidade da SCL, é possível usar a classe `CloudBlockBlob` para trabalhar com blobs. Se quiser reutilizar código escrito para funcionar com objetos `Stream`, você pode usar a classe `Stream`.

> [WACOM.NOTE]
>
> -   Se o site for executado em várias VMs, esse programa será executado em todos os computadores e cada computador aguardará os gatilhos e tentará executar as funções. Em alguns cenários, isso pode fazer com que algumas funções processem os mesmos dados duas vezes. Assim, as funções devem ser idempotentes (escritas de forma que chamá-las repetidamente com os mesmos dados de entrada não produza resultados duplicados).
> -   Para obter informações sobre como implementar um desligamento normal, consulte [o anúncio do SDK de Trabalhos Web 0.3.0 beta][o anúncio do SDK de Trabalhos Web 0.3.0 beta].
> -   O código no método `ConvertImageToThumbnailJPG` (não mostrado) usa classes no namespace `System.Drawing` por uma questão de simplicidade. Entretanto, as classes nesse namespace foram projetadas para uso nos formulários do Windows. Elas não têm suporte para uso em um serviço Windows ou ASP.NET.

### Trabalhos Web X função de trabalho Serviço de Nuvem

Ao comparar a quantidade de código no método `GenerateThumbnails` desse aplicativo de exemplo com o código da função de trabalho na [versão de Serviço de Nuvem do aplicativo][versão de Serviço de Nuvem do aplicativo], você pode ver quanto trabalho o SDK de Trabalhos Web está fazendo por você. A vantagem é maior do que parece, porque o aplicativo de exemplo de Serviço de Nuvem não faz todas as coisas (como a manipulação de mensagens suspeitas) que você faria em um aplicativo em produção, algo que o SDK de Trabalhos Web faz para você.

A versão de Serviço de Nuvem desse aplicativo de exemplo tem um método `ProcessQueueMessage` que realiza as seguintes tarefas:

-   Obter a ID de registro do banco de dados da mensagem da fila.
-   Ler o banco de dados para obter a URL do blob.
-   Converter a imagem em uma miniatura e salvar a miniatura em um novo blob.
-   Atualizar o registro de banco de dados adicionando a URL do blob de miniatura.

Na versão de Serviço de Nuvem do aplicativo, a ID de registro é a única informação na mensagem da fila e o processo em segundo plano obtém a URL da imagem do banco de dados. Na versão do SDK de Trabalhos Web do aplicativo, a mensagem da fila inclui a URL da imagem de forma que ela possa ser fornecida aos atributos `Blob`. Se a mensagem da fila não tivesse a URL do blob, seria necessário escrever o código no método para obter a URL do blob e teria que escrever código para associar objetos aos blobs de entrada e saída, em vez de deixar o SDK de Trabalhos Web fazer esse trabalho por você.

### Usando o SDK de Trabalhos Web em uma função de trabalho

Como você viu, um programa que usa o SDK de Trabalhos Web não precisa ser executado no Azure em um Trabalho Web. Ele pode ser executado localmente, além de ser executado também em uma função de trabalho. No entanto, só é possível acessar o painel do SDK de Trabalhos Web por meio de um Site do Azure. Para usar o painel, é preciso conectar o site à conta de armazenamento que você está usando definindo a cadeia de conexão AzureWebJobsDashboard na guia Configurar do portal de gerenciamento. Em seguida, você pode chegar ao Painel usando a URL https://{websitename}.scm.azurewebsites.net/azurejobs/\#/functions. Para obter mais informações, consulte [Obtendo um painel para desenvolvimento local com o SDK de Trabalhos Web][Obtendo um painel para desenvolvimento local com o SDK de Trabalhos Web], mas observe que ele mostra um nome da cadeia de conexão antigo.

## Próximas etapas

Neste tutorial, você viu um aplicativo multicamadas simples que usa o SDK de Trabalhos Web no processamento back-end. O aplicativo Contoso Ads foi intencionalmente mantido simples para um tutorial de introdução. Por exemplo, ele não implementa [injeção de dependência][injeção de dependência] ou [os padrões de unidade de trabalho e repositório][os padrões de unidade de trabalho e repositório], ele não [usa uma interface para registro em log][usa uma interface para registro em log], não usa [Migrações Iniciais de Código de EF][Migrações Iniciais de Código de EF] para gerenciar as alterações de modelo de dados ou [Resiliência de Conexão de EF][Resiliência de Conexão de EF] para gerenciar erros de rede transitórios, e assim por diante.

Para obter exemplos adicionais que mostrem como usar o SDK de Trabalhos Web em outros cenários, consulte a pasta [AzureWebJobs][AzureWebJobs] no projeto CodePlex ASP.NET.

Quando um Trabalho Web é executado em um site, você pode solucionar problemas executando em modo de depuração, assim como acontece com sites. Para obter mais informações, consulte [Solucionando problemas de Sites do Azure no Visual Studio][Solucionando problemas de Sites do Azure no Visual Studio]. Você precisa anexar manualmente ao processo Trabalho Web. Consulte as instruções do Visual Studio 2012 no tutorial para obter informações sobre como anexar a um processo.

Para obter mais informações, consulte [Recursos recomendados para Trabalhos Web do Azure][Recursos recomendados para Trabalhos Web do Azure].

  [site do Azure]: /pt-br/documentation/articles/fundamentals-application-models/#WebSites
  [Banco de Dados SQL do Azure]: http://msdn.microsoft.com/library/azure/ee336279
  [serviço Blob do Azure]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage
  [serviço Fila do Azure]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern
  [Lista de anúncios]: ./media/websites-dotnet-webjobs-sdk-get-started/list.png
  [baixar o projeto do Visual Studio]: http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb
  [Pré-requisitos]: #prerequisites
  [O que você vai aprender]: #learn
  [O SDK de Trabalhos Web]: #webjobssdk
  [Arquitetura do aplicativo]: #contosoads
  [Configurar o ambiente de desenvolvimento]: #setupdevenv
  [Compilar, executar e implantar o aplicativo]: #storage
  [Criar o aplicativo a partir do zero]: #create
  [Examinar o código do aplicativo]: #code
  [Solucionar problemas]: #troubleshoot
  [Próximas etapas]: #next-steps
  [ASP.NET MVC]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
  [Web Forms]: http://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview
  [Trabalhos Web]: /pt-br/documentation/articles/web-sites-create-web-jobs/
  [AlwaysOn]: http://weblogs.asp.net/scottgu/archive/2014/01/16/windows-azure-staging-publishing-support-for-web-sites-monitoring-improvements-hyper-v-recovery-manager-ga-and-pci-compliance.aspx
  [Tabela de anúncios]: ./media/websites-dotnet-webjobs-sdk-get-started/adtable.png
  [Arquitetura do Contoso Ads]: ./media/websites-dotnet-webjobs-sdk-get-started/apparchitecture.png
  [Comparação entre Sites do Azure, Serviços de Nuvem e Máquinas Virtuais]: /pt-br/documentation/articles/choose-web-site-cloud-service-vm/
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Como gerenciar contas de armazenamento]: /pt-br/documentation/articles/storage-manage-storage-account/
  [Nova conta de armazenamento]: ./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png
  [SQL Server Express LocalDB]: http://msdn.microsoft.com/pt-br/library/hh510202.aspx
  [1]: http://manage.windowsazure.com/
  [Botão Gerenciar Chaves de Acesso]: ./media/websites-dotnet-webjobs-sdk-get-started/mak.png
  [Caixa de diálogo Gerenciar Chaves de Acesso]: ./media/websites-dotnet-webjobs-sdk-get-started/cpak.png
  [Home page dos Anúncios da Contoso]: ./media/websites-dotnet-webjobs-sdk-get-started/home.png
  [Janela do aplicativo de console mostrando que o back-end está em execução]: ./media/websites-dotnet-webjobs-sdk-get-started/backendrunning.png
  [Criar página]: ./media/websites-dotnet-webjobs-sdk-get-started/create.png
  [Janela do aplicativo de console mostrando que uma mensagem da fila foi processada]: ./media/websites-dotnet-webjobs-sdk-get-started/backendlogs.png
  [Página de detalhes]: ./media/websites-dotnet-webjobs-sdk-get-started/details.png
  [Selecionar destino de publicação Site do Azure]: ./media/websites-dotnet-webjobs-sdk-get-started/pubweb.png
  [Clique em Entrar]: ./media/websites-dotnet-webjobs-sdk-get-started/signin.png
  [Clique em Novo]: ./media/websites-dotnet-webjobs-sdk-get-started/clicknew.png
  [Caixa de diálogo Criar site no Microsoft Azure]: ./media/websites-dotnet-webjobs-sdk-get-started/newdb.png
  [Etapa de Conexão]: ./media/websites-dotnet-webjobs-sdk-get-started/connstep.png
  [Etapa configurações]: ./media/websites-dotnet-webjobs-sdk-get-started/settingsstep.png
  [Clique em Iniciar Visualização]: ./media/websites-dotnet-webjobs-sdk-get-started/previewstep.png
  [Arquivos de Trabalhos Web na janela de visualização]: ./media/websites-dotnet-webjobs-sdk-get-started/previewwjfiles.png
  [evitar colocar informações confidenciais como cadeias de conexão em arquivos armazenados em repositórios de código-fonte]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets
  [Cadeias de conexão no portal de gerenciamento]: ./media/websites-dotnet-webjobs-sdk-get-started/azconnstr.png
  [Guia Trabalhos Web]: ./media/websites-dotnet-webjobs-sdk-get-started/wjtab.png
  [Painel do SDK de Trabalhos Web]: ./media/websites-dotnet-webjobs-sdk-get-started/wjdashboardhome.png
  [2]: ./media/websites-dotnet-webjobs-sdk-get-started/wjfunctiondetails.png
  [Novo Projeto]: ./media/websites-dotnet-webjobs-sdk-get-started/newproject.png
  [Alterar autenticação]: ./media/websites-dotnet-webjobs-sdk-get-started/chgauth.png
  [Sem Autenticação]: ./media/websites-dotnet-webjobs-sdk-get-started/noauth.png
  [Seleção de menu de Novo Projeto de Trabalho Web do Azure]: ./media/websites-dotnet-webjobs-sdk-get-started/newawjp.png
  [Como implantar Trabalhos Web usando o Visual Studio]: /pt-br/documentation/articles/websites-dotnet-deploy-webjobs/
  [Encontrar o pacote do SDK de Trabalhos Web]: ./media/websites-dotnet-webjobs-sdk-get-started/updstg.png
  [Instalar o pacote do SDK de Trabalhos Web apenas no projeto Trabalho Web]: ./media/websites-dotnet-webjobs-sdk-get-started/updstg2.png
  [escrever código do Entity Framework que funciona com bancos de dados do SQL Server]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc
  [as noções básicas da programação assíncrona no ASP.NET 4.5]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async
  [Program.cs]: #programcs
  [ResolveBlobName]: #resolveblobname
  [política de recuperação]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling
  [HttpPostedFileBase]: http://msdn.microsoft.com/pt-br/library/system.web.httppostedfilebase.aspx
  [o anúncio do SDK de Trabalhos Web 0.3.0 beta]: http://azure.microsoft.com/blog/2014/06/18/announcing-the-0-3-0-beta-preview-of-microsoft-azure-webjobs-sdk/http://azure.microsoft.com/blog/2014/06/18/announcing-the-0-3-0-beta-preview-of-microsoft-azure-webjobs-sdk/
  [versão de Serviço de Nuvem do aplicativo]: /pt-br/documentation/articles/cloud-services-dotnet-get-started/
  [Obtendo um painel para desenvolvimento local com o SDK de Trabalhos Web]: http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx
  [injeção de dependência]: http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection
  [os padrões de unidade de trabalho e repositório]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo
  [usa uma interface para registro em log]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log
  [Migrações Iniciais de Código de EF]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application
  [Resiliência de Conexão de EF]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application
  [AzureWebJobs]: http://aspnet.codeplex.com/SourceControl/latest#Samples/AzureWebJobs/ReadMe.txt
  [Solucionando problemas de Sites do Azure no Visual Studio]: /pt-br/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/
  [Recursos recomendados para Trabalhos Web do Azure]: http://go.microsoft.com/fwlink/?LinkId=390226
