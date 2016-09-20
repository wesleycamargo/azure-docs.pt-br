<properties
	pageTitle="Monitorar a disponibilidade e a capacidade de resposta de qualquer site da Web | Microsoft Azure"
	description="Configure testes da web no Application Insights. Obtenha alertas se um site fica indisponível ou responde lentamente."
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/07/2016"
	ms.author="awills"/>

# Monitorar a disponibilidade e a capacidade de resposta de qualquer site

Após implantar o aplicativo Web ou site em qualquer servidor, você pode configurar testes na Web para monitorar sua disponibilidade e capacidade de resposta. O [Visual Studio Application Insights](app-insights-overview.md) envia solicitações da Web ao aplicativo a intervalos regulares de pontos em todo o mundo. Ele o alertará se o aplicativo não responder ou responder lentamente.

![Exemplo de teste da Web](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

Você pode configurar testes na Web para qualquer ponto de extremidade HTTP ou HTTPS que for acessível da Internet pública.

Há dois tipos de teste da Web:

* [Teste de ping de URL](#create): um teste simples que você pode criar no Portal do Azure.
* [Teste da web de várias etapas](#multi-step-web-tests): criado no Visual Studio Ultimate ou Visual Studio Enterprise e carregado no portal.

Você pode criar até 10 testes na Web por recurso de aplicativo.

## <a name="create"></a>1. Criar um recurso para os relatórios de teste

Pule esta etapa se você já [configurou um recurso do Application Insights][start] para este aplicativo e deseja ver os relatórios de disponibilidade no mesmo local.

Inscreva-se no [Microsoft Azure](http://azure.com), vá para o [Portal do Azure](https://portal.azure.com) e crie um recurso do Application Insights.

![Novo > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Clique em **Todos os recursos** para abrir a folha de Visão geral do novo recurso.

## <a name="setup"></a>2. Criar um teste de ping de URL

Em seu recurso do Application Insights, procure o bloco de Disponibilidade. Clique para abrir a folha de testes da Web para seu aplicativo e adicione um teste da Web.

![Preencha pelo menos o URL do seu site](./media/app-insights-monitor-web-app-availability/13-availability.png)

- **A URL** deve estar visível na Internet pública. Ela pode incluir uma cadeia de caracteres de consulta&#151. Por exemplo, você pode utilizar um pouco seu banco de dados. Se a URL for resolvida para um redirecionamento, nós a seguiremos, até um máximo de 10 redirecionamentos.
- **Analisar solicitações dependentes**: imagens, scripts, arquivos de estilo e outros recursos da página são solicitados como parte do teste, e o tempo de resposta gravado inclui esses tempos. O teste falhará se todos esses recursos não puderem ser baixados com êxito dentro do tempo limite para o teste inteiro.
- **Habilitar tentativas**: quando o teste falha, ele é repetido após um breve intervalo. Uma falha só será relatada se três tentativas sucessivas falharem. Testes subsequentes são então executados com a frequência de teste normal. A repetição é suspensa temporariamente até o próximo sucesso. Essa regra é aplicada independentemente em cada local de teste. (Recomendamos essa configuração. Em média, aproximadamente 80% das falhas desaparecem na repetição.)
- **Frequência de teste**: define a frequência com que o teste é executado em cada local de teste. Com uma frequência de cinco minutos e cinco locais de teste, seu site é testado em média a cada minuto.
- Os **locais de teste** são os locais por meio dos quais nossos servidores enviam solicitações da Web para sua URL. Escolha dois ou três para que você possa diferenciar problemas no site de problemas da rede. Você pode selecionar até 16 locais.

- **Critérios de sucesso**:

    **Tempo limite do teste**: reduza esse valor para ser alertado sobre respostas lentas. O teste é considerado uma falha se as respostas de seu site não são recebidas dentro desse período. Se você tiver selecionado **Analisar solicitações dependentes**, todas as imagens, arquivos de estilo, scripts e outros recursos dependentes devem ter sido recebidos dentro desse período.

    **Resposta HTTP**: o código de status retornado que é contado como êxito. 200 é o código que indica que uma página da Web normal foi retornada.

    **Correspondência de conteúdo**: uma cadeia de caracteres como "Bem-vindo!" Fazemos o teste para comprovar se ela ocorre em todas as respostas. É necessário que seja uma cadeia de caracteres simples, sem curingas. Lembre-se de que se o conteúdo de sua página for alterado, talvez seja necessário atualizá-lo.


- **Alertas** serão, por padrão, enviados a você se houver falhas em três locais em cinco minutos. Uma falha em um único local provavelmente é um problema de rede, não um problema com seu site. Porém, você pode alterar o limite para ser mais ou menos sensível e também pode alterar a quem os emails devem ser enviados.

    Você pode configurar um [webhook](../azure-portal/insights-webhooks-alerts.md), que é chamado quando um alerta é gerado. (Mas observe que, no momento, os parâmetros de consulta não são passados como Propriedades.)

### Testar mais URLs

Adicione mais testes. Por exemplo, além de testar a página inicial, você pode verificar se o banco de dados está sendo executado testando a URL para uma pesquisa.


## <a name="monitor"></a>3. Ver seus resultados de teste na Web

Depois de 1 a 2 minutos, os resultados aparecem na folha de Teste na Web.

![Resumo dos resultados na lâmina inicial](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

Clique em qualquer barra no gráfico de resumo para obter uma exibição mais detalhada do período de tempo.

Esses gráficos combinam os resultados de todos os testes da Web desse aplicativo.


## <a name="failures"></a>Se você encontrar falhas

Clique em um ponto vermelho.

![Clique em um ponto vermelho](./media/app-insights-monitor-web-app-availability/14-availRedDot.png)

Ou role para baixo e clique em um teste onde você pode ver menos de 100% de êxito.

![Clique em um teste da Web específico](./media/app-insights-monitor-web-app-availability/15-webTestList.png)

Os resultados do teste são abertos.

![Clique em um teste da Web específico](./media/app-insights-monitor-web-app-availability/16-1test.png)

O teste é executado em vários locais&#151. Escolha um local em que os resultados são menores que 100%.

![Clique em um teste da Web específico](./media/app-insights-monitor-web-app-availability/17-availViewDetails.png)


Role para baixo até **Testes com falha** e selecione um resultado.

Clique no resultado para avaliá-lo no portal e ver o motivo da falha.

![Resultado do teste da Web](./media/app-insights-monitor-web-app-availability/18-availDetails.png)


Outra opção é baixar o arquivo de resultado e inspecioná-lo no Visual Studio.


*Parece correto, mas é relatado como uma falha?* Verifique todas as imagens, scripts, folhas de estilos e outros arquivos carregados pela página. Se qualquer um deles falhar, o teste será relatado como falha, mesmo se a página html principal carregar com êxito.



## Testes na Web com diversas etapas

Você pode monitorar um cenário que envolve uma sequência de URLs. Por exemplo, se estiver monitorando um site de vendas, você poderá testar se adicionar itens ao carrinho de compras funciona corretamente.

Para criar um teste de várias etapas, grave o cenário usando o Visual Studio e, em seguida, carregue a gravação no Application Insights. O Application Insights reproduz o cenário em intervalos e verifica as respostas.

Observe que você não pode usar funções codificadas em seus testes: as etapas do cenário devem estar contidas como um script no arquivo .webtest.

#### 1\. Registrar um cenário

Use o Visual Studio Enterprise ou Ultimate para registrar uma sessão da Web.

1. Crie um projeto de teste de desempenho na Web.

    ![No Visual Studio, crie um projeto do modelo de teste de carga e desempenho na Web.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

2. Abra o arquivo .webtest e inicie a gravação.

    ![Abra o arquivo .webtest e clique em Registrar.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)

3. Siga as ações do usuário que você deseja simular em seu teste: abra seu site, adicione um produto ao carrinho e assim por diante. Em seguida, interrompa seu teste.

    ![O gravador de teste da Web é executado no Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    Não crie um cenário longo. Há um limite de 100 etapas e 2 minutos.

4. Edite o teste para:
 - Adicionar validações para verificar o texto recebido e os códigos de resposta.
 - Remover todas as interações supérfluas. Você também pode remover solicitações dependentes de imagens, ou de sites de anúncio ou rastreamento.

    Lembre-se de que você só pode editar o script de teste — não é possível adicionar código personalizado ou chamar outros testes da web. Não insira loops no teste. Você pode usar plug-ins de teste da Web padrão.

5. Execute o teste no Visual Studio para confirmar que ele funciona.

    O executor do teste na Web abre um navegador da Web e repete as ações gravadas por você. Verifique se ele funciona conforme o esperado.

    ![No Visual Studio, abra o arquivo .webtest e clique em Executar.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)


#### 2\. Carregar o teste na Web para o Application Insights

1. No portal do Application Insights, crie um novo teste na Web.

    ![Na folha de testes na Web, selecione Adicionar.](./media/app-insights-monitor-web-app-availability/16-another-test.png)

2. Selecione o teste com várias etapas e carregue o arquivo .webtest.

    ![Selecione teste da Web em várias etapas.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    Defina os locais de teste, a frequência e os parâmetros de alerta da mesma forma que para testes de ping.

Exiba seus resultados de teste quaisquer eventuais falhas do mesmo modo que faria para testes de url única.

Uma razão comum para falha é executar o teste por um período excessivamente longo. Ele não deve ser executado por mais de dois minutos.

Não se esqueça de que todos os recursos de uma página devem carregar corretamente para o teste ter êxito, incluindo scripts, folhas de estilos, imagens e assim por diante.

Observe que o teste na Web deve estar totalmente contido no arquivo .webtest: não é possível usar funções codificadas no teste.


### Conectando a hora e números aleatórios em seu teste de várias etapas

Suponha que você está testando uma ferramenta que obtém dados dependentes de tempo, como estoques de um feed externo. Quando grava seu teste na Web você deve usar horários específicos, definindo-os, todavia, como parâmetros do teste, StartTime e EndTime.

![Um teste da Web com parâmetros.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

Quando você executa o teste, o ideal é que EndTime seja sempre a hora atual e StartTime seja o horário de 15 minutos atrás.

Plug-ins de teste da Web fornecem uma maneira de gerar tempos parametrizados.

1. Adicione um plug-in de teste na Web para cada valor de parâmetro variável desejado. Na barra de ferramentas de teste da Web, escolha **Adicionar Plug-in de Teste na Web**.

    ![Escolha Adicionar plug-ins de teste da Web e selecione um tipo.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    Neste exemplo, usamos duas instâncias do plug-in de Data e Hora. É uma instância de "15 minutos atrás" e outra de "agora".

2. Abra as propriedades de cada plug-in. Atribua um nome e configure-o para usar a hora atual. Para um deles, defina Add Minutes = -15.

    ![Definir nome, Usar hora atual e Adicionar minutos.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)

3. Nos parâmetros de teste na Web, use {{nome do plug-in}} para fazer referência a um nome de plug-in.

    ![No parâmetro de teste, use {{nome do plug-in}}.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Agora, carregue seu teste no portal. Ele usa os valores dinâmicos em todas as execuções do teste.

## Lidando com a entrada

Se os usuários entrarem em seu aplicativo, você terá várias opções para simular entradas para poder testar as páginas por trás da entrada. A abordagem usada dependerá do tipo de segurança fornecida pelo aplicativo.

Em todos os casos, você deve criar uma conta no aplicativo apenas para fins de teste. Se possível, restrinja as permissões da conta de teste para que não haja possibilidade de que os testes na Web afetem usuários reais.

### Senha e nome de usuário simples

Grave um teste na Web da maneira usual. Exclua os cookies primeiro.

### Autenticação de SAML

Use o plug-in do SAML que está disponível para testes na Web.

### Segredo do cliente

Se seu aplicativo tiver uma rota de entrada que envolva um segredo do cliente, use-a. O AAD (Azure Active Directory) é um exemplo de um serviço que fornece uma entrada de segredo do cliente. No AAD, o segredo do cliente é a Chave do Aplicativo.

Aqui está um teste na Web de exemplo de um aplicativo Web que usa uma chave de aplicativo:

![Exemplo de segredo do cliente](./media/app-insights-monitor-web-app-availability/110.png)

1. Obtenha o token do AAD usando o segredo do cliente (AppKey).
2. Extraia o token de portador da resposta.
3. Chame a API usando o token de portador no cabeçalho de autorização.

Verifique se o teste na Web é um cliente real, ou seja, se ele tem seu próprio aplicativo no AAD, e use seu clientId + appkey. O serviço que está sendo testado também tem seu próprio aplicativo no AAD: o URI appID desse aplicativo é refletido no teste na Web no campo "recurso".

### Autenticação Aberta

Um exemplo de autenticação aberta é entrar com sua conta da Microsoft ou do Google. Muitos aplicativos que usam OAuth fornecem a alternativa de segredo do cliente. Portanto, sua primeira tática deve ser investigar essa possibilidade.

Se o teste tiver de entrar usando OAuth, a abordagem geral será:

 * Use uma ferramenta como o Fiddler para examinar o tráfego entre o navegador da web, o site de autenticação e seu aplicativo.
 * Executar duas ou mais entradas usando computadores ou navegadores diferentes ou em longos intervalos (para permitir que os tokens expirem).
 * Ao comparar sessões diferentes, identifique o token passado de volta ao site de autenticação, que será então passado para o servidor de aplicativos após a entrada.
 * Registre um teste na Web usando o Visual Studio.
 * Parametrize os tokens, definindo o parâmetro quando o token for retornado do autenticador e usando-o na consulta ao site. (O Visual Studio tenta parametrizar o teste, mas não parametriza os tokens corretamente.)


## <a name="edit"></a> Editar ou desabilitar um teste

Abra um teste individual para editá-lo ou desabilitá-lo.

![Editar ou desabilitar um teste na Web](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

Talvez você deseje desabilitar testes na Web quando estiver fazendo a manutenção de seu serviço.

## Testes de desempenho

Você pode executar um teste de carga em seu site. Como o teste de disponibilidade, você pode enviar solicitações simples ou solicitações de várias etapas de nossos pontos em todo o mundo. Diferentemente de um teste de disponibilidade, muitas solicitações são enviadas, simulando vários usuários simultâneos.

Na folha de Visão geral, abra **Configurações**, **Testes de Desempenho**. Ao criar um teste, você é convidado a se conectar a ou criar uma conta do Visual Studio Team Services.

Quando o teste for concluído, você verá os tempos de resposta e as taxas de êxito.


## Automação

* [Use os scripts do PowerShell para configurar um teste na Web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/) automaticamente.
* Configure um [webhook](../azure-portal/insights-webhooks-alerts.md), que é chamado quando um alerta é gerado.

## Perguntas? Problemas?

* *Posso chamar o código através do meu teste na Web?*

    Não. As etapas do teste devem estar no arquivo .webtest. E não é possível chamar outros testes da Web nem usar loops. Porém, há vários plug-ins que podem ser úteis.

* *Há suporte para HTTPS?*

    Damos suporte a TLS 1.1 e TLS 1.2.

* *Há diferença entre "testes na Web" e "testes de disponibilidade"?*

    Usamos os dois termos de forma intercambiável.

* *Eu gostaria de usar testes de disponibilidade em nosso servidor interno que é executado por trás de um firewall.*

    Configure o firewall para permitir solicitações dos [endereços IP de agentes de teste Web](app-insights-ip-addresses.md#availability).

* *Falha de carregamento de um teste na Web de várias etapas*

    Há um limite de tamanho de 300 K.

    Não há suporte para loops.

    Não há suporte para referências a outros testes na Web.

    Não há suporte para fontes de dados.

    
* *O teste de várias etapas não foi concluído*

    Há um limite de 100 solicitações por teste.

    O teste será interrompido se for executado por mais de dois minutos.

* *Como executar um teste com certificados de cliente*

    Não há suporte para isso, infelizmente.


## <a name="video"></a>Vídeo

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>Próximas etapas

[Pesquisar logs de diagnóstico][diagnostic]

[Solucionar problemas][qna]

[Endereços IP de agentes de teste Web](app-insights-ip-addresses.md)


<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md

<!---HONumber=AcomDC_0914_2016-->