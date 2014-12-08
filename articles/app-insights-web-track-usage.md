<properties title="Track usage in web applications with Application Insights" pageTitle="Acompanhe o uso em aplicativos Web" description="Log user activities." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 
# Acompanhe o uso de aplicativos Web

Descubra como o seu aplicativo da Web está sendo usado. Configure a análise de uso e você descobrirá quais páginas os usuários estão visitando, quantos deles retornam e com que frequência eles visitam seu site. Adicione alguns [eventos personalizados e][acompanhamento] de métricas, e você pode analisar detalhadamente os recursos mais populares, os erros mais comuns e ajustar seu aplicativo para ter sucesso com os usuários.

A telemetria é coletada tanto do cliente quanto do servidor. Os dados do cliente são coletados de todos os navegadores modernos e, se sua plataforma é ASP.NET, os dados do servidor também podem ser coletados. (Ela não precisa estar em execução no Azure.) 

* [Configuração da análise de uso da Web](#webclient)
* [Análise de uso](#usage)
* [Contagens de página personalizadas para aplicativos de página única](#spa)
* [Inspecionando eventos das páginas individuais](#inspect)
* [Acompanhamento detalhado com métricas e eventos personalizados](#custom)
* [Vídeo](#video)

## <a name="webclient"></a> Configurando a análise de cliente Web

#### Obtenha um recurso Application Insights no Azure

**Se você estiver desenvolvendo um aplicativo ASP.NET** e você ainda não fez isso, [adicione o Application Insights ao][início] de seu projeto da Web. 

**Se sua plataforma de site não for ASP.NET:** Inscreva-se no [Microsoft Azure](http://azure.com), vá para o [Portal de visualização](https://portal.azure.com) e adicione um recurso Application Insights.

![](./media/appinsights/appinsights-11newApp.png)

(Você pode voltar a ele mais tarde com o botão Procurar.)



#### Adicione nosso script a suas páginas da Web

Em Início Rápido, obtenha o script para páginas da Web.

![](./media/appinsights/appinsights-06webcode.png)

Insira o script antes da tag </head> de cada página que você deseja acompanhar. Se seu site possui uma página mestra, você poderá colocar o script lá. Por exemplo, em um projeto ASP.NET MVC, você deve colocá-lo em View\Shared\_Layout.cshtml

## <a name="usage"></a>Análise de uso

Execute seu site, use-o um pouco para gerar telemetria e aguarde 1 a 2 minutos. Você pode executá-lo com F5 em sua máquina de desenvolvimento ou então implantá-lo em seu servidor.

Na lâmina de visão geral do aplicativo, você verá estes blocos de uso:

![](./media/appinsights/appinsights-47usage.png)

*Não há dados ainda? Clique em **Atualizar** na parte superior da página.*

* **Sessões por navegador**

    Uma *sessão* é um período que começa quando um usuário abre qualquer página em seu site, e termina depois de o usuário não ter enviado nenhuma solicitação da Web por um período de tempo limite de 30 minutos. 

    Clique para aplicar zoom ao gráfico.

* **Visualizações da página principal**

    Mostra a contagem total nas últimas 24 horas.

    Clique no bloco de visualizações da página para obter um histórico mais detalhado.

![](./media/appinsights/appinsights-49usage.png)

Clique em Intervalo de Tempo para ver um histórico maior do que sete dias.

Clique em um gráfico para ver outras medidas além daquelas que você pode exibir.

![](./media/appinsights/appinsights-63usermetrics.png)

> [AZURE.NOTE] Desmarque *todas* as métricas para habilitar todas elas. As métricas só podem ser exibidas em algumas combinações. Quando você seleciona uma métrica, aquelas incompatíveis são desabilitadas.



## <a name="spa"></a> Contagens de página personalizadas para aplicativos de página única

Por padrão, uma contagem de página ocorre todas as vezes que uma nova página é carregada no navegador do cliente.  Por exemplo, você talvez queira contar visualizações de página adicionais. Por exemplo, uma página pode exibir seu conteúdo em guias e você quer contar uma página quando o usuário muda as guias. Ou o código do JavaScript na página pode carregar novo conteúdo sem mudar a URL do navegador. 

Insira uma chamada do JavaScript como essa no ponto apropriado no código do seu cliente:

    appInsights.trackPageView(myPageName);

O nome da página pode conter os mesmos caracteres da URL, mas nada após o "#" ou "?" será ignorado.


## <a name="inspect"></a> Inspecionando os eventos de exibição da página individual

Normalmente a telemetria de exibição da página é analisada pelo Application Insights e você vê somente relatórios cumulativos, calculados sobre todos os seus usuários. Mas para a finalidade de depuração, você também pode olhar para os eventos de exibição da página individua.

Na folha de Pesquisa de Diagnóstico, defina Filtros para Exibição da Página.

![](./media/appinsights/appinsights-51searchpageviews.png)

Selecione qualquer evento para ver mais detalhes.

> [AZURE.NOTE] Se você usar [Pesquisar][diagnóstico], observe que você tem que coincidir palavras inteiras: "Sobr" e "obre" não correspondem a "Sobre", mas "Sobr*" sim. E você não pode iniciar um termo de pesquisa com um curinga. Por exemplo, pesquisar por "*obr" não corresponde a "Sobre". 

> [Saiba mais sobre pesquisa de diagnóstico][diagnóstico]

## <a name="custom"></a> Acompanhamento detalhado com métricas e eventos personalizados

Quer saber o que os usuários fazem com seu aplicativo? Inserindo chamadas em seu código de cliente e servidor, você pode enviar sua própria telemetria ao Application Insights. Por exemplo, você pode descobrir o número de usuários que criam pedidos sem concluí-los ou quais erros de validação ocorrem com mais frequência, ou ainda a pontuação média em um jogo.

[Saiba mais sobre os eventos personalizados e][acompanhamento] de métricas de API.

## <a name="video"></a> Vídeo: Acompanhamento de uso

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> Próximas etapas

[Acompanhe o uso com métricas e eventos personalizados][acompanhe]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



