<properties 
	pageTitle="Usando o New Relic com o Azure - guias de recursos do Azure" 
	description="Saiba como usar o serviço New Relic para gerenciar e monitorar seu aplicativo do Azure." 
	services="" 
	documentationCenter=".net" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="stepsic"/>



#Gerenciamento de desempenho do aplicativo New Relic no Azure

Este guia descreve como adicionar o monitoramento de desempenho de classe mundial do New Relic aos seus aplicativos hospedados no Microsoft Azure. Abordaremos o processo simples e rápido para adicionar New Relic ao seu aplicativo e apresentar alguns dos recursos do New Relic. Para obter mais informações sobre como usar o New Relic, consulte [Usando o New Relic](#using-new-relic).

O que é New Relic?
--

O New Relic é uma ferramenta focada no desenvolvedor que monitora seus aplicativos de produção e fornece informações detalhadas sobre seu desempenho e confiabilidade. Ele foi projetado para economizar tempo ao identificar e diagnosticar problemas de desempenho, e ele coloca as informações necessárias para solucionar esses problemas em suas mãos.

O New Relic controla o tempo de carregamento e taxa de transferência da sua transação da web, tanto do servidor quanto dos navegadores dos seus usuários. Ele mostra quanto tempo você gasta no banco de dados, analisa consultas lentas e solicitações da web, fornece monitoramento de tempo de atividade e alertas, faixas exceções de aplicativos e muito mais.

Nova Relíquia especial de preços por meio do armazenamento do Azure
--

Nova Relíquia padrão é gratuito para usuários do Azure
Nova Relíquia Pro é oferecido com base no tamanho da instância para os serviços de nuvem do Azure

Para obter informações sobre preços, consulte a [página do New Relic na Azure Store](http://www.windowsazure.com/pt-br/gallery/store/new-relic/new-relic/).

> [AZURE.NOTE] Preços somente é listada para até 10 instâncias de computação. Para um número maior que 10 entre em contato com o novo Relíquia (sales@newrelic.com) para o preço por volume.

Azure clientes recebem uma assinatura de avaliação de 2 semanas do nova Relíquia Pro ao implantar o agente Relíquia de novo.

Inscrever-se para o New Relic usando a Azure Store
--

Relíquia nova integra-se perfeitamente com funções de funções do Azure da Web e de trabalho.

Para inscrever-se para nova Relíquia diretamente do armazenamento do Azure, siga estas três etapas simples.

### Etapa 1. Inscrever-se por meio do armazenamento do Azure

1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. No painel inferior do portal de gerenciamento, clique em **Novo**.
3. Clique em **Repositório**.
4. Na caixa de diálogo **Escolher um complemento**, selecione **New Relic** e clique em **Próximo**.
5. Na caixa de diálogo **Personalizar complemento**, selecione o plano New Relic que você deseja.
6. Digite um código de promoção, se aplicável.
7. Digite um nome para como o serviço New Relic aparecerá nas configurações do Azure ou use o valor padrão **NewRelic**. O nome deve ser exclusivo na sua lista de itens inscritos da Azure Store.
8. Escolha um valor para a região; por exemplo, **oeste dos EUA**.
9. Clique em **Próximo**.
10. Na caixa de diálogo **Revisar Compra**, revise o plano e as informações sobre preços, bem como os termos legais. Se você concordar com os termos, clique em **Comprar**.
11. Depois que você clicar em **Comprar**, a sua conta New Relic iniciará o processo de criação. Você pode monitorar o status no portal de gerenciamento do Azure.
12. Para recuperar a chave de licença do New Relic, clique em **Valores de saída**. 
13. Copie a chave de licença é exibida. Você precisará inseri-la quando você instala o pacote novo Relíquia Nuget.

### Etapa 2. Instalar o pacote NuGet.

1. Abrir sua solução do Visual Studio ou criar um novo selecionando **arquivo > Novo > projeto**.

	![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2. Se você não tiver um projeto de serviço de nuvem do Azure em sua solução, adicione um mouse em seu aplicativo no Solution Explorer e selecionando **Adicionar projeto de serviço de nuvem do Azure**.

	![Create cloud service](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3. Abra o console do Gerenciador de Pacotes selecionando **Ferramentas > Gerenciador de Pacotes da Biblioteca > Console do Gerenciador de Pacotes**. Defina seu projeto como o projeto padrão na parte superior da janela do Console do Gerenciador de Pacotes.

	![Package manager console](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4. No prompt de comando do Gerenciador de Pacotes, digite  `Install-Package NewRelicWindowsAzure` e pressione **Enter**.

	![install in package manager](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5. No prompt de chave de licença, insira a chave de licença que você recebeu da Azure Store.

	![enter license key](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6. Opcional: No prompt de nome do aplicativo, digite o nome de seu aplicativo da maneira como ele será exibido no painel do New Relic. Ou, use o nome da solução como o padrão.

	![enter application name](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7. A partir do Gerenciador de Soluções, clique com o botão direito do mouse no Projeto de Serviço de Nuvem e selecione **Publicar**.

	![public the cloud project](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)


**Observação:** se esta for a primeira vez que implanta esse aplicativo no Azure, será solicitado que você insira suas 
credenciais do Azure. Para obter mais informações, consulte <a href="/pt-br/develop/net/tutorials/get-started/">Implantando um aplicativo web ASP.NET em um site do Azure</a>.

![publish settings](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### Etapa 3. Conferir o desempenho do seu aplicativo no New Relic.

Para exibir o painel novo Relíquia:

1. No portal do Azure, clique no botão **Gerenciar**.
2. Entrar com seu e-mail do Relíquia nova conta e senha.
3. Na barra de menu New Relic, selecione **Aplicativos > (nome do aplicativo)**.

	O **Monitoring > Visão geral** painel aparece automaticamente.

	![New Relic monitoring dashboard](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png)

	Depois de selecionar um aplicativo a partir da lista no seu **aplicativos** menu, o **visão geral** painel mostra informações atuais de servidor e o navegador do aplicativo.

### <a id="using-new-relic"></a>Usando o nova Relíquia

Depois de selecionar um aplicativo na lista no menu Aplicativos, o painel Visão Geral mostra informações atuais de servidor de aplicativos e do navegador. Para alternar entre dois modos de exibição, clique o **o servidor de aplicativo** ou **navegador** botão.

Além da <a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">Relíquia novo padrão de interface de usuário</a> e <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">painel drill-down</a> funções, o painel de visão geral de aplicativos tem funções adicionais.

<table border="1">
  <thead>
    <tr>
      <th><b>Se você quiser...</b></th>
      <th><b>Faça isso...</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Mostrar informações do painel para o servidor ou navegador de app&#39;s selecionado</td>
       <td>Clique o <b>Aplicativo servidor</b> ou <b>navegador</b> botão.</td>
    </tr>
<tr>
<td>Exibir níveis de limite para o resultado do <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> do seu aplicativo</td>
       <td>Aponte para o resultado de Apdex <b>?<b> icon.</b></b></td>
    </tr>
    <tr>
       <td>Exibir detalhes de Apdex em todo o mundo</td>
       <td>Da exibição do <b>navegador</b>de Overview&#39;s, aponte para qualquer local do mapa de Apdex global. < br / ><b>Dica:</b> Para ir diretamente para o painel de controle <a href="https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard" target="_blank">geografia</a> do app&#39;s selecionado, clique no título <b>Apdex global</b> ou clique em qualquer lugar no mapa Apdex Global.</td>
    </tr>
    <tr>
       <td>Exibir o <a href="https://newrelic.com/docs/applications-dashboards/web-transactions" target="_blank">Web transações</a> painel de controle</td>
       <td>Clique na tabela de transações na Web no painel Visão geral de aplicativos. Ou, para exibir detalhes sobre uma transação web específica (incluindo <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Chave transações</a>), clique em seu nome.</td>
    </tr>
    <tr>
       <td>Modo de exibição de <a href="https://newrelic.com/docs/site/errors" target="_blank">erros</a> painel de controle</td>
       <td>Clique no título do chart&#39;s de taxa de erro no painel de Visão geral dos aplicativos.<br /><b>Dica:</b> Você também pode exibir o painel de Erros em<b>Aplicativos</b>&gt; (seu aplicativo) &gt; Eventos &gt; Erros.</td>
    </tr>
    <tr>
       <td>Exibir detalhes do servidor de app&#39;s</td>
       <td><p>Faça uma das opções a seguir:<p>
        <ul>
          <li>Alternar entre um modo de exibição de tabela dos hosts ou detalhes de métrica de análise de cada host.</li>
          <li>Clique no nome de um server&#39;s individual.</li>
          <li>Aponte para pontuação de Apdex do server&#39;s individual.</li>
          <li>Clique no uso da CPU ou na memória de um server&#39;s individual.</li>
        </ul>
       </p></p></td>
    </tr>
</tbody>
</table>

Abaixo está um exemplo do painel de visão geral de aplicativos quando você selecionar o modo de exibição do navegador.

![Package manager console](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

## Próximas etapas

Confira estes recursos adicionais para obter mais informações:

 * [ Instalação do agente do .NET no Azure](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure): Procedimentos de instalação de agente do .NET do New Relic 
 * [ A Interface de usuário do New Relic](https://newrelic.com/docs/site/the-new-relic-ui): 
Visão geral da nova Relíquia UI, definir direitos de usuário e perfis, usando as funções padrão e detalhes de detalhamento do painel de controle
 * [Visão Geral dos Aplicativos](https://newrelic.com/docs/site/applications-overview): Recursos e funções ao usar o painel de visão geral de aplicativos do New Relic
 * [Apdex](https://newrelic.com/docs/site/apdex): Visão geral de como o Apdex mede a satisfação dos usuários finais com seu aplicativo
 * [Monitoramento do Usuário Real](https://newrelic.com/docs/features/real-user-monitoring): Visão geral de como RUM detalha o tempo que os navegadores de seus usuários levam para carregar suas páginas da Web, de onde eles vêm, e quais navegadores usam
 * [Encontrar Ajuda](https://newrelic.com/docs/site/finding-help): Recursos disponibilizados pelo centro de ajuda on-line do New Relic


<!--HONumber=42-->
