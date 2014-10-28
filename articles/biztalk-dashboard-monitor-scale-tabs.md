<properties linkid="manage-services-biztalk-services-dashboard-monitor-scale-tabs" urlDisplayName="Dashboard, Monitor, Scale, Configure and Hybrid Connections tabs" pageTitle="Dashboard, Monitor, and Scale in BizTalk Services | Azure" metaKeywords="BizTalk Services, Azure, dashboard, monitor, scale, wabs, mabs" description="Learn about the controls on the Management Portal tabs for BizTalk Services: Dashboard, Monitor, and Scale." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Monitor and Scale tabs" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia"></tags>

# Serviços do BizTalk: Guias Painel, Monitor, Escala, Configurar e Conexão Híbrida

Na primeira vez que você abre o Portal de Gerenciamento do Azure, você é levado automaticamente para a guia **TODOS OS ITENS**. As colunas da guia **TODOS OS ITENS** podem ser classificadas. Para exibir seu Serviço do BizTalk, selecione o Serviço do BizTalk na guia **TODOS OS ITENS** ou selecione a guia **SERVIÇOS BIZTALK** e, em seguida, selecione o nome do seu Serviço do BizTalk.

Isso abre uma nova janela com as seguintes guias: Este tópico descreve essas guias.

-   ![Início Rápido][Início Rápido] [Início Rápido][1]

-   [Painel][Painel]

-   [Monitoramento][Monitoramento]

-   [Escala][Escala]

-   [Configurar][Configurar]

-   [Conexões Híbridas][Conexões Híbridas]

## <a name="QuickStart"></a>Início Rápido (![Início Rápido][Início Rápido])

Dependendo da Edição dos Serviços do BizTalk, é possível que não todas as opções listadas estejam disponíveis.

<table border="1">

<tr>

<td>
**Obter as ferramentas**

</td>
</p>
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
<td>
**Saiba mais sobre os Serviços do BizTalk**

</td>
<td>
Acesse o [centro de aprendizado][centro de aprendizado] para saber mais sobre os Serviços do BizTalk do Azure.

</td>
</tr>
</table>
Na barra de tarefas na parte inferior, você pode:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Gerenciar</strong> seu aplicativo implantado</td>
<td align="left">Abre o Portal dos Serviços do BizTalk do Azure. O Portal dos Serviços do BizTalk é a entrada para a configuração de EDI, inclusive a adição de parceiros e criação de acordos AS2 e X12 e EDIFACT.<br /><br /> É igual à opção <strong>Criar contratos de parceiros</strong> na guia <strong>Início Rápido</strong>.<br /><br /> <a href="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurando componentes para mensagens de EDI no Portal de Serviços do BizTalk (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Serviços do BizTalk.</td>
</tr>
<tr class="even">
<td align="left"><strong>Informação da conexão</strong> do Namespace de Controle de Acesso</td>
<td align="left">Quando você seleciona Informações de Conexão, o Namespace do Controle de Acesso, o Emissor Padrão e a Chave Padrão são exibidos. Você pode copiar esses valores.<br /><br /> Você também pode abrir o Portal de Gerenciamento de Controle de Acesso. Esse Portal de Gerenciamento de Controle de Acesso é igual ao uso da opção <strong>Active Directory</strong> no painel de navegação à esquerda.<br /><br /> <a href="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gerenciando o namespace do ACS (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Gerenciamento de Controle de Acesso.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Chaves de sincronização</strong> na conta de armazenamento</td>
<td align="left">Quando você cria uma Conta de Armazenamento, uma chave primária e uma chave secundária são criadas automaticamente. Essas chaves de criptografia controlam o acesso à sua conta de armazenamento. Seu Serviço do BizTalk usa automaticamente a chave primária. A opção<strong>Sincronizar Chaves</strong> permite que os usuários alternem entre as chaves primária e secundária sem interromper o Serviço do BizTalk.<br /><br /> Por exemplo, você deseja que o Serviço do BizTalk use uma nova Chave Primária para a Conta de Armazenamento. Para fazer isso:<br /><br />
<ol>
<li>Selecione o Serviço do BizTalk e selecione <strong>Sincronizar Chaves</strong>. Selecione a chave secundária. Quando você faz isso, o Serviço do BizTalk começa a usar a chave secundária.</li>
<li>No Portal de Gerenciamento do Azure, clique em sua Conta de Armazenamento e regenere a Chave Primária. Lembre-se que seu Serviço do BizTalk está usando a chave secundária.</li>
<li>Selecione o Serviço do BizTalk e selecione <strong>Sincronizar Chaves</strong>. Agora, selecione a chave primário. Esta é a nova chave primária que você regenerou.</li>
<li>No Portal de Gerenciamento do Azure, clique em sua Conta de Armazenamento e regenere a Chave Secundária.</li>
</ol>
<br /> Esse processo é chamado de &quot;chaves de substituição&quot;. O propósito é permitir que os usuários alternem entre as chaves primária e secundária sem interromper o Serviço do BizTalk.</td>
</tr>
<tr class="even">
<td align="left"><strong>Excluir</strong> seu aplicativo</td>
<td align="left">Quando você seleciona Excluir, seu Serviço do BizTalk e todos os itens implantados nele são removidos.</td>
</tr>
</tbody>
</table>

## <a name="Dashboard"></a>Painel

Dependendo da Edição dos Serviços do BizTalk, é possível que não todas as opções listadas estejam disponíveis.

Quando você seleciona o nome do Serviço do BizTalk, a guia de Painel é exibida. O painel exibe o seguinte:

##### Visão geral sobre o uso: Mostra o número de Conexões Híbridas usadas

Também mostra os dados usados em GB.

##### Gráfico de métrica: Mostra uma lista fixa de métricas de desempenho

Essas métricas fornecem valores em tempo real relacionados à integridade do Serviço do BizTalk. Você também pode especificar os valores **Relativo** ou **Absoluto** e o intervalo de tempo **Intervalo** das métricas que são mostradas no gráfico.

Para obter uma descrição dessas métricas de desempenho, acesse [Métricas disponíveis][Métricas disponíveis] neste tópico.

##### Visão Rápida: Lista as propriedades do Serviço BizTalk

|---------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Atualização das credenciais do banco de dados de acompanhamento** | Altera o nome de usuário e senha usado para fazer logon no banco de dados de acompanhamento.                                                                                                                                                                                                                                                      |
| **Atualização do certificado de SSL**                               | Pode modificar o Serviço do BizTalk para usar um certificado SSL diferente. Um Certificado SSL autoassinado é criado automaticamente ao [criar o Serviço do BizTalk][criar o Serviço do BizTalk].                                                                                                                                                                           |
| **Baixar Certificado**                                              | Você pode baixar o certificado SSL usado pelo Serviço de BizTalk em uma máquina local.                                                                                                                                                                                                                                                            |
| **Status**                                                          | Mostra o status atual do Serviço do BizTalk. Consulte [Serviços do BizTalk: Gráfico de estado de serviço][Serviços do BizTalk: Gráfico de estado de serviço].                                                                                                                                                                                                                                      |
| **URL do Serviço**                                                  | A URL para o seu Serviço do BizTalk. É igual à **URL do Domínio** inserida quando o Serviço do BizTalk é criado.                                                                                                                                                                                                                                  |
| **Endereço IP virtual público (VIP)**                               | O endereço IP atribuído ao seu Serviço do BizTalk. Ele é usado para todos os pontos de extremidade de entrada e é o endereço de origem para o tráfego de saída. Esse endereço IP pertence ao seu Serviço do BizTalk enquanto ele for provisionado. Se você excluir o Serviço do BizTalk, o endereço IP será atribuído a outro Serviço do BizTalk. |
| **Namespace do ACS**                                                | Autenticados com o Serviço do BizTalk.                                                                                                                                                                                                                                                                                                            |
| **Edição**                                                          | Lista a Edição inserida quando o Serviço do BizTalk é criado.                                                                                                                                                                                                                                                                                     |
| **Local**                                                           | Mostra a região geográfica que hospeda seu Serviço do BizTalk.                                                                                                                                                                                                                                                                                    |
| **Criação**                                                         | Mostra a data e a hora em que o Serviço do BizTalk foi criado.                                                                                                                                                                                                                                                                                    |
| **Banco de Dados de Acompanhamento**                                | O nome do Banco de Dados SQL que armazena as tabelas de acompanhamento usadas pelo Serviço do BizTalk.                                                                                                                                                                                                                                            
                                                                        [Requisitos explicados][criar o Serviço do BizTalk] fornece detalhes sobre o Banco de Dados de Acompanhamento.                                                                                                                                                                                                                                    |
| **Armazenamento de monitoramento/arquivamento**                     | O nome da conta de Armazenamento do Azure que armazena a saída do monitoramento de seu Serviço do BizTalk.                                                                                                                                                                                                                                        
                                                                        [Requisitos explicados][criar o Serviço do BizTalk] fornece detalhes sobre a conta de Armazenamento.                                                                                                                                                                                                                                              |
| **Nome da assinatura**                                              | Lista a assinatura que hospeda seu Serviço do BizTalk. A assinatura rege o acesso ao Portal de Gerenciamento do Azure.                                                                                                                                                                                                                            |
| **ID da assinatura**                                                | Quando uma assinatura é criada, uma ID da assinatura é gerada automaticamente. Ao usar APIs REST, talvez seja necessário inserir a ID da assinatura                                                                                                                                                                                               |

[Serviços do BizTalk: Provisionamento usando o Portal de Gerenciamento do Windows Azure][criar o Serviço do BizTalk] lista as etapas para criar um Serviço do BizTalk.

##### Gerenciar, Informação de Conexão, Chaves de sincronização, e Excluir na barra de tarefas:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Gerenciar</strong> seu aplicativo implantado</td>
<td align="left">Abre o Portal dos Serviços do BizTalk do Azure. O Portal dos Serviços do BizTalk é a entrada para a configuração de EDI, inclusive a adição de parceiros e criação de acordos AS2 e X12 e EDIFACT.<br /><br /> É igual à opção <strong>Criar contratos de parceiros</strong> na guia <strong>Início Rápido</strong>.<br /><br /> <a href="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurando componentes para mensagens de EDI no Portal de Serviços do BizTalk (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Serviços do BizTalk.</td>
</tr>
<tr class="even">
<td align="left"><strong>Informação da conexão</strong> do Namespace de Controle de Acesso</td>
<td align="left">Mostra o Namespace do Controle de Acesso, Emissor Padrão, e valores de Chave Padrão; que podem ser copiados.<br /><br /> Você também pode abrir o Portal de Gerenciamento de Controle de Acesso. Esse Portal de Gerenciamento de Controle de Acesso é igual ao uso da opção Active Directory no painel de navegação à esquerda.<br /><br /> <a href="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gerenciando o namespace do ACS (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Gerenciamento de Controle de Acesso.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Chaves de sincronização</strong> na conta de armazenamento</td>
<td align="left">Quando você cria uma Conta de Armazenamento, uma chave primária e uma chave secundária são criadas automaticamente. Essas chaves de criptografia controlam o acesso à sua conta de armazenamento. Seu Serviço do BizTalk usa automaticamente a chave primária. A opção<strong>Sincronizar Chaves</strong> permite que os usuários alternem entre as chaves primária e secundária sem interromper o Serviço do BizTalk.<br /><br /> Por exemplo, você deseja que o Serviço do BizTalk use uma nova Chave Primária para a Conta de Armazenamento. Para fazer isso:<br /><br />
<ol>
<li>Selecione o Serviço do BizTalk e selecione <strong>Sincronizar Chaves</strong>. Selecione a chave secundária. Quando você faz isso, o Serviço do BizTalk começa a usar a chave secundária.</li>
<li>No Portal de Gerenciamento do Azure, clique em sua Conta de Armazenamento e regenere a Chave Primária. Lembre-se que seu Serviço do BizTalk está usando a chave secundária.</li>
<li>Selecione o Serviço do BizTalk e selecione <strong>Sincronizar Chaves</strong>. Agora, selecione a chave primário. Esta é a nova chave primária que você regenerou.</li>
<li>No Portal de Gerenciamento do Azure, clique em sua Conta de Armazenamento e regenere a Chave Secundária.</li>
</ol>
<br /> Esse processo é chamado de &quot;chaves de substituição&quot;. O propósito é permitir que os usuários alternem entre as chaves primária e secundária sem interromper o Serviço do BizTalk.</td>
</tr>
<tr class="even">
<td align="left"><strong>Excluir</strong> seu aplicativo</td>
<td align="left">Seu Serviço do BizTalk e todos os itens implantados a ele são removidos.</td>
</tr>
</tbody>
</table>

## <a name="Monitor"></a>Monitorar

Não é aplicado à Edição Gratuita.

Ao selecionar o nome do seu Serviço do BizTalk, a guia do Monitor está disponível e mostra o seguinte:

##### Gráfico de métrica: Mostra as métricas de desempenho selecionadas

Essas métricas fornecem valores em tempo real relacionados à integridade do Serviço do BizTalk. Você escolhe quais métricas de desempenho são exibidas. No máximo seis métricas de desempenho podem ser exibidas simultaneamente.

Você também pode especificar os valores **Relativo** ou **Absoluto** e o intervalo de tempo **Intervalo** das métricas que são mostradas.

##### Para remover ou exibir métricas no gráfico:

1.  Selecione a guia **Processo**.
2.  Selecione **Adicionar Métricas** na barra de tarefas:
    ![Selecionar Adicionar Métricas][Selecionar Adicionar Métricas]
3.  Verifique que as métricas de desempenho que você deseja mostrar.
4.  Selecione a marca de seleção para retornar à guia **Monitoramento**.
5.  Selecione o círculo do lado da métrica para mostrar o valor dessa métrica no gráfico.
    Por exemplo, a métrica **Uso do CPU** fica esmaecida; sua saída não é mostrada no gráfico:
    ![A métrica de Uso do CPU fica esmaecida][A métrica de Uso do CPU fica esmaecida]
    Selecione o círculo esmaecido para habilitar a métrica **Uso do CPU** para mostrar sua saída no gráfico:
    ![A métrica de Uso do CPU é habilitada][A métrica de Uso do CPU é habilitada]

6.  Para remover uma métrica do gráfico de exibição e da lista, selecione **Excluir Métricas** na barra de tarefas. Para adicionar a métrica à lista novamente, selecione **Adicionar Métricas** na barra de tarefas, selecione a métrica e clique na marca de seleção para retornar para a guia **Monitorar**. Selecione o círculo esmaecido para habilitar a métrica.

## <a name="Metrics"></a>Métricas disponíveis

As contagens/métricas de desempenho a seguir estão disponíveis:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Latência de viagem de ida e volta</strong></td>
<td align="left">Mostra a média do tempo em milissegundos (ms) que leva processar uma mensagem a partir do tempo que a mensagem é recebida até que a mensagem seja completamente processada pelo Serviço do BizTalk através de todas as pontes. São contadas apenas mensagens processadas com êxito.<br /><br /> Quando os eventos a seguir ocorrerem, será criado um carimbo de data/hora:
<ul>
<li>A mensagem entra no gateway</li>
<li>A mensagem é roteada ao destino</li>
<li>A resposta do destino é recebida</li>
<li>A resposta da confirmação do destino é enviada ao gateway</li>
</ul>
<br /> Esta métrica mostra o resultado do seguinte cálculo:<br /><br /> [Resposta de confirmação do destino enviada ao gateway] - [Mensagem entra no gateway]</td>
</tr>
<tr class="even">
<td align="left"><strong>Falhas na origem</strong></td>
<td align="left">Exibe o número total de mensagens que falharam quando o Serviço do BizTalk puxa as mensagens nos pontos de extremidade de origem.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Uso da CPU</strong></td>
<td align="left">Lista a média da % de Tempo do Processador para todas as instâncias de função.</td>
</tr>
<tr class="even">
<td align="left"><strong>Latência de processamento</strong></td>
<td align="left">Exibe o tempo médio para processar uma mensagem pelo Serviço do BizTalk em todas as pontes, exceto o tempo gasto nos destinos. São contadas apenas mensagens processadas com êxito.<br /><br /> Quando cada um dos eventos a seguir ocorre, é criado um carimbo de data/hora:
<ul>
<li>A mensagem entra no gateway</li>
<li>A mensagem é roteada ao destino</li>
<li>A resposta do destino é recebida</li>
<li>A resposta da confirmação do destino é enviada ao gateway</li>
</ul>
<br />Esta métrica mostra o resultado do seguinte cálculo:<br /><br /> [Resposta da confirmação do destino enviada ao gateway] – [Mensagem entra no gateway] – [A resposta do destino é recebida] + [A mensagem é roteada ao destino]</td>
</tr>
<tr class="odd">
<td align="left"><strong>Falhas no processo</strong></td>
<td align="left">Exibe o número total de mensagens que falharam durante o processamento pelo Serviço do BizTalk em todas as pontes em um intervalo de tempo.</td>
</tr>
<tr class="even">
<td align="left"><strong>Mensagens Enviadas</strong></td>
<td align="left">Exibe o número total de mensagens enviadas pelo Serviço do BizTalk por todas as pontes dentro em um intervalo de tempo. Essa métrica é incrementada quando uma mensagem enviada de uma pipeline alcança o destino da rota. Essa métrica não indica se uma mensagem foi processada com êxito.<br /><br /> Em um cenário de solicitação-resposta, a métrica é incrementada quando o destino de rota envia uma confirmação de recebimento de volta à pipeline.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Mensagens Recebidas</strong></td>
<td align="left">Exibe o número total de mensagens recebidas pelo Serviço do BizTalk por todas as pontes dentro em um intervalo de tempo. Essa métrica é incrementada quando uma nova mensagem é recebida pela pipeline.</td>
</tr>
<tr class="even">
<td align="left"><strong>Mensagens em Processamento</strong></td>
<td align="left">Exibe o número total de mensagens sendo processadas no momento pelo Serviço do BizTalk em um intervalo de tempo.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Mensagens Processadas</strong></td>
<td align="left">Exibe o número total de mensagens processadas com êxito pelo Serviço do BizTalk por todas as pontes dentro em um intervalo de tempo. Essa métrica é incrementada quando uma mensagem é recebida com êxito pela pipeline e roteada com êxito ao destino.</td>
</tr>
</tbody>
</table>

## <a name="Scale"></a>Escala

Na guia Escala, você pode adicionar ou subtrair o número de unidades usadas pelo Serviço do BizTalk. Por padrão, há uma unidade configurada. Unidades adicionais podem ser adicionadas para dimensionar seu Serviço do BizTalk. Quando você aumenta a escala, você está aumentando a produção. A quantidade de recursos também aumenta, inclusive as pontes implantadas, os acordos, as conexões de LOB e a capacidade de processamento. Por exemplo, você aumenta a escala de uma unidade para duas. Nessa situação, você pode implantar duas vezes mais pontes, contratos, conexões de LOB e a capacidade de processamento.

Algumas edições do BizTalk não oferecem uma opção de escala. Nesta situação, uma unidade é permitida. Para determinar a quantidade de unidades de dimensionamento da sua edição, consulte [Serviços do BizTalk: Tabela de edições][Serviços do BizTalk: Tabela de edições].

Aumentar o número de unidades pode afetar os preços. Se você aumentar as unidades, selecione **Salvar** para exibir uma mensagem que informa se a cobrança é afetada. Você escolhe continuar. Quando você aumenta o número de unidades, o status do Serviço do BizTalk é alterado de Ativo para Atualizando. Durante o estado Atualizando, seu Serviço do BizTalk continua funcionando.

[Serviços do BizTalk: Gráfica de Edições][Serviços do BizTalk: Tabela de edições] define uma "Unidade".

## <a name="Configure"></a>Configurar

Não é aplicado para Conexões Híbridas.

Define o Status de Backup para Nenhum ou Automático. Quando estiver definido para Nenhum, nenhum backup será criado automaticamente. Quando estiver definido para Automático, você configura o local do backup, a frequência do backup, e por quanto tempo manter os arquivos de backup.

[Serviços do BizTalk: Backup e Restauração][Serviços do BizTalk: Backup e Restauração] fornece mais detalhes.

## <a name="HybridConnections"></a>Conexões Híbridas

As Conexões Híbridas se conectam um aplicativo do Azure, como Sites ou Serviços Móveis, a um recurso local que utilize uma porta TCP estática, como SQL Server, MySQL, APIs da web HTTP, e os serviços Web mais personalizados. As Conexões Híbridas são gerenciadas nos Serviços do BizTalk no Portal de Gerenciamento do Azure.

Para criar uma Conexão Híbrida nos Sites do Azure, consulte [Conexão Híbrida: Conecte um site do Azure a um recurso local][Conexão Híbrida: Conecte um site do Azure a um recurso local].

Para usar Conexões Híbridas nos Serviços Móveis do Azure, consulte [Serviços Móveis do Azure e Conexões Híbridas][Serviços Móveis do Azure e Conexões Híbridas].

Para criar ou gerenciar as Conexões Híbridas nos Serviços do BizTalk do Azure, consulte [Conexões Híbridas][2].

## Avançar

Agora que está familiarizado com as diferentes guias, você pode obter mais informações sobre os recursos dos Serviços BizTalk do Azure:

-   [Serviços do BizTalk: limitação][Serviços do BizTalk: limitação]
-   [Serviços do BizTalk: nome e chave do emissor][Serviços do BizTalk: nome e chave do emissor]
-   [Serviços do BizTalk: Backup e restauração][Serviços do BizTalk: Backup e Restauração]

## Consulte também

-   [Conexões Híbridas][2]
-   [Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium][Serviços do BizTalk: Tabela de edições]
-   [Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure][criar o Serviço do BizTalk]
-   [Serviços do BizTalk: Gráfica de Estado do Serviço do BizTalk][Serviços do BizTalk: Gráfico de estado de serviço]
-   [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)][Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)]

  [Início Rápido]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
  [1]: #QuickStart
  [Painel]: #Dashboard
  [Monitoramento]: #Monitor
  [Escala]: #Scale
  [Configurar]: #Configure
  [Conexões Híbridas]: #HybridConnections
  [centro de aprendizado]: http://azure.microsoft.com/pt-BR/documentation/services/biztalk-services/
  [Configurando componentes para mensagens de EDI no Portal de Serviços do BizTalk (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/p/?LinkID=303653
  [Gerenciando o namespace do ACS (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/p/?LinkID=285670
  [Métricas disponíveis]: #Metrics
  [criar o Serviço do BizTalk]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Serviços do BizTalk: Gráfico de estado de serviço]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Selecionar Adicionar Métricas]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
  [A métrica de Uso do CPU fica esmaecida]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
  [A métrica de Uso do CPU é habilitada]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
  [Serviços do BizTalk: Tabela de edições]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Serviços do BizTalk: Backup e Restauração]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [Conexão Híbrida: Conecte um site do Azure a um recurso local]: http://go.microsoft.com/fwlink/p/?LinkId=397538
  [Serviços Móveis do Azure e Conexões Híbridas]: http://azure.microsoft.com/pt-BR/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started
  [2]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Serviços do BizTalk: limitação]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [Serviços do BizTalk: nome e chave do emissor]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/p/?LinkID=302335
