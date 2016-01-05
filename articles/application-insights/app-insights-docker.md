<properties 
	pageTitle="Monitorar aplicativos do Docker no Application Insights" 
	description="Contadores de desempenho, eventos e exceções do Docker podem ser exibidos no Application Insights, juntamente com a telemetria dos aplicativos contidos." 
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
	ms.date="12/01/2015" 
	ms.author="awills"/>
 
# Monitorar aplicativos do Docker no Application Insights

Os eventos de ciclo de vida e os contadores de desempenho dos contêineres [Docker](https://www.docker.com/) podem ser representados em gráfico no Application Insights. Instale a imagem [Application Insights](app-insights-overview.md) em um contêiner em seu host e ele exibirá os contadores de desempenho para o host, bem como para outras imagens.

Com o Docker, você distribui seus aplicativos em contêineres leves com todas as dependências. Eles serão executados em qualquer máquina host que executa um Mecanismo de Docker.

Ao executar a [imagem do Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) no host do Docker, você obterá estes benefícios:

* A telemetria do ciclo de vida sobre todos os contêineres em execução no host - início, parada e assim por diante.
* Contadores de desempenho para todos os contêineres. CPU, memória, uso da rede e muito mais.
* Se você [instalou o SDK do Application Insights](app-insights-java-live.md) nos aplicativos em execução nos contêineres, toda a telemetria desses aplicativos terá propriedades adicionais que identificam o contêiner e o computador host. Portanto, por exemplo, se você tiver instâncias de um aplicativo em execução em mais de um host, você poderá filtrar facilmente a telemetria do aplicativo pelo host.

![exemplo](./media/app-insights-docker/00.png)


## Configurar seu recurso do Application Insights

1. Entre no [Portal do Microsoft Azure](https://azure.com) e abra o recurso Application Insights para o seu aplicativo; ou [crie um novo](app-insights-create-new-resource.md). 

    *Qual recurso devo usar?* Se os aplicativos que estão em execução em seu host foram desenvolvidos por outra pessoa, você precisará [criar um novo recurso do Application Insights](app-insights-create-new-resource.md). Esse é o local em que você pode exibir e analisar a telemetria. (Selecione “Outro” para o tipo de aplicativo.)

    Mas se você for o desenvolvedor dos aplicativos, esperamos que você tenha [adicionado o SDK do Application Insights](app-insights-java-live.md) a cada um deles. Se eles forem realmente todos os componentes de um único aplicativo de negócios, você poderá configurar todos eles para enviar a telemetria para um recurso e usará esse mesmo recurso para exibir os dados de desempenho e do ciclo de vida do Docker.

    Um terceiro cenário é que você desenvolveu a maioria dos aplicativos, mas está usando recursos separados para exibir a telemetria deles. Nesse caso, você provavelmente também deseja criar um recurso separado para os dados do Docker.

2.	Adicione o bloco do Docker: escolha **Adicionar Bloco**, arraste o bloco do Docker da galeria e clique em **Concluído**.

    ![exemplo](./media/app-insights-docker/03.png)


3. Clique no menu suspenso **Informações gerais** e copie a Chave de Instrumentação. Você usará ela para informar ao SDK para onde enviar seu telemetria.


    ![exemplo](./media/app-insights-docker/02-props.png)

Mantenha essa janela do navegador à mão, pois você voltará a ele em breve para examinar a telemetria.


## Executar o monitor do Application Insights em seu host
 
Agora que você tem algum lugar para exibir a telemetria, configure o aplicativo contido que a coletará e enviará.

1.	Conecte-se ao seu host do Docker. 
2.	Edite a chave de instrumentação nesse comando e, em seguida, execute-a:
 
    ```

    docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
    ```

Apenas uma imagem do Application Insights é necessária por host do Docker. Se o seu aplicativo for implantado em vários hosts do Docker, repita o comando em todos os hosts.

## Atualizar seu aplicativo

Se o seu aplicativo for instrumentado com o [SDK do Application Insights para o Java](app-insights-java-get-started.md), adicione a seguinte linha no arquivo Applicationinsights.xml em seu projeto, sob o elemento `<TelemetryInitializers>`:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Isso adiciona informações do Docker, como o contêiner e a ID de host, a cada item de telemetria enviado do seu aplicativo.

## Exibir sua telemetria

Volte ao recurso do Application Insights no Portal do Azure.

Clique por meio do bloco do Docker.

Em breve, você verá os dados recebidos do aplicativo do Docker, especialmente se tiver outros contêineres em execução em seu mecanismo do Docker.


Estas são algumas das exibições que você pode obter.

### Contadores de desempenho por host, atividade por imagem


![exemplo](./media/app-insights-docker/10.png)


![exemplo](./media/app-insights-docker/11.png)



Clique em qualquer nome de imagem ou host para obter mais detalhes.



Para personalizar o modo de exibição, clique em qualquer gráfico, no título da grade ou use Adicionar Gráfico.

[Saiba mais sobre o Metrics Explorer](app-insights-metrics-explorer.md).

### Eventos de contêiner do Docker


![exemplo](./media/app-insights-docker/13.png)

Para investigar os eventos individuais, clique em [Pesquisar](app-insights-diagnostic-search.md). Pesquise e filtre para localizar os eventos desejados. Clique em qualquer evento para obter mais detalhes.
 
### Exceções por nome do contêiner
 

![exemplo](./media/app-insights-docker/14.png)

### Contexto do Docker adicionado à telemetria do aplicativo

Telemetria da solicitação enviada do aplicativo instrumentado com o SDK do AI, aprimorada com o contexto do Docker:

![exemplo](./media/app-insights-docker/16.png)

Tempo do processador e contadores de desempenho de memória disponíveis, aprimorados e agrupados por nome de contêiner do Docker:


![exemplo](./media/app-insights-docker/15.png)





## Perguntas e respostas

*O que o Application Insights me proporciona que eu não consigo obter com o Docker?*

* Análise detalhada dos contadores de desempenho por contêiner e imagem.
* Integração dos dados de contêiner e do aplicativo em um painel.
* [Exporte a telemetria](app-insights-export-telemetry.md) para uma análise adicional em um banco de dados, no Power BI ou em outro painel.

*Como posso obter a telemetria do próprio aplicativo?*

* Instale o SDK do Application Insights no aplicativo. Saiba como para: [aplicativos Web do Java](app-insights-java-get-started.md) e [aplicativos Web do Windows](app-insights-asp-net.md).

<!---HONumber=AcomDC_1203_2015-->