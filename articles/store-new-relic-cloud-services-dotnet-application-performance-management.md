<properties linkid="develop-net-how-to-guides-new-relic" urlDisplayName="New Relic" pageTitle="Using New Relic with Azure - Azure feature guides" metaKeywords="" description="Learn how to use the New Relic service to manage and monitor your Azure application." metaCanonical="" services="" documentationCenter=".NET" title="New Relic Application Performance Management on Azure" authors="" solutions="" manager="dwrede" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="new relic"></tags>

# Gerenciamento de desempenho do aplicativo New Relic no Azure

Este guia descreve como adicionar o monitoramento de
desempenho de classe mundial do New Relic aos seus aplicativos hospedados no Windows Azure. Abordaremos o
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

New Relic Standard é oferecido gratuitamente aos usuários do Azure
New Relic Pro é oferecido com base no tamanho da instância para os serviços de nuvem do Azure

Para informações sobre preços, consulte o [página nova Relíquia no armazenamento do Azure][]

<div class="dev-callout"> 
<strong>Observa&ccedil;&atilde;o:</strong>
<p>Pre&ccedil;os somente &eacute; listada para at&eacute; 10 inst&acirc;ncias de computa&ccedil;&atilde;o. Para um n&uacute;mero maior que 10 entre em contato com o novo Rel&iacute;quia (sales@newrelic.com) para o pre&ccedil;o por volume.</p>
</div>

Azure clientes recebem uma assinatura de avaliação de 2 semanas do nova Relíquia Pro ao implantar o agente Relíquia de novo.

## Inscreva-se no novo Relíquia usando o armazenamento do Azure

Relíquia nova integra-se perfeitamente com funções de funções do Azure da Web e de trabalho.

Para inscrever-se para nova Relíquia diretamente do armazenamento do Azure, siga estas três etapas simples.

### Etapa 1. Inscrever-se por meio da Azure Store

1.  Faça logon no [Portal de Gerenciamento do Azure][].
2.  No painel inferior do portal de gerenciamento, clique em **Novo**.
3.  Clique em **Repositório**.
4.  Na caixa de diálogo **Escolher um complemento**, selecione **New Relic** e clique em **Próximo**.
5.  Na caixa de diálogo **Personalizar complemento**, selecione o plano New Relic que você deseja.
6.  Digite um código de promoção, se aplicável.
7.  Digite um nome para como o serviço novo Relíquia aparecerá nas configurações do Azure
    ou usar o valor padrão **NewRelic**. O nome deve ser exclusivo na
     sua lista de itens inscritos da Azure Store.
8.  Escolha um valor para a região; por exemplo, **oeste dos Estados Unidos**.
9.  Clique em **Próximo**.
10. Na caixa de diálogo **Revisar Compra**, revise o plano e as informações sobre preços,
    bem como os termos legais. Se você concordar com os termos, clique em **Comprar**
11. Depois que você clicar em **Comprar**, a sua conta New Relic iniciará o processo de criação. Você pode monitorar o status no portal de gerenciamento do Azure.
12. Para recuperar a chave de licença Relíquia de novo, clique em **valores de saída**.
13. Copie a chave de licença é exibida. Você precisará inseri-la quando você instala o pacote novo Relíquia Nuget.

### Etapa 2. Instalar o pacote do Nuget

1.  Abrir sua solução do Visual Studio ou criar um novo selecionando
    **arquivo \> Novo \> projeto**.

    ![Visual Studio][]

2.  Se você não tiver um projeto de serviço de nuvem do Azure em sua
    solução, adicione um mouse em seu aplicativo no
    Solution Explorer e selecionando **Adicionar projeto de serviço de nuvem do Azure**.

    ![Criar serviço de nuvem][]

3.  Abra o console do Gerenciador de Pacotes selecionando **Ferramentas \> Gerenciador de Pacotes da Biblioteca \>
    Console do Gerenciador de Pacotes**. Defina seu projeto como o projeto padrão na
     parte superior da janela do Console do Gerenciador de Pacotes.

    ![Console do Gerenciador de Pacotes][]

4.  No prompt de comando do Gerenciador de Pacotes, digite `Install-Package    NewRelicWindowsAzure` e pressione **Enter**.

    ![instalar o Gerenciador de pacotes][]

5.  No prompt de chave de licença, insira a chave de licença que você recebeu da Azure Store.

    ![Insira a chave de licença][]

6.  Opcional: No prompt de nome do aplicativo, digite o nome de seu aplicativo da maneira como ele será
     exibido no painel do New Relic. Ou, use o nome da solução como o padrão.

    ![Digite o nome do aplicativo][]

7.  A partir do Gerenciador de Soluções, clique com o botão direito do mouse no Projeto de Serviço de Nuvem e selecione **Publicar**.

    ![o projeto de nuvem pública][]

**Observação:** Se esta for a primeira vez que implantar esse aplicativo no Azure, será solicitado que você insira suas
credenciais do Azure. Para obter mais informações, consulte [Implantando um aplicativo web ASP.NET em um site do Azure][].

![configurações de publicação][]

### Etapa 3. Verificar o desempenho do aplicativo no New Relic.

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
<td align="left">Do visualização geral do <strong>navegador</strong>, aponte em qualquer lugar no mapa de Apdex globais.<br /><strong>Dica:</strong> Para ir diretamente para o painel de controle <a href="https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard">geografia</a> do aplicativo selecionado, clique no título <strong>Apdex globais</strong> ou clique em qualquer lugar no mapa Apdex Global.</td>
</tr>
<tr class="even">
<td align="left">Exibir o <a href="https://newrelic.com/docs/applications-dashboards/web-transactions">Web transações</a> painel de controle</td>
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

-   [Instalação do agente do .NET no Azure][]: Procedimentos de instalação de agente do .NET do New Relic
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
  [Visual Studio]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png
  [Criar serviço de nuvem]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png
  [Console do Gerenciador de Pacotes]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png
  [instalar o Gerenciador de pacotes]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png
  [Insira a chave de licença]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png
  [Digite o nome do aplicativo]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png
  [o projeto de nuvem pública]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png
  [Implantando um aplicativo web ASP.NET em um site do Azure]: /pt-br/develop/net/tutorials/get-started/
  [configurações de publicação]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png
  [Painel de monitoramento do New Relic]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png
  [Relíquia novo padrão de interface de usuário]: https://newrelic.com/docs/site/the-new-relic-ui#functions
  [painel drill-down]: https://newrelic.com/docs/site/the-new-relic-ui#drilldown
  [Apdex]: https://newrelic.com/docs/site/apdex
  [geografia]: https://docs.newrelic.com/docs/new-relic-browser/geography-dashboard
  [Web transações]: https://newrelic.com/docs/applications-dashboards/web-transactions
  [Chave transações]: https://newrelic.com/docs/site/key-transactions
  [erros]: https://newrelic.com/docs/site/errors
  [1]: ./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png
  [Instalação do agente do .NET no Azure]: https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure
  [Interface do usuário do New Relic]: https://newrelic.com/docs/site/the-new-relic-ui
  [Visão Geral dos Aplicativos]: https://newrelic.com/docs/site/applications-overview
  [Monitoramento do Usuário Real]: https://newrelic.com/docs/features/real-user-monitoring
  [Encontrar Ajuda]: https://newrelic.com/docs/site/finding-help
