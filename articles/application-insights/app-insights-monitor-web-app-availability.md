<properties 
	pageTitle="Monitorar a disponibilidade e a capacidade de resposta de qualquer site" 
	description="Configure testes da web no Application Insights. Obtenha alertas se um site fica indisponível ou responde lentamente." 
	services="application-insights" 
documentationCenter=""
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/27/2015" 
	ms.author="awills"/>
 
# Monitorar a disponibilidade e a capacidade de resposta de qualquer site

Após ter implantado seu aplicativo Web, você pode configurar testes na Web para monitorar sua disponibilidade e capacidade de resposta. O Application Insights envia solicitações em intervalos regulares por meio de pontos no mundo inteiro, e pode alertá-lo se o seu aplicativo responder lentamente ou simplesmente não responder.

![Web test example](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

Você pode configurar testes na Web para qualquer ponto de extremidade HTTP que for acessível da Internet pública.

*Trata-se de um site do Azure? Apenas [crie o teste na lâmina][azurewebtest] do site.*


1. [Criar um novo recurso?](#create)
1. [Configurar um teste na Web](#setup)
1. [Exibir resultados](#monitor)
2. [Se você encontrar falhas...](#failures)
2. [Testes na Web com diversas etapas](#multistep)
1. [Editar ou desabilitar um teste](#edit)


 [Vídeo](#video)
 [Próximas etapas](#next)

## Configurando um teste na Web

### <a name="create"></a>1. Criar um novo recurso?

Pule esta etapa se você já [configurou um][início] do recurso Application Insights para este aplicativo, e deseja ver os dados de disponibilidade no mesmo local.

Inscreva-se para o [Microsoft Azure](http://azure.com), vá para o [Portal de visualização](https://portal.azure.com) e crie um novo recurso do Application Insights. 

![New > Application Insights](./media/app-insights-monitor-web-app-availability/appinsights-11newApp.png)

### <a name="setup"></a>2. Crie um teste na Web

Na lâmina visão geral do seu aplicativo, clique no bloco de testes na Web. 

![Click the empty availability test](./media/app-insights-monitor-web-app-availability/appinsights-12avail.png)

*Já obteve alguns testes na Web? Clique no bloco de testes na Web e selecione Adicionar teste na Web.*

Configure os detalhes do teste.

![Fill at least the URL of your website](./media/app-insights-monitor-web-app-availability/appinsights-13availChoices.png)

- **A URL** deve estar visível na Internet pública. Ela pode incluir uma cadeia de caracteres de consulta - por exemplo, você poderia utilizar um pouco seu banco de dados. Se a URL for resolvida para um redirecionamento nós a seguiremos, até um máximo de 10 redirecionamentos.

- **Os locais de teste** são os locais por meio dos quais nossos servidores enviam solicitações da Web para sua URL. Escolha dois ou três para que você possa diferenciar problemas no site de problemas da rede. Não é possível selecionar mais de três.

- **Critérios de sucesso**:
    **Códigos de retorno HTTP**: 200 é normal. 

    **Cadeia de caracteres de correspondência de** conteúdo, como "Bem-vindo!" Faremos o teste para comprovar se ela ocorre em todas as respostas. É necessário que seja uma cadeia de caracteres simples, sem curingas. Lembre-se de que se o conteúdo de sua página for alterado, talvez seja necessário atualizá-lo.

- **Alertas**: Por padrão, alertas são enviados a você se houver falhas repetidas por 15 minutos. Você pode alterar a configuração para torná-la mais ou menos sensível, e pode alterar também os endereços de email para notificação.

#### Testar mais URLs

Você pode adicionar mais testes para quantas URLs quiser. Por exemplo, além de testar a página inicial, você poderia certificar-se de que o banco de dados está sendo executado testando a URL para uma pesquisa.

![On the web tests blade, choose Add](./media/app-insights-monitor-web-app-availability/appinsights-16anotherWebtest.png)


### <a name="monitor"></a>3. Exibir relatórios de disponibilidade

Depois de 1 a 2 minutos, clique em Atualizar na lâmina visão geral. (Nesta versão, ela não é atualizada automaticamente.)

![Summary results on the home blade](./media/app-insights-monitor-web-app-availability/appinsights-14availSummary.png)

O gráfico na lâmina visão geral combina os resultados para todos os testes na Web desse aplicativo.

#### Componentes de página

Imagens, folhas de estilo e scripts, além de outros componentes estáticos, são solicitados como parte do teste.  

O tempo de resposta gravado é o tempo transcorrido até que o carregamento de todos os componentes tenha sido concluído.

Se algum componente não for carregado, o teste será marcado com falha.

## <a name="failures"></a>Se você encontrar falhas...

Clique na lâmina testes na Web para ver os resultados separados para cada teste.

Abra um teste na Web específico.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/appinsights-15webTestList.png)

Role para baixo até  **Testes com falha** e selecione um resultado.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/appinsights-17-availViewDetails.png)

O resultado mostra o motivo da falha.

![Webtest run result](./media/app-insights-monitor-web-app-availability/appinsights-18-availDetails.png)

Para obter mais detalhes, baixe o arquivo de resultado e inspecione-o no Visual Studio.



##<a name="multistep"></a>Testes na Web de várias etapas

Você pode monitorar um cenário que envolve uma sequência de URLs. Por exemplo, se você estiver monitorando um site de vendas, você poderá testar se adicionar itens ao carrinho de compras funciona corretamente. 

Para criar um teste de várias etapas, grave o cenário usando o Visual Studio e, em seguida, carregue a gravação no Application Insights. O Application Insights reproduzirá o cenário em intervalos e verificará as respostas.

#### 1. Registrar um cenário

Use o Visual Studio Ultimate para registrar uma sessão da Web.

1. Crie um projeto de teste de desempenho na Web.
    ![In Visual Studio, create a new project from the Web Performance and Load Test template.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)
2. Abra o arquivo .webtest e inicie a gravação.
    ![Open the .webtest file and click Record.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Siga as ações do usuário que você deseja simular em seu teste: abra seu site, adicione um produto ao carrinho e assim por diante. Em seguida, interrompa seu teste. 
    ![The web test recorder runs in Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)
    Não crie um cenário longo. Há um limite de 100 etapas e 2 minutos.
4. Execute o teste no Visual Studio para confirmar que ele funciona.
    O executor do teste na Web abre um navegador da Web e repete as ações gravadas por você. Verifique se que ele funciona conforme o esperado. 
    ![In Visual Studio, open the .webtest file and click Run.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)
 

(Não insira loops em seu código de teste na Web.)

#### 2. Carregar o teste na Web para o Application Insights

No portal do Application Insights, crie um novo teste na Web.

![On the web tests blade, choose Add.](./media/app-insights-monitor-web-app-availability/appinsights-16anotherWebtest.png)

Selecione o teste com várias etapas e carregue o arquivo .webtest.

![Select multi-step webtest.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

Visualize seus resultados do teste quaisquer eventuais falhas do mesmo modo que faria para testes de url única. 

Uma razão comum para falha é executar o teste por um período excessivamente longo. Ele não deve ser executado por mais de dois minutos.


### Conectando a hora e números aleatórios em seu teste de várias etapas

Suponha que você está testando uma ferramenta que obtém dados dependentes de tempo, como estoques por meio de um feed externo. Quando grava seu teste na Web você deve usar horários específicos, definindo-os todavia como parâmetros do teste, StartTime e EndTime.

![A web test with parameters.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

Quando você executa o teste, o ideal é que EndTime seja sempre a hora atual e StartTime seja o horário de 15 minutos atrás.

Plug-ins de teste na Web fornecem uma maneira de fazer isso.

1. Adicione um plug-in de teste na Web para cada valor de parâmetro variável desejado. Na barra de ferramentas de teste da web, escolha **Adicionar Plugin de Teste na Web**.

    ![Choose Add Web Test Plugin and select a type.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    Neste exemplo, vamos usar duas instâncias do plug-in Data e Hora. Uma instância é para "15 minutos atrás" e outra é para "agora". 

2. Abra as propriedades de cada plug-in. Dê a ele um nome e configure-o para usar a hora atual. Para um deles, defina Add Minutes = -15.

    ![Set name, Use Current Time, and Add Minutes.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)

3. Nos parâmetros de teste na Web, use {{nome do plug-in}} para fazer referência a um nome de plug-in.

    ![In the test parameter, use {{plug-in name}}.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Agora, carregue seu teste no portal. Ele usará os valores dinâmicos em todas as execuções do teste.

## <a name="edit"></a> Editar ou desabilitar um teste

Abra um teste individual para editá-lo ou desabilitá-lo.

![Edit or disable a web test](./media/app-insights-monitor-web-app-availability/appinsights-19-availEdit.png)

Talvez você deseje desabilitar testes na Web quando estiver fazendo a manutenção de seu serviço.

## <a name="video"></a>Vídeo

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>Próximas etapas

[Pesquise por logs de diagnóstico][diagnóstico]

[Solução de problemas][qna]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]




[azurewebtest]: ../insights-create-web-tests/

<!--HONumber=46--> 
 