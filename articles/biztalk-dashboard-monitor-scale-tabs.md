<properties linkid="manage-services-biztalk-services-dashboard-monitor-scale-tabs" urlDisplayName="Guias Painel, Monitor e Escala" pageTitle="Painel, Monitor e Escala nos Serviços BizTalk | Azure" metaKeywords="Serviços BizTalk, Azure, painel, monitor, escala" description="Saiba mais sobre os controles das guias do Portal de Gerenciamento para os Serviços BizTalk: Painel, Monitor e Escala." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Guiar Monitorar e Escala" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />




# Serviços BizTalk: guias Painel, Monitor e Escala

Na primeira vez que você abre o Portal de Gerenciamento do Azure, você é levado automaticamente para a guia **TODOS OS ITENS**. As colunas da guia **TODOS OS ITENS** podem ser classificadas. Para exibir seu Serviço do BizTalk, selecione o Serviço do BizTalk na guia **TODOS OS ITENS** ou clique na guia **SERVIÇOS BIZTALK** e, em seguida, clique no nome do seu Serviço do BizTalk.

Isso abre uma nova janela com as seguintes opções:

- [Início rápido](#QuickStart)

- [Painel](#Dashboard)

- [Monitoramento](#Monitor)

- [Escala](#Scale)

Este tópico descreve essas guias.


##<a name="QuickStart"></a>Início rápido
Na guia Início Rápido, você pode fazer o seguinte:

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Opção</strong></td>
        <td><strong>Descrição</strong></td>
</tr>
    <tr>
        <td>Obter as ferramentas</td>

        <td>Baixar o SDK dos Serviços do BizTalk para instalar os modelos do projeto do Visual Studio em seu computador de desenvolvimento local. Esses modelos criam os projetos do Visual Studio <strong>Serviços BizTalk</strong> (ponte) e <strong>Artefatos do Serviço do BizTalk</strong> (Transformação) que estão implantados em seu Serviço do BizTalk.

        <br/><br/>
		<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335"> Como começo a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)</a> e <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">Instalando o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)</a> listam as etapas para começar.
        </td>
    </tr>

    <tr>
        <td>Criar contratos de parceiro</td>

        <td>Abre o Portal dos Serviços BizTalk hospedados no Azure onde você adiciona parceiros e cria contratos EDI X12 e AS2.

        <br/><br/>

        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurando componentes para mensagens EDI no Portal dos Serviços BizTalk (a página pode estar em inglês)</a> lista as etapas para começar.
        </td>
    </tr>

<tr>
        <td>Saiba mais sobre os Serviços do BizTalk</td>

        <td>Acesse o centro de aprendizado saber mais sobre os Serviços do BizTalk do Azure.</td>
</tr>

</table>


Na barra de tarefas, na parte inferior, você pode **Gerenciar** o Serviço do BizTalk, exibir as **Informações de Conexão** do Namespace do Controle de Acesso, **Sincronizar Chaves** da Conta de Armazenamento ou **Excluir** o Serviço do BizTalk:


<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Opção</strong></td>
        <td><strong>Descrição</strong></td>
</tr>
<tr>
<td>Gerenciar</td>
<td>Quando você clica em Gerenciar, o Portal dos Serviços BizTalk é aberto. O Portal dos Serviços do BizTalk é a entrada para a configuração de EDI, inclusive a adição de parceiros e criação de acordos AS2 e X12.
<br/><br/>
É igual à opção <strong>Criar contratos de parceiros</strong> na guia <strong>Início Rápido</strong>.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurando componentes para mensagens de EDI no Portal de Serviços BizTalk (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Serviços BizTalk.</td>
</tr>

<tr>
<td>Informações de Conexão</td>
<td>Quando você clica em Informações de Conexão, o Namespace do Controle de Acesso, o Emissor Padrão e a Chave Padrão são exibidos. Você pode copiar esses valores.
<br/><br/>
Você também pode abrir o Portal de Gerenciamento de Controle de Acesso. Esse Portal de Gerenciamento de Controle de Acesso é igual ao uso da opção Active Directory no painel de navegação à esquerda.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gerenciando o namespace do ACS (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Gerenciamento de Controle de Acesso.</td>
</tr>

<tr>
<td>Sincronizar Chaves</td>
<td>Quando você cria uma conta de armazenamento, uma chave primária e uma secundária são criadas automaticamente. Essas chaves controlam o acesso à sua conta de armazenamento. Seu Serviço do BizTalk usa automaticamente a chave primária. A opção<strong>Sincronizar Chaves</strong> permite que os usuários alternem entre as chaves primária e secundária sem interromper o Serviço do BizTalk.
<br/><br/>
Por exemplo, você deseja que o Serviço do BizTalk use uma nova Chave Primária para a Conta de Armazenamento. Para fazer isso:
<br/><br/>
<ol>
<li>Clique no Serviço do BizTalk e clique em <strong>Sincronizar Chaves</strong>. Selecione a chave secundária. Quando você faz isso, o Serviço do BizTalk começa a usar a chave secundária.</li>
<li>No Portal de Gerenciamento do Azure, clique em sua Conta de Armazenamento e regenere a Chave Primária. Lembre-se que seu Serviço do BizTalk está usando a chave secundária.</li>
<li>Clique no Serviço do BizTalk e clique em <strong>Sincronizar Chaves</strong>. Agora, selecione a chave primário. Esta é a nova chave primária que você regenerou.</li>
<li>No Portal de Gerenciamento do Azure, clique em sua Conta de Armazenamento e regenere a Chave Secundária.</li>
</ol>
<br/>
Esse processo é chamado de "chaves de substituição". O propósito é permitir que os usuários alternem entre as chaves primária e secundária sem interromper o Serviço do BizTalk.</td>
</tr>

<tr>
<td>Excluir</td>
<td>Quando você clica em Excluir, seu Serviço do BizTalk e todos os itens implantados nele são removidos.</td>
</tr>
</table>


##<a name="Dashboard"></a>Painel
Quando você clica no nome do Serviço do BizTalk, a guia de Painel é exibida. O Painel exibe as seguintes informações:

#### Gráfico de métricas
Um gráfico que mostra uma lista fixa de métricas de desempenho. Essas métricas fornecem valores em tempo real relacionados à integridade do seu Serviço do BizTalk. As métricas incluem:

- Uso da CPU
- Falhas na origem
- Falhas no processo
- Mensagens processadas
- Mensagens recebidas
- Latência de processamento

Para obter uma descrição dessas métricas de desempenho, acesse [Métricas disponíveis](#Metrics) neste tópico.

##### Relativo ou absoluto
O gráfico mostra as tendências, exibindo somente o valor atual de cada métrica. Essa é a opção **Relativo**. Para exibir o eixo Y para ver os valores absolutos, selecione **Absoluto**.

##### Intervalo
Modifica o intervalo de tempo das métricas exibidas no gráfico. As opções incluem:

- 1 hora
- 1 dia
- 7 dias


####Visão rápida
Lista as propriedades do Serviço do BizTalk, incluindo:

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Opção</strong></td>
        <td><strong>Descrição</strong></td>
</tr>
<tr>
<td>Atualização das credenciais do banco de dados de acompanhamento</td>
<td>Altera o nome de usuário e senha usado para fazer logon no banco de dados de acompanhamento.<br/><br/>
Quando você provisiona o Serviço do BizTalk, você insere um nome de usuário e uma senha para fazer logon no Banco de Dados de Acompanhamento. Usando essa opção, você pode modificar seu Serviço do BizTalk para usar um nome de usuário e senha diferentes para fazer logon no Banco de Dados de Acompanhamento.<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure (a página pode estar em inglês)</a> lista as etapas para provisionar um Serviço do BizTalk.</td>
</tr>
<tr>
<td>Atualização do certificado de SSL</td>
<td>É possível inserir um certificado de SSL diferente.<br/><br/>
Quando você provisiona o Serviço do BizTalk, um certificado SSL autoassinado é criado automaticamente. Usando essa opção, você pode modificar seu Serviço do BizTalk para usar outro certificado SSL.<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure (a página pode estar em inglês)</a> lista as etapas para provisionar um Serviço do BizTalk.</td>
</tr>
<tr>
<td>Baixar Certificado</td>
<td>Com essa opção, você pode baixar o certificado SSL usado por seu Serviço do BizTalk.<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure (a página pode estar em inglês)</a> lista as etapas para provisionar um Serviço do BizTalk.</td>
</tr>
<tr>
<td>Status</td>
<td>Mostra o status atual do Serviço do BizTalk.</td>
</tr>
<tr>
<td>URL do Serviço</td>
<td>Essa URL o encaminha para o seu Serviço do BizTalk. É igual à <strong>URL do Domínio</strong> inserida quando no provisionamento do Serviço do BizTalk. </td>
</tr>
<tr>
<td>Endereço IP virtual público (VIP)</td>
<td>Este endereço IP é atribuído ao seu Serviço do BizTalk. Ele é usado para todos os pontos de extremidade de entrada e é o endereço de origem para o tráfego de saída. Esse endereço IP pertence ao seu Serviço do BizTalk enquanto ele for provisionado. Se você excluir o Serviço do BizTalk, o endereço IP será atribuído a outra implantação de serviço.</td>
</tr>
<tr>
<td>Namespace do ACS</td>
<td>Autenticados com o Serviço do BizTalk. É igual ao <strong>Namespace do ACS</strong> inserido no provisionamento do Serviço do BizTalk.</td>
</tr>
<tr>
<td>Edição</td>
<td>Lista a edição. As opções incluem Developer, Basic, Standard e Premium. Ela é igual à edição inserida no provisionamento do Serviço do BizTalk. <br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302281">Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium (a página pode estar em inglês)</a> lista as diferenças entre as edições, inclusive os custos.</td>
</tr>
<tr>
<td>Local</td>
<td>Mostra a região geográfica que hospeda seu Serviço do BizTalk. É igual à <strong>Região</strong> inserida no provisionamento do Serviço do BizTalk.</td>
</tr>
<tr>
<td>Criação</td>
<td>Mostra a data e a hora em que o Serviço do BizTalk foi provisionado.</td>
</tr>
<tr>
<td>Banco de Dados de Acompanhamento</td>
<td>O nome do Banco de Dados SQL que armazena as tabelas de acompanhamento usadas pelo Serviço do BizTalk. É igual ao <strong>Banco de Dados de Acompanhamento</strong> inserido no provisionamento do Serviço do BizTalk.</td>
</tr>
<tr>
<td>Armazenamento de monitoramento/arquivamento</td>
<td>O nome da conta de Armazenamento do Azure que armazena a saída do monitoramento de seu Serviço do BizTalk. É igual à <strong>Conta de Armazenamento de Monitoramento/Arquivamento</strong> inserida no provisionamento do Serviço do BizTalk.</td>
</tr>
<tr>
<td>Nome da assinatura</td>
<td>A assinatura rege o acesso ao Portal de Gerenciamento do Azure. É igual ao nome da <strong>Assinatura</strong> selecionado no provisionamento do Serviço do BizTalk.</td>
</tr>
<tr>
<td>ID da assinatura</td>
<td>A assinatura rege o acesso ao Portal de Gerenciamento do Azure. Quando uma assinatura é criada, uma ID da assinatura é gerada automaticamente. Ao usar APIs REST, talvez seja necessário inserir a ID da assinatura</td>
</tr>
</table>

[Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302280) lista as etapas para provisionar um Serviço do BizTalk.


#### Gerenciar, Informações de Conexão, Sincronizar Chaves e Excluir
Na barra de tarefas, na parte inferior, você pode **Gerenciar** o Serviço do BizTalk, exibir as **Informações de Conexão** do Namespace do Controle de Acesso, **Sincronizar Chaves** da Conta de Armazenamento ou **Excluir** o Serviço do BizTalk:

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Opção</strong></td>
        <td><strong>Descrição</strong></td>
</tr>
<tr>
<td>Gerenciar</td>
<td>Quando você clica em Gerenciar, o Portal dos Serviços BizTalk é aberto. O Portal dos Serviços do BizTalk é a entrada para a configuração de EDI, inclusive a adição de parceiros e criação de acordos AS2 e X12.
<br/><br/>
É igual à opção <strong>Criar contratos de parceiros</strong> na guia <strong>Início Rápido</strong>.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurando componentes para mensagens de EDI no Portal de Serviços BizTalk (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Serviços BizTalk.</td>
</tr>
<tr>
<td>Informações de Conexão</td>
<td>Quando você clica em Informações de Conexão, o Namespace do Controle de Acesso, o Emissor Padrão e a Chave Padrão são exibidos. Você pode copiar esses valores.
<br/><br/>
Você também pode abrir o Portal de Gerenciamento de Controle de Acesso. Esse Portal de Gerenciamento de Controle de Acesso é igual ao uso da opção Active Directory no painel de navegação à esquerda.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gerenciando o namespace do ACS (a página pode estar em inglês)</a> fornece mais informações sobre o Portal de Gerenciamento de Controle de Acesso.</td>
</tr>
<tr>
<td>Sincronizar Chaves</td>
<td>Quando você cria uma conta de armazenamento, uma chave primária e uma secundária são criadas automaticamente. Essas chaves controlam o acesso à sua conta de armazenamento. Seu Serviço do BizTalk usa automaticamente a chave primária. A opção<strong>Sincronizar Chaves</strong> permite que os usuários alternem entre as chaves primária e secundária sem interromper o Serviço do BizTalk.
<br/><br/>
Por exemplo, você deseja que o Serviço do BizTalk use uma nova Chave Primária para a Conta de Armazenamento. Para fazer isso:
<br/><br/>
<ol>
<li>Clique no Serviço do BizTalk e clique em <strong>Sincronizar Chaves</strong>. Selecione a chave secundária. Quando você faz isso, o Serviço do BizTalk começa a usar a chave secundária.</li>
<li>No Portal de Gerenciamento do Azure, clique em sua Conta de Armazenamento e regenere a Chave Primária. Lembre-se que seu Serviço do BizTalk está usando a chave secundária.</li>
<li>Clique no Serviço do BizTalk e clique em <strong>Sincronizar Chaves</strong>. Agora, selecione a chave primário. Esta é a nova chave primária que você regenerou.</li>
<li>No Portal de Gerenciamento do Azure, clique em sua Conta de Armazenamento e regenere a Chave Secundária.</li>
</ol>
<br/>
Esse processo é chamado de "chaves de substituição". O propósito é permitir que os usuários alternem entre as chaves primária e secundária sem interromper o Serviço do BizTalk.</td>
</tr>

<tr>
<td>Excluir</td>
<td>Quando você clica em Excluir, seu Serviço do BizTalk e todos os itens implantados nele são removidos.</td>
</tr>
</table>


##<a name="Monitor"></a>Monitorar
A guia Monitoramento exibe as seguintes informações: 

#### Gráfico de métricas
Um gráfico que exibe as métricas de desempenho selecionadas. Essas métricas fornecem valores em tempo real relacionados à integridade do seu Serviço do BizTalk. Você escolhe quais métricas de desempenho são exibidas. No máximo seis métricas de desempenho podem ser exibidas simultaneamente.

##### Relativo ou absoluto
O gráfico mostra as tendências, exibindo somente o valor atual de cada métrica. Essa é a opção **Relativo**. Para exibir o eixo Y para ver os valores absolutos, selecione **Absoluto**.

##### Intervalo
Modifica o intervalo de tempo das métricas exibidas no gráfico. As opções incluem:

- 1 hora
- 1 dia
- 7 dias

#### Para remover ou exibir métricas no gráfico
>1. Clique na guia **Monitorar**.
>2. Clique em **Adicionar Métricas** na barra de tarefas.<br/>
![Clique em Adicionar Métricas.][AddMetrics]
>3. Verifique as métricas de desempenho que você deseja exibir na guia **Monitorar**.
>4. Clique na marca de seleção para retornar para a guia **Monitorar**.
>5. Clique no círculo ao lado da métrica para exibir o valor da métrica no gráfico.<br/>
Por exemplo, a métrica **Uso de CPU** fica esmaecida. A saída não é exibida no gráfico:<br/>
![A métrica Uso de CPU fica esmaecida][GrayedMetric]
<br/>
Clique no círculo esmaecido para habilitar a métrica **Uso de CPU** para exibir sua saída no gráfico:<br/>
![A métrica Uso de CPU está habilitada][EnabledMetric]

>6. Para remover uma métrica do gráfico de exibição e da lista, clique em **Excluir Métricas** na barra de tarefas. Clicar em **Excluir Métricas** remove a métrica da guia Monitorar. Para adicionar a métrica à lista novamente, clique em **Adicionar Métricas** na barra de tarefas, selecione a métrica e clique na marca de seleção para retornar para a guia **Monitorar**. Clique no círculo esmaecido para habilitar a métrica no gráfico.

##<a name="Metrics"></a>Métricas disponíveis
As contagens/métricas de desempenho a seguir estão disponíveis:

<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>Métrica</strong></td>
<td><strong>Descrição</strong></td>
</tr>
<tr>
<td>Latência de viagem de ida e volta</td>
<td>Em milissegundos (ms), essa métrica de desempenho exibe o tempo médio para processar uma mensagem desde o momento do recebimento até o processamento completo pelo Serviço do BizTalk por todas as pontes. São contadas apenas mensagens processadas com êxito.<br/><br/>
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
[Resposta de confirmação do destino enviada ao gateway] – - [A mensagem entra no gateway]</td>
</tr>
<tr>
<td>Falhas na origem</td>
<td>Uma métrica de desempenho que exibe o número total de mensagens que falharam quando o Serviço do BizTalk efetua pull de mensagens nos pontos de extremidade de origem.</td>
</tr>
<tr>
<td>Uso da CPU</td>
<td>Lista a média da % de Tempo do Processador para todas as instâncias de função.</td>
</tr>
<tr>
<td>Latência de processamento</td>
<td>Em milissegundos (ms), essa métrica de desempenho exibe o tempo médio para processar uma mensagem pelo Serviço do BizTalk em todas as pontes, exceto o tempo gasto nos destinos. São contadas apenas mensagens processadas com êxito.<br/><br/>
Quando cada um dos eventos a seguir ocorre, é criado um carimbo de data/hora:

<ul>
<li>A mensagem entra no gateway</li>
<li>A mensagem é roteada ao destino</li>
<li>A resposta do destino é recebida</li>
<li>A resposta da confirmação do destino é enviada ao gateway</li>
</ul>
<br/>Esta métrica mostra o resultado do seguinte cálculo:<br/><br/>
[Resposta da confirmação do destino enviada ao gateway] – - [A mensagem entra no gateway] – - [A resposta do destino é recebida] + [A mensagem é roteada ao destino]</td>
</tr>
<tr>
<td>Falhas no processo</td>
<td>Uma métrica de desempenho que exibe o número total de mensagens que falharam durante o processamento pelo Serviço do BizTalk em todas as pontes em um intervalo de tempo.</td>
</tr>
<tr>
<td>Mensagens Enviadas</td>
<td>Uma métrica de desempenho que exibe o número total de mensagens enviadas pelo Serviço do BizTalk por todas as pontes dentro em um intervalo de tempo. Essa métrica é incrementada quando uma mensagem enviada de uma pipeline alcança o destino da rota. Essa métrica não indica se uma mensagem foi processada com êxito.<br/><br/>
Em um cenário de solicitação-resposta, a métrica é incrementada quando o destino de rota envia uma confirmação de recebimento de volta à pipeline.</td>
</tr>
<tr>
<td>Mensagens Recebidas</td>
<td>Uma métrica de desempenho que exibe o número total de mensagens recebidas pelo Serviço do BizTalk em todas as pontes em um intervalo de tempo. Essa métrica é incrementada quando uma nova mensagem é recebida pela pipeline.</td>
</tr>
<tr>
<td>Mensagens em Processamento</td>
<td>Uma métrica de desempenho que exibe o número total de mensagens sendo processadas no momento pelo Serviço do BizTalk em um intervalo de tempo.</td>
</tr>
<tr>
<td>Mensagens Processadas</td>
<td>Uma métrica de desempenho que exibe o número total de mensagens processadas com êxito pelo Serviço do BizTalk em todas as pontes em um intervalo de tempo. Essa métrica é incrementada quando uma mensagem é recebida com êxito pela pipeline e roteada com êxito ao destino.</td>
</tr>
</table>


##<a name="Scale"></a>Escala
Na guia Escala, você pode adicionar ou subtrair o número de unidades usadas pelo Serviço do BizTalk. Por padrão, há uma unidade configurada. Unidades adicionais podem ser adicionadas para dimensionar seu Serviço do BizTalk. Quando você aumenta a escala, você está aumentando a produção. A quantidade de recursos também aumenta, inclusive as pontes implantadas, os acordos, as conexões de LOB e a capacidade de processamento. Por exemplo, você aumenta a escala de uma unidade para duas. Nessa situação, você pode implantar duas vezes mais pontes, contratos, conexões de LOB e a capacidade de processamento.

Algumas edições do BizTalk não oferecem uma opção de escala. Nesta situação, uma unidade é permitida. Para determinar a quantidade de unidades de dimensionamento de sua edição, consulte [Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302279).

Aumentar o número de unidades pode afetar os preços. Se você aumentar as unidades, clicar em **Salvar** exibirá uma mensagem que informa se a cobrança é afetada. Você escolhe continuar. Quando você aumenta o número de unidades, o status do Serviço do BizTalk é alterado de Ativo para Atualizando. Durante o estado Atualizando, seu Serviço do BizTalk continua funcionando.



## Avançar
Agora que está familiarizado com as diferentes guias, você pode obter mais informações sobre os recursos dos Serviços BizTalk do Azure:

- [Serviços BizTalk: limitação (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Serviços BizTalk: nome e chave do emissor (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Serviços BizTalk: backup e restauração (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>

## Consulte também
- [Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços BizTalk: gráfico de estado do Serviço do BizTalk (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png

