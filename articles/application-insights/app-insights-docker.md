<properties 
	pageTitle="Monitorar um host do Docker no Application Insights" 
	description="Contadores de desempenho, eventos e exceções do host do Docker podem ser exibidos no Application Insights, juntamente com a telemetria dos aplicativos em contêiners." 
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
	ms.date="11/20/2015" 
	ms.author="awills"/>
 
# Monitorar um host do Docker no Application Insights

Os contadores de desempenho e os relatórios de exceção de um host do [Docker](https://www.docker.com/) podem ser representados no Application Insights. Instale o aplicativo [Application Insights](app-insights-overview.md) em um contêiner em seu host e ele exibirá os contadores de desempenho gerais para o host, bem como para outras imagens.

Com o Docker, você distribui seus aplicativos em contêineres leves com todas as dependências. Eles serão executados em qualquer máquina host que executa um Mecanismo de Docker.

O Application Insights pode monitorar o desempenho e a atividade de seu host do Docker e contêineres.

![exemplo](./media/app-insights-docker/00.png)

(E, obviamente, se você for proprietário da fonte de qualquer um dos aplicativos nos contêineres, [instale o SDK do Application Insights](app-insights-java-live) em cada um deles a fim de obter um log detalhado do desempenho e o acompanhamento de uso.)

## Criar um recurso do Application Insights

Um recurso do Application Insights é o local onde você exibirá e analisará os dados. Ele fica hospedado no portal do Microsoft Azure.

1.	Você precisará de uma assinatura do [Microsoft Azure](https://azure.com). (Há uma opção pré-paga na qual não é necessário pagar nada se você não usar os serviços. Além disso, você pode usar a camada gratuita do Application Insights.)
2.	Entre no [Portal do Azure](https://portal.azure.com) e crie um novo recurso do Application Insights. Escolha Outro como o tipo de aplicativo. (Essa opção afeta somente a seleção inicial de gráficos que você vê, e não é muito importante.)

    ![exemplo](./media/app-insights-docker/01-new.png)
3.	Você pode acessar seu novo recurso de monitoramento quando quiser usando Procurar, Tudo. Mas, provavelmente, você também optou por colocar um bloco de seu aplicativo no painel Início, que você verá sempre que acessar o portal.

    Abra o novo recurso agora.
4.	Adicione o bloco de Docker: escolha **Editar**, **Adicionar blocos** e arraste o bloco Docker da galeria. Ele mostrará apenas gráficos vazios no início.

    ![exemplo](./media/app-insights-docker/03.png)

5. Clique na lista suspensa **Essentials** e copie a Chave de Instrumentação. Você usará ela para informar ao SDK para onde enviar seu telemetria.

Mantenha essa janela do navegador à mão, pois você voltará a ele em breve para examinar a telemetria.


## Instale o Application Insights em seu host
 
Agora que você tem algum lugar para exibir a telemetria, configure o aplicativo que a coletará e enviará. 1. Entre em seu host do Docker. 2. Procure a imagem do Microsoft Application Insights no Hub do Docker e leve-a até seu host. 3. Defina a chave de instrumentação:

    docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444

4. Execute a imagem.

## Exibir sua telemetria

Volte ao recurso do Application Insights no Portal do Azure.

Em breve você verá os dados recebidos do contêiner do Docker, especialmente se tiver outros contêineres em execução em seu mecanismo do Docker.

Clique em qualquer um dos gráficos para ver mais detalhes.

Estas são algumas das exibições que você pode obter.

### Contadores de desempenho por host, atividade por imagem


![exemplo](./media/app-insights-docker/10.png)

Clique em qualquer nome de imagem para obter mais detalhes. Para personalizar o modo de exibição, clique em qualquer gráfico, no título da grade ou use Adicionar Gráfico.

[Saiba mais sobre o Metrics Explorer](app-insights-metrics-explorer.md)

## Eventos individuais


![exemplo](./media/app-insights-docker/12.png)

Para investigar os eventos individuais, clique em [Pesquisar](app-insights-diagnostic-search.md). Pesquise e filtre para localizar os eventos desejados. Clique em qualquer evento para obter mais detalhes.
 
## Exceções por nome do contêiner
 

![exemplo](./media/app-insights-docker/14.png)



## Perguntas e respostas

*O que o Application Insights me proporciona que eu não consigo obter com o Docker?*

* Análise detalhada dos contadores de desempenho por contêiner e imagem.
* Integração dos dados de contêiner e do aplicativo em um painel.
* [Exportar a telemetria](app-insights-export-telemetry.md) para análise adicional em um banco de dados, Power BI ou outro painel.

*Como posso obter a telemetria do próprio aplicativo?*

* Instale o SDK do Application Insights no aplicativo. Saiba como para: [aplicativos Web do Java](app-insights-java-get-started.md), [aplicativos web do Windows](app-insights-asp-net.md).

<!---HONumber=AcomDC_1125_2015-->