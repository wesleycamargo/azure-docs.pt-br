<properties 
	pageTitle="Painel, Monitor, Escala, Configurar e Conexões Híbridas nos Serviços BizTalk | Microsoft Azure"
	description="Saiba mais sobre os controles e monitore o desempenho nas guias do Portal de Gerenciamento para os Serviços BizTalk: Painel, Monitor, Escala, Configurar e Conexões Híbridas MABS, WABS"
	services="biztalk-services"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2015"
	ms.author="mandia"/>




# Examine as guias Painel, Monitor, Escala, Configurar e Conexão Híbrid

Depois de criar o Serviço BizTalk e implantar seu aplicativo, você pode alterar algumas configurações do Serviço BizTalk e monitorar o desempenho do aplicativo.

Ao abrir o Portal de Gerenciamento do Azure, você será levado automaticamente para a guia **TODOS OS ITENS**. Para exibir seu Serviço do BizTalk, selecione o Serviço do BizTalk na guia **TODOS OS ITENS** ou selecione a guia **SERVIÇOS BIZTALK** e, em seguida, selecione o nome do seu Serviço do BizTalk.

Isso abre uma nova janela com as guias a seguir. Este tópico descreve essas guias.

## Início Rápido (![Início rápido][QuickStart])
Dependendo da Edição dos Serviços do BizTalk, é possível que não todas as opções listadas estejam disponíveis. 
<table border="1"> <tr> <td><strong>Obtenha as ferramentas</strong></td>
<table border="1">
    <tr>
        <td><strong>Obtenha as ferramentas</strong></td>

        <td>Download the BizTalk Services SDK to install the Visual Studio project templates on your on-premises development computer. These templates create the <strong>BizTalk Services</strong> (bridge) and the <strong>BizTalk Service Artifacts</strong> (Transform) Visual Studio projects that are deployed to your BizTalk Service.
        <br/><br/>
		<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335"> How do I Start Using the Azure BizTalk Services SDK </a> and <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Installing the Azure BizTalk Services SDK</a> lists the steps to get started.
        </td>
    </tr>

    <tr>
        <td><strong>Create partner agreements</strong></td>

        <td>Opens the Azure BizTalk Services Portal hosted on Azure where you add partners and create X12, AS2, and EDIFACT EDI agreements.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuring Components for EDI Messaging on BizTalk Services Portal</a> lists the steps to get started.
        </td>
    </tr>

<tr>
        <td><strong>Saiba mais sobre os Serviços BizTalk</strong></td>
        <td>Acesse o <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">centro de aprendizado</a> para saber mais sobre os Serviços do BizTalk do Azure.</td>
</tr>
</table>


Na barra de tarefas na parte inferior, você pode:

<table border="1">

<tr>
<td><strong>Gerenciar</strong> seu aplicativo implantado</td>
<td>Abre o Portal dos Serviços do BizTalk do Azure. O Portal dos Serviços do BizTalk é a entrada para a configuração de EDI, inclusive a adição de parceiros e criação de acordos AS2 e X12 e EDIFACT.
<br/><br/>
É igual à opção <strong>Criar contratos de parceiros</strong> na guia <strong>Início Rápido</strong>.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurando componentes para mensagens de EDI no Portal de Serviços do BizTalk (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Serviços do BizTalk.</td>
</tr>

<tr>
<td><strong>Informação da conexão</strong> do Namespace de Controle de Acesso</td>
<td>Quando você seleciona Informações de Conexão, o Namespace do Controle de Acesso, o Emissor Padrão e a Chave Padrão são exibidos. Você pode copiar esses valores.
<br/><br/>
Você também pode abrir o Portal de Gerenciamento de Controle de Acesso. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Criar um namespace do Controle de Acesso</a> fornece mais informações sobre o Portal de Gerenciamento de Controle de Acesso.</td>
</tr>

<tr>
<td><strong>Chaves de sincronização</strong> na conta de armazenamento</td>
<td>Quando você cria uma Conta de Armazenamento, uma chave primária e uma chave secundária são criadas automaticamente. Essas chaves de criptografia controlam o acesso à sua conta de armazenamento. Seu Serviço do BizTalk usa automaticamente a chave primária. A opção<strong>Sincronizar Chaves</strong> permite que os usuários alternem entre as chaves primária e secundária sem interromper o Serviço do BizTalk.
<br/><br/>
Por exemplo, você deseja que o Serviço do BizTalk use uma nova Chave Primária para a Conta de Armazenamento. Para fazer isso:
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


## Painel
Dependendo da Edição dos Serviços do BizTalk, é possível que não todas as opções listadas estejam disponíveis.

Quando você seleciona o nome do Serviço do BizTalk, a guia de Painel é exibida. No Painel, você pode:

##### Visão geral do usuário: mostra o número de Conexões Híbridas usadas
Também mostra os dados usados em GB.

##### Gráfico de métrica: mostra uma lista fixa de métricas de desempenho
Essas métricas fornecem valores em tempo real relacionados à integridade do Serviço do BizTalk. Você também pode especificar os valores **Relativo** ou **Absoluto** e o **Intervalo** de tempo das métricas que são mostradas no gráfico.

Para obter uma descrição dessas métricas de desempenho, acesse [Métricas disponíveis](#Metrics) neste tópico.


##### Visão Rápida: lista as propriedades do Serviço BizTalk

<table border="1">

<tr>
<td><strong>Atualização das credenciais do Banco de Dados de Acompanhamento</strong></td>
<td>Altera o nome de usuário e senha usado para fazer logon no banco de dados de acompanhamento.</td>
</tr>
<tr>
<td><strong>Atualização do Certificado de SSL</strong></td>
<td>É possível atualizar o Serviço BizTalk para usar um certificado SSL diferente. Um Certificado SSL autoassinado é criado automaticamente ao <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">criar o Serviço do BizTalk</a>.</td>
</tr>
<tr>
<td><strong>Baixar Certificado</strong></td>
<td>Você pode baixar o certificado SSL usado pelo Serviço de BizTalk em uma máquina local.</td>
</tr>
<tr>
<td><strong>Status</strong></td>
<td>Mostra o status atual do Serviço do BizTalk. Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">Serviços BizTalk: gráfico de estado do serviço</a>. </td>
</tr>
<tr>
<td><strong>URL do Serviço</strong></td>
<td>A URL para o seu Serviço do BizTalk. É igual à <strong>URL do Domínio</strong> inserida quando o Serviço do BizTalk é criado.</td>
</tr>
<tr>
<td><strong>Endereço IP Virtual Público (VIP)</strong></td>
<td>O endereço IP atribuído ao seu Serviço do BizTalk. Ele é usado para todos os pontos de extremidade de entrada e é o endereço de origem para o tráfego de saída. Esse endereço IP pertence ao seu Serviço BizTalk contanto que ele seja criado. Se você excluir o Serviço do BizTalk, o endereço IP será atribuído a outro Serviço do BizTalk.</td>
</tr>
<tr>
<td><strong>Namespace do ACS</strong></td>
<td>Autentica com o Serviço do BizTalk.</td>
</tr>
<tr>
<td><strong>Edição</strong></td>
<td>Lista a Edição inserida quando o Serviço do BizTalk é criado.</td>
</tr>
<tr>
<td><strong>Localidade</strong></td>
<td>Mostra a região geográfica que hospeda seu Serviço do BizTalk.</td>
</tr>
<tr>
<td><strong>Criado</strong></td>
<td>Mostra a data e a hora em que o Serviço do BizTalk foi criado.</td>
</tr>
<tr>
<td><strong>Acompanhamento de banco de dados</strong></td>
<td>O nome do Banco de Dados SQL que armazena as tabelas de acompanhamento usadas pelo Serviço do BizTalk. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Requisitos explicados</a> fornece detalhes sobre o Banco de Dados de Acompanhamento.</td>
</tr>
<tr>
<td><strong>Armazenamento de Monitoramento/Arquivamento</strong></td>
<td>O nome da conta de Armazenamento do Azure que armazena a saída do monitoramento de seu Serviço do BizTalk.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Requisitos explicados</a> fornece detalhes sobre a conta de Armazenamento.</td>
</tr>
<tr>
<td><strong>Nome da Assinatura</strong></td>
<td>Lista a assinatura que hospeda seu Serviço do BizTalk. A assinatura rege o acesso ao Portal de Gerenciamento do Azure.</td>
</tr>
<tr>
<td><strong>ID da Assinatura</strong></td>
<td>Quando uma assinatura é criada, uma ID da assinatura é gerada automaticamente. Ao usar APIs REST, talvez seja necessário inserir a ID da assinatura</td>
</tr>
</table>

[Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure ](http://go.microsoft.com/fwlink/p/?LinkID=302280) lista as etapas para criar um Serviço BizTalk.


##### Gerenciar, Informação de Conexão, Chaves de sincronização, e Excluir na barra de tarefas:

<table border="1">

<tr>
<td><strong>Gerenciar</strong> seu aplicativo implantado</td>
<td>Abre o Portal dos Serviços do BizTalk do Azure. O Portal dos Serviços do BizTalk é a entrada para a configuração de EDI, inclusive a adição de parceiros e criação de acordos AS2 e X12 e EDIFACT.
<br/><br/>
É igual à opção <strong>Criar contratos de parceiros</strong> na guia <strong>Início Rápido</strong>.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurando componentes para mensagens de EDI no Portal de Serviços do BizTalk (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Serviços do BizTalk.</td>
</tr>
<tr>
<td><strong>Informação da conexão</strong> do Namespace de Controle de Acesso</td>
<td>Mostra o Namespace do Controle de Acesso, Emissor Padrão, e valores de Chave Padrão; que podem ser copiados.
<br/><br/>
Você também pode abrir o Portal de Gerenciamento de Controle de Acesso. Esse Portal de Gerenciamento de Controle de Acesso é igual ao uso da opção Active Directory no painel de navegação à esquerda.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gerenciando o namespace do ACS (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Gerenciamento de Controle de Acesso.</td>
</tr>
<tr>
<td><strong>Chaves de sincronização</strong> na conta de armazenamento</td>
<td>Quando você cria uma Conta de Armazenamento, uma chave primária e uma chave secundária são criadas automaticamente. Essas chaves de criptografia controlam o acesso à sua conta de armazenamento. Seu Serviço do BizTalk usa automaticamente a chave primária. A opção<strong>Sincronizar Chaves</strong> permite que os usuários alternem entre as chaves primária e secundária sem interromper o Serviço do BizTalk.
<br/><br/>
Por exemplo, você deseja que o Serviço do BizTalk use uma nova Chave Primária para a Conta de Armazenamento. Para fazer isso:
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


## Monitoramento
Não é aplicado à Edição Gratuita.

Ao selecionar o nome do seu Serviço do BizTalk, a guia do Monitor está disponível e mostra o seguinte:

##### Gráfico de Métricas: exibe as métricas de desempenho selecionadas
Essas métricas fornecem valores em tempo real relacionados à integridade do Serviço do BizTalk. Você escolhe quais métricas de desempenho são exibidas. No máximo seis métricas de desempenho podem ser exibidas simultaneamente.

Você também pode especificar os valores **Relativo** ou **Absoluto** e o **Intervalo** de tempo das métricas exibidas.

##### Para remover ou exibir métricas no gráfico:
1. Selecione a guia **Processo**.
2. Selecione **Adicionar Métricas** na barra de tarefas:
<br/>
![Selecione Adicionar Métricas][AddMetrics]
3. Verifique as métricas de desempenho que você deseja mostrar.
4. Selecione a marca de seleção para retornar à guia **Monitoramento**.
5. Selecione o círculo ao lado de métrica para exibir o valor dela no gráfico.
<br/>
Por exemplo, a métrica **Uso do CPU** está esmaecida; seu resultado não é exibido no gráfico:
<br/>
![A métrica Uso de CPU fica esmaecida][GrayedMetric]
<br/>
Selecione o círculo esmaecido para ativar a métrica **Uso do CPU ** para exibir seu resultado no gráfico:
<br/>
![A métrica Uso de CPU está habilitada][EnabledMetric]

6. Para remover uma métrica do gráfico de exibição e da lista, selecione **Excluir Métricas** na barra de tarefas. Para adicionar a métrica à lista novamente, selecione **Adicionar Métricas** na barra de tarefas, selecione a métrica e clique na marca de seleção para retornar para a guia **Monitorar**. Selecione o círculo esmaecido para habilitar a métrica.

## <a name="Metrics"></a>Métricas disponíveis
As contagens/métricas de desempenho a seguir estão disponíveis:

<table border="1">

<tr>
<td><strong>Latência de Viagem de Ida e Volta</strong></td>
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
[Resposta de confirmação do destino enviada ao gateway] - [Mensagem entra no gateway]</td>
</tr>
<tr>
<td><strong>Falhas na Origem</strong></td>
<td>Exibe o número total de mensagens que falharam quando o Serviço do BizTalk puxa as mensagens nos pontos de extremidade de origem.</td>
</tr>
<tr>
<td><strong>Uso da CPU</strong></td>
<td>Lista a média da % de Tempo do Processador para todas as instâncias de função.</td>
</tr>
<tr>
<td><strong>Latência de Processamento</strong></td>
<td>Exibe o tempo médio para processar uma mensagem pelo Serviço do BizTalk em todas as pontes, exceto o tempo gasto nos destinos. São contadas apenas mensagens processadas com êxito.<br/><br/>
Quando cada um dos eventos a seguir ocorre, é criado um carimbo de data/hora:

<ul>
<li>A mensagem entra no gateway</li>
<li>A mensagem é roteada ao destino</li>
<li>A resposta do destino é recebida</li>
<li>A resposta da confirmação do destino é enviada ao gateway</li>
</ul>
<br/>Esta métrica mostra o resultado do seguinte cálculo:<br/><br/>
[Resposta da confirmação do destino enviada ao gateway] – [Mensagem entra no gateway] – [A resposta do destino é recebida] + [A mensagem é roteada ao destino]</td>
</tr>
<tr>
<td><strong>Falhas no Processo</strong></td>
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


## Escala
Na guia Escala, você pode adicionar ou subtrair o número de unidades usadas pelo Serviço do BizTalk. Por padrão, há uma unidade configurada. Unidades adicionais podem ser adicionadas para dimensionar seu Serviço do BizTalk. Quando você aumenta a escala, você está aumentando a produção. A quantidade de recursos também aumenta, inclusive as pontes implantadas, os acordos, as conexões de LOB e a capacidade de processamento. Por exemplo, você aumenta a escala de uma unidade para duas. Nessa situação, você pode implantar duas vezes mais pontes, contratos, conexões de LOB e a capacidade de processamento.

Algumas edições do BizTalk não oferecem uma opção de escala. Nesta situação, uma unidade é permitida. Para determinar a quantidade de unidades de dimensionamento da sua edição, consulte [Serviços BizTalk: gráfico de edições](biztalk-editions-feature-chart.md)

Aumentar o número de unidades pode afetar os preços. Se você aumentar as unidades, selecione **Salvar** para exibir uma mensagem que informa se a cobrança é afetada. Você escolhe continuar. Quando você aumenta o número de unidades, o status do Serviço do BizTalk é alterado de Ativo para Atualizando. Durante o estado Atualizando, seu Serviço do BizTalk continua funcionando.

[Serviços BizTalk: gráfico de edições](biztalk-editions-feature-chart.md) define uma “Unidade”.


## Configurar
Não é aplicado para Conexões Híbridas.

Define o Status de Backup para Nenhum ou Automático. Quando estiver definido para Nenhum, nenhum backup será criado automaticamente. Quando estiver definido para Automático, você configura o local do backup, a frequência do backup, e por quanto tempo manter os arquivos de backup.

[Serviços BizTalk: backup e restauração](biztalk-backup-restore.md) fornece os detalhes.


## <a name="HybridConnections"></a>Conexões Híbridas
As Conexões Híbridas se conectam um aplicativo do Azure, como Sites ou Serviços Móveis, a um recurso local que utilize uma porta TCP estática, como SQL Server, MySQL, APIs da web HTTP, e os serviços Web mais personalizados. As Conexões Híbridas são gerenciadas nos Serviços BizTalk, no Portal de Gerenciamento do Azure.

Para criar Conexões Híbridas nos Sites do Azure, consulte [Conexão Híbrida: conecte um site do Azure a um recurso local](http://go.microsoft.com/fwlink/p/?LinkId=397538)

Para usar Conexões Híbridas nos Serviços Móveis do Azure, consulte [Serviços Móveis do Azure e Conexões Híbridas](../mobile-services-dotnet-backend-hybrid-connections-get-started.md).

Para criar ou gerenciar as Conexões Híbridas nos Serviços do BizTalk do Azure, consulte [Conexões Híbridas](integration-hybrid-connection-overview.md).



## Avançar
Agora que está familiarizado com as diferentes guias, você pode obter mais informações sobre os recursos dos Serviços BizTalk do Azure:

- [Serviços BizTalk: limitação](biztalk-throttling-thresholds.md)  
- [Serviços BizTalk: nome e chave do emissor](biztalk-issuer-name-issuer-key.md)  
- [Serviços BizTalk: backup e restauração](biztalk-backup-restore.md)

## Consulte também
- [Conexões Híbridas](integration-hybrid-connection-overview.md)  
- [Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium](biztalk-editions-feature-chart.md)  
- [Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure](biztalk-provision-services.md)  
- [Serviços BizTalk: gráfico de estado do Serviço do BizTalk (a página pode estar em inglês)](biztalk-service-state-chart.md)  
- [Como começar a usar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
 

<!-----HONumber=August15_HO9-->