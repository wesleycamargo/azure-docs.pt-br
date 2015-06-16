<properties 
	pageTitle="Acompanhe o uso em aplicativos Web" 
	description="Log de atividades do usuário." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-09" 
	ms.author="awills"/>
 
# Acompanhe o uso de aplicativos Web

Descubra como o seu aplicativo Web está sendo usado. Configure a análise de uso e você descobrirá quais páginas os usuários estão visitando, quantos deles retornam e com que frequência eles visitam seu site. Adicione algumas [métricas e eventos personalizados][track] e você poderá analisar detalhadamente os recursos mais populares, os erros mais comuns e ajustar seu aplicativo para ter sucesso com os usuários.

A telemetria é coletada tanto do cliente quanto do servidor. Os dados do cliente são coletados de todos os navegadores modernos e, se sua plataforma é ASP.NET, os dados do servidor também podem ser coletados. (Ela não precisa estar em execução no Azure.) 

* [Configurar análise de uso da Web](#webclient)
* [Análise de uso](#usage)
* [Contagens de página personalizadas para aplicativos de página única](#spa)
* [Inspecionando eventos das páginas individuais](#inspect)
* [Acompanhamento detalhado com métricas e eventos personalizados](#custom)
* [Vídeo](#video)

## <a name="webclient"></a> Configurando a análise de cliente Web

#### Obtenha um recurso Application Insights no Azure

**Se você estiver desenvolvendo um aplicativo ASP.NET** e você ainda não fez isso, [adicione o Application Insights ao seu projeto da Web][start].

**Se sua plataforma de site não for ASP.NET:** Increva-se no [Microsoft Azure](http://azure.com), vá para o [Portal de visualização](https://portal.azure.com)e adicione um recurso de Application Insights.

![](./media/app-insights-web-track-usage/appinsights-11newApp.png)

(Você pode voltar a ele mais tarde com o botão Procurar.)



#### Adicione nosso script a suas páginas da Web

Em Início Rápido, obtenha o script para páginas da Web.

![](./media/app-insights-web-track-usage/appinsights-06webcode.png)

Insira o script antes da marca </head> de cada página que você deseja acompanhar. Se seu site possui uma página mestra, você poderá colocar o script lá. Por exemplo, em um projeto ASP.NET MVC, você deve colocá-lo em View\Shared_Layout.cshtml

## <a name="usage"></a>Análise de uso

Execute seu site, use-o um pouco para gerar telemetria e aguarde 1 a 2 minutos. Você pode executá-lo com F5 em seu computador de desenvolvimento ou então implantá-lo em seu servidor.

Na lâmina de visão geral do aplicativo, você verá estes blocos de uso:

![](./media/app-insights-web-track-usage/appinsights-47usage.png)

*Nenhum dado ainda? Clique em **Atualizar** na parte superior da página.*

* **Sessões por navegador**

    Uma *sessão* é um período que começa quando um usuário abre qualquer página em seu site e termina depois do usuário não ter enviado nenhuma solicitação da Web por um período de tempo limite de 30 minutos. 

    Clique para aplicar zoom ao gráfico.

* **Visualizações da página principal**

    Mostra a contagem total nas últimas 24 horas.

    Clique no bloco de visualizações da página para obter um histórico mais detalhado. Para obter um histórico mais longo, você pode alterar o intervalo de tempo do relatório.

![](./media/app-insights-web-track-usage/appinsights-49usage.png)


Clique em um gráfico para ver outras medidas que você pode exibir, ou adicionar um novo gráfico e selecione a métrica que ele exibe.

![](./media/app-insights-web-track-usage/appinsights-63usermetrics.png)

> [AZURE.NOTE] Desmarque *todas* as métricas para habilitar todas elas. As métricas só podem ser exibidas em algumas combinações. Quando você seleciona uma métrica, aquelas incompatíveis são desabilitadas.



## <a name="spa"></a> Contagens de página personalizadas para aplicativos de página única

Por padrão, uma contagem de página ocorre todas as vezes que uma nova página é carregada no navegador do cliente.  Por exemplo, você talvez queira contar visualizações de página adicionais. Por exemplo, uma página pode exibir seu conteúdo em guias e você quer contar uma página quando o usuário muda as guias. Ou o código do JavaScript na página pode carregar novo conteúdo sem mudar a URL do navegador. 

Insira uma chamada do JavaScript como essa no ponto apropriado no código do seu cliente:

    appInsights.trackPageView(myPageName);

O nome da página pode conter os mesmos caracteres da URL, mas nada após o "#" ou "?" será ignorado.


## <a name="inspect"></a> Inspecionando os eventos de exibição da página individual

Normalmente a telemetria de exibição da página é analisada pelo Application Insights e você vê somente relatórios cumulativos, calculados sobre todos os seus usuários. Mas para a finalidade de depuração, você também pode olhar para os eventos de exibição da página individua.

Na folha de Pesquisa de Diagnóstico, defina Filtros para Exibição da Página.

![](./media/app-insights-web-track-usage/appinsights-51searchpageviews.png)

Selecione qualquer evento para ver mais detalhes.

> [AZURE.NOTE] Se você usar [Pesquisar][diagnostic], observe que você tem que coincidir palavras inteiras: "Sobr" e "obre" não correspondem a "Sobre", mas "Sobr*" sim. E você não pode iniciar um termo de pesquisa com um curinga. Por exemplo, pesquisar por "*obr" não corresponde a "Sobre". 

> [Saiba mais sobre pesquisa de diagnóstico][diagnostic]

## <a name="custom"></a> Acompanhamento detalhado com métricas e eventos personalizados

Quer saber o que os usuários fazem com seu aplicativo? Inserindo chamadas em seu código de cliente e servidor, você pode enviar sua própria telemetria ao Application Insights. Por exemplo, você pode descobrir o número de usuários que criam pedidos sem concluí-los ou quais erros de validação ocorrem com mais frequência, ou ainda a pontuação média em um jogo.

[Saiba mais sobre os eventos personalizados e métricas de API][track].

## <a name="video"></a> Vídeo: Acompanhamento de uso

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> Próximas etapas

[Controlar o uso com métricas e eventos personalizados][track]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]





<!--HONumber=35.2-->

<!--HONumber=46--> 
 