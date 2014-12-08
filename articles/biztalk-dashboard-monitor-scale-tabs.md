<properties urlDisplayName="Dashboard, Monitor, Scale, Configure and Hybrid Connections tabs" pageTitle="Painel, Monitor e escala nos Serviços BizTalk | Azure" metaKeywords="BizTalk Services, Azure, dashboard, monitor, scale, wabs, mabs" description="Learn about the controls on the Management Portal tabs for BizTalk Services: Dashboard, Monitor, and Scale." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Monitor and Scale tabs" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />




# Serviços do BizTalk: Guias Painel, Monitor, Escala, Configurar e Conexão Híbrida

Ao abrir o Portal de Gerenciamento do Azure pela primeira vez, você será levado automaticamente para a guia **TODOS OS ITENS**. As colunas da guia **TODOS OS ITENS** podem ser classificadas. Para exibir seu Serviço BizTalk, selecione o Serviço BizTalk na guia **TODOS OS ITENS** ou selecione a guia **SERVIÇOS BIZTALK** e, em seguida, selecione o nome do seu Serviço BizTalk.

Isso abre uma nova janela com as guias a seguir. Este tópico descreve essas guias.

- ![Quick Start][QuickStart]  [Início rápido](#QuickStart)

- [Painel](#Dashboard)

- [Monitoramento](#Monitor)

- [Escala](#Scale)

- [Configurar](#Configure)

- [Conexões Híbridas](#HybridConnections)


##<a name="QuickStart"></a>Início rápido (![Quick Start][QuickStart])
Dependendo da Edição dos Serviços do BizTalk, é possível que não todas as opções listadas estejam disponíveis. 
<table border="1">
    <tr>
        <td><strong>Obter as ferramentas</strong></td>

        <td>Baixe o SDK dos Serviços BizTalk para instalar os modelos do projeto do Visual Studio em seu computador de desenvolvimento local. Esses modelos criam os projetos do Visual Studio <strong>Serviços BizTalk</strong> (ponte) e <strong>Artefatos do Serviço do BizTalk</strong> (Transformação), que são implantados em seu Serviço do BizTalk.
        <br/><br/>
		<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">  Como começo a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês) </a> e <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Instalando o SDK dos Serviços BizTalk do Azure</a> (a página pode estar em inglês) listam as etapas para começar.
        </td>
    </tr>

    <tr>
        <td><strong>Criar contratos de parceiro</strong></td>

        <td>Abre o Portal dos Serviços BizTalk hospedados no Azure onde você adiciona parceiros e cria contratos EDI X12, AS2 e EDIFACT.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurando componentes para mensagens EDI no portal dos Serviços do BizTalk</a> (a página pode estar em inglês) lista as etapas para começar.
        </td>
    </tr>

<tr>
        <td><strong>Saiba mais sobre os Serviços do BizTalk</strong></td>
        <td>Acesse o <a HREF="http://azure.microsoft.com/pt-br/documentation/services/biztalk-services/">centro de aprendizado</a> para saber mais sobre os Serviços BizTalk do Azure.</td>
</tr>
</table>


Na barra de tarefas na parte inferior, você pode:

<table border="1">

<tr>
<td><strong>Gerenciar</strong> a implantação de seu aplicativo</td>
<td>Abre o Portal dos Serviços BizTalk do Azure. O Portal dos Serviços BizTalk é a entrada para a configuração de EDI, inclusive a adição de parceiros e criação de acordos AS2 e X12 e EDIFACT.
<br/><br/>
Isso é o mesmo que <strong>Criar contratos de parceiro</strong> na guia <strong>Início Rápido</strong>.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurando componentes para mensagens de EDI no Portal de Serviços BizTalk (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Serviços BizTalk.</td>
</tr>

<tr>
<td><strong>Informação da Conexão</strong> do Namespace de Controle de Acesso</td>
<td>Quando você seleciona Informações de Conexão, o Namespace do Controle de Acesso, o Emissor Padrão e a Chave Padrão são exibidos. Você pode copiar esses valores.
<br/><br/>
Você também pode abrir o Portal de Gerenciamento de Controle de Acesso. Esse Portal de Gerenciamento de Controle de Acesso equivale a usar a opção <strong>Active Directory</strong> no painel de navegação à esquerda.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gerenciando o namespace do ACS (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Gerenciamento de Controle de Acesso.</td>
</tr>

<tr>
<td><strong>Chaves de Sincronização</strong> na conta de armazenamento</td>
<td>Quando você cria uma conta de armazenamento, uma chave primária e uma secundária são criadas automaticamente. Essas chaves de criptografia controlam o acesso à sua conta de armazenamento. Seu Serviço do BizTalk usa automaticamente a chave primária. <strong>A opção Sincronizar Chaves</strong> permite que os usuários alternem entre as chaves primária e secundária sem interromper o Serviço do BizTalk.
<br/><br/>
 Por exemplo, se você desejar que o Serviço do BizTalk use uma nova chave primária para a conta do armazenamento. Para fazer isso:
<br/><br/>
<ol>
<li>Selecione o Serviço do BizTalk e selecione <strong>Sincronizar Chaves</strong>. Selecione a chave secundária. Quando você faz isso, o Serviço do BizTalk começa a usar a chave secundária.</li>
<li>No Portal de Gerenciamento do Azure, clique em sua Conta de Armazenamento e regenere a Chave Primária. Lembre-se que seu Serviço do BizTalk está usando a chave secundária.</li>
<li>Selecione o Serviço do BizTalk e selecione <strong>Sincronizar Chaves</strong>. Agora, selecione a chave primário. Esta é a nova chave primária que você regenerou.</li>
<li>No Portal de Gerenciamento do Azure, clique em sua Conta de Armazenamento e regenere a Chave Secundária.</li>
</ol>
<br/>
Esse processo é chamado de "chaves de substituição". O propósito é permitir que os usuários alternem entre as chaves primária e secundária sem interromper o Serviço do BizTalk.</td>
</tr>

<tr>
<td><strong>Excluir</strong> seu aplicativo</td>
<td>Quando você seleciona Excluir, seu Serviço do BizTalk e todos os itens implantados nele são removidos.</td>
</tr>
</table>


##<a name="Dashboard"></a>Painel
Dependendo da Edição dos Serviços do BizTalk, é possível que não todas as opções listadas estejam disponíveis. 

Quando você seleciona o nome do Serviço do BizTalk, a guia de Painel é exibida. O painel exibe o seguinte:

##### Visão geral sobre o uso: Mostra o número de Conexões Híbridas usadas
Também mostra os dados usados em GB. 

##### Gráfico de métrica: Mostra uma lista fixa de métricas de desempenho
Essas métricas fornecem valores em tempo real relacionados à integridade do Serviço do BizTalk. Você também pode especificar os valores **Relativo** ou **Absoluto** e o intervalo de tempo **Intervalo** das métricas que são mostradas no gráfico. 

Para obter uma descrição desses métricas de desempenho, acesse [Métricas disponíveis](#Metrics) neste tópico.


#####Visão Rápida: Lista as propriedades do Serviço BizTalk

<table border="1">

<tr>
<td><strong>Atualização das credenciais do banco de dados de acompanhamento</strong></td>
<td>Altera o nome de usuário e senha usado para fazer logon no banco de dados de acompanhamento.</td>
</tr>
<tr>
<td><strong>Atualização do certificado de SSL</strong></td>
<td>Pode modificar o Serviço do BizTalk para usar um certificado SSL diferente. Um Certificado SSL autoassinado é criado automaticamente quando você <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">cria o Serviço do BizTalk</a>.</td>
</tr>
<tr>
<td><strong>Baixar Certificado</strong></td>
<td>Você pode baixar o certificado SSL usado pelo Serviço de BizTalk em uma máquina local.</td>
</tr>
<tr>
<td><strong>Status</strong></td>
<td>Mostra o status atual do Serviço do BizTalk. Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">Serviços do BizTalk: Gráfico de estado de serviço</a>. </td>
</tr>
<tr>
<td><strong>URL do Serviço</strong></td>
<td>A URL para o seu Serviço do BizTalk. Ela é igual à <strong>URL do Domínio</strong> inserida quando o Serviço do BizTalk é criado.</td>
</tr>
<tr>
<td><strong>Endereço IP virtual público (VIP)</strong></td>
<td>O endereço IP atribuído ao seu Serviço do BizTalk. Ele é usado para todos os pontos de extremidade de entrada e é o endereço de origem para o tráfego de saída. Esse endereço IP pertence ao seu Serviço do BizTalk enquanto ele for provisionado. Se você excluir o Serviço do BizTalk, o endereço IP será atribuído a outro Serviço do BizTalk.</td>
</tr>
<tr>
<td><strong>Namespace do ACS</strong></td>
<td>Autenticados com o Serviço do BizTalk.</td>
</tr>
<tr>
<td><strong>Edição</strong></td>
<td>Lista a Edição inserida quando o Serviço do BizTalk é criado.</td>
</tr>
<tr>
<td><strong>Local</strong></td>
<td>Mostra a região geográfica que hospeda seu Serviço do BizTalk.</td>
</tr>
<tr>
<td><strong>Criação</strong></td>
<td>Mostra a data e a hora em que o Serviço do BizTalk foi criado.</td>
</tr>
<tr>
<td><strong>Banco de Dados de Acompanhamento</strong></td>
<td>O nome do Banco de Dados SQL que armazena as tabelas de acompanhamento usadas pelo Serviço do BizTalk. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Requisitos Explicados</a>  fornece detalhes sobre o Banco de Dados de Acompanhamento.</td>
</tr>
<tr>
<td><strong>Armazenamento de monitoramento/arquivamento</strong></td>
<td>O nome da conta de Armazenamento do Azure que armazena a saída do monitoramento de seu Serviço do BizTalk.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Requisitos Explicados</a> fornece detalhes sobre a conta de Armazenamento.</td>
</tr>
<tr>
<td><strong>Nome da assinatura</strong></td>
<td>Lista a assinatura que hospeda seu Serviço do BizTalk. A assinatura rege o acesso ao Portal de Gerenciamento do Azure.</td>
</tr>
<tr>
<td><strong>ID da assinatura</strong></td>
<td>Quando uma assinatura é criada, uma ID da assinatura é gerada automaticamente. Ao usar APIs REST, talvez seja necessário inserir a ID da assinatura</td>
</tr>
</table>

[Serviços do BizTalk: O Provisionamento usando o Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280) lista as etapas para criar um Serviço do BizTalk.


#####Gerenciar, Informação de Conexão, Chaves de sincronização, e Excluir na barra de tarefas:

<table border="1">

<tr>
<td><strong>Gerenciar</strong> a implantação de seu aplicativo</td>
<td>Abre o Portal dos Serviços BizTalk do Azure. O Portal dos Serviços BizTalk é a entrada para a configuração de EDI, inclusive a adição de parceiros e criação de acordos AS2 e X12 e EDIFACT.
<br/><br/>
Isso é o mesmo que <strong>Criar contratos de parceiro</strong> na guia <strong>Início Rápido</strong>.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurando componentes para mensagens de EDI no Portal de Serviços BizTalk (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Serviços BizTalk.</td>
</tr>
<tr>
<td><strong>Informação da Conexão</strong> do Namespace de Controle de Acesso</td>
<td>Mostra o Namespace do Controle de Acesso, Emissor Padrão, e valores de Chave Padrão; que podem ser copiados.
<br/><br/>
Você também pode abrir o Portal de Gerenciamento de Controle de Acesso. Esse Portal de Gerenciamento de Controle de Acesso equivale a usar a opção Active Directory no painel de navegação à esquerda.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gerenciando o namespace do ACS (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Gerenciamento de Controle de Acesso.</td>
</tr>
<tr>
<td><strong>Chaves de Sincronização</strong> na conta de armazenamento</td>
<td>Quando você cria uma conta de armazenamento, uma chave primária e uma secundária são criadas automaticamente. Essas chaves de criptografia controlam o acesso à sua conta de armazenamento. Seu Serviço do BizTalk usa automaticamente a chave primária. <strong>A opção Sincronizar Chaves</strong> permite que os usuários alternem entre as chaves primária e secundária sem interromper o Serviço do BizTalk.
<br/><br/>
 Por exemplo, se você desejar que o Serviço do BizTalk use uma nova chave primária para a conta do armazenamento. Para fazer isso:
<br/><br/>
<ol>
<li>Selecione o Serviço do BizTalk e selecione <strong>Sincronizar Chaves</strong>. Selecione a chave secundária. Quando você faz isso, o Serviço do BizTalk começa a usar a chave secundária.</li>
<li>No Portal de Gerenciamento do Azure, clique em sua Conta de Armazenamento e regenere a Chave Primária. Lembre-se que seu Serviço do BizTalk está usando a chave secundária.</li>
<li>Selecione o Serviço do BizTalk e selecione <strong>Sincronizar Chaves</strong>. Agora, selecione a chave primário. Esta é a nova chave primária que você regenerou.</li>
<li>No Portal de Gerenciamento do Azure, clique em sua Conta de Armazenamento e regenere a Chave Secundária.</li>
</ol>
<br/>
Esse processo é chamado de "chaves de substituição". O propósito é permitir que os usuários alternem entre as chaves primária e secundária sem interromper o Serviço do BizTalk.</td>
</tr>

<tr>
<td><strong>Excluir</strong> seu aplicativo</td>
<td>Seu Serviço do BizTalk e todos os itens implantados a ele são removidos.</td>
</tr>
</table>


##<a name="Monitor"></a>Monitoramento
Não é aplicado à Edição Gratuita.

Ao selecionar o nome do seu Serviço do BizTalk, a guia do Monitor está disponível e mostra o seguinte:

##### Gráfico de métrica: Mostra as métricas de desempenho selecionadas
Essas métricas fornecem valores em tempo real relacionados à integridade do Serviço do BizTalk. Você escolhe quais métricas de desempenho são exibidas. Um máximo de seis métricas de desempenho podem ser exibidas simultaneamente. 

Você também pode especificar os valores **Relativo** ou **Absoluto** e o intervalo de tempo **Intervalo** das métricas que são mostradas. 

##### Para remover ou exibir métricas no gráfico:
1. Selecione a guia **Monitoramento**.
2. Selecione **Adicionar Métricas** na barra de tarefas:
<br/>
![Select Add Metrics][AddMetrics]
3. Verifique as métricas de desempenho que você deseja mostrar.
4. Clique na marca de seleção para retornar à guia **Monitoramento**.
5. Selecione o círculo ao lado de métrica para exibir o valor dela no gráfico.
<br/>
Por exemplo, a métrica **Uso de CPU** fica esmaecida; sua saída não é exibida no gráfico:
<br/>
![CPU Usage metric is grayed out][GrayedMetric]
<br/>
Clique no círculo esmaecido para habilitar a métrica **Uso de CPU** e exibir sua saída no gráfico:
<br/>
![CPU Usage metric is enabled][EnabledMetric]

6. Para remover uma métrica do gráfico de exibição e da lista, selecione **Excluir Métrica** na barra de tarefas. Para adicionar a métrica à lista novamente, selecione **Adicionar Métricas** na barra de tarefas, selecione a métrica e clique na marca de seleção para retornar para a guia **Monitorar**. Selecione o círculo esmaecido para habilitar a métrica.

##<a name="Metrics"></a>Métricas disponíveis
As contagens/métricas de desempenho a seguir estão disponíveis:

<table border="1">

<tr>
<td><strong>Latência de viagem de ida e volta</strong></td>
<td>Mostra a média do tempo em milissegundos (ms) que leva para processar uma mensagem a partir do tempo em que a mensagem é recebida até que a mensagem seja completamente processada pelo Serviço do BizTalk através de todas as pontes. São contadas apenas mensagens processadas com êxito.<br/><br/>
Quando os eventos a seguir ocorrerem, será criado um carimbo de data/hora:
<ul>
<li>A mensagem entra no gateway</li>
<li>A mensagem é roteada ao destino</li>
<li>A resposta do destino é recebida</li>
<li>A resposta da confirmação do destino é enviada ao gateway</li>
</ul>
<br/>
Esta métrica mostra o resultado do seguinte cálculo:
<br/><br/>
[Resposta de confirmação do destino enviada ao gateway] - [A mensagem entra no gateway]</td>
</tr>
<tr>
<td><strong>Falhas na origem</strong></td>
<td>Exibe o número total de mensagens que falharam quando o Serviço do BizTalk puxa as mensagens nos pontos de extremidade de origem.</td>
</tr>
<tr>
<td><strong>Uso da CPU</strong></td>
<td>Lista a média da % de Tempo do Processador para todas as instâncias de função.</td>
</tr>
<tr>
<td><strong>Latência de processamento</strong></td>
<td>Exibe o tempo médio para processar uma mensagem pelo Serviço do BizTalk em todas as pontes, exceto o tempo gasto nos destinos. São contadas apenas mensagens processadas com êxito.<br/><br/>
Quando cada um dos eventos a seguir ocorre, é criado um carimbo de data/hora:

<ul>
<li>A mensagem entra no gateway</li>
<li>A mensagem é roteada ao destino</li>
<li>A resposta do destino é recebida</li>
<li>A resposta da confirmação do destino é enviada ao gateway</li>
</ul>
<br/>Esta métrica mostra o resultado do seguinte cálculo:<br/><br/>
[DResposta de confirmação do destino enviada ao gateway] - [A mensagem entra no gateway] - [A resposta do destino é recebida] + [A mensagem é roteada ao destino]</td>
</tr>
<tr>
<td><strong>Falhas no processo</strong></td>
<td>Exibe o número total de mensagens que falharam durante o processamento pelo Serviço do BizTalk em todas as pontes em um intervalo de tempo.</td>
</tr>
<tr>
<td><strong>Mensagens Enviadas</strong></td>
<td>Exibe o número total de mensagens enviadas pelo Serviço do BizTalk por todas as pontes dentro em um intervalo de tempo. Essa métrica é incrementada quando uma mensagem enviada de uma pipeline alcança o destino da rota. Essa métrica não indica se uma mensagem foi processada com êxito.<br/><br/>
Em um cenário de solicitação-resposta, a métrica é incrementada quando o destino de rota envia uma confirmação de recebimento de volta à pipeline.</td>
</tr>
<tr>
<td><strong>Mensagens Recebidas</strong></td>
<td>Exibe o número total de mensagens recebidas pelo Serviço do BizTalk por todas as pontes dentro em um intervalo de tempo. Essa métrica é incrementada quando uma nova mensagem é recebida pela pipeline.</td>
</tr>
<tr>
<td><strong>Mensagens em Processamento</strong></td>
<td>Exibe o número total de mensagens sendo processadas no momento pelo Serviço do BizTalk em um intervalo de tempo.</td>
</tr>
<tr>
<td><strong>Mensagens Processadas</strong></td>
<td>Exibe o número total de mensagens processadas com êxito pelo Serviço do BizTalk por todas as pontes dentro em um intervalo de tempo. Essa métrica é incrementada quando uma mensagem é recebida com êxito pela pipeline e roteada com êxito ao destino.</td>
</tr>
</table>


##<a name="Scale"></a>Escala
Na guia Escala, você pode adicionar ou subtrair o número de unidades usadas pelo Serviço do BizTalk. Por padrão, há uma unidade configurada. Unidades adicionais podem ser adicionadas para dimensionar seu Serviço do BizTalk. Quando você aumenta a escala, você está aumentando a produção. A quantidade de recursos também aumenta, inclusive as pontes implantadas, os acordos, as conexões de LOB e a capacidade de processamento. Por exemplo, você aumenta a escala de uma unidade para duas. Nesta situação, você pode implantar duas vezes mais pontes, acordos, conexões de LOB e dobrar a capacidade de processamento.

Algumas edições do BizTalk não oferecem uma opção da escala. Nesta situação, uma unidade é permitida. Para determinar a quantidade de unidades de dimensionamento da sua edição, consulte [Serviços do BizTalk: Tabela de edições](http://go.microsoft.com/fwlink/p/?LinkID=302279).

Aumentar o número de unidades pode afetar o preço. Se você aumentar as unidades, selecione **Salvar** para exibir uma mensagem que informa se a cobrança é afetada. Você escolhe continuar. Quando você aumenta o número de unidades, o status do Serviço do BizTalk é alterado de Ativo para Atualizando. Durante a atualização, seu Serviço do BizTalk continua funcionado.

[Serviços do BizTalk: O Gráfico de Edições](http://go.microsoft.com/fwlink/p/?LinkID=302279) define uma "Unidade".


##<a name="Configure"></a>Configurar
Não é aplicado para Conexões Híbridas.

Define o Status de Backup para Nenhum ou Automático. Quando estiver definido para Nenhum, nenhum backup será criado automaticamente. Quando estiver definido para Automático, você configura o local do backup, a frequência do backup, e por quanto tempo manter os arquivos de backup. 

[Serviços do BizTalk: Backup e Restauração](http://go.microsoft.com/fwlink/p/?LinkID=329873) fornece mais detalhes. 


##<a name="HybridConnections"></a>Conexões Híbridas
As Conexões Híbridas se conectam um aplicativo do Azure, como Sites ou Serviços Móveis, a um recurso local que utilize uma porta TCP estática, como SQL Server, MySQL, APIs da web HTTP, e os serviços Web mais personalizados. As Conexões Híbridas são gerenciadas nos Serviços BizTalk, no Portal de Gerenciamento do Azure.

Para criar conexões híbridas nos sites do Azure, consulte [conexão híbrida: Conectar um site do Azure a um recurso local](http://go.microsoft.com/fwlink/p/?LinkId=397538).

Para usar Conexões Híbridas nos Serviços Móveis do Azure, consulte [Serviços Móveis do Azure e Conexões Híbridas](http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started).

Para criar ou gerenciar as Conexões Híbridas nos Serviços BizTalk do Azure, consulte [Conexões Híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274).



## Avançar
Agora que está familiarizado com as diferentes guias, você pode obter mais informações sobre os recursos dos Serviços BizTalk do Azure:

- [Serviços do BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Serviços do BizTalk: nome e chave do emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Serviços do BizTalk: Backup e restauração](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>

## Consulte também
- [Conexões Híbridas](http://go.microsoft.com/fwlink/p/?LinkID=397274)
- [Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços do BizTalk: Gráfica de Estado do Serviço do BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
