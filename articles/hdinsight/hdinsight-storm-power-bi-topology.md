<properties
 pageTitle="Gravar dados no Power BI do Apache Storm | Microsoft Azure"
 description="Grave dados no Power BI de uma topologia C# em execução em um cluster do Apache Storm no HDInsight. Além disso, crie um relatório e um painel em tempo real usando o Power BI."
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
 ms.date="09/23/2015"
 ms.author="larryfr"/>

# Usar o Power BI (visualização) para visualizar dados de uma topologia do Apache Storm

A visualização do Power BI permite exibir dados visualmente como relatórios ou painéis. Usando a API REST do Power BI, você pode facilmente usar dados de uma topologia em execução em um Apache Storm no cluster HDInsight para Power BI.

Neste documento, você aprenderá como usar o Power BI para criar um relatório e um painel por meio dos dados criados por uma topologia Storm.

## Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um usuário do Active Directory do Azure com acesso ao [Power BI](https://powerbi.com)

* Visual Studio (uma das seguintes versões a seguir)

    * Visual Studio 2012 com [atualização 4](http://www.microsoft.com/download/details.aspx?id=39305)

    * Visual Studio 2013 com [atualização 4](http://www.microsoft.com/download/details.aspx?id=44921) ou [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)

    * Visual Studio 2015 [CTP6](http://visualstudio.com/downloads/visual-studio-2015-ctp-vs)

* Ferramentas do HDInsight para o Visual Studio: consulte [Comece a usar as Ferramentas do HDInsight para o Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md) para obter informações sobre a instalação.

## Como ele funciona

Este exemplo contém uma topologia Storm C# que gera uma frase aleatoriamente, divide a frase em palavras, conta as palavras e envia as palavras e a contagem para a API REST do Power BI. O pacote Nuget do [PowerBi.Api.Client](https://github.com/Vtek/PowerBI.Api.Client) é usado para se comunicar com o Power BI.

Os seguintes arquivos neste projeto implementam a funcionalidade específica do Power BI:

* **PowerBiBolt.cs**: implementa o bolt do Storm, que envia dados ao Power BI

* **Data.cs**: descreve a objeto/linha de dados que será enviado para o Power BI

> [AZURE.WARNING]O Power BI parece permitir a criação de vários conjuntos de dados com o mesmo nome. Isso pode ocorrer se o conjunto de dados não existir e sua topologia criar várias instâncias de bolt do Power BI. Para evitar isso, defina a dica de paralelismo do bolt como 1 (como neste exemplo) ou crie o conjunto de dados antes de implantar a topologia.
>
> O aplicativo console **CreateDataset** incluído nesta solução é fornecido como um exemplo de como criar o conjunto de dados fora da topologia.

## Registrar um aplicativo Power BI

1. Siga as etapas no [Início rápido do Power BI](https://msdn.microsoft.com/PT-BR/library/dn931989.aspx) para inscrever-se no Power BI.

2. Siga as etapas em [Registrar um aplicativo](https://msdn.microsoft.com/PT-BR/library/dn877542.aspx) para criar um registro de aplicativo. Isso será usado ao acessar a API REST do Power BI.

    > [AZURE.IMPORTANT]Salve a **ID do cliente** para o registro do aplicativo.

## Baixar o exemplo

Baixe o [exemplo do Power BI do Storm em C# do HDInsight](https://github.com/Blackmist/hdinsight-csharp-storm-powerbi). Para baixá-lo, use uma bifurcação/clone-o usando [git](http://git-scm.com/) ou use o link de **Download** link para baixar um .zip do arquivo.

## Configurar o exemplo

1. Abra o exemplo no Visual Studio. No **Gerenciador de Soluções**, abra o arquivo **SCPHost.exe.config** e localize o elemento **<OAuth .../>**. Insira valores para as seguintes propriedades desse elemento.

    * **Cliente**: a ID do cliente para o registro do aplicativo que você criou anteriormente.

    * **Usuário**: uma conta do Active Directory do Azure que tem acesso ao Power BI.

    * **Senha**: a senha da conta do Active Directory do Azure.

2. (Opcional). O nome do conjunto de dados padrão usado por este projeto é **Palavras**. Para alterar isso, clique com botão direito no projeto **WordCount** no **Gerenciador de Soluções**, selecione **Propriedades** e selecione **Configurações**. Altere a entrada **DatasetName** para o valor desejado.

2. Salve e feche os arquivos.

## Implantar o exemplo

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **WordCount** e selecione **Enviar para o Storm no HDInsight**. Selecione o cluster HDInsight no menu suspenso **Cluster Storm**.

    > [AZURE.NOTE]Pode levar alguns segundos para a lista suspensa **Cluster Storm** ser populada com os nomes de servidor.
    >
    > Se solicitado, insira as credenciais de logon para sua assinatura do Azure. Se você tiver mais de uma assinatura, faça o logon naquela que contém seu Storm no cluster HDInsight.

2. Depois que a topologia tiver sido enviada com êxito, as Topologias Storm para o cluster deverão ser exibidas. Selecione a topologia WordCount na lista para exibir informações sobre a topologia em execução.

    ![As topologias, com a topologia WordCount selecionada](./media/hdinsight-storm-power-bi-topology/topologysummary.png)

    > [AZURE.NOTE]Você também pode exibir as Topologias Storm no Gerenciador de Servidores: expanda o Azure, HDInsight e clique com o botão direito em um Storm no cluster HDInsight e selecione Exibir Topologias Storm.

3. Ao exibir o **Resumo da Topologia**, role até ver a seção **Bolts**. Nesta seção, observe a coluna **Executado** para o bolt **PowerBI**. Use o botão Atualizar na parte superior da página para atualizar até que o valor seja alterado para algo diferente de zero. Quando esse número começa a aumentar, isso indica que os itens estão sendo gravados no Power BI.

## Criar um relatório

1. Em um navegador, visite [https://PowerBI.com](https://powerbi.com). Faça logon usando sua conta.

2. No lado esquerdo da página, expanda **Conjuntos de Dados**. Selecione a entrada **Palavras**. Esse é o conjunto de dados criado pelo exemplo de topologia.

    ![Entrada de conjunto de dados Palavras](./media/hdinsight-storm-power-bi-topology/words.png)

3. Na área **Campos**, expanda **WordCount**. Arraste as entradas **Contagem** e **Palavra** para a parte central da página. Isso criará um novo gráfico que exibe uma barra para cada palavra indicando quantas vezes a palavra ocorreu.

    ![Gráfico WordCount](./media/hdinsight-storm-power-bi-topology/wordcountchart.png)

4. No canto superior esquerdo da página, selecione **Salvar** para criar um novo relatório. Digite **Contagem de Palavras** como o nome do relatório.

5. Selecione o logotipo do Power BI para retornar ao painel. O relatório **Contagem de Palavras** agora será exibido em **Relatórios**.

## Criar um painel dinâmico

1. Ao lado de **Painel**, selecione o ícone **+** para criar um novo painel. Nomeie o novo painel como **Contagem de Palavras Dinâmica**.

2. Selecione o relatório **Contagem de Palavras** criado anteriormente. Quando for exibido, selecione o gráfico e depois o ícone de pino na parte superior direita do gráfico. Você deve receber uma notificação de que ele foi fixado no painel.

    ![Gráfico com pino exibido](./media/hdinsight-storm-power-bi-topology/pushpin.png)

2. Selecione o logotipo do Power BI para retornar ao painel. Selecione o painel **Contagem de Palavras Dinâmica**. Agora, ele contém o gráfico de Contagem de Palavras e as atualizações de gráfico como novas entradas enviadas para o Power BI da topologia WordCount em execução no HDInsight.

    ![O painel dinâmico](./media/hdinsight-storm-power-bi-topology/dashboard.png)

## Parar a topologia WordCount

A topologia continuará sendo executado até você pará-la ou excluir o Storm no cluster HDInsight. Execute as seguintes etapas para interromper a topologia.

1. No Visual Studio, abra a janela **Resumo da Topologia** para a topologia WordCount. Se o Resumo da Topologia ainda não estiver aberto, vá para **Gerenciador de Servidores**, expanda as entradas **Azure** e **HDInsight**, clique com o botão direito no Storm no cluster HDInsight e selecione **Exibir Topologias Storm**. Por fim, selecione a topologia **WordCount**.

2. Selecione o botão **Eliminar** para interromper a topologia **WordCount**.

    ![Botão Eliminar no resumo de topologia](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## Próximas etapas

Neste documento, você aprendeu como enviar dados de uma topologia Storm no Power BI usando REST. Para obter informações sobre como trabalhar com outras tecnologias do Azure, consulte o seguinte:

* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)

<!---HONumber=Sept15_HO4-->