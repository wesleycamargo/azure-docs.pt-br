<properties 
	pageTitle="Introdução ao Application Insights com Java no Eclipse" 
	description="Use o plug-in Eclipse para adicionar o monitoramento de desempenho e uso a seu site Java com o Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="awills"/>
 
# Introdução ao Application Insights com Java no Eclipse

O SDK do Application Insights envia telemetria por meio do seu aplicativo Web Java para que você possa analisar o uso e o desempenho. O plug-in Eclipse para o Application Insights instala automaticamente o SDK em seu projeto para que você obtenha telemetria já pronta, além de uma API que você pode usar para escrever telemetria personalizada.


## Pré-requisitos

Atualmente, o plug-in funciona para projetos dinâmicos da Web no Eclipse. ([Adicione o Application Insights para outros tipos de projeto Java][java].)

Você precisará de:

* Oracle JRE 1.6 ou posterior
* Uma assinatura do [Microsoft Azure](http://azure.microsoft.com/). (Você pode começar com a [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).)
* [Um IDE do Eclipse para desenvolvedores do Java EE](http://www.eclipse.org/downloads/), Indigo ou posterior.
* Windows 7 ou posterior, ou Windows Server 2008 ou posterior

## Instalar o SDK no Eclipse (uma vez)

Você só precisa fazer isso uma vez por computador. Esta etapa instala um kit de ferramentas que pode então adicionar o SDK para cada projeto Web dinâmico.

1. No Eclipse, clique em Ajuda, depois em Instalar novo Software.

    ![Ajuda, Instalar Novo Software](./media/app-insights-java-eclipse/0-plugin.png)

2. O SDK está em http://dl.windowsazure.com/eclipse, sob Kit de ferramentas do Azure.
3. Desmarque **Contatar todos os sites de atualização...**

    ![Para o SDK do Application Insights, limpe a opção Contatar todos os sites de atualização](./media/app-insights-java-eclipse/1-plugin.png)

Siga as etapas restantes para cada projeto Java.

## Obter uma chave de instrumentação do Application Insights

A análise de uso e o desempenho serão exibidos em um recurso do Azure no portal da Web do Azure. Nesta etapa, você configura um recurso do Azure para seu aplicativo.

1. Faça logon no [Portal do Microsoft Azure](https://portal.azure.com). (Você precisará de uma [assinatura do Azure](http://azure.microsoft.com/).)
2. Criar um novo recurso do Application Insights

    ![Clique em + e escolha Application Insights](./media/app-insights-java-eclipse/01-create.png)
3. Defina o tipo de aplicativo para aplicativo Web Java.

    ![Preencha um nome, escolha o aplicativo Java da Web e clique em Criar](./media/app-insights-java-eclipse/02-create.png)
4. Localize a chave de instrumentação do novo recurso. Você precisará colar isto em seu projeto no Eclipse.

    ![Na visão geral do novo recurso, clique em Propriedades e copie a chave de instrumentação](./media/app-insights-java-eclipse/03-key.png)

## Adicione o SDK ao seu projeto de Java

1. Adicione o Application Insights no menu de contexto do seu projeto Web.

    ![Na visão geral do novo recurso, clique em Propriedades e copie a chave de instrumentação](./media/app-insights-java-eclipse/4-addai.png)
2. Cole a chave de instrumentação que você obteve no portal do Azure.

    ![Na visão geral do novo recurso, clique em Propriedades e copie a chave de instrumentação](./media/app-insights-java-eclipse/5-config.png)


A chave é enviada junto com todos os itens de telemetria e orienta o Application Insights a exibi-los em seu recurso.

## Executar o aplicativo e ver as métricas

Execute seu aplicativo.

Retorne para seu recurso Application Insights no Microsoft Azure.

Dados de solicitações HTTP aparecerão na folha de visão geral. (Se não estiverem lá, aguarde alguns segundos e, em seguida, clique em Atualizar.)

![Falhas, contagens de solicitação e resposta do servidor](./media/app-insights-java-eclipse/5-results.png)
 

Clique em qualquer gráfico para ver métricas mais detalhadas.

![Contagens de solicitação por nome](./media/app-insights-java-eclipse/6-barchart.png)


[Saiba mais sobre métricas.][metrics]

 

Ao exibir as propriedades de uma solicitação, você pode ver os eventos de telemetria associados a ela, como solicitações e exceções.
 
![Todos os rastreamentos para esta solicitação](./media/app-insights-java-eclipse/7-instance.png)


## Telemetria do lado do cliente

Na folha Início Rápido, clique em Obter código para monitorar as minhas páginas da Web:

![Na folha de visão geral de seu aplicativo, escolha Início Rápido, Obter o código para monitorar minhas páginas da Web. Copie o script.](./media/app-insights-java-eclipse/02-monitor-web-page.png)

Insira o trecho de código no título dos arquivos HTML.

#### Exibir dados do lado do cliente

Abra suas páginas da Web atualizadas e use-as. Aguarde um ou dois minutos, retorne ao Application Insights e atualize a folha de uso.

As métricas de sessão, usuário e exibição de página serão exibidas na folha de uso:

![Sessões, usuários e modos de exibição de página](./media/app-insights-java-eclipse/appinsights-47usage-2.png)

[Saiba mais sobre como configurar a telemetria do lado do cliente.][usage]

## Testes de disponibilidade via Web

O Application Insights pode testar seu site em intervalos regulares para verificar ele está operante e respondendo bem. Para configurar, clique no gráfico vazio de testes da Web na folha de visão geral e forneça sua URL pública.

Se seu site ficar inativo, você obterá gráficos de tempos de resposta e também notificações por email.

![Exemplo de teste da Web](./media/app-insights-java-eclipse/appinsights-10webtestresult.png)

[Saiba mais sobre testes de disponibilidade via web.][availability]

## Logs de diagnóstico

Se você estiver usando Logback ou Log4J (v 1.2 ou 2.0) para rastreamento, você pode enviar seus logs de rastreamento automaticamente para o Application Insights, no qual você pode explorá-los e pesquisar o conteúdo deles.

[Saiba mais sobre logs de diagnóstico][javalogs]

## Telemetria personalizada 

Insira algumas linhas de código em seu aplicativo Web Java para descobrir o que os usuários estão fazendo com ele, ou para ajudar a diagnosticar problemas.

Você pode inserir o código tanto no JavaScript da página da Web quanto no Java do lado do servidor.

[Saiba mais sobre a telemetria personalizada][track]



## Próximas etapas

#### Detectar e diagnosticar problemas

* [Adicione telemetria do cliente Web][usage] para obter a telemetria de desempenho do cliente Web.
* [Configure os testes da Web][availability] para certificar-se de manter seu aplicativo operante e responsivo.
* [Pesquise eventos e logs][diagnostic] para ajudar a diagnosticar problemas.
* [Capturar rastreamentos do Log4J ou Logback][javalogs]

#### Acompanhar uso

* [Adicione telemetria do cliente Web][usage] para monitorar modos de exibição de página e métricas de usuário básico.
* [Acompanhe métricas e eventos personalizados][track] para saber mais sobre como o aplicativo é usado, tanto no cliente quanto no servidor.


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=July15_HO4-->