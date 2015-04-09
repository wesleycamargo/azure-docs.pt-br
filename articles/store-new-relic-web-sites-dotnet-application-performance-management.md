<properties 
	pageTitle="Gerenciamento de desempenho do aplicativo New Relic no Azure" 
	description="Saiba como usar o monitoramento de desempenho do New Relic no Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="stepsic"/>



#Gerenciamento de desempenho de aplicativos do New Relic em aplicativos do Azure

Este guia descreve como adicionar o monitoramento de desempenho de classe mundial do New Relic
a seu aplicativo Web do Azure. Abordaremos o processo rápido e simples
para adicionar o New Relic a seu aplicativo e apresentaremos alguns dos
recursos do New Relic. Para obter mais informações sobre como usar o New Relic, consulte [Usando o New Relic](#using-new-relic).

O que é o New Relic?
--

New Relic é uma ferramenta focada no desenvolvedor que monitora seus aplicativos de produção
e fornece informações detalhadas sobre seu desempenho e confiabilidade. Ele é
projetado para economizar tempo ao identificar e diagnosticar problemas de desempenho e
coloca em suas mãos as informações necessárias para solucionar esses problemas.

O New Relic acompanha o tempo de carregamento e a produtividade da transação da Web, tanto no
servidor quanto nos navegadores dos usuários. Ele mostra quanto tempo você gasta no
banco de dados, analisa consultas e solicitações da Web lentas, fornece monitoramento de tempo de atividade e
alertas, acompanha exceções de aplicativos e muito mais.

Preços especiais de New Relic por meio da Azure Store
--

O New Relic Standard é gratuito para usuários do Azure.
O New Relic Pro é oferecido em vários pacotes com base no modo de site que você está usando e no tamanho da instância se você estiver usando o modo reservado.

Para obter informações sobre preços, consulte a [página do New Relic na Azure Store](http://azure.microsoft.com/marketplace/partners/newrelic/newrelic).

> [AZURE.NOTE] Os preços são listados apenas para até 10 instâncias de computação. Para um número maior que 10, entre em contato com o New Relic (sales@newrelic.com) para obter os preços por volume.

Os clientes do Azure recebem uma assinatura de avaliação de dias semanas do New Relic Pro ao implantar o agente do New Relic.

Inscrever-se para o New Relic usando a Azure Store
--

O New Relic integra-se perfeitamente com as funções Web, as funções de trabalho e os sites do Azure.

Para se inscrever para o New Relic diretamente da Azure Store, siga estas três etapas simples.

### Etapa 1. Inscrever-se por meio do armazenamento do Azure

1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. No painel inferior do portal de gerenciamento, clique em **Novo**.
3. Clique em **Store**.
4. Na caixa de diálogo **Escolher um Complemento**, selecione **New Relic** e clique em **Avançar**.
5. Na caixa de diálogo **Personalizar Complemento**, selecione o plano do New Relic desejado.
7. Insira um nome para como o serviço New Relic aparecerá nas configurações do Azure
   ou use o valor padrão **NewRelic**. Esse nome deve ser exclusivo em
   sua lista de itens inscritos da Azure Store.
8. Escolha um valor para a região. Por exemplo, **oeste dos Estados Unidos**.
9. Clique em **Avançar**.
10. Na caixa de diálogo **Examinar Compra**, examine o plano e informações sobre preços
    e examine os termos legais. Se você concordar com os termos, clique em **Comprar**.
11. Depois que você clicar em **Comprar**, sua conta do New Relic iniciará o processo de criação. Você pode monitorar o status no portal de gerenciamento do Azure.
12. Para recuperar sua chave de licença do New Relic, clique no complemento que você acabou de criar e, em seguida, clique em **Informações de Conexão**. 
13. Copie a chave de licença é exibida. Você precisará inseri-la quando instalar o pacote New Relic Nuget.

### Etapa 2. Instalar o pacote do New Relic

O agente de sites do New Relic é distribuído como um pacote do NuGet, que pode ser adicionado ao seu site usando o Visual Studio ou o WebMatrix. Se você não estiver familiarizado com o uso do Visual Studio ou do WebMatrix com um Site do Azure, consulte [Implantando um aplicativo Web ASP.NET a um Site do Azure usando o Visual Studio][vswebsite] ou [Desenvolver e implantar um site com o Microsoft WebMatrix][webmatrixwebsite].

Execute as etapas a seguir para o ambiente de desenvolvimento específico que você está usando:

**Visual Studio**

1. Abra sua solução de Site do Visual Studio.

2. Abra o console do Gerenciador de pacotes selecionando **Ferramentas > Gerenciador de Pacotes de Biblioteca > 
   Console do Gerenciador de Pacotes**. Defina seu projeto como o Projeto Padrão na
   parte superior da janela do Console do Gerenciador de Pacotes.

	![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png)

3. No prompt de comando do Gerenciador de Pacotes, use o comando a seguir para instalar o pacote:

		Install-Package NewRelic.Azure.WebSites

4. No prompt de chave de licença, insira a chave de licença que você recebeu da Azure Store.

	![enter license key][vslicensekey]

<!--5. Opcional: No prompt de nome de aplicativo, insira o nome do aplicativo como ele
   aparecerá no painel do New Relic. Ou então, use o nome da solução como o padrão.

	![digite o nome do aplicativo](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget08.png)-->

**WebMatrix**

1. Abra o Site usando o WebMatrix.

2. Na guia **Início** da faixa de opções, selecione **NuGet**.

	![nuget buton on home tab][wmnugetbutton]

3. Na galeria do NuGet, defina a origem para a **Origem do Pacote Oficial do NuGet** e, em seguida, pesquise NewRelic.Azure.WebSites.

	![nuget gallery searching for NewRelic.Azure.WebSites][wmnugetgallery]

4. Selecione a entrado **New Relic para Sites do Azure** e clique em **Instalar**.

5. Depois de instalar o pacote, o site conterá uma pasta chamada **newrelic**. Expanda essa pasta e abra o arquivo **newrelic.config**. Nesse arquivo, substitua o valor **REPLACE\_WITH\_LICENSE_KEY** pela chave de licença recebida da Azure Store.

	![newrelic folder expanded with newrelic.conf selected][newrelicconf]

	Depois de adicionar as informações de chave de licença, salve as alterações feitas no arquivo **newrelic.config**.

### Etapa 3. Configurar o Site e publicar o aplicativo.

O pacote do New Relic adicionado ao seu aplicativo na etapa anterior é definido pelas **Configurações do Aplicativo** adicionadas ao seu Site do Azure. Execute as etapas a seguir para adicionar essas configurações.

1. Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) e vá até o seu Site.

2. Em seu Site, selecione **Configurar**. Na seção **Análise para o Desenvolvedor**, selecione **Complemento** ou **Personalizar**. Os dois métodos geram a mesma saída, mas exigem entradas ligeiramente diferentes. **Complemento** lista suas licenças atuais do New-Relic e permite que você selecione uma delas, ao passo que **Personalizar** exige que você especifique manualmente a chave de licença.

	Se você tiver selecionado **Complemento**, use o campo **Escolher Complemento** para selecionar a licença do New Relic.

	![Imagem dos campos de complemento][complemento]

	Se você tiver selecionado **Personalizar**, selecione New Relic como o **Provedor** e, em seguida, digite sua licença no campo **Chave do Provedor**.

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
	<td>NEWRELIC\_LICENSEKEY</td><td>Your license key</td>
	</tr>
	</tbody>
	</table><br/>

	> [AZURE.NOTE] Pode levar até 30 segundos para que as novas <strong>Configurações do Aplicativo</strong> entrem em vigor. Para fazer com que as configurações entrem em vigor imediatamente, reinicie o site.


4. Usando o Visual Studio ou o WebMatrix, publique seu aplicativo.

### Etapa 4. Conferir o desempenho do seu aplicativo no New Relic.

Para exibir o painel do New Relic:

1. No portal do Azure, clique no botão **Gerenciar**.
2. Entre com seu email da conta do New Relic e a senha.
3. Na barra de menu New Relic, selecione **Aplicativos > (nome do aplicativo)**.

	O painel **Monitoramento > Visão geral** aparece automaticamente.

		![New Relic monitoring dashboard](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png)

	Depois que você selecionar um aplicativo na lista no menu **Aplicativos**, o painel **Visão Geral** mostrará informações atuais do servidor de aplicativos e do navegador.

### <a id="using-new-relic"></a>Usando o New Relic

Depois que você selecionar um aplicativo na lista no menu Aplicativos, o painel Visão Geral mostrará informações atuais de servidor de aplicativos e do navegador. Para alternar entre dois modos de exibição, clique no botão **Servidor de aplicativos** ou **Navegador**.

Além das funções de <a href="https://newrelic.com/docs/site/the-new-relic-ui#functions">nova interface do usuário do New Relic padrão</a> e <a href="https://newrelic.com/docs/site/the-new-relic-ui#drilldown">drill-down do painel</a>, o painel de Visão Geral de Aplicativos tem funções adicionais.

<table border="1">
  <thead>
    <tr>
      <th><b>Se você quiser...</b></th>
      <th><b>Faça isso...</b></th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Mostrar informações do painel para o servidor ou navegador de aplicativos selecionado</td>
       <td>Clique no botão <b>Servidor de aplicativos</b> ou <b>Navegador</b>.</td>
    </tr>
     <tr>
       <td>Exibir níveis de limite para a pontuação <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> de seu aplicativo</td>
       <td>Aponte para o ícone de pontuação Apdex <b>?<b>.</b></b></td>
    </tr>
    <tr>
       <td>Exibir detalhes de pontuação Apdex em todo o mundo</td>
       <td>Na exibição de <b>Navegador</b> da Visão Geral, aponte em qualquer lugar no mapa de Apdex Global.<br /><b>Dica:</b> Para ir diretamente para o painel <a href="https://newrelic.com/docs/site/geography" target="_blank">Geografia</a> do aplicativo selecionado, clique no título <b>Apdex Global</b> ou clique em qualquer lugar no mapa do Apdex Global.</td>
    </tr>
    <tr>
       <td>Exibir o painel <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard" target="_blank">Transações da Web</a></td>
       <td>Clique na tabela de Transações na Web no painel Visão Geral de Aplicativos. Ou então, para exibir detalhes sobre uma transação da Web específica (incluindo <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Transações de Chave</a>), clique em seu nome.</td>
    </tr>
    <tr>
       <td>Exibir o painel <a href="https://newrelic.com/docs/site/errors" target="_blank">Erros</a></td>
       <td>Clique no título do gráfico de Taxa de erros no painel Visão Geral de Aplicativos.<br /><b>Dica:</b> Você também pode exibir o painel Erros por meio de <b>Aplicativos</b> &gt; (seu aplicativo) &gt; Eventos &gt; Erros.</td>
    </tr>
    <tr>
       <td>Exibir detalhes do servidor de app&#39;s</td>
       <td><p>Faça uma das opções a seguir:<p>
        <ul>
          <li>Alternar entre um modo de exibição de tabela dos hosts ou detalhes de métrica de análise de cada host.</li>
          <li>Clique no nome de um servidor individual.</li>
          <li>Aponte para a pontuação Apdex do servidor individual.</li>
          <li>Clique no uso de CPU ou na memória de um servidor individual.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

Abaixo está um exemplo do painel de visão geral de aplicativos quando você seleciona o modo de exibição do navegador.

![Package manager console](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png)

## Próximas etapas

Confira estes recursos adicionais para obter mais informações:

 * [Instalando o Agente do .NET para Sites do Azure](https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install): Procedimentos de instalação de agente do .NET do New Relic 
 * [ A Interface de usuário do New Relic](https://newrelic.com/docs/site/the-new-relic-ui): 
Visão geral da interface do usuário do New Relic, definindo direitos de usuário e perfis, usando as funções padrão e detalhes de drill-down do painel
 * [Visão Geral dos Aplicativos](https://newrelic.com/docs/site/applications-overview): Recursos e funções ao usar o painel de visão geral de aplicativos do New Relic
 * [Apdex](https://newrelic.com/docs/site/apdex): Visão geral de como o Apdex mede a satisfação dos usuários finais com seu aplicativo
 * [Monitoramento do Usuário Real](https://newrelic.com/docs/features/real-user-monitoring): Visão geral de como o RUM detalha o tempo que leva para os 
navegadores dos usuários carregarem as páginas da Web, de onde eles vêm e que navegadores usam
 * [Encontrar Ajuda](https://newrelic.com/docs/site/finding-help): Recursos disponibilizados pelo centro de ajuda online do New Relic


[webmatrixwebsite]: http://www.windowsazure.com/develop/net/tutorials/website-with-webmatrix/
[vswebsite]: http://www.windowsazure.com/develop/net/tutorials/get-started/

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png

<!--HONumber=49-->