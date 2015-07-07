<properties 
	pageTitle="Gerenciar cotas e preços do Application Insights" 
	description="Escolher o plano de preço certo para você" 
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
	ms.date="06/09/2015" 
	ms.author="awills"/>

# Gerenciar cotas e preços do Application Insights

*O Application Insights está em modo de visualização.*

Os [preços][pricing] do [Visual Studio Application Insights][start] baseiam-se no volume de dados por aplicativo. Há uma camada gratuita significativa na qual você obtém a maioria dos recursos com algumas limitações.

Cada recurso Application Insights é cobrado como um serviço separado e contribui para a conta da sua assinatura do Azure.

[Consulte o esquema de preços][pricing].

## Examine o plano de preços e cotas para o recurso do Application Insights

Você pode abrir a folha de Cota + Preços nas Configurações do recurso do aplicativo.

![Escolha Configurações, Cota + Preços.](./media/app-insights-pricing/01-pricing.png)

## Como funciona a cota?

* Em cada mês corrido, o aplicativo pode enviar no máximo quantidade especificada de telemetria ao Application Insights. Consulte o [esquema de preços][pricing] para ver os números reais. 
* A cota depende da camada de preços que você escolheu.
* A cota é contada a partir de meia-noite UTC até o primeiro dia de cada mês.
* O gráfico de pontos de dados mostra o quanto da sua cota foi usada neste mês.
* A cota é medida em *pontos de dados.* Um único ponto de dados é uma chamada para um dos métodos de Rastreamento, seja chamado explicitamente no seu código ou por um dos módulos padrão de telemetria. Cada linha exibida na pesquisa de diagnóstico é um ponto de dados. Cada medida de uma métrica, como um contador de desempenho, é um ponto de dados. 
* *Dados da sessão* não são contados na cota. Isso inclui contagens de usuários, sessões, ambiente e os dados do dispositivo.


## Excedente

Se seu aplicativo enviar mais do que a cota mensal, você pode:

* Pague por dados adicionais. Consulte o [esquema de preços][pricing] para obter detalhes. Você pode escolher essa opção com antecedência. Essa opção não está disponível na faixa de preços Gratuita.
* Atualize sua camada de preços.
* Não faça nada. Dados de sessão continuarão a ser gravados, mas outros dados não aparecerão na pesquisa de diagnóstico ou no Metrics Explorer.

## Avaliação gratuita Premium

Quando você cria um novo recurso do Application Insights pela primeira vez, ele começa na camada Gratuita.

A qualquer momento, você pode alternar para a avaliação Premium gratuita de 30 dias. Isso concede a você os benefícios da camada Premium. Após 30 dias, ela será revertida automaticamente para a camada anterior, a menos que você escolha explicitamente outra camada. Você pode selecionar a camada que você deseja a qualquer momento durante o período de avaliação, mas você ainda terá a avaliação gratuita até o término do período de 30 dias.


## Como minha cota foi usada?

O gráfico na parte inferior da folha dos preços mostra o uso de ponto de dados do aplicativo, agrupados por tipo de ponto de dados.

![Na parte inferior da folha de preços](./media/app-insights-pricing/03-allocation.png)

Clique no gráfico para obter mais detalhes ou percorra os detalhes de um intervalo de tempo.

## Examine a conta da sua assinatura do Azure

Encargos do Application Insights são adicionados à sua conta do Azure. Você pode ver os detalhes da sua conta do Azure cobrar na seção de Cobrança do Portal do Azure ou no [Portal de Cobrança do Azure](https://account.windowsazure.com/Subscriptions).

![No menu lateral, escolha Cobrança.](./media/app-insights-pricing/02-billing.png)




<!--Link references-->


[start]: app-insights-get-started.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=62-->