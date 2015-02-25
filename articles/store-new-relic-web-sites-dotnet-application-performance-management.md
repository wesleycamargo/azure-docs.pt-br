<properties 
	pageTitle="Gerenciamento de desempenho do aplicativo New Relic no Azure" 
	description="Saiba como usar o monitoramento de desempenho do New Relic no Azure." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="stepsic"/>



#Gerenciamento de desempenho do aplicativo New Relic nos sites do Azure

Este guia descreve como adicionar o monitoramento de desempenho de classe mundial do New Relic ao seu site do Microsoft Azure. Abordaremos o processo simples e rápido para adicionar Nova Relíquia ao seu aplicativo e apresentar alguns dos
recursos da Nova Relíquia. Para obter mais informações sobre como usar o New Relic, consulte [Usando o New Relic](#using-new-relic).

O que é New Relic?
--

O New Relic é uma ferramenta focada no desenvolvedor que monitora seus aplicativos de produção e fornece informações detalhadas sobre seu desempenho e confiabilidade. Ele foi projetado para economizar tempo ao identificar e diagnosticar problemas de desempenho, e ele coloca as informações necessárias para solucionar esses problemas em suas mãos.

O New Relic controla o tempo de carregamento e taxa de transferência da sua transação da web, tanto do servidor quanto dos navegadores dos seus usuários. Ele mostra quanto tempo você gasta no banco de dados, analisa consultas lentas e solicitações da web, fornece monitoramento de tempo de atividade e alertas, faixas exceções de aplicativos e muito mais.

Nova Relíquia especial de preços por meio do armazenamento do Azure
--

O New Relic Standard é gratuito para usuários do Azure.
O New Relic Pro é oferecido em vários pacotes com base no modo de site que você está usando e no tamanho da instância se você estiver usando o modo reservado.

Para obter informações sobre preços, consulte a [página do New Relic na Azure Store](http://www.windowsazure.com/pt-br/gallery/store/new-relic/new-relic/).

> [AZURE.NOTE] Preços somente é listada para até 10 instâncias de computação. Para um número maior que 10 entre em contato com o novo Relíquia (sales@newrelic.com) para o preço por volume.

Azure clientes recebem uma assinatura de avaliação de 2 semanas do nova Relíquia Pro ao implantar o agente Relíquia de novo.

Inscrever-se para o New Relic usando a Azure Store
--

O New Relic integra-se perfeitamente com as funções Web, as funções de trabalho e os sites do Azure.

Para se inscrever para o New Relic diretamente da Azure Store, siga estas três etapas simples.

### Etapa 1. Inscrever-se por meio do armazenamento do Azure

1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. No painel inferior do portal de gerenciamento, clique em **Novo**.
3. Clique em **Repositório**.
4. Na caixa de diálogo **Escolher um complemento**, selecione **New Relic** e clique em **Próximo**.
5. Na caixa de diálogo **Personalizar complemento**, selecione o plano New Relic que você deseja.
7. Digite um nome para como o serviço New Relic aparecerá nas configurações do Azure ou use o valor padrão **NewRelic**. O nome deve ser exclusivo na sua lista de itens inscritos da Azure Store.
8. Escolha um valor para a região; por exemplo, **oeste dos EUA**.
9. Clique em **Próximo**.
10. Na caixa de diálogo **Revisar Compra**, revise o plano e as informações sobre preços, bem como os termos legais. Se você concordar com os termos, clique em **Comprar**.
11. Depois que você clicar em **Comprar**, a sua conta New Relic iniciará o processo de criação. Você pode monitorar o status no portal de gerenciamento do Azure.
12. Para recuperar sua chave de licença do New Relic, clique no complemento que você acabou de criar e, em seguida, clique em **Informações de Conexão**. 
13. Copie a chave de licença é exibida. Você precisará inseri-la quando você instala o pacote novo Relíquia Nuget.

### Etapa 2. Instalar o pacote do New Relic

O agente de sites do New Relic é distribuído como um pacote do NuGet, que pode ser adicionado ao seu site usando o Visual Studio ou o WebMatrix. Se você não estiver familiarizado com o uso do Visual Studio ou do WebMatrix com um Website do Azure, consulte [Implantando um aplicativo Web ASP.NET a um Website do Azure usando o Visual Studio ][vswebsite] ou [Desenvolver e implantar um Website com o Microsoft WebMatrix][webmatrixwebsite].

Execute as etapas a seguir para o ambiente de desenvolvimento específico que você está usando:

**Visual Studio**

1. Abra sua solução de Site do Visual Studio.

2. Abra o console do Gerenciador de Pacotes selecionando **Ferramentas > Gerenciador de Pacotes da Biblioteca > Console do Gerenciador de Pacotes**. Defina seu projeto como o projeto padrão na parte superior da janela do Console do Gerenciador de Pacotes.

	![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png)

3. No prompt de comando do Gerenciador de Pacotes, use o comando a seguir para instalar o pacote:

		Install-Package NewRelic.Azure.WebSites

4. No prompt de chave de licença, insira a chave de licença que você recebeu da Azure Store.

	![enter license key][vslicensekey]

<!--5. Opcional: No prompt de nome do aplicativo, digite o nome de seu aplicativo da maneira como ele será exibido no painel do New Relic. Ou, use o nome da solução como o padrão.

	![enter application name](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget08.png)-->

**WebMatrix**

1. Abra o Site usando o WebMatrix.

2. Na guia **Início** da faixa de opções, selecione **NuGet**.

	![nuget buton on home tab][wmnugetbutton]

3. Na galeria do NuGet, defina a origem para a **Origem do Pacote Oficial do NuGet** e, em seguida, pesquise NewRelic.Azure.WebSites.

	![nuget gallery searching for NewRelic.Azure.WebSites][wmnugetgallery]

4. Selecione a entrada **New Relic para Sites do Azure** e clique em **Instalar**.

5. Depois de instalar o pacote, o site conterá uma pasta chamada **newrelic**. Expanda essa pasta e abra o arquivo **newrelic.config**. Nesse arquivo, substitua o valor **REPLACE\_WITH\_LICENSE_KEY** pela chave de licença recebida da Azure Store.

	![newrelic folder expanded with newrelic.conf selected][newrelicconf]

	Depois de adicionar as informações de chave de licença, salve as alterações feitas no arquivo **newrelic.config**.

### Etapa 3. Configurar o Site e publicar o aplicativo.

O pacote do New Relic adicionado ao seu aplicativo na etapa anterior é definido pelas **Configurações do Aplicativo** adicionadas ao seu Site do Azure. Execute as etapas a seguir para adicionar essas configurações.

1. Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) e vá até o seu Site.

2. Em seu Site, selecione **Configurar**. Na seção **Análise para o Desenvolvedor**, selecione **Complemento** ou **Personalizar**. Os dois métodos geram a mesma saída, mas exigem entradas ligeiramente diferentes. **Complemento** lista suas licenças atuais do New-Relic e permite que você selecione uma delas, ao passo que **Personalizar** exige que você especifique manualmente a chave de licença.

	Se você tiver selecionado **Complemento**, use o campo **Escolher Complemento** para selecionar a licença do New Relic.

	![Image of the add-on fields][add-on]

	Se você tiver selecionado **Personalizar**, selecione New Relic como o **Provedor**e, em seguida, digite sua licença no campo **Chave do Provedor**.

	![Image of the custom fields][custom]

3. Depois de especificar a licença em **Análise para o Desenvolvedor**, clique em **Salvar**. Após a conclusão da operação de salvamento, os seguintes valores terão sido adicionados à seção **Configurações do Aplicativo** da página para dar suporte ao New Relic:

	<table border="1">
	<thead>
	<tr>
	<td>Chave</td>
	<td>Valor</td>
	</tr>
	</thead>
	<tbody>
	<tr>
	<td>COR\_ENABLE\_PROFILING</td><td>1</td>
	</tr>
	<tr>
	<td>COR\_PROFILER</td><td>{71DA0A04-7777-4EC6-9643-7D28B46A8A41}</td>
	</tr>
	<tr>
	<td>COR\_PROFILER\_PATH</td><td>C:\Home\site\wwwroot\newrelic\NewRelic.Profiler.dll</td>
	</tr>
	<tr>
	<td>NEWRELIC\_HOME</td><td>C:\Home\site\wwwroot\newrelic</td>
	</tr>
	<tr>
	<td>NEWRELIC\_LICENSEKEY</td><td>Sua chave de licença</td>
	</tr>
	</tbody>
	</table><br/>

	> [AZURE.NOTE] Pode levar até 30 segundos para que as novas <strong>Configurações do Aplicativo</strong> entrem em vigor. Para fazer com que as configurações entrem em vigor imediatamente, reinicie o site.


4. Usando o Visual Studio ou o WebMatrix, publique seu aplicativo.

### Etapa 4. Conferir o desempenho do seu aplicativo no New Relic.

Para exibir o painel novo Relíquia:

1. No portal do Azure, clique no botão **Gerenciar**.
2. Entrar com seu e-mail do Relíquia nova conta e senha.
3. Na barra de menu New Relic, selecione **Aplicativos > (nome do aplicativo)**.

	O **Monitoring > Visão geral** painel aparece automaticamente.

	![New Relic monitoring dashboard](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png)

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
       <td>Da exibição do <b>navegador</b>de Overview&#39;s, aponte para qualquer local do mapa de Apdex global. <br/><b>Dica:</b> Para ir diretamente para o painel de controle <a href="https://newrelic.com/docs/site/geography" target="_blank">geografia</a> do app&#39;s selecionado, clique no título <b>Apdex global</b> ou clique em qualquer lugar no mapa Apdex Global.</td>
    </tr>
    <tr>
       <td>Exibir o <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">Web transações</a> painel de controle</td>
       <td>Clique na tabela de transações na Web no painel Visão geral de aplicativos. Ou, para exibir detalhes sobre uma transação web específica (incluindo <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Chave transações</a>), clique em seu nome.</td>
    </tr>
    <tr>
       <td>Modo de exibição de <a href="https://newrelic.com/docs/site/errors" target="_blank">erros</a> painel de controle</td>
       <td>Clique no título do chart&#39;s de taxa de erro no painel de Visão geral dos aplicativos.<br /><b>Dica:</b> Você também pode exibir o painel de Erros em <b>Aplicativos</b> &gt; (seu aplicativo) &gt; Eventos &gt; Erros.</td>
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

![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png)

## Próximas etapas

Confira estes recursos adicionais para obter mais informações:

 * [ Instalação do agente do .NET nos sites do Azure](https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install): Procedimentos de instalação de agente do .NET do New Relic 
 * [ A Interface de usuário do New Relic](https://newrelic.com/docs/site/the-new-relic-ui): 
Visão geral da nova Relíquia UI, definir direitos de usuário e perfis, usando as funções padrão e detalhes de detalhamento do painel de controle
 * [Visão Geral dos Aplicativos](https://newrelic.com/docs/site/applications-overview): Recursos e funções ao usar o painel de visão geral de aplicativos do New Relic
 * [Apdex](https://newrelic.com/docs/site/apdex): Visão geral de como o Apdex mede a satisfação dos usuários finais com seu aplicativo
 * [Monitoramento do Usuário Real](https://newrelic.com/docs/features/real-user-monitoring): Visão geral de como RUM detalha o tempo que os navegadores de seus usuários levam para carregar suas páginas da Web, de onde eles vêm, e quais navegadores usam
 * [Encontrar Ajuda](https://newrelic.com/docs/site/finding-help): Recursos disponibilizados pelo centro de ajuda on-line do New Relic


[webmatrixwebsite]: http://www.windowsazure.com/pt-br/develop/net/tutorials/website-with-webmatrix/
[vswebsite]: http://www.windowsazure.com/pt-br/develop/net/tutorials/get-started/

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png


<!--HONumber=42-->
