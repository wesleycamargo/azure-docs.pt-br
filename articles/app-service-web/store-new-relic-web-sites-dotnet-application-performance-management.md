<properties 
	pageTitle="Aplicativo Web do .NET no Serviço de Aplicativo do Azure com o gerenciamento de desempenho de aplicativo New Relic"
	description="Saiba como usar o monitoramento de desempenho do New Relic para aplicativos ASP.NET em execução no Serviço de Aplicativo do Azure."
	services="app-service\web"
	documentationCenter=".net"
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags 
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/30/2015"
	ms.author="stepsic"/>



# Aplicativo Web do .NET no Serviço de Aplicativo do Azure com o gerenciamento de desempenho de aplicativo New Relic

Este guia descreve como adicionar o monitoramento de desempenho de classe mundial do New Relic ao seu aplicativo Web no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Abordaremos o processo simples e rápido para adicionar New Relic ao seu aplicativo e apresentar alguns dos recursos do New Relic. Para obter mais informações sobre como usar o novo Relíquia, consulte [usando o novo Relíquia](#using-new-relic)

## O que há de novo Relíquia?

O New Relic é uma ferramenta focada no desenvolvedor que monitora seus aplicativos de produção e fornece informações detalhadas sobre seu desempenho e confiabilidade. Ele foi projetado para economizar tempo ao identificar e diagnosticar problemas de desempenho, e ele coloca as informações necessárias para solucionar esses problemas em suas mãos.

O New Relic controla o tempo de carregamento e taxa de transferência da sua transação da web, tanto do servidor quanto dos navegadores dos seus usuários. Ele mostra quanto tempo você gasta no banco de dados, analisa consultas lentas e solicitações da web, fornece monitoramento de tempo de atividade e alertas, faixas exceções de aplicativos e muito mais.

## Nova Relíquia especial de preços por meio do armazenamento do Azure

O New Relic Standard é gratuito para usuários do Azure. O New Relic Pro é oferecido em vários pacotes com base no modo de site que você está usando e no tamanho da instância se você estiver usando o modo reservado.

Para obter informações sobre preços, consulte a [Página New Relic no Azure Marketplace](/marketplace/partners/newrelic/newrelic).

> [AZURE.NOTE]Preços somente é listada para até 10 instâncias de computação. Para um número maior que 10, entre em contato com o New Relic (sales@newrelic.com) para obter o preço por volume.

Azure clientes recebem uma assinatura de avaliação de 2 semanas do nova Relíquia Pro ao implantar o agente Relíquia de novo.

Inscrever-se para o New Relic usando o Azure Marketplace
--

O New Relic integra-se perfeitamente com as funções Web, as funções de trabalho e o Serviço de Aplicativo do Azure.

Para se inscrever para o New Relic diretamente do Azure Marketplace, siga estas três etapas simples.

## Etapa 1. Criar uma conta do New Relic

1. Faça logon no [portal de visualização do Azure](https://portal.azure.com) e clique em **Novo** no canto.
3. Clique em **Serviços de desenvolvedor** > **APM do New Relic**.
4. Configurar sua conta do New Relic especificando o seguinte e clique em **Criar**.
	- **Nome**
	- **Camada de preços**
	- **Grupo de recursos**
	- **Assinatura**
	- **Localidade**
	- **Termos legais**

11. Após clicar em **Criar**, a sua conta do New Relic iniciará o processo de criação. Você pode monitorar o status clicando no botão **Notificações**. Depois de criada, a folha da conta do New Relic será exibida.

12. Para recuperar sua chave de licença do New Relic, examine o painel **Essenciais** na parte superior da folha. A instância de aplicativos Web registrará automaticamente essa chave de licença em suas configurações de aplicativo ao integrar seu aplicativo Web à conta do New Relic.

## Etapa 2: Configurar a integração do New Relic ao seu aplicativo Web

1. Abrir a folha do seu aplicativo Web no [Portal de Visualização do Azure](https://portal.azure.com).
2. No menu “...” na parte superior da folha e selecione **Adicionar blocos**.
3. Na guia **Monitoramento**, selecione **Resumo do aplicativo** e arraste-o para onde você deseja que o bloco apareça na folha do aplicativo Web.
4. Clique em Concluir para terminar a adição de blocos.
5. Clique no bloco **Monitoramento de aplicativos** e selecione **Nova Relíquia**.
6. Selecione a conta que você criou na etapa anterior e clique em **OK**. 

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/configure-new-relic-integration.png)

	Assim que o salvamento da operação for concluído, clique em **Todas as configurações** na folha do aplicativo Web e, em seguida, clique em **Configurações do aplicativo**. Você deve ver a configuração **NEWRELIC\_LICENSEKEY** adicionada à seção **Configurações do aplicativo** da folha para dar suporte ao New Relic:

	>[AZURE.NOTE]Pode levar até 30 segundos para que as novas configurações do aplicativo entrem em vigor. Para fazer com que as configurações entrem em vigor imediatamente, reinicie o aplicativo Web.

## Etapa 3: Publicar o aplicativo Web ASP.NET

Usando o Visual Studio, publique seu aplicativo Web. Se você já publicou anteriormente o seu aplicativo Web, publique-o novamente para que a instância de aplicativos Web seja adicionada ao pacote New Relic NuGet necessário para habilitar o monitoramento do New Relic.

## Etapa 4. Conferir o desempenho do seu aplicativo no New Relic.

Para exibir o painel novo Relíquia:

2. Abrir a folha do seu aplicativo Web no [Portal de Visualização do Azure](https://portal.azure.com).
3. Clique em **Monitoramento de aplicativos** > **nome do aplicativo** > **Exibição no New Relic**.

	![](./media/store-new-relic-web-sites-dotnet-application-performance-management/view-new-relic-data.png)

3. Se essa for a primeira vez que você usa a sua conta, configure suas informações de conta.
3. Na barra de menu Novo Relíquia, selecione **aplicativos > (nome do aplicativo)**.

	O **Monitoring > Visão geral** painel aparece automaticamente.

	![Painel de monitoramento do New Relic](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app.png)

	Depois de selecionar um aplicativo a partir da lista no seu **aplicativos** menu, o **visão geral** painel mostra informações atuais de servidor e o navegador do aplicativo.

### <a id="using-new-relic"></a>Usando o New Relic

Depois de selecionar um aplicativo a partir da lista no menu Aplicativos, o painel Visão Geral mostra informações atuais de servidor de aplicativos e do navegador. Para alternar entre dois modos de exibição, clique o **o servidor de aplicativo** ou **navegador** botão.

Além das funções <a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">Interface de usuário padrão do New Relic</a> e <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">Busca detalhada do painel</a>, o painel de Visão geral de aplicativos tem funções adicionais.

<table border="1">
  <thead>
    <tr>
      <th><b>Se você quiser...</b></th>
      <th><b>Faça isso...</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Mostrar informações do painel para o servidor ou o navegador do aplicativo selecionado</td>
       <td>Clique o <b>Aplicativo servidor</b> ou <b>navegador</b> botão.</td>
    </tr>
     <tr>
       <td>Exibir níveis de limite para seu aplicativo <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> pontuação</td>
       <td>Aponte para o resultado de Apdex <b>?<b> icon.</b></b></td>
    </tr>
    <tr>
       <td>Exibir detalhes de Apdex em todo o mundo</td>
       <td>Na exibição <b>Navegador</b> da Visão Geral, aponte em qualquer lugar no mapa de Apdex Global.<br /><b>Dica:</b> Para ir diretamente para o painel <a href="https://newrelic.com/docs/site/geography" target="_blank">Geografia</a> do aplicativo selecionado, clique no título <b>Apdex Global</b> ou clique em qualquer lugar no mapa Apdex Global.</td>
    </tr>
    <tr>
       <td>Exibir o <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">Web transações</a> painel de controle</td>
       <td>Clique na tabela de transações na Web no painel Visão geral de aplicativos. Ou, para exibir detalhes sobre uma transação web específica (incluindo <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Chave transações</a>), clique em seu nome.</td>
    </tr>
    <tr>
       <td>Modo de exibição de <a href="https://newrelic.com/docs/site/errors" target="_blank">erros</a> painel de controle</td>
       <td>Clique no título do gráfico de taxa de Erro no painel Visão geral dos aplicativos.<br /><b>Dica:</b> Você também pode exibir o painel de Erros em <b>Aplicativos</b> > (seu aplicativo) > Eventos > Erros.</td>
    </tr>
    <tr>
       <td>Exibir detalhes do servidor do aplicativo</td>
       <td><p>Faça uma das opções a seguir:<p>
        <ul>
          <li>Alternar entre um modo de exibição de tabela dos hosts ou detalhes de métrica de análise de cada host.</li>
          <li>Clique no nome de um servidor individual.</li>
          <li>Aponte para pontuação de Apdex do servidor individual da lista.</li>
          <li>Clique em um servidor individual da CPU ou memória.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

Abaixo está um exemplo do painel de visão geral de aplicativos quando você selecionar o modo de exibição do navegador.

![Console do Gerenciador de Pacotes](./media/store-new-relic-web-sites-dotnet-application-performance-management/NewRelic_app_browser.png)

## Próximas etapas

Confira estes recursos adicionais para obter mais informações:

 * [Instalando o agente do .NET para sites do Azure](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/azure-websites#manual): os procedimentos de instalação do agente de .NET do New Relic 
 * [Interface do usuário do New Relic](https://newrelic.com/docs/site/the-new-relic-ui): Visão geral da IU do New Relic, definindo direitos de usuário e perfis usando as funções padrão e os detalhes de busca detalhada do painel
 * [Visão geral sobre aplicativos](https://newrelic.com/docs/site/applications-overview): Recursos e funções ao usar o painel de visão geral de aplicativos do New Relic
 * [Apdex](https://newrelic.com/docs/site/apdex): Visão geral de como o Apdex mede a satisfação dos usuários finais com seu aplicativo
 * [Monitoramento real de usuários](https://newrelic.com/docs/features/real-user-monitoring): Visão geral de como o RUM detalha o tempo que leva para os navegadores de seus usuários carregarem as suas páginas da Web, de onde eles vêm e quais navegadores usam
 * [Encontrar Ajuda](https://newrelic.com/docs/site/finding-help): recursos disponíveis por meio do Centro de ajuda online do New Relic

>[AZURE.NOTE]Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a alteração do portal do Azure para o portal de visualização do Azure, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)


[webmatrixwebsite]: web-sites-dotnet-using-webmatrix.md
[vswebsite]: web-sites-dotnet-get-started.md

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png
 

<!----HONumber=August15_HO9-->