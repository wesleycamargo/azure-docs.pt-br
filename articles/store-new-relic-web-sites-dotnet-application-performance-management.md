<properties linkid="develop-net-how-to-guides-new-relic-app" urlDisplayName="New Relic App Performance Management" pageTitle="New Relic App Performance Management on Azure" metaKeywords="new relic Azure, performance azure" description="Learn how to use New Relic's performance monitoring on Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="New Relic Application Performance Management on Azure Websites" authors="new relic" solutions="" manager="dwrede" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="new relic"></tags>

# Gerenciamento de desempenho do aplicativo New Relic nos sites do Azure

Este guia descreve como adicionar o monitoramento de
desempenho de classe mundial do New Relic ao seu site do Windows Azure. Abordaremos o
processo simples e rápido para adicionar New Relic ao seu aplicativo e apresentar alguns dos
recursos do New Relic. Para obter mais informações sobre como usar o novo Relíquia, consulte [usando o novo Relíquia][]

## O que há de novo Relíquia?

O New Relic é uma ferramenta focada no desenvolvedor que monitora seus aplicativos de produção
e fornece informações detalhadas sobre seu desempenho e confiabilidade. Ele foi
projetado para economizar tempo ao identificar e diagnosticar problemas de desempenho, e
ele coloca as informações necessárias para solucionar esses problemas em suas mãos.

O New Relic controla o tempo de carregamento e taxa de transferência da sua transação da web, tanto do
servidor quanto dos navegadores dos seus usuários. Ele mostra quanto tempo você gasta no
banco de dados, analisa consultas lentas e solicitações da web, fornece monitoramento de tempo de atividade e
alertas, faixas exceções de aplicativos e muito mais.

## Nova Relíquia especial de preços por meio do armazenamento do Azure

O New Relic Standard é gratuito para usuários do Azure.
O New Relic Pro é oferecido em vários pacotes com base no modo de site que você está usando e no tamanho da instância se você estiver usando o modo reservado.

Para informações sobre preços, consulte o [página nova Relíquia no armazenamento do Azure][]

<div class="dev-callout"> 
<strong>Observa&ccedil;&atilde;o:</strong>
<p>Pre&ccedil;os somente &eacute; listada para at&eacute; 10 inst&acirc;ncias de computa&ccedil;&atilde;o. Para um n&uacute;mero maior que 10 entre em contato com o novo Rel&iacute;quia (sales@newrelic.com) para o pre&ccedil;o por volume.</p>
</div>

Azure clientes recebem uma assinatura de avaliação de 2 semanas do nova Relíquia Pro ao implantar o agente Relíquia de novo.

## Inscreva-se no novo Relíquia usando o armazenamento do Azure

O New Relic integra-se perfeitamente com as funções Web, as funções de trabalho e os sites do Azure.

Para se inscrever para o New Relic diretamente da Azure Store, siga estas três etapas simples.

### Etapa 1. Inscrever-se por meio da Azure Store

1.  Faça logon no [Portal de Gerenciamento do Azure][].
2.  No painel inferior do portal de gerenciamento, clique em **Novo**.
3.  Clique em **Repositório**.
4.  Na caixa de diálogo **Escolher um complemento**, selecione **New Relic** e clique em **Próximo**.
5.  Na caixa de diálogo **Personalizar complemento**, selecione o plano New Relic que você deseja.
6.  Digite um nome para como o serviço novo Relíquia aparecerá nas configurações do Azure
    ou usar o valor padrão **NewRelic**. O nome deve ser exclusivo na
     sua lista de itens inscritos da Azure Store.
7.  Escolha um valor para a região; por exemplo, **oeste dos Estados Unidos**.
8.  Clique em **Próximo**.
9.  Na caixa de diálogo **Revisar Compra**, revise o plano e as informações sobre preços,
    bem como os termos legais. Se você concordar com os termos, clique em **Comprar**
10. Depois que você clicar em **Comprar**, a sua conta New Relic iniciará o processo de criação. Você pode monitorar o status no portal de gerenciamento do Azure.
11. Para recuperar sua chave de licença do New Relic, clique no complemento que você acabou de criar e, em seguida, clique em **Informações de Conexão**.
12. Copie a chave de licença é exibida. Você precisará inseri-la quando você instala o pacote novo Relíquia Nuget.

### Etapa 2. Instalar o pacote do New Relic

O agente de sites do New Relic é distribuído como um pacote do NuGet, que pode ser adicionado ao seu site usando o Visual Studio ou o WebMatrix. Se você não estiver familiarizado com o uso do Visual Studio ou do WebMatrix com um Site do Azure, consulte [Implantando um aplicativo Web ASP.NET a um Site do Azure usando o Visual Studio (a página pode estar em inglês)][] ou [Desenvolver e implantar um site com o Microsoft WebMatrix (a página pode estar em inglês)][].

Execute as etapas a seguir para o ambiente de desenvolvimento específico que você está usando:

**Visual Studio**

1.  Abra sua solução de Site do Visual Studio.

2.  Abra o console do Gerenciador de Pacotes selecionando **Ferramentas \> Gerenciador de Pacotes da Biblioteca \>
    Console do Gerenciador de Pacotes**. Defina seu projeto como o projeto padrão na
     parte superior da janela do Console do Gerenciador de Pacotes.

    ![Console do Gerenciador de Pacotes][]

3.  No prompt de comando do Gerenciador de Pacotes, use o comando a seguir para instalar o pacote:

        Install-Package NewRelic.Azure.WebSites

4.  No prompt de chave de licença, insira a chave de licença que você recebeu da Azure Store.

    ![Insira a chave de licença][]

<!--5. Optional: At the application name prompt, enter your app's name as it will    appear in New Relic's dashboard. Or, use your solution name as the default.      ![enter application name](./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget08.png)-->

**WebMatrix**

1.  Abra o Site usando o WebMatrix.

2.  Na guia **Início** da faixa de opções, selecione **NuGet**.

    ![Botão do NuGet na guia Início][]

3.  Na galeria do NuGet, defina a origem para a **Origem do Pacote Oficial do NuGet** e, em seguida, pesquise NewRelic.Azure.WebSites.

    ![Galeria do NuGet procurando por NewRelic.Azure.WebSites][]

4.  Selecione a entrada **New Relic para Sites do Azure** e clique em **Instalar**.

5.  Depois de instalar o pacote, o site conterá uma pasta chamada **newrelic**. Expanda essa pasta e abra o arquivo **newrelic.config**. Nesse arquivo, substitua o valor **REPLACE\_WITH\_LICENSE\_KEY** pela chave de licença recebida da Azure Store.

    ![Pasta do New Relic expandida com newrelic.conf selecionado][]

    Depois de adicionar as informações de chave de licença, salve as alterações feitas no arquivo **newrelic.config**.

### Etapa 3. Configurar o Site e publicar o aplicativo.

O pacote do New Relic adicionado ao seu aplicativo na etapa anterior é definido pelas **Configurações do Aplicativo** adicionadas ao seu Site do Azure. Execute as etapas a seguir para adicionar essas configurações.

1.  Entre no [Portal de Gerenciamento do Azure][] e vá até o seu Site.

2.  Em seu Site, selecione **Configurar**. Na seção **Análise para o Desenvolvedor**, selecione **Complemento** ou **Personalizar**. Os dois métodos geram a mesma saída, mas exigem entradas ligeiramente diferentes. **Complemento** lista suas licenças atuais do New-Relic e permite que você selecione uma delas, ao passo que **Personalizar** exige que você especifique manualmente a chave de licença.

    Se você tiver selecionado **Complemento**, use o campo **Escolher Complemento** para selecionar a licença do New Relic.

    ![Imagem dos campos de complemento][]

    Se você tiver selecionado **Personalizar**, selecione New Relic como o **Provedor**e, em seguida, digite sua licença no campo **Chave do Provedor**.

    ![Imagem dos campos de personalização][]

3.  Depois de especificar a licença em **Análise para o Desenvolvedor**, clique em **Salvar**. Após a conclusão da operação de salvamento, os seguintes valores terão sido adicionados à seção **Configurações do Aplicativo** da página para dar suporte ao New Relic:

    <table border="1">
    <thead>
    <tr>
    <td>
    Chave

    </td>
    <td>
    Valor

    </td>
    </tr>
    </thead>
    <tbody>
    <tr>
    <td>
    COR\\\_ENABLE\\\_PROFILING

    </td>
    <td>
    1

    </td>
    </tr>
    <tr>
    <td>
    COR\\\_PROFILER

    </td>
    <td>
    {71DA0A04-7777-4EC6-9643-7D28B46A8A41}

    </td>
    </tr>
    <tr>
    <td>
    COR\\\_PROFILER\\\_PATH

    </td>
    <td>
    C:\\Home\\site\\wwwroot\\newrelic\\NewRelic.Profiler.dll

    </td>
    </tr>
    <tr>
    <td>
    NEWRELIC\\\_HOME

    </td>
    <td>
    C:\\Home\\site\\wwwroot\\newrelic

    </td>
    </tr>
    <tr>
    <td>
    NEWRELIC\\\_LICENSEKEY

    </td>
    <td>
    Sua chave de licença

    </td>
    </tr>
    </tbody>
    </table>

    <div class="dev-callout"> 
<strong>Observa&ccedil;&atilde;o</strong> 
<p>Pode levar at&eacute; 30 segundos para que as novas <strong>Configura&ccedil;&otilde;es do Aplicativo</strong> entrem em vigor. Para fazer com que as configura&ccedil;&otilde;es entrem em vigor imediatamente, reinicie o site.</p> 
</div>

4.  Usando o Visual Studio ou o WebMatrix, publique seu aplicativo.

### Etapa 4. Verificar o desempenho do aplicativo no New Relic.

Para exibir o painel novo Relíquia:

1.  No portal do Azure, clique o **gerenciar** botão.
2.  Entrar com seu e-mail do Relíquia nova conta e senha.
3.  Na barra de menu Novo Relíquia, selecione **aplicativos \> (nome do aplicativo)**.

    O **Monitoring \> Visão geral** painel aparece automaticamente.

    ![Painel de monitoramento do New Relic][]

    Depois de selecionar um aplicativo a partir da lista no seu **aplicativos** menu, o **visão geral** painel mostra informações atuais de servidor e o navegador do aplicativo.

### <span id="using-new-relic"></span></a>Usando o nova Relíquia

Depois de selecionar um aplicativo a partir da lista no menu Aplicativos, o painel Visão Geral mostra informações atuais de servidor de aplicativos e do navegador. Para alternar entre dois modos de exibição, clique o **o servidor de aplicativo** ou **navegador** botão.

Além da [Relíquia novo padrão de interface de usuário][] e [painel drill-down][] funções, o painel de visão geral de aplicativos tem funções adicionais.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Se você quiser...</strong></th>
<th align="left"><strong>Faça isso...</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Mostrar informações do painel para o servidor ou o navegador do aplicativo selecionado</td>
<td align="left">Clique o <strong>Aplicativo servidor</strong> ou <strong>navegador</strong> botão.</td>
</tr>
<tr class="even">
<td align="left">Exibir níveis de limite para seu aplicativo <a href="https://newrelic.com/docs/site/apdex">Apdex</a> pontuação</td>
<td align="left">Aponte para o resultado de Apdex <strong>? <strong>icon.</strong></strong></td>
</tr>
<tr class="odd">
<td align="left">Exibir detalhes de Apdex em todo o mundo</td>
<td align="left">Do visualização geral do <strong>navegador</strong>, aponte em qualquer lugar no mapa de Apdex globais.<br /><strong>Dica:</strong> Para ir diretamente para o painel de controle <a href="https://newrelic.com/docs/site/geography">geografia</a> do aplicativo selecionado, clique no título <strong>Apdex globais</strong> ou clique em qualquer lugar no mapa Apdex Global.</td>
</tr>
<tr class="even">
<td align="left">Exibir o <a href="https://docs.newrelic.com/docs/applications-menu/transactions-dashboard">Web transações</a> painel de controle</td>
<td align="left">Clique na tabela de transações na Web no painel Visão geral de aplicativos. Ou, para exibir detalhes sobre uma transação web específica (incluindo <a href="https://newrelic.com/docs/site/key-transactions">Chave transações</a>), clique em seu nome.</td>
</tr>
<tr class="odd">
<td align="left">Modo de exibição de <a href="https://newrelic.com/docs/site/errors">erros</a> painel de controle</td>
<td align="left">Clique no título do gráfico de taxa de erro no painel Visão geral dos aplicativos.<br /><strong>Dica:</strong> Você também pode exibir o painel de erros a partir de <strong>Aplicativos</strong> &gt; (seu aplicativo) &gt; Eventos &gt; Erros.</td>
</tr>
<tr class="even">
<td align="left">Exibir detalhes do servidor do aplicativo</td>
<td align="left"><p>Faça uma das opções a seguir:</p>
<p></p>
<ul>
<li>Alternar entre um modo de exibição de tabela dos hosts ou detalhes de métrica de análise de cada host.</li>
<li>Clique no nome de um servidor individual.</li>
<li>Aponte para pontuação de Apdex do servidor individual da lista.</li>
<li>Clique em um servidor individual da CPU ou memória.</li>
</ul>
</p>
</p></td>
</tr>
</tbody>
</table>

Abaixo está um exemplo do painel de visão geral de aplicativos quando você selecionar o modo de exibição do navegador.

![Console do Gerenciador de Pacotes][1]

## Próximas etapas

Confira estes recursos adicionais para obter mais informações:

-   [Instalação do agente do .NET nos sites do Azure][]: Procedimentos de instalação de agente do .NET do New Relic
-   [Interface do usuário do New Relic][]:
    Visão geral da IU do New Relic, definir direitos de usuário e perfis usando as funções padrão e detalhes de detalhamento do painel de controle
-   [Visão Geral dos Aplicativos][]: Recursos e funções ao usar o painel de visão geral de aplicativos do New Relic
-   [Apdex][]: Visão geral de como o Apdex mede a satisfação dos usuários finais com seu aplicativo
-   [Monitoramento do Usuário Real][]: Visão geral de como RUM detalha o tempo que leva para
    navegadores de seus usuários carregar suas páginas da Web, de onde eles vêm, e quais navegadores usam
-   [Encontrar Ajuda][]: Recursos disponibilizados pelo centro de ajuda on-line do New Relic

  [usando o novo Relíquia]: #using-new-relic
  [página nova Relíquia no armazenamento do Azure]: http://www.windowsazure.com/pt-br/gallery/store/new-relic/new-relic/
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [Implantando um aplicativo Web ASP.NET a um Site do Azure usando o Visual Studio (a página pode estar em inglês)]: http://www.windowsazure.com/pt-br/develop/net/tutorials/get-started/
  [Desenvolver e implantar um site com o Microsoft WebMatrix (a página pode estar em inglês)]: http://www.windowsazure.com/pt-br/develop/net/tutorials/website-with-webmatrix/
  [Console do Gerenciador de Pacotes]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelicAzureNuget04.png
  [Insira a chave de licença]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrvslicensekey.png
  [Botão do NuGet na guia Início]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetbutton.png
  [Galeria do NuGet procurando por NewRelic.Azure.WebSites]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmnugetgallery.png
  [Pasta do New Relic expandida com newrelic.conf selecionado]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrwmlicensekey.png
  [Imagem dos campos de complemento]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nraddon.png
  [Imagem dos campos de personalização]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/nrcustom.png
  [Painel de monitoramento do New Relic]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app.png
  [Relíquia novo padrão de interface de usuário]: https://newrelic.com/docs/site/the-new-relic-ui#functions
  [painel drill-down]: https://newrelic.com/docs/site/the-new-relic-ui#drilldown
  [Apdex]: https://newrelic.com/docs/site/apdex
  [geografia]: https://newrelic.com/docs/site/geography
  [Web transações]: https://docs.newrelic.com/docs/applications-menu/transactions-dashboard
  [Chave transações]: https://newrelic.com/docs/site/key-transactions
  [erros]: https://newrelic.com/docs/site/errors
  [1]: ./media/store-new-relic-web-sites-dotnet-application-performce-management/NewRelic_app_browser.png
  [Instalação do agente do .NET nos sites do Azure]: https://newrelic.com/docs/dotnet/azure-web-sites-beta#manual_install
  [Interface do usuário do New Relic]: https://newrelic.com/docs/site/the-new-relic-ui
  [Visão Geral dos Aplicativos]: https://newrelic.com/docs/site/applications-overview
  [Monitoramento do Usuário Real]: https://newrelic.com/docs/features/real-user-monitoring
  [Encontrar Ajuda]: https://newrelic.com/docs/site/finding-help
