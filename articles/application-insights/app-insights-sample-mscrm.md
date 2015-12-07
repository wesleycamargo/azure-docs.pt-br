<properties 
	pageTitle="Passo a passo: monitorar o Microsoft Dynamics CRM com o Application Insights" 
	description="Obtenha a telemetria do Microsoft Dynamics CRM Online usando o Application Insights. Passo a passo da instalação, obtenção de dados, visualização e exportação." 
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
	ms.date="11/17/2015" 
	ms.author="awills"/>
 
# Passo a passo: Ativar a telemetria para o Microsoft Dynamics CRM Online usando o Application Insights

Este artigo mostra como obter dados de telemetria do [Microsoft Dhynamics CRM Online](https://www.dynamics.com/) usando o [Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/). Percorreremos o processo completo de adição de um script do Application Insights ao seu aplicativo, captura de dados e visualização de dados.

>[AZURE.NOTE] [Browse the sample solution](https://dynamicsandappinsights.codeplex.com/).

## Adicionar o Application Insights a uma instância nova ou existente do CRM Online 

Para monitorar seu aplicativo, você adiciona um SDK do Application Insights a ele. O SDK envia a telemetria para o [Portal do Application Insights](https://portal.azure.com), no qual você pode usar nossas potentes ferramentas de análise e diagnóstico ou exportar os dados para o armazenamento.

### Criar um recurso do Application Insights no Azure

1. Obtenha [uma conta no Microsoft Azure](http://azure.com/pricing). 
2. Entre no [Portal do Azure](https://portal.azure.com) e adicione um novo recurso do Application Insights. Este é o local em que os dados serão processados e exibidos.

    ![Clique em +, Serviços de Desenvolvedor, Application Insights.](./media/app-insights-sample-mscrm/01.png)

    Escolha ASP.NET como o tipo de aplicativo.

3. Abra a guia Início Rápido e abra o script de código.

    ![](./media/app-insights-sample-mscrm/03.png)

**Mantenha a página de código aberta** enquanto realiza a próxima etapa na outra janela do navegador. Você precisará do código em breve.

### Criar um recurso da Web em JavaScript no Microsoft Dynamics CRM

1. Abra sua instância do CRM Online e faça logon com privilégios de administrador.
2. Abra o Microsoft Dynamics CRM Configurações, Personalizações, Personalizar o Sistema

    ![](./media/app-insights-sample-mscrm/04.png)
    
    ![](./media/app-insights-sample-mscrm/05.png)


    ![](./media/app-insights-sample-mscrm/06.png)

3. Crie um recurso de JavaScript.

    ![](./media/app-insights-sample-mscrm/07.png)

    Atribua um nome, selecione **Script (JScript)** e abra o editor de texto.

    ![](./media/app-insights-sample-mscrm/08.png)
    
4. Copie o código do Application Insights. Ao copiar, certifique-se de ignorar marcas de script. Consulte a captura de tela abaixo:

    ![](./media/app-insights-sample-mscrm/09.png)

    O código inclui a chave de instrumentação que identifica seu recurso do Application insights.

5. Salve e publique.

    ![](./media/app-insights-sample-mscrm/10.png)

### Formulários de instrumento

1. No Microsoft CRM Online, abra o formulário Conta

    ![](./media/app-insights-sample-mscrm/11.png)

2. Abra as propriedades do formulário

    ![](./media/app-insights-sample-mscrm/12.png)

3. Adicione o recurso da Web de JavaScript que você criou

    ![](./media/app-insights-sample-mscrm/13.png)

    ![](./media/app-insights-sample-mscrm/14.png)

4. Salve e publique as personalizações do formulário.


## Métricas capturadas

Agora você configurou a captura de telemetria do formulário. Sempre que ele for usado, os dados serão enviados ao recurso do Application Insights.

Aqui estão exemplos dos dados que você verá.

#### Integridade do aplicativo

![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

Exceções de navegador:

![](./media/app-insights-sample-mscrm/17.png)

Clique no gráfico para obter mais detalhes:

![](./media/app-insights-sample-mscrm/18.png)

#### Uso

![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### Navegadores

![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### Geolocalização

![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### Solicitação de exibição de página interna

![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## Exemplo de código

[Procurar no código de amostra](https://dynamicsandappinsights.codeplex.com/).

## Power BI

Você pode fazer análises ainda mais profundas se [exportar os dados para o Microsoft Power BI](app-insights-export-power-bi.md).

## Exemplo de Solução para o Microsoft Dynamics CRM

[Veja a solução do exemplo implementada no Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## Saiba mais

* [O que é o Application Insights?](app-insights-overview.md)
* [Application Insights para páginas da Web](app-insights-javascript.md)
* [Mais exemplos e explicações passo a passo](app-insights-code-samples.md)

 

<!---HONumber=AcomDC_1125_2015-->