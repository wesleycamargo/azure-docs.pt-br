<properties linkid="develop-net-how-to-guides-new-relic-app" urlDisplayName="Gerenciamento de desempenho de aplicativos do New Relic" pageTitle="Gerenciamento de desempenho de aplicativos do New Relic no Azure" metaKeywords="new relic para Azure, desempenho do azure" description="Saiba como usar o monitoramento de desempenho do New Relic no Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Gerenciamento de desempenho de aplicativos do New Relic em Sites do Azure" authors="larryfr" solutions="" manager="" editor="" />



#Gerenciamento de desempenho de aplicativos do New Relic em Sites do Azure

Este guia descreve como adicionar o monitoramento de desempenho de classe mundial do New Relic ao seu Site do Azure. Abordaremos o processo simples e rápido para adicionar nova Relíquia ao seu aplicativo e apresentar alguns dos recursos do novo Relíquia. Para obter mais informações sobre como usar o novo Relíquia, consulte [usando o novo Relíquia](#using-new-relic)

O que há de novo Relíquia?
--

O New Relic é uma ferramenta focada no desenvolvedor que monitora seus aplicativos de produção e fornece informações detalhadas sobre seu desempenho e confiabilidade. Ele é projetado para economizar tempo ao identificar e diagnosticar problemas de desempenho, e ele coloca as informações necessárias para solucionar esses problemas em suas mãos.

Relíquia nova controla o tempo de carregamento e taxa de transferência para a transação da web, tanto do servidor e navegadores dos usuários. Ele mostra quanto tempo você gasta no banco de dados, analisa consultas lentas e solicitações da web, fornece monitoramento de tempo de atividade e alertas, faixas exceções de aplicativos e muito mais.

Nova Relíquia especial de preços por meio do armazenamento do Azure
--

O New Relic Standard é gratuito para usuários do Azure.
O New Relic Pro é oferecido em vários pacotes com base no modo de site que você está usando e no tamanho da instância se você estiver usando o modo reservado.

Para informações sobre preços, consulte o [página nova Relíquia no armazenamento do Azure](http://www.windowsazure.com/pt-br/gallery/store/new-relic/new-relic/)

<div class="dev-callout"> 
<strong>Observação:</strong>
<p>Preços somente é listada para até 10 instâncias de computação. Para um número maior que 10 entre em contato com o novo Relíquia (sales@newrelic.com) para o preço por volume.</p>
</div>

Azure clientes recebem uma assinatura de avaliação de 2 semanas do nova Relíquia Pro ao implantar o agente Relíquia de novo.

Inscreva-se no novo Relíquia usando o armazenamento do Azure
--

O New Relic integra-se perfeitamente com as funções Web, as funções de trabalho e os sites do Azure.

Para se inscrever para o New Relic diretamente da Azure Store, siga estas três etapas simples.

### Etapa 1. Inscrever-se por meio da Azure Store

1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. No painel inferior do portal de gerenciamento, clique em **Novo**.
3. Clique em **Repositório**.
4. Na caixa de diálogo **Escolher um complemento**, selecione **New Relic** e clique em **Próximo**.
5. Na caixa de diálogo **Personalizar complemento**, selecione o plano New Relic que você deseja.
7. Digite um nome para como o serviço novo Relíquia aparecerá nas configurações do Azure ou usar o valor padrão **NewRelic** O nome deve ser exclusivo na
   sua lista de itens inscritos da Azure Store.
8. Escolha um valor para a região; por exemplo, **oeste dos EUA**.
9. Clique em **Próximo**.
10. Na caixa de diálogo **Revisar Compra**, revise o plano e as informações sobre preços, 
      bem como os termos legais. Se você concordar com os termos, clique em **Comprar**
11. Depois que você clicar em **Comprar**, a sua conta New Relic iniciará o processo de criação. Você pode monitorar o status no portal de gerenciamento do Azure.
12. Para recuperar sua chave de licença do New Relic, clique no complemento que você acabou de criar e, em seguida, clique em **Informações de Conexão**. 
13. Copie a chave de licença é exibida. Você precisará inseri-la quando você instala o pacote novo Relíquia Nuget.

### Etapa 2. Instalar o pacote do New Relic

O agente de sites do New Relic é distribuído como um pacote do NuGet, que pode ser adicionado ao seu site usando o Visual Studio ou o WebMatrix. Se você não estiver familiarizado com o uso do Visual Studio ou do WebMatrix com um Site do Azure, consulte [Implantando um aplicativo Web ASP.NET a um Site do Azure usando o Visual Studio (a página pode estar em inglês)][vswebsite] ou [Desenvolver e implantar um site com o Microsoft WebMatrix (a página pode estar em inglês)][webmatrixwebsite].

Execute as etapas a seguir para o ambiente de desenvolvimento específico que você está usando:

**Visual Studio**

1. Abra sua solução de Site do Visual Studio.

2. Abra o console do Gerenciador de Pacotes selecionando **Ferramentas > Gerenciador de Pacotes da Biblioteca > Console do Gerenciador de Pacotes**. Defina seu projeto como o projeto padrão na parte superior da janela do Console do Gerenciador de Pacotes.

	![Console do Gerenciador de Pacotes](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png)

3. No prompt de comando do Gerenciador de Pacotes, use o comando a seguir para instalar o pacote:

		Install-Package NewRelic.Azure.WebSites

4. No prompt de chave de licença, insira a chave de licença que você recebeu da Azure Store.

	![Insira a chave de licença][vslicensekey]

<!--5. (Opcional) No prompt de nome do aplicativo, digite o nome de seu aplicativo da maneira como ele será exibido no painel do New Relic. Ou use o nome da solução como padrão.

	![Digite o nome do aplicativo](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget08.png)-->

**WebMatrix**

1. Abra o Site usando o WebMatrix.

2. Na guia **Início** da faixa de opções, selecione **NuGet**.

	![Botão do NuGet na guia Início][wmnugetbutton]

3. Na galeria do NuGet, defina a origem para a **Origem do Pacote Oficial do NuGet** e, em seguida, pesquise NewRelic.Azure.WebSites.

	![Galeria do NuGet procurando por NewRelic.Azure.WebSites][wmnugetgallery]

4. Selecione a entrada **New Relic para Sites do Azure** e clique em **Instalar**.

5. Depois de instalar o pacote, o site conterá uma pasta chamada **newrelic**. Expanda essa pasta e abra o arquivo **newrelic.config**. Nesse arquivo, substitua o valor **REPLACE\_WITH\_LICENSE_KEY** pela chave de licença recebida da Azure Store.

	![Pasta do New Relic expandida com newrelic.conf selecionado][newrelicconf]

	Depois de adicionar as informações de chave de licença, salve as alterações feitas no arquivo **newrelic.config**.

### Etapa 3. Configurar o Site e publicar o aplicativo.

O pacote do New Relic adicionado ao seu aplicativo na etapa anterior é definido pelas **Configurações do Aplicativo** adicionadas ao seu Site do Azure. Execute as etapas a seguir para adicionar essas configurações.

1. Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) e vá até o seu Site.

2. Em seu Site, selecione **Configurar**. Na seção **Análise para o Desenvolvedor**, selecione **Complemento** ou **Personalizar**. Os dois métodos geram a mesma saída, mas exigem entradas ligeiramente diferentes. **Complemento** lista suas licenças atuais do New-Relic e permite que você selecione uma delas, ao passo que **Personalizar** exige que você especifique manualmente a chave de licença.

	Se você tiver selecionado **Complemento**, use o campo **Escolher Complemento** para selecionar a licença do New Relic.

	![Imagem dos campos de complemento][add-on]

	Se você tiver selecionado **Personalizar**, selecione New Relic como o **Provedor**e, em seguida, digite sua licença no campo **Chave do Provedor**.

	![Imagem dos campos de personalização][custom]

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

	<div class="dev-callout"> 
	<strong>Observação</strong> 
	<p>Pode levar até 30 segundos para que as novas <strong>Configurações do Aplicativo</strong> entrem em vigor. Para fazer com que as configurações entrem em vigor imediatamente, reinicie o site.</p> 
	</div>


4. Usando o Visual Studio ou o WebMatrix, publique seu aplicativo.

### Etapa 4. Verificar o desempenho do aplicativo no New Relic.

Para exibir o painel novo Relíquia:

1. No portal do Azure, clique o **gerenciar** botão.
2. Entrar com seu e-mail do Relíquia nova conta e senha.
3. Na barra de menu Novo Relíquia, selecione **aplicativos > (nome do aplicativo)**

	O **Monitoring > Visão geral** painel aparece automaticamente.

	![Painel de monitoramento do New Relic](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png)

	Depois de selecionar um aplicativo a partir da lista no seu **aplicativos** menu, o **visão geral** painel mostra informações atuais de servidor e o navegador do aplicativo.

### <a id="using-new-relic"></a>Usando o nova Relíquia

Depois de selecionar um aplicativo a partir da lista no menu Aplicativos, o painel Visão Geral mostra informações atuais de servidor de aplicativos e do navegador. Para alternar entre dois modos de exibição, clique o **o servidor de aplicativo** ou **navegador** botão.

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
       <td>Mostrar informações do painel para o servidor ou o navegador do aplicativo selecionado</td>
       <td>Clique o <b>Aplicativo servidor</b> ou <b>navegador</b> botão.</td>
    </tr>
     <tr>
       <td>Exibir os níveis de limite da pontuação de <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> de seu aplicativo</td>
       <td>Aponte para o resultado de Apdex <b>?<b> icon.</b></b></td>
    </tr>
    <tr>
       <td>Exibir detalhes de Apdex em todo o mundo</td>
       <td>No modo de exibição de <b>navegador</b> da visão geral, aponte para qualquer local do mapa de apdex globais.<br /><b>Dica:</b> para ir diretamente para o painel de <a href="https://newrelic.com/docs/site/geography" target="_blank">localização geográfica</a> do aplicativo selecionado, clique no título <b>Apdex Global</b> ou clique em qualquer local do mapa de apdex locais.</td>
    </tr>
    <tr>
       <td>Exibir o <a href="https://newrelic.com/docs/applications-dashboards/web-transactions" target="_blank">Web transações</a> painel de controle</td>
       <td>Clique na tabela de transações na Web no painel Visão geral de aplicativos. Ou, para exibir detalhes sobre uma transação web específica (incluindo <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Chave transações</a>), clique em seu nome.</td>
    </tr>
    <tr>
       <td>Modo de exibição de <a href="https://newrelic.com/docs/site/errors" target="_blank">erros</a> painel de controle</td>
       <td>Clique no título do gráfico de taxa de erros no painel de visão geral sobre aplicativos.<br /><b>Dica:</b> você também pode exibir o painel de erros a partir de <b>Aplicativos</b> &gt; (seu aplicativo) &gt; Eventos &gt; Erros.</td>
    </tr>
    <tr>
       <td>Exibir detalhes do servidor do aplicativo</td>
       <td><p>Faça uma das opções a seguir:<p>
        <ul>
          <li>Alternar entre um modo de exibição de tabela dos hosts ou detalhes de métrica de análise de cada host.</li>
          <li>Clique no nome de um servidor individual.</li>
          <li>Aponte para a pontuação de apdex de um servidor individual.</li>
          <li>Clique no uso da CPU ou na memória de um servidor individual.</li>
        </ul>
       </p></p></td>
    </tr>
  </tbody>
</table>

Abaixo está um exemplo do painel de visão geral de aplicativos quando você selecionar o modo de exibição do navegador.

![Console do Gerenciador de Pacotes](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png)

## Próximas etapas

Confira estes recursos adicionais para obter mais informações:

 * [Instalando o agente do .NET para Sites do Azure (a página pode estar em inglês)](https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install): Procedimentos de instalação do agente do .NET para New Relic 
 * [A interface de usuário do New Relic (a página pode estar em inglês)](https://newrelic.com/docs/site/the-new-relic-ui): Visão geral da interface de usuário do New Relic, configuração de direitos e perfis de usuários, utilização de funções padrão e detalhes sobre o painel
 * [Visão geral de aplicativos (a página pode estar em inglês)](https://newrelic.com/docs/site/applications-overview): Recursos e funções ao usar o painel de visão geral de aplicativos do New Relic
 * [Apdex (a página pode estar em inglês)](https://newrelic.com/docs/site/apdex): Visão geral de como o Apdex mede a satisfação de usuários finais com seu aplicativo
 * [Monitoramento de usuário real (a página pode estar em inglês)](https://newrelic.com/docs/features/real-user-monitoring): Visão geral de como esse monitoramento fornece detalhes sobre o tempo levado pelos navegadores de seus usuários para carregar suas páginas da Web, de onde elas vêm e o quais navegadores eles usam
 * [Obtendo ajuda (a página pode estar em inglês)](https://newrelic.com/docs/site/finding-help): Recursos disponibilizados por meio da central de ajuda online do New Relic


[webmatrixwebsite]: http://www.windowsazure.com/pt-br/develop/net/tutorials/website-with-webmatrix/
[vswebsite]: http://www.windowsazure.com/pt-br/develop/net/tutorials/get-started/

[wmnugetbutton]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
[wmnugetgallery]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png

[newrelicconf]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
[vslicensekey]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
[add-on]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
[custom]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png

