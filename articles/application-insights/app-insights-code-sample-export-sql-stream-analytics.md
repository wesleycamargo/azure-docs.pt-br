<properties 
	pageTitle="Passo a passo: exportar telemetria para o Banco de Dados SQL do Application Insights" 
	description="Codifique sua própria análise de telemetria no Application Insights usando o recurso de exportação contínua." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/13/2015" 
	ms.author="awills"/>
 
# Passo a passo: exportar para SQL do Application Insights usando o Stream Analytics

Este artigo mostra como mover os dados de telemetria do [Visual Studio Application Insights][start] em um banco de dados SQL do Azure usando [Exportação Contínua][export] e [Azure Stream Analytics](http://azure.microsoft.com/services/stream-analytics/).

A exportação contínua move os dados de telemetria no Armazenamento do Azure no formato JSON. Vamos analisar objetos JSON usando o Azure Stream Analytics e criando linhas em uma tabela de banco de dados.

(Normalmente, a Exportação Contínua é a maneira de fazer sua própria análise da telemetria enviada pelos seus aplicativos ao Application Insights. Você pode adaptar este exemplo de código para fazer outras coisas com a telemetria exportada, como agregação de dados e publicação dos dados para o pacote de visualização do PowerBI.)

Vamos começar supondo que você já tenha o aplicativo que você deseja monitorar.


Neste exemplo, usaremos os dados de exibição de página, mas o mesmo padrão pode ser facilmente ampliado para outros tipos de dados como exceções e eventos personalizados.


## Adicionar o SDK do Application Insights

Para monitorar seu aplicativo, [Adicione um SDK do Application Insights][start] a ele. Há diferentes SDKs e ferramentas de auxílio para diferentes plataformas, IDEs e linguagens. Você pode monitorar as páginas da Web, Java ou servidores Web ASP.NET, bem como dispositivos móveis de vários tipos. Todos os SDKs enviam telemetria para o [Portal do Application Insights][portal], no qual você pode usar nossas potentes ferramentas de análise e diagnóstico para exportar os dados para o armazenamento.

Introdução:

1. Obtenha uma [conta no Microsoft Azure](http://azure.microsoft.com/pricing/).
2. No [Portal do Azure][portal], adicione um novo recurso do Application Insights ao seu aplicativo:

    ![Escolha Novo, Serviços de Desenvolvedor, Application Insights e escolha o tipo de aplicativo](./media/app-insights-code-sample-export-sql-stream-analytics/010-new-asp.png)


    (Seu tipo de aplicativo e de assinatura podem ser diferentes.)
3. Abra o Início Rápido para saber como configurar o SDK para seu tipo de aplicativo.

    ![Escolha Início Rápido e siga as instruções](./media/app-insights-code-sample-export-sql-stream-analytics/020-quick.png)

    Se o tipo de aplicativo não estiver listado, dê uma olhada na página [Introdução][start].

4. Neste exemplo, estamos monitorando um aplicativo Web, por isso é possível usar as ferramentas do Azure no Visual Studio para instalar o SDK. Dizemos a ele o nome do nosso recurso do Application Insights:

    ![Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e selecione Adicionar Application Insights. Em Enviar telemetria para, crie um novo recurso ou use um já existente.](./media/app-insights-code-sample-export-sql-stream-analytics/appinsights-d012-addbrown.png)

5. Publicar seu aplicativo e observar os dados de telemetria que aparecem em seu recurso Application Insights.


## Criar armazenamento no Azure

Exportação contínua sempre gera dados para uma conta de armazenamento do Azure, por isso você precisa primeiro criar o armazenamento.

1. Crie uma conta de armazenamento na sua assinatura do [Portal do Azure][portal].

    ![No portal do Azure, escolha Novo, Dados e Armazenamento](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)

2. Criar um contêiner

    ![No novo armazenamento, selecione Contêineres e, em seguida, Adicionar](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)

3. Copie a chave de acesso de armazenamento

    Você precisará dela em breve para configurar a entrada para o serviço do Stream Analytics.

    ![No armazenamento, abra Configurações, Chaves e faça uma cópia da Chave de Acesso Primária](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## Iniciar exportação contínua no armazenamento do Azure

1. No portal do Azure, navegue até o recurso do Application Insights que você criou para seu aplicativo.

    ![Selecione Navegar, Application Insights e o nome do seu projeto.](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)

2. Crie uma exportação contínua.

    ![Escolha as Configurações, Exportação Contínua e Adicionar](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)


    Selecione a conta de armazenamento criada anteriormente:

    ![Definir o destino de exportação](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)
    
    Defina os tipos de eventos que você deseja ver:

    ![Escolher os tipos de evento](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)

Agora relaxe e deixe as pessoas usarem seu aplicativo por um tempo. A telemetria chegará e você verá os gráficos estatísticos no [gerenciador de métricas][metrics] e eventos individuais na [pesquisa de diagnóstico][diagnostic].

E, além disso, os dados serão exportados para o armazenamento, no qual você pode inspecionar o conteúdo. Por exemplo, há um navegador de armazenamento no Visual Studio:


![No Visual Studio, abra o Navegador do Servidor, Azure e Armazenamento](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)

Os eventos são gravados em arquivos blob formato JSON. Cada arquivo pode conter um ou mais eventos. Portanto, gostaríamos de escrever um código para ler os dados de evento e filtrar os campos desejados. Podemos fazer todos os tipos de coisas com os dados, mas nosso plano para hoje é escrever um código para mover os dados para um banco de dados SQL. Isso nos permitirá executar diversas consultas interessantes.

## Criar um Banco de Dados SQL do Azure

Mais uma vez, começando com a sua assinatura no [Portal do Azure][portal], crie o banco de dados (e um novo servidor, a menos que você já tenha um) para o qual você vai gravar os dados.

![Novo, Dados, SQL](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)


Verifique se o servidor de banco de dados permite o acesso aos serviços do Azure:


![Navegar, Servidores, seu servidor, Configurações, Firewall, Permitir Acesso ao Azure](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## Criar uma tabela no banco de dados do Azure SQL

Conecte-se ao banco de dados criado na seção anterior com sua ferramenta de gerenciamento preferida. Neste passo a passo, usaremos as SSMS ([Ferramentas de Gerenciamento do SQL Server](https://msdn.microsoft.com/ms174173.aspx)).

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

Crie uma nova consulta e execute o T-SQL a seguir:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
	[pageName] [nvarchar](max) NOT NULL,
	[viewCount] [int] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlDataPort] [int] NULL,
	[urlDataprotocol] [nvarchar](50) NULL,
	[urlDataHost] [nvarchar](50) NULL,
	[urlDataBase] [nvarchar](50) NULL,
	[urlDataHashTag] [nvarchar](max) NULL,
	[eventTime] [datetime] NOT NULL,
	[isSynthetic] [nvarchar](50) NULL,
	[deviceId] [nvarchar](50) NULL,
	[deviceType] [nvarchar](50) NULL,
	[os] [nvarchar](50) NULL,
	[osVersion] [nvarchar](50) NULL,
	[locale] [nvarchar](50) NULL,
	[userAgent] [nvarchar](max) NULL,
	[browser] [nvarchar](50) NULL,
	[browserVersion] [nvarchar](50) NULL,
	[screenResolution] [nvarchar](50) NULL,
	[sessionId] [nvarchar](max) NULL,
	[sessionIsFirst] [nvarchar](50) NULL,
	[clientIp] [nvarchar](50) NULL,
	[continent] [nvarchar](50) NULL,
	[country] [nvarchar](50) NULL,
	[province] [nvarchar](50) NULL,
	[city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
	[eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

## Criar uma instância do Azure Stream Analytics

No [Portal do Azure Clássico](https://manage.windowsazure.com/), selecione o serviço do Azure Stream Analytics e crie um novo trabalho do Stream Analytics:


![](./media/app-insights-code-sample-export-sql-stream-analytics/37-create-stream-analytics.png)



![](./media/app-insights-code-sample-export-sql-stream-analytics/38-create-stream-analytics-form.png)

Quando o novo trabalho for criado, expanda seus detalhes:

![](./media/app-insights-code-sample-export-sql-stream-analytics/41-sa-job.png)

#### Definir local de blob

Defina a entrada do seu blob de Exportação Contínua:

![](./media/app-insights-code-sample-export-sql-stream-analytics/42-sa-wizard1.png)

Agora, você precisará da Chave de Acesso Primária da sua Conta de Armazenamento, previamente anotada. Defina isso como a chave da conta de armazenamento.

![](./media/app-insights-code-sample-export-sql-stream-analytics/46-sa-wizard2.png)

#### Definir padrão de prefixo de caminho 

![](./media/app-insights-code-sample-export-sql-stream-analytics/47-sa-wizard3.png)

Defina o Formato de Data como AAAA-MM-DD (com traços).

O Padrão de Prefixo de Caminho especifica como o Stream Analytics encontra os arquivos de entrada no armazenamento. Você precisa configurá-lo para corresponder à maneira como a Exportação Contínua armazena os dados. Defina-o assim:

    webapplication27_100000000-0000-0000-0000-000000000000/PageViews/{date}/{time}

Neste exemplo:

* `webapplication27` é o nome do recurso do Application Insights. 
* `1000...` é a chave de instrumentação do recurso do Application Insights. 
* `PageViews` é o tipo de dados que desejamos analisar. Os tipos disponíveis dependem do filtro definido na Exportação Contínua. Examine os dados exportados para ver os outros tipos disponíveis.
* `/{date}/{time}` um padrão escrito literalmente.

Para obter o nome e iKey do seu recurso do Application Insights, abra Essentials na sua página de visão geral ou abra as Configurações.

#### Concluir a configuração inicial

Confirme o formato de serialização:

![Confirme e feche o assistente](./media/app-insights-code-sample-export-sql-stream-analytics/48-sa-wizard4.png)

Feche o assistente e aguarde até que a instalação seja concluída.

## Definir a consulta

Abra a seção de consulta:

![No Stream Analytics, selecione Consulta](./media/app-insights-code-sample-export-sql-stream-analytics/51-query.png)

Substitua a consulta padrão por:

```SQL

    SELECT flat.ArrayValue.name as pageName
	, flat.ArrayValue.count as viewCount
	, flat.ArrayValue.url as url
	, flat.ArrayValue.urlData.port as urlDataPort
	, flat.ArrayValue.urlData.protocol as urlDataprotocol
	, flat.ArrayValue.urlData.host as urlDataHost
	, flat.ArrayValue.urlData.base as urlDataBase
	, flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOuput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Observe que as primeiras propriedades são específicas aos dados de exibição da página. Exportações de outros tipos de telemetria terão propriedades diferentes.

## Configurar a saída para o banco de dados

Selecione SQL como a saída.

![No Stream Analytics, selecione Saídas](./media/app-insights-code-sample-export-sql-stream-analytics/53-store.png)

Especifique o Banco de Dados SQL.


![Preencha os detalhes do seu banco de dados](./media/app-insights-code-sample-export-sql-stream-analytics/55-output.png)

Feche o assistente e aguarde uma notificação de que a saída foi configurada.

## Iniciar o processamento

Inicie o trabalho na barra de ação:

![No Stream Analytics, clique em Iniciar](./media/app-insights-code-sample-export-sql-stream-analytics/61-start.png)

Você pode optar por iniciar o processamento de dados neste momento ou iniciar com dados anteriores. O último é útil se você tiver Exportação Contínua já em execução por um tempo.


![No Stream Analytics, clique em Iniciar](./media/app-insights-code-sample-export-sql-stream-analytics/63-start.png)

Depois de alguns minutos, volte para as Ferramentas de Gerenciamento do SQL Server e observe os dados entrando. Por exemplo, use uma consulta como esta:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## Artigos relacionados

* [Exportar para SQL usando uma função de trabalho](app-insights-code-sample-export-telemetry-sql-database.md)
* [Exportação Contínua no Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 

<!---HONumber=62-->