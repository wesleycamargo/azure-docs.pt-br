<properties 
	pageTitle="Application Insights: Detecção de anomalias pró-ativa" 
	description="O Application Insights executa uma análise profunda de telemetria do seu aplicativo e o avisará sobre possíveis problemas de desempenho." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/02/2015" 
	ms.author="awills"/>

#  Application Insights: alertas proativos

*O Application Insights está em modo de visualização.*


O Application Insights executa uma análise profunda de telemetria do seu aplicativo e o avisará sobre possíveis problemas de desempenho. Provavelmente você está lendo este artigo porque recebeu um dos nossas alertas proativos por email.

## Sobre o alerta proativo

* *Por que eu recebei esse alerta?*
 * O Application Insights analisa periodicamente suas regras de reconhecimento padrão de utilização dos dados. Ele procura por anomalias que possam indicar problemas de desempenho em seu aplicativo.
* *A notificação significa que, definitivamente, tenho um problema?*
 * Não. É simplesmente uma sugestão sobre algo que talvez você queira examinar mais detalhadamente. 
* *O que devo fazer?*
 * [Examine os dados apresentados](#responding-to-an-alert) e considere se os mesmos podem representar um problema. Caso contrário, está tudo bem.
* *Então, vocês examinam os meus dados?*
 * Não. O serviço é totalmente automático. Somente você recebe as notificações. Os dados são [privados](app-insights-data-retention-privacy.md).


## O processo de detecção

* *Quais tipos de anomalias são detectadas?*
 * Padrões que você acharia demorado verificar por conta própria. Por exemplo, um baixo desempenho em uma combinação específica de local, hora do dia e plataforma.
* *Posso criar minhas próprias regras de detecção de anomalias?*
 * Ainda não. Mas você pode:
 * [Configurar alertas](app-insights-alerts.md) que informam quando uma métrica exceder um limite).
 * [Exportar telemetria](app-insights-export-telemetry.md) para um [banco de dados](app-insights-code-sample-export-sql-stream-analytics.md) ou [para o PowerBI](app-insights-export-power-bi.md) ou para [outras](app-insights-code-sample-export-telemetry-sql-database.md) ferramentas, onde será possível analisá-los por conta própria.
* *Com que frequência a análise é executada?*
 * Não fazemos análise em um recurso de aplicativo que não possui muita telemetria. É pouco provável que você receba avisos sobre sessões de depuração.


## Respondendo a um alerta

Abra o relatório de anomalias no email ou na lista de anomalias.

![](./media/app-insights-anomaly/02.png)

Aviso:

* A descrição
* A declaração de impacto, que informa quantos ou com que frequência os usuários são afetados.

Clique em um gráfico para abrir uma folha com mais detalhes.

Modifique o intervalo de tempo e os filtros para explorar a telemetria.

## É ótimo receber estes alertas. Mas o que posso fazer para melhorar o desempenho?

As respostas lentas e com falha são uma das maiores frustrações para os usuários do site, como você já deve saber por experiência própria. Então é importante resolver os problemas.

### Triagem

Primeiro, isto é importante? Se uma página sempre for carregada lentamente, mas apenas 1% dos usuários do site tiverem de vê-la, talvez haja coisas mais importantes a considerar. Por outro lado, se apenas 1% dos usuários abrir essa página, mas ela lançar exceções todas as vezes, talvez valha a pena investigar.

Use a instrução de impacto no email como um guia geral, mas lembre-se de que isso não é tudo. Obtenha outras evidências para confirmar.

Considere os parâmetros do problema. Se for dependente da geografia, configure [testes de disponibilidade](app-insights-monitor-web-app-availability.md) incluindo a região: talvez a área esteja simplesmente enfrentando problemas de rede.

### Diagnosticar carregamentos lentos de página 

Onde está o problema? O servidor responde lentamente, a página é muito longa ou o navegador precisa trabalhar muito para exibi-la?

Abra a folha Métrica de navegadores. A [exibição segmentada do tempo de carregamento da página do navegador](app-insights-javascript.md#explore-your-data) mostra onde o tempo está sendo gasto.

* Se o **Tempo de Envio da Solicitação** for alto, o servidor estará respondendo lentamente ou a solicitação será uma publicação com uma grande quantidade de dados. Examine as [métricas de desempenho](app-insights-web-monitor-performance.md#metrics) para investigar os tempos de resposta. 
* Configure o [rastreamento de dependências](app-insights-dependencies.md) para verificar se a lentidão ocorre devido a serviços externos ou ao seu banco de dados.
* Se **Recebendo Resposta** for predominante, sua página e seus componentes dependentes, JavaScript, CSS, imagens e assim por diante (mas não os dados carregados de forma assíncrona) serão longos. Configure um [teste de disponibilidade](app-insights-monitor-web-app-availability.md) e não se esqueça de definir a opção para carregar os componentes dependentes. Ao obter alguns resultados, abra os detalhes de um resultado e amplie-os para ver os tempos de carregamento de arquivos diferentes.
* Um **Tempo de Processamento do Cliente** alto sugere que os scripts estão sendo executados lentamente. Se o motivo não for óbvio, considere a adição de um código de tempo e envie os tempos em chamadas trackMetric.

### Aprimorar páginas lentas

Há muitos conselhos na Web sobre como melhorar os tempos de resposta de seu servidor e de carregamento da página, portanto não os repetiremos aqui. Veja algumas dicas as quais você provavelmente já conhece, apenas para ajudá-lo a pensar:

* Carregamento lento devido a arquivos grandes: carregue os scripts e outros componentes de forma assíncrona. Use o agrupamento de script. Divida a página principal em widgets que carregam os dados separadamente. Não envie o antigo HTML simples para tabelas longas: use um script para solicitar os dados como JSON ou outro formato compacto e preencha a tabela. Há estruturas excelentes para ajudar você com tudo isso. (Que também envolvem scripts grandes, é claro).
* Diminuir as dependências do servidor: considere os locais geográficos de seus componentes. Por exemplo, se você estiver usando o Azure, verifique se o servidor Web e o banco de dados estão na mesma região. As consultas recuperam mais informações do que o necessário? O armazenamento em cache ou o envio em lote ajudaria?
* Problemas de capacidade: examine as métricas do servidor relacionadas aos tempos de resposta e contagens de solicitação. Se os tempos de resposta apresentarem picos desproporcionais, com picos nas contagens de solicitação, é provável que seus servidores estejam alongados. 


## Emails de notificação

* *É necessário assinar este serviço para receber notificações?*
 * Não. Nosso bot periodicamente pesquisa os dados de todos os usuários do Application Insights e envia notificações se detectar problemas.
* *Posso cancelar a assinatura ou ter as notificações enviadas para meus colegas em vez disso?*
 * Clique no link que está no alerta ou no email. Abra as configurações de anomalias.
 
    ![](./media/app-insights-anomaly/01.png)

    No momento, elas são enviadas para aqueles com [acesso de gravação ao recurso Application Insights](app-insights-resources-roles-access-control.md).
* *Não quero ser inundado com essas mensagens.*
 * Elas são limitados a três por dia. Você não receberá nenhuma mensagem repetida.
* *Se eu não fizer nada, posso receber um lembrete?*
 * Não, você receberá uma mensagem sobre cada problema apenas uma vez.
* *Perdi o email. Onde posso encontrar as notificações no portal?*
 * Na visão geral do Application Insights do seu aplicativo, clique no bloco **Anomalias**. 






 

<!---HONumber=Nov15_HO2-->