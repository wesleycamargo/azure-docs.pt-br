<properties 
	pageTitle="Definir alertas no Application Insights" 
	description="Receba emails sobre falhas, exceções e alterações de métricas." 
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
	ms.date="08/12/2015" 
	ms.author="awills"/>
 
# Definir alertas no Application Insights

O [Application Insights do Visual Studio][start] pode alertá-lo de alterações em métricas de desempenho ou de uso em seu aplicativo.

O Application Insights monitora seu aplicativo ativo em um [ampla variedade de plataformas][platforms] para ajudá-lo a diagnosticar problemas de desempenho e compreender padrões de uso.

Há dois tipos de alertas:
 
* **Testes da Web** informam quando seu site está indisponível na Internet ou está respondendo lentamente. [Saiba mais][availability].
* **Alertas de métrica** informam quando qualquer métrica ultrapassa um valor limite durante determinado período, como contagens de falhas, memória ou exibições de páginas. 

Há um [página separada sobre testes na Web][availability]; portanto, vamos nos concentrar nos alertas de métricas aqui.

## Alertas de métricas

Se você ainda não configurou o Application Insights para seu aplicativo, [faça isso primeiro][start].

Para receber um email quando uma métrica ultrapassar um limite, inicie no Metrics Explorer ou no bloco de Regras de alerta na folha de visão geral.

![Na folha das Regras de alerta, escolha Adicionar Alerta. Defina seu aplicativo como o recurso a ser medido, forneça um nome para o alerta e escolha uma métrica.](./media/app-insights-alerts/01-set-metric.png)

Defina o recurso antes de outras propriedades. **Escolha o recurso "(componentes)"** se desejar definir alertas em métricas de desempenho ou de uso.

Observe as unidades quando você for solicitado para inserir o valor de limite.

*Não vejo o botão Adicionar Alerta.* Você está usando uma conta organizacional? Você poderá definir alertas se tiver acesso de proprietário ou colaborador a esse recurso de aplicativo. Consulte Configurações -> Usuários. [Saiba mais sobre o controle de acesso][roles].

## Ver seus alertas

Você recebe um email quando o estado de um alerta é mudado de inativo para ativo.

O estado atual de cada alerta é mostrado na folha de regras de Alerta.

O histórico das alterações de estado está no log de Eventos de operações:

![Na folha Visão geral, próximo à parte inferior, clique em 'Eventos da semana anterior'](./media/app-insights-alerts/09-alerts.png)

*Esses "eventos" estão relacionados a eventos de telemetria ou a eventos personalizados?*

* Não. Esses eventos operacionais são apenas um log de eventos que ocorreram nesse recurso de aplicativo. 

## Alertas de disponibilidade

Você pode configurar testes na Web que testam qualquer site de pontos em todo o mundo. [Saiba mais][availability].

## Quais são alguns alertas que é recomendável definir?

Depende de seu aplicativo. Para começar, é melhor não definir um número excessivo de métricas. Passe algum tempo examinando seus gráficos de métricas enquanto seu aplicativo está em execução para ter uma noção de como ele se comporta normalmente. Isso o ajudará a encontrar maneiras de melhorar seu desempenho. Em seguida, configure alertas para informá-lo quando as métricas estiverem fora da zona normal.

Alguns alertas populares são:

* [Testes da web][availability] são importantes se seu aplicativo é um site ou serviço Web que é visível na Internet pública. Eles informam se seu site fica inativo ou responde lentamente, mesmo que haja um problema na operadora, não em seu aplicativo. Porém, eles são testes sintéticos; portanto, não medem a experiência real dos usuários.
* [Métricas de navegador][client], principalmente tempos de carregamento de páginas de navegador, são adequados para aplicativos Web. Se sua página tem vários scripts, convém procurar exceções de navegador. Para obter essas métricas e alertas, você precisa configurar o [monitoramento de página da Web][client].
* Tempo de resposta do servidor e solicitações que falharam para a perspectiva do servidor de aplicativos Web. Além de configurar alertas, confira essas métricas para ver se elas variam de forma desproporcional com altas taxas de solicitação: isso pode indicar que seu aplicativo está ficando sem recursos.



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

 

<!---HONumber=August15_HO7-->