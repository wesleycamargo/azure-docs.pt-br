<properties 
	pageTitle="Exemplo de código: Exportar para SQL do Application Insights usando uma função de trabalho" 
	description="Codifique sua própria análise de telemetria no Application Insights usando o recurso de exportação contínua." 
	services="application-insights" 
    documentationCenter=""
	authors="mazharmicrosoft" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="awills"/>
 
# Exemplo de código: Exportar para SQL do Application Insights usando uma função de trabalho

Este artigo mostra como mover seus dados de telemetria do [Visual Studio Application Insights][start] em um banco de dados SQL do Azure usando [Exportação Contínua][export] e um breve trecho de código.

A exportação contínua move a telemetria no armazenamento do Azure no formato JSON, por isso vamos escrever um código para analisar objetos JSON e criar linhas em uma tabela de banco de dados.

(Normalmente, a Exportação Contínua é a maneira de fazer sua própria análise da telemetria enviada pelos seus aplicativos ao Application Insights. Você pode adaptar este exemplo de código para fazer outras coisas com a telemetria exportada.)

Vamos começar supondo que você já tenha o aplicativo que você deseja monitorar.

## Adicionar o SDK do Application Insights

Para monitorar seu aplicativo, [Adicione um SDK do Application Insights][start] a ele. Há diferentes SDKs e ferramentas de auxílio para diferentes plataformas, IDEs e linguagens. Você pode monitorar as páginas da Web, Java ou servidores Web ASP.NET, bem como dispositivos móveis de vários tipos. Todos os SDKs enviam telemetria para o [Portal do Application Insights][portal], no qual você pode usar nossas potentes ferramentas de análise e diagnóstico para exportar os dados para o armazenamento.

Introdução:

1. Obtenha uma [conta no Microsoft Azure](http://azure.microsoft.com/pricing/).
2. No [Portal do Azure][portal], adicione um novo recurso do Application Insights ao seu aplicativo:

    ![Escolha Novo, Serviços de Desenvolvedor, Application Insights e escolha o tipo de aplicativo](./media/app-insights-code-sample-export-telemetry-sql-database/010-new-asp.png)


    (Seu tipo de aplicativo e de assinatura podem ser diferentes.)
3. Abra o Início Rápido para saber como configurar o SDK para seu tipo de aplicativo.

    ![Escolha Início Rápido e siga as instruções](./media/app-insights-code-sample-export-telemetry-sql-database/020-quick.png)

    Se o tipo de aplicativo não estiver listado, dê uma olhada na página [Introdução][start].

4. Neste exemplo, estamos monitorando um aplicativo Web, por isso é possível usar as ferramentas do Azure no Visual Studio para instalar o SDK. Dizemos a ele o nome do nosso recurso do Application Insights:

    ![No Visual Studio, na caixa de diálogo Novo Projeto, marque Adicionar Application Insights e, em Enviar telemetria para, opte por criar um novo aplicativo ou usar um já existente.](./media/app-insights-code-sample-export-telemetry-sql-database/030-new-project.png)


## Criar armazenamento no Azure

1. Crie uma conta de armazenamento na sua assinatura do [Portal do Azure][portal].

    ![No portal do Azure, escolha Novo, Dados e Armazenamento](./media/app-insights-code-sample-export-telemetry-sql-database/040-store.png)

2. Criar um contêiner

    ![No novo armazenamento, selecione Contêineres e, em seguida, Adicionar](./media/app-insights-code-sample-export-telemetry-sql-database/050-container.png)


## Iniciar exportação contínua no armazenamento do Azure

1. No portal do Azure, navegue até o recurso do Application Insights que você criou para seu aplicativo.

    ![Selecione Navegar, Application Insights e o nome do seu projeto.](./media/app-insights-code-sample-export-telemetry-sql-database/060-browse.png)

2. Crie uma exportação contínua.

    ![Escolha as Configurações, Exportação Contínua e Adicionar](./media/app-insights-code-sample-export-telemetry-sql-database/070-export.png)


    Selecione a conta de armazenamento criada anteriormente:

    ![Definir o destino de exportação](./media/app-insights-code-sample-export-telemetry-sql-database/080-add.png)
    
    Defina os tipos de eventos que você deseja ver:

    ![Escolher os tipos de evento](./media/app-insights-code-sample-export-telemetry-sql-database/085-types.png)

Agora relaxe e deixe as pessoas usarem seu aplicativo por um tempo. A telemetria chegará e você verá os gráficos estatísticos no [gerenciador de métricas][metrics] e eventos individuais na [pesquisa de diagnóstico][diagnostic].

E, além disso, os dados serão exportados para o armazenamento, no qual você pode inspecionar o conteúdo. Por exemplo, há um navegador de armazenamento no Visual Studio:


![No Visual Studio, abra o Navegador do Servidor, Azure e Armazenamento](./media/app-insights-code-sample-export-telemetry-sql-database/087-explorer.png)

Os eventos são gravados em arquivos blob formato JSON. Cada arquivo pode conter um ou mais eventos. Portanto, gostaríamos de escrever um código para ler os dados de evento e filtrar os campos desejados. Podemos fazer todo tipo de coisas com os dados, mas nosso plano para hoje é escrever um código para mover os dados para um banco de dados SQL. Isso nos permitirá executar diversas consultas interessantes.

## Criar um Banco de Dados SQL do Azure

Mais uma vez, começando com a sua assinatura no [Portal do Azure][portal], crie o banco de dados (e um novo servidor, a menos que você já tenha um) para o qual você vai gravar os dados.

![Novo, Dados, SQL](./media/app-insights-code-sample-export-telemetry-sql-database/090-sql.png)


Verifique se o servidor de banco de dados permite o acesso aos serviços do Azure:


![Navegar, Servidores, seu servidor, Configurações, Firewall, Permitir Acesso ao Azure](./media/app-insights-code-sample-export-telemetry-sql-database/100-sqlaccess.png)


## Criar uma função de trabalho 

Agora podemos escrever [código](https://sesitai.codeplex.com/) para analisar o JSON em blobs exportados e criar registros no banco de dados. Como o repositório de exportação e o banco de dados estão no Azure, vamos executar o código em uma função de trabalho do Azure.

Esse código extrai automaticamente quaisquer propriedades que estejam presentes no JSON. Para obter descrições das propriedades, confira [Modelo de exportação de dados](app-insights-export-data-model.md).


#### Criar projeto de função de trabalho

No Visual Studio, crie um novo projeto da função de trabalho:

![Novo Projeto, Visual C#, Nuvem, Serviço de Nuvem do Azure](./media/app-insights-code-sample-export-telemetry-sql-database/110-cloud.png)

![Na caixa de diálogo do novo serviço de nuvem, escolha Visual C#, Função de Trabalho](./media/app-insights-code-sample-export-telemetry-sql-database/120-worker.png)


#### Conecte-se à conta de armazenamento

No Azure, obtenha a cadeia de conexão da sua conta de armazenamento:

![Na Conta de Armazenamento, selecione as Chaves e copie a Cadeia de Conexão Primária](./media/app-insights-code-sample-export-telemetry-sql-database/055-get-connection.png)

No Visual Studio, defina as configurações de função de trabalho com a cadeia de conexão da conta de armazenamento:


![No Gerenciador de Soluções, no projeto do Serviço de Nuvem, expanda Funções e abra sua função de trabalho. Abra a guia Configurações, escolha Adicionar configuração e defina name=StorageConnectionString, type= cadeia de conexão e clique para definir o valor. Defina-o manualmente e cole a cadeia de conexão.](./media/app-insights-code-sample-export-telemetry-sql-database/130-connection-string.png)


#### Pacotes

No Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto de Função de Trabalho e escolha Gerenciar Pacotes NuGet. Pesquise e instale esses pacotes:

 * EntityFramework 6.1.2 ou posterior - Usaremos isso para gerar o esquema da tabela de banco de dados rapidamente, com base no conteúdo do JSON no blob.
 * JsonFx - Usaremos isso para mesclar o JSON com propriedades de classe de C#.

Use essa ferramenta para gerar a classe c# do nosso único documento JSON. Ele requer algumas pequenas alterações como o nivelamento de matrizes JSON em uma única propriedade de C# na coluna única da tabela do banco de dados (ex. urlData\_port)

 * [Gerador de classe C# JSON](http://jsonclassgenerator.codeplex.com/)

## Código 

Você pode colocar esse código em `WorkerRole.cs`.

#### Importações

    using Microsoft.WindowsAzure.Storage;

    using Microsoft.WindowsAzure.Storage.Blob;

#### Recuperar a cadeia de conexão de armazenamento

    private static string GetConnectionString()
    {
      return Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    }

#### Executar o trabalho em intervalos regulares

Substitua o método de execução existente e escolha o intervalo de sua preferência. Ele deve ser pelo menos uma hora porque o recurso de exportação conclui um objeto JSON em uma hora.

    public override void Run()
    {
      Trace.TraceInformation("WorkerRole1 is running");

      while (true)
      {
        Trace.WriteLine("Sleeping", "Information");

        Thread.Sleep(86400000); //86400000=24 hours //1 hour=3600000
                
        Trace.WriteLine("Awake", "Information");

        ImportBlobtoDB();
      }
    }

#### Insira cada objeto JSON como uma linha da tabela


    public void ImportBlobtoDB()
    {
      try
      {
        CloudStorageAccount account = CloudStorageAccount.Parse(GetConnectionString());

        var blobClient = account.CreateCloudBlobClient();
        var container = blobClient.GetContainerReference(FilterContainer);

        foreach (CloudBlobDirectory directory in container.ListBlobs())//Parent directory
        {
          foreach (CloudBlobDirectory subDirectory in directory.ListBlobs())//PageViewPerformance
       	  {
            foreach (CloudBlobDirectory dir in subDirectory.ListBlobs())//2015-01-31
            {
              foreach (CloudBlobDirectory subdir in dir.ListBlobs())//22
              {
                foreach (IListBlobItem item in subdir.ListBlobs())//3IAwm6u3-0.blob
                {
                  itemname = item.Uri.ToString();
                  ParseEachBlob(container, item);
                  AuditBlob(container, directory, subDirectory, dir, subdir, item);
                } //item loop
              } //subdir loop
            } //dir loop
          } //subDirectory loop
        } //directory loop
      }
      catch (Exception ex)
      {
		//handle exception
      }
    }

#### Analisar cada blob

    private void ParseEachBlob(CloudBlobContainer container, IListBlobItem item)
    {
      try
      {
        var blob = container.GetBlockBlobReference(item.Parent.Prefix + item.Uri.Segments.Last());
    
        string json;
    
        using (var memoryStream = new MemoryStream())
        {
          blob.DownloadToStream(memoryStream);
          json = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    
          IEnumerable<string> entities = json.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
    
          recCount = entities.Count();
          failureCount = 0; //resetting failure count
    
          foreach (var entity in entities)
          {
            var reader = new JsonFx.Json.JsonReader();
            dynamic output = reader.Read(entity);
    
            Dictionary<string, object> dict = new Dictionary<string, object>();
    
            GenerateDictionary((System.Dynamic.ExpandoObject)output, dict, "");
    
            switch (FilterType)
            {
              case "PageViewPerformance":
    
              if (dict.ContainsKey("clientPerformance"))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["clientPerformance"])[0], dict, "");
    	        }
    
              if (dict.ContainsKey("context_custom_dimensions"))
              {
                if (dict["context_custom_dimensions"].GetType() == typeof(System.Dynamic.ExpandoObject[]))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["context_custom_dimensions"])[0], dict, "");
                }
              }
    
            PageViewPerformance objPageViewPerformance = (PageViewPerformance)GetObject(dict);
    
            try
            {
              using (var db = new TelemetryContext())
              {
                db.PageViewPerformanceContext.Add(objPageViewPerformance);
                db.SaveChanges();
              }
            }
            catch (Exception ex)
            {
              failureCount++;
            }
            break;
    
            default:
            break;
          }
        }
      }
    }
    catch (Exception ex)
    {
      //handle exception 
    }
    }

#### Preparar um dicionário para cada documento JSON


    private void GenerateDictionary(System.Dynamic.ExpandoObject output, Dictionary<string, object> dict, string parent)
        {
            try
            {
                foreach (var v in output)
                {
                    string key = parent + v.Key;
                    object o = v.Value;

                    if (o.GetType() == typeof(System.Dynamic.ExpandoObject))
                    {
                        GenerateDictionary((System.Dynamic.ExpandoObject)o, dict, key + "_");
                    }
                    else
                    {
                        if (!dict.ContainsKey(key))
                        {
                            dict.Add(key, o);
                        }
                    }
                }
            }
            catch (Exception ex)
            {
      		//handle exception 
    	    }
        }

#### Converter o documento JSON em propriedades do objeto de telemetria de classe C#

     public object GetObject(IDictionary<string, object> d)
        {
            PropertyInfo[] props = null;
            object res = null;

            try
            {
                switch (FilterType)
                {
                    case "PageViewPerformance":

                        props = typeof(PageViewPerformance).GetProperties();
                        res = Activator.CreateInstance<PageViewPerformance>();
                        break;

                    default:
                        break;
                }

                for (int i = 0; i < props.Length; i++)
                {
                    if (props[i].CanWrite && d.ContainsKey(props[i].Name))
                    {
                        props[i].SetValue(res, d[props[i].Name], null);
                    }
                }
            }
            catch (Exception ex)
            {
      		//handle exception 
    	    }

            return res;
        }

#### Arquivo de classe PageViewPerformance gerado fora do documento JSON



    public class PageViewPerformance
    {
    	[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        public Guid Id { get; set; }

        public string url { get; set; }

        public int urlData_port { get; set; }

        public string urlData_protocol { get; set; }

        public string urlData_host { get; set; }

        public string urlData_base { get; set; }

        public string urlData_hashTag { get; set; }

        public double total_value { get; set; }

        public double networkConnection_value { get; set; }

        public double sendRequest_value { get; set; }

        public double receiveRequest_value { get; set; }

        public double clientProcess_value { get; set; }

        public string name { get; set; }

        public string internal_data_id { get; set; }

        public string internal_data_documentVersion { get; set; }

        public DateTime? context_data_eventTime { get; set; }

        public string context_device_id { get; set; }

        public string context_device_type { get; set; }

        public string context_device_os { get; set; }

        public string context_device_osVersion { get; set; }

        public string context_device_locale { get; set; }

        public string context_device_userAgent { get; set; }

        public string context_device_browser { get; set; }

        public string context_device_browserVersion { get; set; }

        public string context_device_screenResolution_value { get; set; }

        public string context_user_anonId { get; set; }

        public string context_user_anonAcquisitionDate { get; set; }

        public string context_user_authAcquisitionDate { get; set; }

        public string context_user_accountAcquisitionDate { get; set; }

        public string context_session_id { get; set; }

        public bool context_session_isFirst { get; set; }

        public string context_operation_id { get; set; }

        public double context_location_point_lat { get; set; }

        public double context_location_point_lon { get; set; }

        public string context_location_clientip { get; set; }

        public string context_location_continent { get; set; }

        public string context_location_country { get; set; }

        public string context_location_province { get; set; }

        public string context_location_city { get; set; }
    }


#### DBcontext para interação de SQL por Entity Framework

	public class TelemetryContext : DbContext
    {
        public DbSet<PageViewPerformance> PageViewPerformanceContext { get; set; }
        public TelemetryContext()
            : base("name=TelemetryContext")
        {
        }
    }

Adicionar a cadeia de conexão do banco de dados com o nome `TelemetryContext` em `app.config`.

## Esquema (somente informações)

Este é o esquema para a tabela que será gerada para a PageView.

> [AZURE.NOTE]Você não precisa executar este script. Os atributos em JSON determinam as colunas na tabela.

    CREATE TABLE [dbo].[PageViewPerformances](
	[Id] [uniqueidentifier] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlData_port] [int] NOT NULL,
	[urlData_protocol] [nvarchar](max) NULL,
	[urlData_host] [nvarchar](max) NULL,
	[urlData_base] [nvarchar](max) NULL,
	[urlData_hashTag] [nvarchar](max) NULL,
	[total_value] [float] NOT NULL,
	[networkConnection_value] [float] NOT NULL,
	[sendRequest_value] [float] NOT NULL,
	[receiveRequest_value] [float] NOT NULL,
	[clientProcess_value] [float] NOT NULL,
	[name] [nvarchar](max) NULL,
	[User] [nvarchar](max) NULL,
	[internal_data_id] [nvarchar](max) NULL,
	[internal_data_documentVersion] [nvarchar](max) NULL,
	[context_data_eventTime] [datetime] NULL,
	[context_device_id] [nvarchar](max) NULL,
	[context_device_type] [nvarchar](max) NULL,
	[context_device_os] [nvarchar](max) NULL,
	[context_device_osVersion] [nvarchar](max) NULL,
	[context_device_locale] [nvarchar](max) NULL,
	[context_device_userAgent] [nvarchar](max) NULL,
	[context_device_browser] [nvarchar](max) NULL,
	[context_device_browserVersion] [nvarchar](max) NULL,
	[context_device_screenResolution_value] [nvarchar](max) NULL,
	[context_user_anonId] [nvarchar](max) NULL,
	[context_user_anonAcquisitionDate] [nvarchar](max) NULL,
	[context_user_authAcquisitionDate] [nvarchar](max) NULL,
	[context_user_accountAcquisitionDate] [nvarchar](max) NULL,
	[context_session_id] [nvarchar](max) NULL,
	[context_session_isFirst] [bit] NOT NULL,
	[context_operation_id] [nvarchar](max) NULL,
	[context_location_point_lat] [float] NOT NULL,
	[context_location_point_lon] [float] NOT NULL,
	[context_location_clientip] [nvarchar](max) NULL,
	[context_location_continent] [nvarchar](max) NULL,
	[context_location_country] [nvarchar](max) NULL,
	[context_location_province] [nvarchar](max) NULL,
	[context_location_city] [nvarchar](max) NULL,
    CONSTRAINT [PK_dbo.PageViewPerformances] PRIMARY KEY CLUSTERED 
    (
     [Id] ASC
    )WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]

    GO

    ALTER TABLE [dbo].[PageViewPerformances] ADD  DEFAULT (newsequentialid()) FOR [Id]
    GO


Para ver esse exemplo em ação, [baixe](https://sesitai.codeplex.com/) o código de trabalho completo, altere as configurações `app.config` e publique a função de trabalho no Azure.


## Artigos relacionados

* [Exportar para SQL usando uma função de trabalho](app-insights-code-sample-export-telemetry-sql-database.md)
* [Exportação Contínua no Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)
* [Exportar modelo de dados](app-insights-export-data-model.md)
* [Mais exemplos e explicações passo a passo](app-insights-code-samples.md)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 

<!---HONumber=August15_HO8-->