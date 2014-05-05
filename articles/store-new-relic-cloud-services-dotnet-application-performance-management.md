<properties linkid="develop-net-how-to-guides-new-relic" urlDisplayName="Nova Relíquia" pageTitle="usando o novo Relíquia com o Azure - recurso Azure guias" metaKeywords="" description="aprender a usar o serviço de novo Relíquia para gerenciar e monitorar o seu aplicativo do Azure." metaCanonical="" services="" documentationCenter=".NET" title="Novo gerenciamento de desempenho de aplicativo Relíquia no Azure" authors="" solutions="" manager="" editor="" />



#Gerenciamento de desempenho do aplicativo New Relic no Azure

Este guia descreve como adicionar o monitoramento de desempenho de classe mundial do New Relic a seus aplicativos hospedados do Azure. Abordaremos o processo simples e rápido para adicionar nova Relíquia ao seu aplicativo e apresentar alguns dos recursos do novo Relíquia. Para obter mais informações sobre como usar o novo Relíquia, consulte [usando o novo Relíquia](#using-new-relic)

O que há de novo Relíquia?
--

O New Relic é uma ferramenta focada no desenvolvedor que monitora seus aplicativos de produção e fornece informações detalhadas sobre seu desempenho e confiabilidade. Ele é projetado para economizar tempo ao identificar e diagnosticar problemas de desempenho, e ele coloca as informações necessárias para solucionar esses problemas em suas mãos.

Relíquia nova controla o tempo de carregamento e taxa de transferência para a transação da web, tanto do servidor e navegadores dos usuários. Ele mostra quanto tempo você gasta no banco de dados, analisa consultas lentas e solicitações da web, fornece monitoramento de tempo de atividade e alertas, faixas exceções de aplicativos e muito mais.

Nova Relíquia especial de preços por meio do armazenamento do Azure
--

Nova Relíquia padrão é gratuito para usuários do Azure
Nova Relíquia Pro é oferecido com base no tamanho da instância para os serviços de nuvem do Azure

Para informações sobre preços, consulte o [página nova Relíquia no armazenamento do Azure](http://www.windowsazure.com/pt-br/gallery/store/new-relic/new-relic/)

<div class="dev-callout"> 
<strong>Observação:</strong>
<p>Preços somente é listada para até 10 instâncias de computação. Para um número maior que 10 entre em contato com o novo Relíquia (sales@newrelic.com) para o preço por volume.</p>
</div>

Azure clientes recebem uma assinatura de avaliação de 2 semanas do nova Relíquia Pro ao implantar o agente Relíquia de novo.

Inscreva-se no novo Relíquia usando o armazenamento do Azure
--

Relíquia nova integra-se perfeitamente com funções de funções do Azure da Web e de trabalho.

Para inscrever-se para nova Relíquia diretamente do armazenamento do Azure, siga estas três etapas simples.

### Etapa 1. se inscrever por meio do armazenamento do Azure

1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
2. No painel inferior do portal de gerenciamento, clique em **Novo**.
3. Clique em **Repositório**.
4. Na caixa de diálogo **Escolher um complemento**, selecione **New Relic** e clique em **Avançar**.
5. Na caixa de diálogo **Personalizar complemento**, selecione o plano New Relic que você deseja.
6. Digite um código de promoção, se aplicável.
7. Digite um nome para como o serviço novo Relíquia aparecerá nas configurações do Azure ou usar o valor padrão **NewRelic** O nome deve ser exclusivo na
   sua lista de itens inscritos da Azure Store.
8. Escolha um valor para a região; por exemplo, **oeste dos EUA**.
9. Clique em **Avançar**.
10. Na caixa de diálogo **Revisar Compra**, revise o plano e as informações sobre preços, 
      bem como os termos legais. Se você concordar com os termos, clique em **Comprar**
11. Depois que você clicar em **Comprar**, a sua conta New Relic iniciará o processo de criação. Você pode monitorar o status no portal de gerenciamento do Azure.
12. Para recuperar a chave de licença Relíquia de novo, clique em **valores de saída** 
13. Copie a chave de licença é exibida. Você precisará inseri-la quando você instala o pacote novo Relíquia Nuget.

### Etapa 2. instalar o pacote do Nuget

1. Abrir sua solução do Visual Studio ou criar um novo selecionando
   **arquivo > Novo > projeto**

	![Visual Studio](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget01.png)

2. Se você não tiver um projeto de serviço de nuvem do Azure em sua solução, adicione um mouse em seu aplicativo no Solution Explorer e selecionando **Adicionar projeto de serviço de nuvem do Azure**

	![Criar serviço de nuvem](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget02.png)

3. Abra o Console do Gerenciador de Pacote selecionando **Ferramentas > Gerenciador de Pacote da Biblioteca >
     Console do Gerenciador de Pacote**. Defina seu projeto como o projeto padrão na parte superior da janela do Console do Gerenciador de Pacotes.

	![Console do Gerenciador de Pacotes](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget04.png)

4. No prompt de comando do Gerenciador de Pacotes, digite `NewRelicWindowsAzure de pacote de instalação` e pressione **Enter**

	![instalar o Gerenciador de pacotes](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget06.png)

5. No prompt de chave de licença, insira a chave de licença que você recebeu do armazenamento do Azure.

	![Insira a chave de licença](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget07.png)

6. Opcional: No prompt do nome do aplicativo, insira nome do aplicativo como ele será exibido no painel de controle do novo Relíquia. Ou, use o nome da solução como o padrão.

	![Digite o nome do aplicativo](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget08.png)

7. A partir do Gerenciador de Soluções, clique com o botão direito do mouse no Projeto de Serviço de Nuvem e selecione **Publicar**.

	![o projeto de nuvem pública](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget09.png)


**Observação:** se esta for a primeira vez que implantar esse aplicativo no Azure, será solicitado que você insira suas credenciais do Azure. Para obter mais informações, consulte <a href="/pt-br/develop/net/tutorials/get-started/">Implantando um aplicativo web ASP.NET em um site do Azure</a>.

![configurações de publicação](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelicAzureNuget10.png)

### Etapa 3. Check-out de desempenho do aplicativo em Relíquia de novo.

Para exibir o painel novo Relíquia:

1. No portal do Azure, clique o **gerenciar** botão.
2. Entrar com seu e-mail do Relíquia nova conta e senha.
3. Na barra de menu Novo Relíquia, selecione **aplicativos > (nome do aplicativo)**

	O **Monitoring > Visão geral** painel aparece automaticamente.

	![Relíquia novo painel de monitoramento](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app.png

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
       <td>Exibir níveis de limite para seu aplicativo <a href="https://newrelic.com/docs/site/apdex" target="_blank">Apdex</a> pontuação</td>
       <td>Aponte para o resultado de Apdex <b>?<b> icon.</b></b></td>
    </tr>
    <tr>
       <td>Exibir detalhes de Apdex em todo o mundo</td>
       <td>Do visão geral <b>navegador</b> exibir, aponte em qualquer lugar no mapa de Apdex globais.<br /><b>Dica:</b> para ir diretamente para o aplicativo selecionado <a href="https://newrelic.com/docs/site/geography" target="_blank">geografia</a>painel de controle, clique no <b>Apdex globais</b> título ou clique em qualquer lugar no mapa Apdex Global.</td>
    </tr>
    <tr>
       <td>Exibir o <a href="https://newrelic.com/docs/applications-dashboards/web-transactions" target="_blank">Web transações</a> painel de controle</td>
       <td>Clique na tabela de transações na Web no painel Visão geral de aplicativos. Ou, para exibir detalhes sobre uma transação web específica (incluindo <a href="https://newrelic.com/docs/site/key-transactions" target="_blank">Chave transações</a>), clique em seu nome.</td>
    </tr>
    <tr>
       <td>Modo de exibição de <a href="https://newrelic.com/docs/site/errors" target="_blank">erros</a> painel de controle</td>
       <td>Clique no título do gráfico de taxa de erro no painel Visão geral sobre aplicativos.<br /><b>Dica:</b> você também pode exibir o painel de controle de erros de <b>aplicativos de</b> &gt; (seu aplicativo) &gt; eventos &gt; erros.</td>
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

![Console do Gerenciador de Pacotes](./media/store-new-relic-cloud-services-dotnet-application-performce-management/NewRelic_app_browser.png)

## Próximas etapas

Confira estes recursos adicionais para obter mais informações:

 * [a instalação do agente de .NET no Azure](https://newrelic.com/docs/dotnet/installing-the-net-agent-on-azure): os procedimentos de instalação de novo agente de .NET Relíquia 
 * [a nova Interface de usuário Relíquia](https://newrelic.com/docs/site/the-new-relic-ui) 
Visão geral da nova Relíquia UI, definir direitos de usuário e perfis, usando as funções padrão e detalhes de detalhamento do painel de controle
 * [Visão geral sobre aplicativos](https://newrelic.com/docs/site/applications-overview): recursos e funções ao usar o painel de visão geral de aplicativos do novo Relíquia
 * [Apdex](https://newrelic.com/docs/site/apdex): Visão geral de como o Apdex mede a satisfação dos usuários finais com seu aplicativo
 * [Monitoramento de usuários real](https://newrelic.com/docs/features/real-user-monitoring): Visão geral de como RUM detalha o tempo que leva para navegadores de seus usuários para carregar suas páginas da Web, onde eles vêm, e quais navegadores usam
 * [Encontrar Ajuda](https://newrelic.com/docs/site/finding-help): recursos disponíveis por meio do Centro de ajuda on-line do novo Relíquia

