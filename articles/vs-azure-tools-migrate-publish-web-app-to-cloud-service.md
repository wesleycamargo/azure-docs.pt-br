<properties
   pageTitle="Como migrar e publicar um aplicativo Web em um serviço de nuvem do Azure por meio do Visual Studio | Microsoft Azure"
   description="Saiba como migrar e publicar seu aplicativo Web em um serviço de nuvem do Azure por meio do Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/08/2015"
   ms.author="kempb" />

# Como migrar e publicar um aplicativo Web em um serviço de nuvem do Azure por meio do Visual Studio

Para tirar proveito dos serviços de hospedagem e a escalabilidade do Azure, você pode migrar e publicar seu aplicativo Web em um serviço de nuvem do Azure. Você pode executar um aplicativo Web no Azure fazendo alterações mínimas nele.

>[AZURE.NOTE]Este tópico é sobre a implantação de serviços de nuvem, não em sites. Para obter informações sobre a implantação em sites, consulte Como implantar um site do Azure.

Para obter uma lista de modelos específicos com suporte para Visual C# e Visual Basic, consulte a seção **Modelos de projeto com suporte** mais adiante neste tópico.

Primeiro, você precisa habilitar o aplicativo Web para o Azure por meio do Visual Studio. A ilustração a seguir mostra as etapas principais para a publicação do aplicativo Web existente adicionando um projeto do Azure para ser usado para a implantação. Esse processo adiciona um projeto do Azure com a função web necessária à sua solução. Com base no tipo de projeto Web que você tem, as propriedades do projeto para assemblies também são atualizadas se o pacote de serviço exigir assemblies adicionais para a implantação.

![Publicar um aplicativo Web no Microsoft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

>[AZURE.NOTE]O comando **Converter**, **Converter em Projeto do Serviço de Nuvem do Azure** é exibido somente para o projeto Web na sua solução. Por exemplo, o comando não ficaria disponível para um projeto do Silverlight na solução. Quando você cria um pacote de serviço ou publica seu aplicativo no Azure, avisos ou erros podem ocorrer. Esses avisos e erros podem ajudá-lo a corrigir problemas antes da implantação no Azure. Por exemplo, você pode receber um aviso sobre um assembly ausente. Para saber mais sobre como tratar todos os avisos como erros, consulte [Como configurar um Projeto do Serviço de Nuvem do Azure com o Visual Studio](vs-azure-tools-cloud-service-how-to-configure-project-with-visual-studio.md). Se criar seu aplicativo, executá-lo localmente usando o emulador de computação ou publicá-lo no Azure, você poderá ver o seguinte erro na janela **Lista de Erros**: **O caminho, o nome do arquivo ou ambos são muito longos**. Esse erro ocorre quando o nome totalmente qualificado do projeto do Azure é muito longo. O nome do projeto, incluindo o caminho completo, não pode ter mais de 146 caracteres. Por exemplo, este é o nome completo do projeto, incluindo o caminho do arquivo para um projeto do Azure criado para um aplicativo Silverlight: `c:\users<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Talvez você precise mover sua solução para um diretório diferente com um caminho mais curto para reduzir o tamanho do nome totalmente qualificado do projeto.

Para migrar e publicar um aplicativo Web no Azure por meio do Visual Studio, siga estas etapas.

## Habilitar um aplicativo Web para implantação no Azure

### Para habilitar um aplicativo Web para implantação no Azure

1. Para habilitar seu aplicativo Web para implantação no Azure, abra o menu de atalho para um projeto Web em sua solução e escolha Adicionar Projeto de Implantação no Azure. As seguintes ações ocorrerão:

- Um projeto do Azure chamado `<name of the web project>.Azure` será adicionado à solução para seu aplicativo.

- Uma função web para o projeto Web é adicionada ao projeto do Azure.

- A propriedade **Copiar Local** será definida como verdadeira para quaisquer assemblies que forem necessários para MVC 2, MVC 3, MVC 4 e aplicativos de negócios do Silverlight. Isso adicionará esses assemblies ao pacote de serviço usado para a implantação.

  >[AZURE.IMPORTANT]Se tiver outros assemblies ou arquivos necessários para o aplicativo Web, você precisa definir manualmente as propriedades para esses arquivos. Para obter informações sobre como definir essas propriedades, consulte a seção **Incluir arquivos no pacote de serviço** posteriormente neste artigo.

  >[AZURE.NOTE]Se já existir uma função web para um projeto Web específico em um projeto do Azure na solução, **Converter**, **Converter em um Projeto de Serviço de Nuvem do Azure** não é exibido no menu de atalho para este projeto Web.

Se tiver vários projetos Web em seu aplicativo Web e desejar criar funções web para cada projeto Web, você deve seguir as etapas neste procedimento para cada um dos projetos Web. Isso cria projetos do Azure separados para cada função web. Cada projeto Web pode ser publicado separadamente. Como alternativa, você pode adicionar manualmente outra função web a um projeto do Azure existente em seu aplicativo Web. Para fazer isso, abra o menu de atalho para a pasta **Funções** em seu projeto do Azure, escolha **Adicionar**, **Projeto de Função Web na solução**. Escolha o projeto a ser adicionado como uma função web e clique no botão **OK**.

## Usar um Banco de Dados SQL do Azure para seu aplicativo

Se você tiver uma cadeia de conexão para o aplicativo Web que usa um banco de dados do SQL Server no local, é necessário alterar essa cadeia de conexão para usar uma instância do Banco de Dados SQL que o Azure hospeda.

>[AZURE.IMPORTANT]Sua assinatura deve habilitá-lo usar o Banco de Dados SQL. Se acessar sua assinatura no Portal de Gerenciamento do Azure, você pode determinar quais serviços são fornecidos pela assinatura. As instruções a seguir se aplicam ao Portal de Gerenciamento lançado. Se estiver usando o Portal de Gerenciamento na versão de visualização, vá para o próximo procedimento. |

### Para usar uma instância do Banco de Dados SQL em sua função web para a cadeia de conexão

1. Para criar uma instância do Banco de Dados SQL no console de gerenciamento do Azure, siga as etapas no seguinte artigo: [Criar um Servidor de Banco de Dados SQL](http://go.microsoft.com/fwlink/?LinkId=225109).

    >[AZURE.NOTE]Quando configura as regras de firewall para sua instância do Banco de Dados SQL, você deve marcar a caixa de seleção **Permitir que outros serviços do Azure acessem este servidor**.

1. Para criar uma instância do Banco de Dados SQL a ser usada para a cadeia de conexão, siga as etapas na próxima seção, no artigo: [Criar um Banco de Dados SQL](http://go.microsoft.com/fwlink/?LinkId=225110).

1. Para copiar a cadeia de conexão ADO.NET a ser usada para a cadeia de conexão, execute as seguintes etapas no portal de gerenciamento do Azure.

  1. Escolha o botão **Banco de Dados** e abra o nó para a assinatura que você usou para criar a instância do Banco de Dados SQL.

  1. Para exibir as instâncias disponíveis do Banco de Dados SQL, escolha o nó **Bancos de Dados SQL**.

  1. Escolha o banco de dados para exibir suas propriedades. A exibição **Propriedades** aparece.

      >[AZURE.NOTE]Se a exibição **Propriedades** não aparecer, talvez seja necessário abri-la usando o divisor.

  1. Para exibir as cadeias de conexão, escolha o botão de reticências (...) ao lado de Exibir.

      A caixa de diálogo **Cadeias de Conexão** é exibida.

  1. Para copiar a cadeia de conexão ADO.NET, realce o texto e escolha as teclas Ctrl + C.

  1. Para fechar a caixa de diálogo, escolha o botão **Fechar**.

1. Para substituir a cadeia de conexão no arquivo web.config para usar essa instância do Banco de Dados SQL, abra o arquivo web.config, realce a entrada da cadeia de conexão existente e escolha as teclas Ctrl + V. A cadeia de conexão ADO.NET para a instância do Banco de Dados SQL substitui a cadeia de conexão existente.

1. Você também deve adicionar o parâmetro `MultipleActiveResultSets=True` à cadeia de conexão. A cadeia de conexão deve ter o seguinte formato:

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```

1. (Opcional) Um método alternativo para alterar a cadeia de conexão diretamente no arquivo web.config é adicionar uma seção a um dos arquivos de transformação web.config, dependendo da configuração da compilação que você usar para criar o pacote de serviço. Abra o arquivo Web.Debug.Config ou o arquivo Web.Release.Config. Adicione a seção a seguir ao arquivo:

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```

1. Salve o arquivo modificado e republique o aplicativo.

### Para usar uma instância do Banco de Dados SQL usando o Portal de Gerenciamento

1. No [Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/?LinkID=213885), escolha o nó Bancos de Dados SQL.

  - Se a instância do Banco de Dados SQL que você deseja usar aparecer, abra-a.

  - Se não tiver criado nenhuma instância, escolha o link apropriado e crie uma instância.

1. Após abrir ou criar uma instância de banco de dados, escolha o link **Cadeias de Conexão**.

1. Na parte inferior da página, escolha o link para definir as configurações de firewall e aceite os valores padrão ou configure os valores de que você precisa.

1. Copie a cadeia de conexão ADO.NET, cole-a em seu arquivo web. config na cadeia de conexão antiga do banco de dados local e certifique-se de adicionar `MultipleActiveResultSets=True`.

## Publicar um aplicativo Web no Azure

### Para publicar um aplicativo Web no Azure

1. Para testar o aplicativo no ambiente de desenvolvimento local usando o emulador de computação do Azure, abra o menu de atalho do projeto do Azure para a função web e escolha **Definir como Projeto de Inicialização**. Escolha **Depurar**, **Iniciar Depuração** (teclado: **F5**).

    A caixa de diálogo **Iniciar o Ambiente de Depuração do Azure** é aberta e o aplicativo é iniciado no navegador. Para obter detalhes específicos sobre como iniciar cada tipo de aplicativo Web no emulador de computação, consulte a tabela nesta seção.

1. Para configurar os serviços para que seu aplicativo seja publicado no Azure, você precisa ter uma conta da Microsoft e uma assinatura do Azure. Siga as etapas neste tópico para configurar seus serviços: [Configurando os serviços necessários para publicar um serviço de nuvem do Visual Studio](vs-azure-tools-setting-up-services-required-to-publish-a-cloud-service-from-visual-studio.md).

1. Para publicar o aplicativo Web no Azure, abra o menu de atalho do projeto Web e escolha **Publicar no Azure**.

    A caixa de diálogo **Publicar Aplicativo do Azure** é aberta e o Visual Studio inicia o processo de implantação. Para obter mais informações sobre como publicar o aplicativo, consulte a seção **Publicar um Aplicativo do Azure do Visual Studio** em [Publicando um Serviço de Nuvem usando as Ferramentas do Azure](vs-azure-tools-publishing-a-cloud-service-using-the-azure-tools.md).

    >[AZURE.NOTE]Você também pode publicar o aplicativo Web por meio do projeto do Azure. Para fazer isso, abra o menu de atalho do projeto do Azure e clique em **Publicar**.

1. Para ver o andamento da implantação, você pode exibir a janela **Log de Atividades do Azure**. Esse log é exibido automaticamente quando o processo de implantação é iniciado. Você pode expandir o item de linha no log de atividades para exibir informações detalhadas, como mostrado na ilustração a seguir:

    ![VST\_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Opcional) Para cancelar o processo de implantação, abra o menu de atalho do item de linha no log de atividades e escolha **Cancelar e remover**. Isso interrompe o processo de implantação e exclui o ambiente de implantação do Azure.

    >[AZURE.NOTE]Para remover este ambiente de implantação após ele ter sido implantado, você deve usar o Portal de Gerenciamento do Azure.

1. (Opcional) Após suas instâncias de função serem iniciadas, o Visual Studio mostra automaticamente o ambiente de implantação no nó **Computação do Azure** no **Gerenciador de Servidores**. Aqui você pode exibir o status das instâncias de função individuais. Para saber mais sobre o nó de Computação do Azure no Gerenciador de Servidores, consulte [Exibindo o estado de um Serviço de Nuvem usando o Gerenciador de Servidores](vs-azure-tools-viewing-the-state-of-a-cloud-service-using-server-explorer.md).

    A ilustração a seguir mostra as instâncias de função enquanto elas ainda estão no estado Inicializando:

    ![VST\_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)

1. Para acessar seu aplicativo após a implantação, escolha a seta ao lado de sua implantação quando o status de **Concluído** aparecer no **Log de Atividades do Azure**. Isso exibe a URL para o aplicativo Web no Azure. Consulte a tabela a seguir para obter detalhes sobre como iniciar um tipo específico de aplicativo Web do Azure.

    A tabela a seguir lista os detalhes de como iniciar aplicativos Web específicos por meio do Azure ou como executar ou depurar um aplicativo Web localmente usando o Emulador de Computação do Azure:

    |Tipo de aplicativo Web|Executar/depurar localmente usando Emulador de Computação|em execução no Azure|
    |---|---|---|
    |Aplicativo Web do ASP.NET|Na barra de menus, escolha **Depurar**, **Iniciar Depuração** (teclado: escolha a tecla **F5**.).|Escolha o hiperlink da URL exibido na guia **Implantação** do **Log de Atividades do Azure** para carregar a página inicial no navegador.|
    |Aplicativo Web ASP.NET MVC 2|Na barra de menus, escolha **Depurar**, **Iniciar Depuração** (teclado: escolha a tecla **F5**.).|Escolha o hiperlink da URL exibido na guia **Implantação** do **Log de Atividades do Azure** para carregar a página inicial no navegador.|
    |Aplicativo Web ASP.NET MVC 3|Na barra de menus, escolha **Depurar**, **Iniciar Depuração** (teclado: escolha a tecla **F5**.).|Escolha o hiperlink da URL exibido na guia **Implantação** do **Log de Atividades do Azure** para carregar a página inicial no navegador.|
    |Aplicativo Web ASP.NET MVC 4|Na barra de menus, escolha **Depurar**, **Iniciar Depuração** (teclado: escolha a tecla **F5**.).|Escolha o hiperlink da URL exibido na guia **Implantação** do **Log de Atividades do Azure** para carregar a página inicial no navegador.|
    |Aplicativo Web ASP.NET vazio|Você deve adicionar uma página .aspx ao seu aplicativo que você define como a página inicial do projeto Web. Em seguida, na barra de menus, escolha **Depurar**, **Iniciar Depuração** (teclado: escolha a tecla **F5**.).|Se você tiver uma página .aspx padrão em seu aplicativo, escolha o hiperlink da URL exibido na guia **Implantação** do **Log de Atividades do Azure** e a página será carregada no navegador. Se tiver uma página .aspx diferente, você precisa navegar até essa página específica usando o seguinte formato para sua URL: `<url for deployment>/<name of page>.aspx`|
    |Aplicativo Silverlight|Na barra de menus, escolha **Depurar**, **Iniciar Depuração** (teclado: escolha a tecla **F5**.).|Você precisará navegar até a página específica para seu aplicativo usando o seguinte formato de URL: `<url for deployment>/<name of page>.aspx`|
    |Aplicativo de negócios Silverlight|Na barra de menus, escolha **Depurar**, **Iniciar Depuração** (teclado: escolha a tecla **F5**.).|Você precisará navegar até a página específica para seu aplicativo usando o seguinte formato de URL: `<url for deployment>/<name of page>.aspx`|
    |Aplicativo de navegação Silverlight|Na barra de menus, escolha **Depurar**, **Iniciar Depuração** (teclado: escolha a tecla **F5**.).|Você precisará navegar até a página específica para seu aplicativo usando o seguinte formato de URL: `<url for deployment>/<name of page>.aspx`|
    |Aplicativo de serviço WCF|Você deve definir o arquivo .svc como a página inicial do seu projeto de serviço WCF. Em seguida, na barra de menus, escolha **Depurar**, **Iniciar Depuração** (teclado: escolha a tecla **F5**.).|Você precisará navegar até o arquivo svc para seu aplicativo usando o seguinte formato de URL: `<url for deployment>/<name of service file>.svc`|
    |Aplicativo de serviço de fluxo de trabalho WCF|Você deve definir o arquivo .svc como a página inicial do seu projeto de serviço WCF. Em seguida, na barra de menus, escolha **Depurar**, **Iniciar Depuração** (teclado: escolha a tecla **F5**.).|Você precisará navegar até o arquivo svc para seu aplicativo usando o seguinte formato de URL: `<url for deployment>/<name of service file>.svc`|
    |Entidades dinâmicas do ASP.NET|Na barra de menus, escolha **Depurar**, **Iniciar Depuração** (teclado: escolha a tecla **F5**.).|Você deve atualizar a cadeia de conexão (consulte a próxima seção). Você também precisará navegar até a página específica para seu aplicativo usando o seguinte formato de URL: `<url for deployment>/<name of page>.aspx`|
    |Dados dinâmicos ASP.NET Linq to SQL|Na barra de menus, escolha **Depurar**, **Iniciar Depuração** (teclado: escolha a tecla **F5**.).|Você deve seguir as etapas neste procedimento: Usar um banco de dados SQL do Azure para seu aplicativo (consulte a seção anterior neste tópico). Você também precisará navegar até a página específica para seu aplicativo usando o seguinte formato de URL: `<url for deployment>/<name of page>.aspx`|

## Atualizar uma cadeia de conexão para Entidades dinâmicas do ASP.NET

### Para atualizar uma cadeia de conexão para Entidades dinâmicas do ASP.NET

1. Para criar um banco de dados SQL do Azure que pode ser usado para um aplicativo Web de Entidades dinâmicas do ASP.NET, siga as etapas no procedimento **Usar um banco de dados SQL do Azure para seu aplicativo** anteriormente neste tópico.

1. Adicione as tabelas e campos de que você precisa para esse banco de dados por meio do Portal de Gerenciamento do Azure.

1. A cadeia de conexão para esse tipo de aplicativo tem o formato a seguir no arquivo web.config:

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=";data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework";"providerName="System.Data.EntityClient"/>
    ```

    Atualize o valor *connectionString* com a cadeia de conexão ADO.NET para seu banco de dados SQL do Azure da seguinte maneira:

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=";Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework";"providerName="System.Data.EntityClient"/>
    ```

1. Para salvar o arquivo web.config com as alterações feitas na cadeia de conexão, na barra de menus, escolha **Arquivo**, **Salvar web.config**.

## Modelos de projeto com suporte

Para publicar um aplicativo Web no Azure, o aplicativo deve usar um dos modelos de projeto para C# ou Visual Basic listados na tabela a seguir.

|Grupo de modelos de projeto|Modelo do projeto|
|---|---|
|Web|Aplicativo Web do ASP.NET|
|Web|Aplicativo Web ASP.NET MVC 2|
|Web|Aplicativo Web ASP.NET MVC 3|
|Web|Aplicativo Web ASP.NET MVC 4|
|Web|Aplicativo Web ASP.NET vazio|
|Web|Aplicativo Web ASP.NET MVC 2 vazio|
|Web|Aplicativo Web de entidades de dados dinâmicos ASP.NET|
|Web|Aplicativo Web de dados dinâmicos ASP.NET Linq do SQL|
|Silverlight|Aplicativo Silverlight|
|Silverlight|Aplicativo de negócios Silverlight|
|Silverlight|Aplicativo de navegação Silverlight|
|WCF|Aplicativo de serviço WCF|
|WCF|Aplicativo de serviço de fluxo de trabalho WCF|
|Fluxo de trabalho|Aplicativo de serviço de fluxo de trabalho WCF|

## Próximas etapas
Para saber mais sobre como publicar, consulte [Configurando os serviços necessários para publicar um serviço de nuvem do Visual Studio](vs-azure-tools-setting-up-services-required-to-publish-a-cloud-service-from-visual-studio.md). Confira também [Configurando credenciais de autenticação nomeadas](vs-azure-tools-setting-up-named-authentication-credentials.md).

<!---HONumber=Sept15_HO3-->