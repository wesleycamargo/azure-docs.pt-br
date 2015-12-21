<properties
 pageTitle="Correlacionar eventos ao longo do tempo com o Storm e o HBase no HDInsight"
 description="Saiba como correlacionar eventos que chegam em momentos diferentes usando o Storm e HBase no HDInsight."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="12/04/2015"
 ms.author="larryfr"/>

# Correlacionar eventos ao longo do tempo com o Storm e o HBase no HDInsight

Usando um armazenamento de dados persistentes com o Apache Storm, você pode correlacionar as entradas de dados que chegam em momentos diferentes. Por exemplo, vincular eventos de logon e logoff de uma sessão de usuário para calcular quanto tempo durou a sessão.

Neste documento, você aprenderá como criar uma topologia Storm C# básica que rastreie eventos de logon e logoff de sessões de usuário e calcule a duração da sessão. A topologia usa HBase como um repositório de dados persistentes. O HBase também permite executar consultas de lote nos dados históricos para produzir informações adicionais, como quantas sessões de usuário foram iniciadas ou encerradas durante um período específico.

[AZURE.INCLUDE [somente Windows](../../includes/hdinsight-windows-only.md)]

## Pré-requisitos

-	Ferramentas do HDInsight para o Visual Studio: consulte [Introdução ao uso das ferramentas do HDInsight para o Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md) para obter informações sobre a instalação.

-	Apache Storm no cluster HDInsight

-	Apache HBase no cluster HDInsight

## Arquitetura

![Diagrama do fluxo de dados por meio da topologia](./media/hdinsight-storm-correlation-topology/correlationtopology.png)

A correlação de eventos requer um identificador comum para a origem do evento. Por exemplo, uma ID de usuário, ID de sessão ou outra parte dos dados que seja a) exclusiva e b) incluída em todos os dados enviados ao Storm. Este exemplo usa um valor de GUID para representar uma ID de sessão.

Esse exemplo consiste em dois clusters HDInsight:

-	HBase: repositório de dados persistente dados históricos

-	Storm: usado para incluir dados de entrada

Os dados são gerados aleatoriamente, pelo Storm e contém dos seguintes itens:

-	ID da sessão: um GUID que identifica exclusivamente cada sessão

-	Evento: um evento INICIAR ou ENCERRAR. Para este exemplo, INICIAR sempre ocorre antes do ENCERRAR

-	Hora: a hora do evento.

Esses dados são processados e armazenados no HBase.

### Topologia Storm

Quando uma sessão é iniciada, um evento **INICIAR** é recebido pela topologia e registrado no HBase. Quando um evento **ENCERRAR** é recebido, a topologia recupera o EVENTO **INICIAR** e calcula o tempo entre os dois eventos. Esse valor **Duração** valor é armazenado no HBase juntamente com as informações do evento **ENCERRAR**.

> [AZURE.IMPORTANT]Enquanto essa topologia demonstra o padrão básico, uma solução de produção precisaria fazer o design para os seguintes cenários:
>
> - Eventos que chegam fora de ordem
> - Eventos duplicados
> - Eventos ignorados

A topologia de exemplo é composta dos seguintes componentes:

- 	Session.cs: simula uma sessão de usuário criando uma ID de sessão aleatória, hora de início e por quanto tempo durará a sessão

- 	Spout.cs: cria 100 sessões, emite um evento INICIAR, aguarda o tempo limite aleatório para cada sessão e emite um evento ENCERRAR. Em seguida, recicla as sessões encerradas para gerar novas.

-	HBaseLookupBolt.cs: usa a ID de sessão para pesquisar informações de sessão do HBase. Quando um evento ENCERRAR é processado, ele localiza o evento INICIAR correspondente e calcula a duração da sessão.

-	HBaseBolt.cs: Armazena informações no HBase.

-	TypeHelper.cs: ajuda na conversão de tipo ao ler/gravar no HBase.

### Esquema do HBase

No HBase, os dados são armazenados em uma tabela com os seguintes esquema/configurações:

-	Chave de linha: a ID da sessão é usada como a chave para linhas nesta tabela

-	Família de coluna: o nome da família é “cf”. As colunas armazenadas nesta família são:

	- 	evento: INICIAR ou ENCERRAR

	- 	tempo: o tempo em milissegundos que o evento ocorreu

	-	duração: o comprimento entre eventos de INICIAR e ENCERRAR

-	VERSÕES: a família “cf” é definida para reter 5 versões de cada linha

	> [AZURE.NOTE]As versões são um log dos valores anteriores armazenados para uma chave de linha específica. Por padrão, o HBase retorna apenas o valor para a versão mais recente de uma linha. Nesse caso, a mesma linha é usada para todos os eventos (INICIAR, ENCERRAR) por que cada versão de uma linha é identificada pelo valor de carimbo de data e hora. Isso fornece uma exibição do histórico de eventos registrados para uma ID específica.

## Baixe o projeto

O projeto de exemplo pode ser baixado em [hdinsight-storm-eventcorrelation](https://github.com/Blackmist/hdinsight-storm-eventcorrelation).

Este download contém os seguintes projetos C#:

-	CorrelationTopology: topologia de Storm C# que emite aleatoriamente eventos de iniciar e encerrar para sessões de usuário. Cada sessão dura entre 1 e 5 minutos.

-	SessionInfo: aplicativo do console C# que cria a tabela de HBase e fornece consultas de exemplo para retornar informações sobre dados de sessão armazenados.

## Criar a tabela.

1. Abra o projeto **SessionInfo** no Visual Studio.

2. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **SessionInfo** e selecione **Propriedades**.

	![Captura de tela do menu com as propriedades selecionadas](./media/hdinsight-storm-correlation-topology/selectproperties.png)

3. Selecione **configurações** e defina os seguintes valores:

	- 	HBaseClusterURL: a URL para o cluster HBase. Por exemplo, https://myhbasecluster.azurehdinsight.net

	- 	HBaseClusterUserName: a conta de usuário de admin/HTTP para o cluster

	- 	HBaseClusterPassword: a senha para a conta de usuário admin/HTTP

	- 	HBaseTableName: o nome da tabela a ser usada com este exemplo

	-	HBaseTableColumnFamily: o nome da família de coluna

	![Imagem da caixa de diálogo de configuração](./media/hdinsight-storm-correlation-topology/settings.png)

5. Execute a solução. Quando solicitado, selecione a chave "c" para criar a tabela no cluster HBase.

## Criar e implantar a topologia Storm

1.	Abra a solução **CorrelationTopology** no Visual Studio.

2.	No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **CorrelationTopology** e selecione Propriedades.

3.	Na janela Propriedades, selecione **Configurações** e forneça as informações a seguir. Os 5 primeiros devem ser os mesmos valores usados pelo projeto **SessionInfo**:

	- 	HBaseClusterURL: a URL para o cluster HBase. Por exemplo, https://myhbasecluster.azurehdinsight.net

	- 	HBaseClusterUserName: a conta de usuário de admin/HTTP para o cluster

	- 	HBaseClusterPassword: a senha para a conta de usuário admin/HTTP

	- 	HBaseTableName: o nome da tabela a ser usada com este exemplo. Este deve conter o mesmo nome de tabela usado no projeto SessionInfo

	- 	HBaseTableColumnFamily: o nome da família de coluna. Este deve conter o mesmo nome de família de coluna usado no projeto SessionInfo

	> [AZURE.IMPORTANT]Não altere HBaseTableColumnNames, pois os padrões são os nomes usados por **SessionInfo** para recuperar dados.

4.  Salvar as propriedades e compilar o projeto.

5.	No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Enviar para o Storm no HDInsight**. Se solicitado, insira as credenciais de logon para sua assinatura do Azure.

	![Imagem do item de menu enviar para o storm](./media/hdinsight-storm-correlation-topology/submittostorm.png)

6.	Na caixa de diálogo **Enviar topologia**, selecione o cluster Storm que executará essa topologia.

	> [AZURE.NOTE]Na primeira vez que você enviar uma topologia, pode levar alguns segundos para recuperar o nome do seus clusters HDInsight.

7.	Depois que a topologia for carregada e enviada para o cluster, a **Visualização da topologia Storm** será aberta e exibirá a topologia em execução. Selecione o **CorrelationTopology** e use o botão Atualizar na parte superior direita da página para atualizar as informações de topologia.

	![Imagem da visualização da topologia](./media/hdinsight-storm-correlation-topology/topologyview.png)

	Quando a topologia começar a gerar dados, o valor da coluna **Emitido** será incrementado.

	> [AZURE.NOTE]Se a **Visualização da topologia Storm** não abrir automaticamente, use as seguintes etapas para abri-la:
	>
	> 1. No **Gerenciador de Soluções**, expanda **Azure** e **HDInsight**.
	>
	> 2. Clique com o botão direito do mouse no cluster Storm em que a topologia está em execução e selecione **Visualizar topologias Storm**

## Consultar os dados

Depois que os dados forem emitidos, use as seguintes etapas para consultar os dados.

1. Volte para o projeto **SessionInfo**. Se não estiver executando, inicie uma nova instância dele.

2. Quando solicitado, selecione **s** para pesquisar pelo evento INICIAR. Você será solicitado a inserir uma hora de início e encerramento para definir um intervalo de tempo; apenas os eventos entre essas duas horas serão retornados.

	Use o seguinte formato ao inserir as horas de início e encerramento: HH:MM e “am” ou “pm”. Por exemplo, 11:20pm.

	Uma vez que a topologia começar, use uma hora de início de tempo de antes de ele ter sido implantado e uma hora de encerramento de agora. Isso deve capturar a maioria dos eventos INICIAR que foram gerados quando iniciado. Quando a consulta for executada, você verá uma lista de entradas semelhantes à seguinte:

		Session e6992b3e-79be-4991-afcf-5cb47dd1c81c started at 6/5/2015 6:10:15 PM. Timestamp = 1433527820737

A busca por eventos ENCERRAR funciona da mesma maneira que os eventos INICIAR. No entanto, os eventos ENCERRAR são gerados aleatoriamente entre 1 e 5 minutos após o evento INICIAR. Portanto, você terá que experimentar alguns intervalos de tempo para encontrar os eventos ENCERRAR. Os eventos ENCERRAR também conterão a duração da sessão; a diferença entre a hora do evento INICIAR e do evento ENCERRAR. Veja um exemplo de dados para eventos ENCERRAR:

	Session fc9fa8e6-6892-4073-93b3-a587040d892e lasted 2 minutes, and ended at 6/5/2015 6:12:15 PM

> [AZURE.NOTE]Embora os valores de hora que você insere estão na hora local, a hora retornada da consulta será em UTC.

##Parar a topologia

Quando você estiver pronto para parar a topologia, volte para o projeto **CorrelationTopology** no Visual Studio. Na **Visualização da topologia Storm**, selecione a topologia e use o botão **Eliminar** na parte superior da visualização de topologia.

##Próximas etapas

Para obter mais exemplos de topologias Storm, consulte [Exemplo de topologias para Storm no HDInsight](hdinsight-storm-example-topology.md).
 

<!---HONumber=AcomDC_1210_2015-->