<properties 
	pageTitle="Exportar para o Power BI do Application Insights" 
	description="Artigos" 
	services="application-insights" 
    documentationCenter=""
	authors="noamben" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/05/2016" 
	ms.author="awills"/>

# Alimentar o Power BI do Application Insights

O [Power BI](http://www.powerbi.com/) é um conjunto de ferramentas de análise de negócios para analisar dados e compartilhar informações. Painéis avançados estão disponíveis em cada dispositivo. Você pode combinar dados de várias fontes, inclusive do [Visual Studio Application Insights](app-insights-overview.md).

Para começar, veja [Exibir os dados do Application Insights no Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/).

Você obtém um painel inicial que pode ser personalizado ao combinar os gráficos do Application Insights com os de outras fontes. Há uma galeria de visualização onde você pode obter mais gráficos e cada um deles possui parâmetros que podem ser definidos.

![](./media/app-insights-export-power-bi/010.png)


Após a importação inicial, o painel e os relatórios continuarão a ser atualizados diariamente. Você pode controlar o agendamento de atualização no conjunto de dados.


**Amostragem.** Se o aplicativo enviar muitos dados e se você estiver usando o SDK do Application Insights para o ASP.NET versão 2.0.0-beta3 ou posterior, o recurso de amostragem adaptável poderá operar e enviar apenas uma porcentagem de sua telemetria. Isso também será verdadeiro se você tiver definido manualmente a amostragem no SDK ou na ingestão. [Saiba mais sobre amostragem.](app-insights-sampling.md)

## Maneiras alternativas de ver dados do Application Insights

* Os [Painéis do Azure que contêm gráficos do Application Insights](app-insights-dashboards.md) podem ser mais apropriados se você não precisar exibir dados que não sejam do Azure. Por exemplo, se você quiser configurar um painel de gráficos do Application Insights que monitore componentes diferentes de um sistema, talvez em conjunto com alguns monitores de serviço do Azure, um painel do Azure é a opção ideal. Por padrão, ele é atualizado com mais frequência. 
* A [exportação contínua](app-insights-export-telemetry.md) copia os dados de entrada para o armazenamento do Azure e, de la, eles podem ser movidos e processados da forma que você desejar.
* A [Análise](app-insights-analytics.md) permite que você execute consultas complexas sobre os dados brutos mantidos pelo Application Insights.


## Criar seu próprio adaptador do Power BI usando o Stream Analytics

O pacote de conteúdo do Power BI para o Application Insights exibe um subconjunto útil de telemetria do aplicativo que provavelmente suprirá suas necessidades. Mas, se você quiser obter um intervalo mais amplo de telemetria do que o fornecido, ou se quiser calcular alguns dados com a telemetria bruta, poderá criar seu próprio adaptador usando o serviço Azure Stream Analytics.

Neste esquema, exportaremos dados do Application Insights para o Armazenamento do Azure. O [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) enviará os dados a partir daí, renomeará e processará alguns dos campos e os enviará por pipe para o Power BI. O Stream Analytics é um serviço que pode filtrar, agregar e executar cálculos em um fluxo contínuo de dados.

![Diagrama de bloco para exportação por meio do SA para PBI](./media/app-insights-export-power-bi/020.png)


>[AZURE.TIP] **Não é necessário seguir os demais procedimentos deste artigo** (usando o Stream Analytics) para ver os dados do Application Insights no Power BI. Há uma maneira muito mais fácil! ([Use o adaptador gratuito](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/). Siga o restante deste artigo somente se o adaptador não fornecer todos os dados desejados ou se você quiser definir suas próprias agregações ou funções aos dados.

### Criar armazenamento no Azure

Exportação contínua sempre gera dados para uma conta de armazenamento do Azure, por isso você precisa primeiro criar o armazenamento.

1. Você já experimentou o [powerpack do Power BI para o Application Insights](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/)? Se for suficiente para suas necessidades, você já não precisará de nada mostrado no restante deste artigo.

2.  Crie uma conta de armazenamento “clássica” na sua assinatura do [portal do Azure](https://portal.azure.com).

    ![No portal do Azure, escolha Novo, Dados e Armazenamento](./media/app-insights-export-power-bi/030.png)

2. Criar um contêiner

    ![No novo armazenamento, selecione Contêineres, clique no bloco Contêineres e, em seguida, Adicionar](./media/app-insights-export-power-bi/040.png)

3. Copie a chave de acesso de armazenamento

    Você precisará dela em breve para configurar a entrada para o serviço do Stream Analytics.

    ![No armazenamento, abra Configurações, Chaves e faça uma cópia da Chave de Acesso Primária](./media/app-insights-export-power-bi/045.png)

### Iniciar exportação contínua no armazenamento do Azure

A [exportação contínua](app-insights-export-telemetry.md) move os dados do Application Insights para o armazenamento do Azure.

1. No portal do Azure, navegue até o recurso do Application Insights que você criou para seu aplicativo.

    ![Selecione Navegar, Application Insights e o nome do seu projeto.](./media/app-insights-export-power-bi/050.png)

2. Crie uma exportação contínua.

    ![Escolha as Configurações, Exportação Contínua e Adicionar](./media/app-insights-export-power-bi/060.png)


    Selecione a conta de armazenamento criada anteriormente:

    ![Definir o destino de exportação](./media/app-insights-export-power-bi/070.png)
    
    Defina os tipos de eventos que você deseja ver:

    ![Escolher os tipos de evento](./media/app-insights-export-power-bi/080.png)

3. Deixe que alguns dados sejam acumulados. Agora relaxe e deixe as pessoas usarem seu aplicativo por um tempo. A telemetria chegará e você verá os gráficos estatísticos no [gerenciador de métricas](app-insights-metrics-explorer.md) e eventos individuais na [pesquisa de diagnóstico](app-insights-diagnostic-search.md).

    E, além disso, os dados serão exportados para seu armazenamento.

4. Inspecione os dados exportados. No Visual Studio, escolha **Exibir/Cloud Explorer** e abra Azure/Armazenamento. (Se você não tiver essa opção de menu, precisará instalar o Azure SDK: abra o diálogo Novo Projeto e abra Visual C#/Nuvem/Obter Microsoft Azure SDK para .NET.)

    ![](./media/app-insights-export-power-bi/04-data.png)

    Anote a parte comum do nome do caminho, que deriva do nome do aplicativo e da chave de instrumentação.

Os eventos são gravados em arquivos blob formato JSON. Cada arquivo pode conter um ou mais eventos. Portanto, gostaríamos de escrever um código para ler os dados de evento e filtrar os campos desejados. Podemos fazer todos os tipos de coisas com os dados, mas nosso plano para hoje é usar o Stream Analytics para redirecionar os dados ao Power BI.

### Criar uma instância do Azure Stream Analytics

No [Portal do Azure Clássico](https://manage.windowsazure.com/), selecione o serviço do Azure Stream Analytics e crie um novo trabalho do Stream Analytics:


![](./media/app-insights-export-power-bi/090.png)



![](./media/app-insights-export-power-bi/100.png)

Quando o novo trabalho for criado, expanda seus detalhes:

![](./media/app-insights-export-power-bi/110.png)


#### Definir local de blob

Defina a entrada do seu blob de Exportação Contínua:

![](./media/app-insights-export-power-bi/120.png)

Agora, você precisará da Chave de Acesso Primária da sua Conta de Armazenamento, previamente anotada. Defina isso como a chave da conta de armazenamento.

![](./media/app-insights-export-power-bi/130.png)

#### Definir padrão de prefixo de caminho 

![](./media/app-insights-export-power-bi/140.png)


**Defina o Formato de Data como AAAA-MM-DD (com traços).**

O Padrão de Prefixo de Caminho especifica como o Stream Analytics encontra os arquivos de entrada no armazenamento. Você precisa configurá-lo para corresponder à maneira como a Exportação Contínua armazena os dados. Defina-o assim:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Neste exemplo:

* `webapplication27` é o nome do recurso do Application Insights **todo em minúsculas**.
* `1234...` é a chave de instrumentação do recurso do Application Insights **sem traços**. 
* `PageViews` é o tipo de dados que você deseja analisar. Os tipos disponíveis dependem do filtro definido na Exportação Contínua. Examine os dados exportados para ver os outros tipos disponíveis e veja o [modelo de exportação de dados](app-insights-export-data-model.md).
* `/{date}/{time}` um padrão escrito literalmente.

> [AZURE.NOTE] Inspecione o armazenamento para garantir que o caminho está certo.

#### Concluir a configuração inicial

Confirme o formato de serialização:

![Confirme e feche o assistente](./media/app-insights-export-power-bi/150.png)

Feche o assistente e aguarde até que a instalação seja concluída.

> [AZURE.TIP] Use o comando Sample para baixar alguns dados. Mantenha-os como exemplo de teste para depurar sua consulta.

### Definir a saída

Agora, selecione seu trabalho e defina a saída.

![Selecione o novo canal, clique em Saídas, Adicionar, Power BI](./media/app-insights-export-power-bi/160.png)

Forneça sua **conta corporativa ou de estudante** para autorizar o Stream Analytics a acessar seu recurso do Power BI. Em seguida, crie um nome para a saída, bem como para a tabela e o conjunto de dados do Power BI de destino.

![Crie três nomes](./media/app-insights-export-power-bi/170.png)

### Definir a consulta

A consulta controla a conversão de entrada para a saída.

![Selecione o trabalho e clique em Consulta. Cole o exemplo a seguir.](./media/app-insights-export-power-bi/180.png)


Use a função Test para verificar se você obteve a saída certa. Atribua a ela os exemplos de dados que você obteve da página de entradas.

#### Consulta para exibir contagens de eventos

Cole esta consulta:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input é o alias que atribuímos à entrada do fluxo
* pbi-output é o alias de saída que definimos
* Usamos [OUTER APPLY GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx) porque o nome do evento está em uma matriz JSON aninhada. Em seguida, o Select seleciona o nome do evento, juntamente com uma contagem do número de instâncias com esse nome no período de tempo. A cláusula [Agrupar Por](https://msdn.microsoft.com/library/azure/dn835023.aspx) agrupa os elementos em períodos de tempo de 1 minuto.


#### Consulta para exibir valores de métricas


```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* Essa consulta detalha a telemetria de métricas para obter a hora do evento e o valor da métrica. Os valores de métrica estão dentro de uma matriz, por isso usamos o padrão OUTER APPLY GetElements para extrair as linhas. "myMetric" é o nome da métrica nesse caso. 

#### Consulta para incluir valores das propriedades de dimensão

```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* Essa consulta inclui os valores das propriedades de dimensão sem a necessidade de ter uma determinada dimensão em um índice fixo na matriz de dimensão.

### Executar o trabalho

Você pode selecionar uma data no passado a partir da qual iniciar o trabalho.

![Selecione o trabalho e clique em Consulta. Cole o exemplo a seguir.](./media/app-insights-export-power-bi/190.png)

Aguarde até o trabalho estar Em execução.

### Ver os resultados no Power BI

Abra o Power BI com sua conta corporativa ou de estudante e selecione o conjunto de dados e a tabela que você definiu como a saída do trabalho do Stream Analytics.

![No Power BI, selecione o conjunto de dados e os campos.](./media/app-insights-export-power-bi/200.png)

Agora você pode usar esse conjunto de dados em relatórios e painéis no [Power BI](https://powerbi.microsoft.com).


![No Power BI, selecione o conjunto de dados e os campos.](./media/app-insights-export-power-bi/210.png)


### Não há dados?

* Verifique se você [definiu o formato de data](#set-path-prefix-pattern) corretamente para AAAA-MM-DD (com traços).


### Vídeo

Noam Ben Zeev mostra como exportar para o Power BI.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## Itens relacionados

* [Exportação contínua](app-insights-export-telemetry.md)
* [Referência de modelo de dados detalhados para os tipos de propriedades e valores.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)
* [Mais exemplos e explicações passo a passo](app-insights-code-samples.md)
 

<!---HONumber=AcomDC_0420_2016-->