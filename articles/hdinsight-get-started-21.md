<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Introdução ao uso do Hadoop 1.2 no HDInsight

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Introdu&ccedil;&atilde;o ao uso do Hadoop 2.2 no HDInsight">Hadoop 2.2</a>
<a href="../hdinsight-get-started-31" title="Introdu&ccedil;&atilde;o ao uso do Hadoop 2.4 no HDInsight">Hadoop 2.4</a>
<a href="../hdinsight-get-started-21" title="Introdu&ccedil;&atilde;o ao uso do Hadoop 1.2 no HDInsight" class="current">Hadoop 1.2</a>
</div>

O HDInsight torna o [Apache Hadoop][Apache Hadoop] disponível como um serviço na nuvem. Disponibiliza a estrutura do software MapReduce em um ambiente mais simples, mais escalonável e econômico do Azure. O HDInsight também fornece uma abordagem econômica para o gerenciamento e o armazenamento de dados usando o armazenamento de Blob do Azure.

Neste tutorial, você provisionará um cluster do Hadoop HDInsight usando o Portal de Gerenciamento do Azure, enviará um trabalho Hive para consulta comparando-o a uma tabela Hive de amostra usando o painel do cluster e, em seguida, importará os dados de saída do trabalho Hive no Excel para análise.

> [WACOM.NOTE] Este tutorial aborda o uso de clusters Hadoop 1.2 no HDInsight. Para obter outra versão suportada, clique no seletor na parte superior da página. Para obter informações sobre a versão, consulte [O que há de novo nas versões de cluster fornecidas pelo HDInsight?][O que há de novo nas versões de cluster fornecidas pelo HDInsight?]

A demonstração ao vivo deste artigo:

<center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target = "_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center>

Em conjunto com a disponibilidade geral do Azure HDInsight, a Microsoft também lançou o Emulador do HDInsight para Azure, anteriormente conhecido como *Microsoft HDInsight Developer Preview*. Esse produto tem como alvo os cenários de desenvolvedor e, como tal, só oferece suporte a implantações de nó único. Para usar o emulador de HDInsight, consulte [Introdução ao emulador de HDInsight][Introdução ao emulador de HDInsight].

**Pré-requisitos:**

Antes de começar este tutorial, você deve ter o seguinte:

-   Uma assinatura do Azure. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][Opções de compra], [Ofertas para membros][Ofertas para membros] ou [Avaliação gratuita][Avaliação gratuita].
-   Um computador com Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone ou Office 2010 Professional Plus.

**Tempo estimado para conclusão:** 30 minutos

## Neste tutorial

-   [Provisione um cluster HDInsight][Provisione um cluster HDInsight]
-   [Executar um trabalho em Hive][Executar um trabalho em Hive]
-   [Conectar-se as ferramentas do Microsoft Business Intelligence][Conectar-se as ferramentas do Microsoft Business Intelligence]
-   [Próximas etapas][Próximas etapas]

## <a name="provision"></a>Provisione um cluster HDInsight

O HDInsight usa o Armazenamento de Blob do Azure para armazenar dados. Ele é chamado *WASB* ou *Armazenamento do Azure - Blob*. O WASB é a implementação do HDFS da Microsoft no Armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][Usar o Armazenamento de Blob do Azure com o HDInsight].

Durante o processo de provisionamento do HDInsight, uma conta de Armazenamento do Azure e um contêiner de armazenamento de Blob específico dessa conta são designados como o sistema de arquivos padrão, como no HDFS. A conta de armazenamento deve estar localizada no mesmo datacenter que os recursos de computação do HDInsight. No momento, você só poderá provisionar clusters do HDInsight nos seguintes datacenters:

-   Sudeste Asiático
-   Norte da Europa
-   Europa Ocidental
-   Leste dos EUA
-   Oeste dos EUA

Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento da mesma assinatura do Azure ou de diferentes assinaturas do Azure. Para obter instruções sobre como adicionar mais contas de armazenamento, consulte [Provisionar clusters HDInsight][Provisionar clusters HDInsight].

Para simplificar este tutorial, somente o contêiner blob padrão e a conta de armazenamento padrão são usados, e todos os arquivos são armazenados no contêiner padrão do sistema de arquivos, localizado em */tutorials/getstarted/*. Na prática, os arquivos de dados geralmente são armazenados em uma conta de armazenamento designada.

**Para criar uma conta de Armazenamento do Azure**

1.  Entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique em **NOVO** no canto inferior esquerdo, aponte para **SERVIÇOS DE DADOS**, aponte para **ARMAZENAMENTO** e, em seguida, clique em **CRIAÇÃO RÁPIDA**.

    ![HDI.StorageAccount.QuickCreate][HDI.StorageAccount.QuickCreate]

3.  Digite **URL**, **LOCAL** e **REPLICAÇÃO** e, em seguida, clique em **CRIAR CONTA DE ARMAZENAMENTO**. Não há suporte para grupos de afinidade. Você verá a nova conta de armazenamento na lista de armazenamento.
4.  Aguarde até que o **STATUS** da nova conta de armazenamento seja alterado para **Online**.
5.  Clique na nova conta de armazenamento da lista para selecioná-la.
6.  Clique em **GERENCIAR CHAVES DE ACESSO** na parte inferior da página.
7.  Tome nota do **NOME DA CONTA DE ARMAZENAMENTO** e da **CHAVE DE ACESSO PRIMÁRIA** (ou a **CHAVE DE ACESSO SECUNDÁRIA**. As duas chaves funcionam). Você precisará deles mais tarde no tutorial.

Para obter mais informações, consulte
[Como criar uma conta de armazenamento][Como criar uma conta de armazenamento] e [Usar o Armazenamento de Blob do Azure com o HDInsight][Usar o Armazenamento de Blob do Azure com o HDInsight].

**Para provisionar o cluster HDInsight**

1.  Entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  Clique em **HDINSIGHT** à esquerda para listar os clusters HDInsight da sua conta. Na seguinte captura de tela, não há nenhum cluster HDInsight existente.

    ![HDI.ClusterStatus][HDI.ClusterStatus]

3.  Clique em **NOVO** no lado inferior esquerdo, clique em **SERVIÇOS DE DADOS**, clique em **HDINSIGHT** e, por fim, clique em **CRIAÇÃO PERSONALIZADA**.

    ![HDI.CustomCreateCluster][HDI.CustomCreateCluster]

4.  Na guia Detalhes do Cluster, digite ou selecione os valores a seguir:

	<table>
    <tr><td> <strong>Nome</strong>                    </td><td> <strong>Valor </td></tr>
    <tr><td> <strong>Nome do cluster</strong>     </td><td> Nome do cluster.  </td></tr>
    <tr><td> <strong>Nós de dados</strong>        </td><td> Número de nós de dados que você deseja implantar. Para fins de teste, crie um cluster de nó único.<br />O limite de tamanho do cluster varia para as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite. </td></tr>
    <tr><td> <strong>Versão do HDInsight</strong> </td><td> Selecione <strong>2.1</strong> para criar um cluster do Hadoop 1.2 no HDInsight. </td></tr>
    <tr><td> <strong>Região</strong>             </td><td> Escolha a mesma região da conta de armazenamento que você criou no último procedimento. O HDInsight requer que a conta de armazenamento esteja localizada na mesma região. Mais adiante na configuração, você poderá escolher somente uma conta de armazenamento que esteja na mesma região especificada aqui. </td></tr>
	</table>

5.  Clique na seta para a direita no canto inferior direito para configurar o usuário do cluster.
6.  Na guia Configurar Usuário de Cluster, digite o **Nome de Usuário** e a **Senha** para a conta de usuário do cluster do HDInsight. Além dessa conta, você pode criar uma conta de usuário RDP depois que o cluster for configurado, para que você possa usar a área de trabalho remota no cluster. Para obter instruções, consulte [Administrar o HDInsight usando o Portal de Gerenciamento][hdinsight-admin-portal]
7.  Clique na seta para a direita no canto inferior direito para configurar a conta de armazenamento.
8.  Na guia Conta de Armazenamento, digite ou selecione os valores a seguir:

	<table>
    <tr><td> <strong>Nome</strong>                               </td><td> <strong>Valor</strong>                                                                                                                                                                                </td></tr>
    <tr><td> CONTA DE ARMAZENAMENTO             </td><td> Selecione <strong>Usar armazenamento existente</strong>. Você também tem a opção de fazer com que o Portal de Gerenciamento crie uma nova conta de armazenamento se ainda não tiver criado uma.   </td></tr>
    <tr><td> NOME DA CONTA                      </td><td> Especifique a conta de armazenamento criada no último procedimento deste tutorial. Observe que somente as contas de armazenamento na mesma região são exibidas na caixa de listagem. </td></tr>
    <tr><td> CONTÊINER PADRÃO                   </td><td> Selecione <strong>Criar contêiner padrão</strong>. Quando essa opção for escolhida, o nome do contêiner padrão será igual ao nome do cluster.                                                     </td></tr>
    <tr><td> CONTAS DE ARMAZENAMENTO ADICIONAIS </td><td> Selecione <strong>0</strong>. Você tem a opção de conectar o cluster a até sete contas de armazenamento adicionais.                                                                               </td></tr>
	</table>

9.  Clique no ícone de seleção no canto inferior direito para criar o cluster. Quando o processo de provisionamento for concluído, a coluna de status mostrará **Em Execução**.

## <a name="sample"></a>Executar um trabalho em Hive

Agora você tem um cluster de HDInsight provisionado. A próxima etapa é executar um trabalho em Hive para consultar uma amostra da tabela Hive, que é fornecida com clusters HDInsight. O nome da tabela é *hivesampletable*.

**Para abrir o painel do cluster**

1.  Entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique em **HDINSIGHT** a partir do painel esquerdo. Você verá uma lista dos clusters criados, incluindo aquele que criou na última seção.
3.  Clique no nome do cluster em que deseja executar a tarefa do Hive.
4.  Clique em **GERENCIAR CLUSTER** na parte inferior da página para abrir o painel do cluster. Ele abre uma página da Web em uma guia diferente do navegador.
5.  Insira o nome de usuário e senha da conta do Usuário Hadoop. O nome de usuário padrão é **admin**, a senha é o que você digitou durante o processo de provisionamento. O painel fica assim:

    ![hdi.dashboard][hdi.dashboard]

    Há várias guias na parte superior. A guia padrão é *Editor do Hive*, as outras guias incluem Trabalhos e Arquivos. Usando o painel, você pode enviar consultas do Hive, verificar logs de trabalho do Hadoop e procurar arquivos do WASB.

> [wacom.note] Observe se a URL é *\<NomeDoCluster\>.azurehdinsight.net*. Em vez de abrir o painel por meio do Portal de gerenciamento, você também pode abrir o painel de um navegador da Web usando a URL.

**Para executar uma consulta do Hive**

1.  No painel do cluster, clique em **Editor do Hive** na parte superior.
2.  Em **Nome da Consulta**, insira **HTC20**. O nome da consulta é o cargo.
3.  No painel de consulta, insira a seguinte consulta:

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][hdi.dashboard.query.select]

4.  Clique em **Enviar**. Demora alguns minutos para obter os resultados. A tela é atualizada a cada 30 segundos. Você também pode clicar em **Atualizar** para atualizar a tela.

    Depois de concluído, a tela fica assim:

    ![hdi.dashboard.query.select.result][hdi.dashboard.query.select.result]

    Anote a **Hora de Início do Trabalho (UTC)**. Você precisará dela mais tarde.

    Faça a rolagem um pouco mais para baixo, você verá o **Log do Trabalho**. A Saída do Trabalho é stdout, o Log do Trabalho é stderr.

5.  Se você quiser reabrir o arquivo de log novamente no futuro, poderá clicar em **Trabalhos** na parte superior da tela, depois clicar no título do trabalho (nome da consulta). Por exemplo, **HTC20** neste caso.

**Para pesquisar o arquivo de saída**

1.  No painel do cluster, clique em **Arquivos** na parte superior.
2.  Clique em **Templeton-Job-Status**.
3.  Clique no número da GUID que possui o último Horário modificado um pouco após o Hora de início da tarefa que você anotou anteriormente. Anote este GUID. Você precisará dele na próxima seção.
4.  O arquivo **stdout** possui os dados necessários para a próxima seção. É possível clicar em **stdout** para baixar uma cópia do arquivo de dados se desejar.

## <a name="powerquery"></a>Conectar-se às ferramentas do Microsoft Business Intelligence

O suplemento Power Query para Excel pode ser usado para exportar a saída do HDInsight no Excel onde as ferramentas do Microsoft Business Intelligence (BI) podem ser usadas para processar ainda mais ou exibir os resultados.

Você deve ter o Excel 2010 ou 2013 instalado para concluir essa parte do tutorial.

**Para baixar o Microsoft Power Query para Excel**

-   Baixe o Microsoft Power Query para Excel no [Centro de Download da Microsoft][Centro de Download da Microsoft] e instale-o.

**Para importar dados no HDInsight**

1.  Abra o Excel e crie uma nova planilha em branco.
2.  Clique no menu **Power Query**, clique em **De Outras Fontes** e, em seguida, clique em **Do Azure HDInsight**.

    ![HDI.GettingStarted.PowerQuery.ImportData][HDI.GettingStarted.PowerQuery.ImportData]

3.  Digite o **Nome da Conta** da conta de armazenamento do Blob do Azure associada ao cluster e, em seguida, clique em **OK**. Esta é a conta de armazenamento criada anteriormente no tutorial.
4.  Digite a **Chave de Conta** para a conta de armazenamento do Blob do Azure e clique em **Salvar**.
5.  No painel de navegação à direita, clique duas vezes no nome do contêiner de armazenamento de Blob. Por padrão, o nome do contêiner é igual ao nome do cluster.

6.  Localize **stdout** na coluna **Nome** (o caminho da pasta é *.../Templeton-Job-Status/<guid>*) e clique em **Binário** à esquerda de **stdout**. O <guid> precisa corresponder a o que você anotou na última seção.

    ![HDI.GettingStarted.PowerQuery.ImportData2][HDI.GettingStarted.PowerQuery.ImportData2]

7.  Clique em **Aplicar e Fechar** no canto superior esquerdo. A consulta importa a saída do trabalho de Hive no Excel.

## <a name="nextsteps"></a>Próximas etapas

Neste tutorial, você aprendeu a provisionar um cluster com o HDInsight, executar um trabalho de MapReduce e importar os resultados para o Excel, onde eles podem ser processados ainda mais e exibidos graficamente usando ferramentas de BI. Para saber mais, consulte os seguintes artigos:

-   [Introdução ao emulador do HDInsight][Introdução ao emulador de HDInsight]
-   [Usar o armazenamento de blobs do Azure com o HDInsight][Usar o Armazenamento de Blob do Azure com o HDInsight]
-   [Administrar o HDInsight usando o PowerShell][Administrar o HDInsight usando o PowerShell]
-   [Carregar dados no HDInsight][Carregar dados no HDInsight]
-   [Usar o MapReduce com HDInsight][Usar o MapReduce com HDInsight]
-   [Use o hive com o HDInsight][Use o hive com o HDInsight]
-   [Use o Pig com o HDInsight][Use o Pig com o HDInsight]
-   [Usar o Oozie com o HDInsight][Usar o Oozie com o HDInsight]
-   [Desenvolver programas de streaming do Hadoop em C# para o HDInsight][Desenvolver programas de streaming do Hadoop em C# para o HDInsight]
-   [Desenvolver programas Java MapReduce para HDInsight][Desenvolver programas Java MapReduce para HDInsight]

  [Apache Hadoop]: http://hadoop.apache.org/
  [img-hdi-getstarted-video]: ./media/hdinsight-get-started/HDI.GetStarted.Video.png
  [Introdução ao emulador de HDInsight]: ../hdinsight-get-started-emulator/
  [Opções de compra]: http://azure.microsoft.com/pt-br/pricing/purchase-options/
  [Ofertas para membros]: http://azure.microsoft.com/pt-br/pricing/member-offers/
  [Avaliação gratuita]: http://azure.microsoft.com/pt-br/pricing/free-trial/
  [Provisione um cluster HDInsight]: #provision
  [Executar um trabalho em Hive]: #sample
  [Conectar-se as ferramentas do Microsoft Business Intelligence]: #powerquery
  [Próximas etapas]: #nextsteps
  [Usar o Armazenamento de Blob do Azure com o HDInsight]: ../hdinsight-use-blob-storage/
  [Provisionar clusters HDInsight]: ../hdinsight-provision-clusters/
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [Como criar uma conta de armazenamento]: ../storage-create-storage-account/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [Centro de Download da Microsoft]: http://www.microsoft.com/pt-br/download/details.aspx?id=39379
  [HDI.GettingStarted.PowerQuery.ImportData]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
  [HDI.GettingStarted.PowerQuery.ImportData2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
  [Administrar o HDInsight usando o PowerShell]: ../hdinsight-administer-use-powershell/
  [Carregar dados no HDInsight]: ../hdinsight-upload-data/
  [Usar o MapReduce com HDInsight]: ../hdinsight-use-mapreduce
  [Use o hive com o HDInsight]: ../hdinsight-use-hive/
  [Use o Pig com o HDInsight]: ../hdinsight-use-pig/
  [Usar o Oozie com o HDInsight]: ../hdinsight-use-oozie/
  [Desenvolver programas de streaming do Hadoop em C# para o HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Desenvolver programas Java MapReduce para HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
