<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop-3.0" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Introdução ao uso do Hadoop 2.2 no HDInsight

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Introdu&ccedil;&atilde;o ao uso do Hadoop 2.4 no HDInsight">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="Introdu&ccedil;&atilde;o ao uso do Hadoop 2.2 no HDInsight" class="current">Hadoop 2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a-->
</div>

O HDInsight disponibiliza o Apache Hadoop, uma estrutura do software MapReduce, em um ambiente mais simples, escalonável e econômico do Azure. O HDInsight também fornece uma abordagem econômica para o gerenciamento e o armazenamento de dados usando o armazenamento de Blob do Azure.

> [WACOM.NOTE] Se você for novo no Hadoop e em Big Data, talvez você queira ler mais sobre os termos [Apache Hadoop][], [MapReduce][], [HDFS][] e [Hive][].

Em conjunto com a disponibilidade geral do Azure HDInsight, a Microsoft também oferece o Emulador do HDInsight para o Azure, anteriormente conhecido como *Microsoft HDInsight Developer Preview*. O Emulador tem como alvo os cenários de desenvolvedor e só dá suporte a implantações de um único nó. Para usar o emulador de HDInsight, consulte [Introdução ao emulador de HDInsight][].

> [WACOM.NOTE] Para obter instruções sobre como provisionar um cluster HBase, consulte [Provision HBase cluster in HDInsight (Provisionar cluster HBase no HDInsight)][]. Consulte [Qual a diferença entre Hadoop e HBase?][] para entender por que você opta por um em vez do outro.

## O que este tutorial aborda?

Suponha que você tenha um grande conjunto de dados não estruturado e deseje executar consultas nele para extrair algumas informações importantes. É exatamente isso que vamos fazer neste tutorial. Veja como faremos isso:

![HDI.GetStartedFlow][]

Você também pode assistir a um vídeo de demonstração deste tutorial:

<center>
<iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen>
</iframe>
</center>
<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target = "_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->

**Pré-requisitos:**

Antes de começar este tutorial, você deve ter o seguinte:

-   Uma assinatura do Azure. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][], [Ofertas para membros][] ou [Avaliação gratuita][].
-   Um computador com Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone ou Office 2010 Professional Plus.

**Tempo estimado para conclusão:** 30 minutos

## Neste tutorial

-   [Criar uma conta de armazenamento do Azure][]
-   [Provisione um cluster HDInsight][]
-   [Executar um trabalho do Hive][]
-   [Conectar-se as ferramentas do Microsoft Business Intelligence][]
-   [Próximas etapas][]

## <a name="storage"></a>Criar uma conta de Armazenamento do Azure

O HDInsight usa o Armazenamento de Blob do Azure para armazenar dados. Ele é chamado *WASB* ou *Armazenamento do Azure - Blob*. O WASB é a implementação do HDFS da Microsoft no Armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][].

Ao provisionar um cluster HDInsight, você especifica uma conta de Armazenamento do Azure. Um contêiner de armazenamento de Blob específico dessa conta é designado como o sistema de arquivos padrão, exatamente como no HDFS. O cluster HDInsight, por padrão, é provisionado no mesmo datacenter que a conta de armazenamento que você especificou.

Além dessa conta de armazenamento, você pode adicionar mais contas de armazenamento ao configurar um cluster HDInsight de modo personalizado. Essa conta de armazenamento adicional pode ser da mesma assinatura do Azure ou de assinaturas diferentes do Azure. Para obter instruções, consulte [Provisionar clusters HDInsight usando opções personalizadas][].

Para simplificar este tutorial, somente o contêiner de blob padrão e a conta de armazenamento padrão são usados. Na prática, os arquivos de dados geralmente são armazenados em uma conta de armazenamento designada.

**Para criar uma conta de Armazenamento do Azure**

1.  Entre no [Portal de Gerenciamento do Azure][].
2.  Clique em **NOVO** no canto inferior esquerdo, aponte para **SERVIÇOS DE DADOS**, aponte para **ARMAZENAMENTO** e, em seguida, clique em **CRIAÇÃO RÁPIDA**

    ![HDI.StorageAccount.QuickCreate][]

3.  Digite **URL**, **LOCAL** e **REPLICAÇÃO** e, em seguida, clique em **CRIAR CONTA DE ARMAZENAMENTO**. Não há suporte para grupos de afinidade. Você verá a nova conta de armazenamento na lista de armazenamento.

    > [WACOM.NOTE] Um cluster HDInsight e a conta de armazenamento do Azure associada a ele devem estar no mesmo data center. Desse modo, verifique se você criou a conta de armazenamento nos locais com suporte para o cluster, que são: **Ásia Oriental**, **Sudeste da Ásia**, **Norte da Europa**, **Oeste da Europa**, **Leste dos EUA**, **Oeste dos EUA**, **Centro-Norte dos EUA**e **Centro-Sul dos EUA**.

4.  Aguarde até que o **STATUS** da nova conta de armazenamento seja alterado para **Online**.
5.  Selecione a nova conta de armazenamento na lista e clique em **GERENCIAR CHAVES DE ACESSO** na parte inferior da página.
6.  Anote o **NOME DA CONTA DE ARMAZENAMENTO** e a **CHAVE DE ACESSO PRIMÁRIA** (ou a **CHAVE DE ACESSO SECUNDÁRIA**. As duas chaves funcionam). Você precisará deles mais tarde no tutorial.

Para obter mais informações, consulte
[Como criar uma conta de armazenamento][] e [Usar o Armazenamento de Blob do Azure com o HDInsight][].

## <a name="provision"></a>Provisione um cluster HDInsight

Ao provisionar um cluster HDInsight, você provisiona recursos de computação do Azure que contêm o Hadoop e aplicativos relacionados. Nesta seção, você provisiona um cluster HDInsight versão 3.0, baseado no Hadoop versão 2.2. Se desejar provisionar um cluster HDInsight com o Hadoop versão 2.4, clique na guia da versão específica no início deste artigo. Também é possível criar clusters Hadoop para outras versões usando cmdlets do HDInsight PowerShell ou usando o SDK do .NET do HDInsight. Para obter instruções, consulte [Provisionar clusters HDInsight usando opções personalizadas][]. Para obter informações sobre versões diferentes do HDInsight e o SLA, consulte a página [Controle de versão do componente do HDInsight][].

**Para provisionar o cluster HDInsight**

1.  Entre no [Portal de Gerenciamento do Azure][].

2.  Clique em **HDInsight** à esquerda para listar o status dos clusters em sua conta. Na captura de tela a seguir, não há nenhum cluster HDInsight existente.

    ![HDI.ClusterStatus][]

3.  Clique em **NOVO** no lado inferior esquerdo, clique em **Serviços de Dados**, clique em **HDInsight** e, por fim, clique em **Criação Personalizada**.

    ![HDI.CustomCreateCluster][]

    Insira ou selecione os valores, conforme mostrado na imagem acima, e clique na seta para a direita.

4.  Na página **Configurar Cluster**, digite ou selecione os valores a seguir:

	<table border="1">
    <tr><th> Nome                </th><th> Valor </th></tr>
    <tr><td> Nós de dados        </td><td> Número de nós de dados que você deseja implantar. Para fins de teste, crie um cluster de nó único.<br />                                                                                                                                                                                                                                                                                                                                                                                                                  
                     O limite de tamanho do cluster varia para as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite. </td></tr>
    <tr><td> Região/Rede virtual </td><td> Escolha a mesma região da conta de armazenamento que você criou no último procedimento. O HDInsight requer que a conta de armazenamento esteja localizada na mesma região. Mais adiante na configuração, você poderá escolher somente uma conta de armazenamento que esteja na mesma região especificada aqui. As regiões disponíveis são: <strong>Ásia Oriental</strong>, <strong>Sudeste da Ásia</strong>, <strong>Norte da Europa</strong>, <strong>Oeste da Europa</strong>, <strong>Leste dos EUA</strong>, <strong>Oeste dos EUA</strong>, <strong>Centro-Norte dos EUA</strong> e <strong>Centro-Sul dos EUA</strong> </td></tr>
	</table>

    Clique na seta à direita.

5.  Na página **Configurar Usuário de Cluster**, forneça os seguintes valores:

    ![HDI.CustomCreateCluster.ClusterUser][]

	<table border="1">
    <tr><th> Propriedade                        </th><th> Valor </th></tr>
    <tr><td> Nome de usuário                    </td><td> Especifique o nome do usuário do cluster HDInsight.  </td></tr>
    <tr><td> Senha/Confirmar senha              </td><td> Especifique a senha do usuário do cluster HDInsight. </td></tr>
    <tr><td> Inserir Hive/Oozie Metastore       </td><td> Marque esta caixa de seleção para especificar um banco de dados SQL no mesmo data center que o cluster a ser usado como o metastore do Hive/Oozie. Isso será útil se você quiser reter os metadados sobre os trabalhos do Hive/Oozie após um cluster ter sido excluído. </td></tr>
    <tr><td> Banco de dados da metastore        </td><td> Especifique o banco de dados SQL do Azure que será usado como o metastore para Hive/OOzie. Esse Banco de Dados SQL deve estar no mesmo data center que o cluster HDInsight. A caixa de listagem lista apenas os bancos de dados SQL no mesmo data center, conforme especificado na página <strong>Detalhes do Cluster</strong>. </td></tr>
    <tr><td> Usuário do banco de dados          </td><td> Especifique o usuário do Banco de Dados SQL que será usado para a conexão ao banco de dados. </td></tr>
    <tr><td> Senha do usuário do banco de dados </td><td> Especifique a senha do usuário do Banco de Dados SQL.  </td></tr>
	</table>

    > [WACOM.NOTE] O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando estiver na exibição do servidor, clique em **Configurar** e, para **Serviços do Windows Azure**, clique em **Sim** e em **Salvar**.

    Clique na seta à direita.

6.  Na página **Conta de Armazenamento**, forneça seguinte valor:

    ![HDI.CustomCreateCluster.StorageAccount][]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr class="header">
    <th align="left">Propriedade</th>
    <th align="left">Valor</th>
    </tr>
    <tr class="odd">
    <td align="left">Conta de armazenamento</td>
    <td align="left">Especifique a conta de armazenamento do Azure que será usada como o sistema de arquivos padrão para o cluster HDInsight. Você pode escolher uma destas três opções:
    <ul>
    <li>Usar Armazenamento Existente</li>
    <li>Criar Novo Armazenamento</li>
    <li>Usar Armazenamento de Outra Assinatura</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">Nome da conta</td>
    <td align="left"><ul>
    <li>Se você optar por usar um armazenamento existente, para <strong>Nome da conta</strong>, selecione uma conta de armazenamento existente. A lista suspensa exibe somente as contas de armazenamento localizadas no mesmo data center onde você optou por provisionar o cluster.</li>
    <li>Se você selecionar as opções <strong>Criar novo armazenamento</strong> ou <strong>Usar armazenamento de outra assinatura</strong>, deverá fornecer o nome da conta de armazenamento.</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left">Chave de conta</td>
    <td align="left">Se você selecionar a opção <strong>Usar armazenamento de outra assinatura</strong>, especifique a chave de conta para essa conta de armazenamento.</td>
    </tr>
    <tr class="even">
    <td align="left">Contêiner padrão</td>
    <td align="left"><p>Especifica o contêiner padrão na conta de armazenamento usado como o sistema de arquivos padrão para o cluster HDInsight. Se você selecionar <strong>Usar Armazenamento Existente</strong> para o campo <strong>Conta de Armazenamento</strong>, e não houver contêineres existentes nessa conta, o contêiner será criado por padrão com o mesmo nome do cluster. Se um contêiner com o nome do cluster já existir, será acrescentado um número de sequência ao nome do contêiner. Por exemplo, meucontêiner1, meucontêiner2 e assim por diante. No entanto, se a conta de armazenamento existente tiver contêiner com um nome diferente do nome do cluster que você especificou, você também pode usar esse contêiner.</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Contas de armazenamento adicionais</td>
    <td align="left">O HDInsight oferece suporte a várias contas de armazenamento. Não há nenhum limite de contas de armazenamento adicionais que podem ser usadas por um cluster. No entanto, se criar um cluster usando o Portal de Gerenciamento, você terá um limite de sete, devido às restrições da interface do usuário. Cada conta de armazenamento adicional que você especificar adiciona uma página extra de Conta de Armazenamento ao assistente, onde você pode especificar as informações da conta. Por exemplo, na captura de tela acima, uma conta de armazenamento adicional está selecionada, e portanto a página cinco é adicionada à caixa de diálogo.</td>
    </tr>
    </table>

    Se você tiver optado por contas de armazenamento adicionais, clique na seta para a direita. Se não, clique na marca de seleção para começar a provisionar o cluster. Quando o provisionamento for concluído, a coluna de status mostrará **Em Execução**.

7.  Na página **Conta de armazenamento**, digite as informações da conta de armazenamento adicional caso tenha optado por isso:

    ![HDI.CustomCreateCluster.AddOnStorage][]

    Aqui, mais uma vez, você tem a opção de escolher entre um armazenamento existente, criar um novo armazenamento ou usar um armazenamento de outra assinatura do Azure. O procedimento para fornecer os valores é semelhante à etapa anterior.

    Clique na marca de seleção para começar a provisionar o cluster. Quando o provisionamento for concluído, a coluna de status mostrará **Em Execução**.

## <a name="sample"></a>Executar um trabalho do Hive

Agora que você provisionou um cluster HDInsight, a próxima etapa é executar um trabalho do Hive para consultar uma amostra da tabela Hive, *hivesampletable*, que é fornecida com clusters HDInsight. A tabela contém dados sobre o fabricante de dispositivos móveis, plataformas e modelos. Consultamos essa tabela para recuperar dados para dispositivos móveis de um fabricante específico.

**Para executar um trabalho do Hive em um painel do cluster**

1.  Entre no [Portal de Gerenciamento do Azure][].
2.  Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista dos clusters criados, incluindo aquele criado na última seção.
3.  Clique no nome do cluster em que deseja executar o trabalho do Hive e em **GERENCIAR CLUSTER** na parte inferior da página.
4.  Uma página da Web será aberta em uma guia diferente do navegador. Insira a conta e a senha do usuário do Hadoop. O nome de usuário padrão é **admin**; a senha é a que você digitou durante o provisionamento do cluster. O painel fica assim:

    ![hdi.dashboard][]

    Há várias guias na parte superior. A guia padrão é **Editor do Hive**, enquanto as outras guias são **Histórico de Trabalhos** e **Navegador de Arquivos**. Usando o painel, você pode enviar consultas do Hive, verificar logs de trabalho do Hadoop e procurar arquivos do WASB.

    > [WACOM.NOTE] Observe que a URL da página da Web é *\<NomedoCluster\>.azurehdinsight.net*. Em vez de abrir o painel por meio do Portal de gerenciamento, você também pode abrir o painel de um navegador da Web usando a URL.

5.  Na guia **Editor do Hive**, de **Nome da Consulta**, insira **HTC20**. O nome da consulta é o cargo.

6.  No painel de consulta, insira a seguinte consulta:

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][]

7.  Clique em **Enviar**. Demora alguns minutos para obter os resultados. A tela é atualizada a cada 30 segundos. Você também pode clicar em **Atualizar** para atualizar a tela.

    Depois de concluído, a tela fica assim:

    ![hdi.dashboard.query.select.result][]

8.  Clique no nome da consulta na tela para ver a saída. Anote a **Hora de Início do Trabalho (UTC)**. Você precisará dela mais tarde.

    ![hdi.dashboard.query.select.result.output][]

    A página também mostra a **Saída do Trabalho** e o **Log do Trabalho**. Além disso, você tem a opção de baixar o arquivo de saída (\_stdout) e o arquivo de log (\_stderr).

    > [WACOM.NOTE] A tabela **Sessão do Trabalho** na guia **Editor do Hive** lista trabalhos concluídos ou em execução desde que você permaneça nessa guia. A tabela não listará nenhum trabalho se você sair da página. A guia **Histórico de Trabalhos** mantém uma lista de todos os trabalhos concluídos ou em execução.

**Para navegar até o arquivo de saída**

1.  No painel do cluster, clique em **Navegador de Arquivos** na parte superior.
2.  Clique no nome da conta de armazenamento, clique no nome do contêiner (que é o mesmo que o nome do cluster) e clique em **usuário**.
3.  Clique no administrador e no número GUID com a última hora de modificação, um pouco depois da hora de início do trabalho anotada anteriormente. Anote esse GUID. Você precisará dele na próxima seção.

    ![hdi.dashboard.query.browse.output][]

## <a name="powerquery"></a>Conectar-se às ferramentas do Microsoft Business Intelligence

Você pode usar o suplemento Power Query para Microsoft Excel para importar a saída de trabalho do HDInsight para o Excel, onde as ferramentas do Microsoft BI (Business Intelligence) podem ser usadas para analisar ainda mais os resultados.

Você deve ter o Excel 2010 ou 2013 instalado para concluir essa parte do tutorial.

**Para baixar o Microsoft Power Query para Excel**

-   Baixe o Microsoft Power Query para Excel no [Centro de Download da Microsoft][] e instale-o.

**Para importar dados no HDInsight**

1.  Abra o Excel e crie uma nova planilha em branco.
2.  Clique no menu **Power Query**, clique em **De Outras Fontes** e, em seguida, clique em **Do Azure HDInsight**.

    ![HDI.GettingStarted.PowerQuery.ImportData][]

3.  Digite o **Nome da Conta** da conta de armazenamento do Blob do Azure associada ao cluster e, em seguida, clique em **OK**. Esta é a conta de armazenamento criada anteriormente no tutorial.
4.  Digite a **Chave de Conta** para a conta de armazenamento do Blob do Azure e clique em **Salvar**.
5.  No painel de navegação à direita, clique duas vezes no nome do contêiner de armazenamento de Blob. Por padrão, o nome do contêiner é igual ao nome do cluster.

6.  Localize **stdout** na coluna **Nome**. Verifique se o GUID na coluna Caminho da Pasta correspondente coincide com o GUID anotado anteriormente. Clique em **Binário** à esquerda de **stdout**.

    ![HDI.GettingStarted.PowerQuery.ImportData2][]

7.  Clique em **Fechar e Carregar** no canto superior esquerdo para importar a saída de trabalho do Hive no Excel.

## <a name="nextsteps"></a>Próximas etapas

Neste tutorial, você aprendeu a provisionar um cluster com o HDInsight, executar um trabalho de MapReduce e importar os resultados para o Excel, onde eles podem ser processados ainda mais e exibidos graficamente usando ferramentas de BI. Para saber mais, consulte os seguintes artigos:

-   [Introdução ao emulador do HDInsight][Introdução ao emulador de HDInsight]
-   [Usar o armazenamento de blobs do Azure com o HDInsight][Usar o Armazenamento de Blob do Azure com o HDInsight]
-   [Administrar o HDInsight usando o PowerShell][]
-   [Carregar dados no HDInsight][]
-   [Usar o MapReduce com HDInsight][]
-   [Use o hive com o HDInsight][]
-   [Use o Pig com o HDInsight][]
-   [Usar o Oozie com o HDInsight][]
-   [Desenvolver programas de streaming do Hadoop em C# para o HDInsight][]
-   [Desenvolver programas Java MapReduce para HDInsight][]

  [Hadoop 2.4]: ../hdinsight-get-started "Introdução ao uso do Hadoop 2.4 no HDInsight"
  [Hadoop 2.2]: ../hdinsight-get-started-30 "Introdução ao uso do Hadoop 2.2 no HDInsight"
  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [MapReduce]: http://go.microsoft.com/fwlink/?LinkId=510086
  [HDFS]: http://go.microsoft.com/fwlink/?LinkId=510087
  [Hive]: http://go.microsoft.com/fwlink/?LinkId=510085
  [Introdução ao emulador de HDInsight]: ../hdinsight-get-started-emulator/
  [Provision HBase cluster in HDInsight (Provisionar cluster HBase no HDInsight)]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/
  [Qual a diferença entre Hadoop e HBase?]: http://go.microsoft.com/fwlink/?LinkId=510237
  [HDI.GetStartedFlow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png
  [Opções de compra]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Ofertas para membros]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Avaliação gratuita]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Criar uma conta de armazenamento do Azure]: #storage
  [Provisione um cluster HDInsight]: #provision
  [Executar um trabalho do Hive]: #sample
  [Conectar-se as ferramentas do Microsoft Business Intelligence]: #powerquery
  [Próximas etapas]: #nextsteps
  [Usar o Armazenamento de Blob do Azure com o HDInsight]: ../hdinsight-use-blob-storage/
  [Provisionar clusters HDInsight usando opções personalizadas]: ../hdinsight-provision-clusters/
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [Como criar uma conta de armazenamento]: ../storage-create-storage-account/
  [Controle de versão do componente do HDInsight]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [HDI.CustomCreateCluster.ClusterUser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
  [HDI.CustomCreateCluster.StorageAccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
  [HDI.CustomCreateCluster.AddOnStorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [hdi.dashboard.query.select.result.output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
  [hdi.dashboard.query.browse.output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
  [Centro de Download da Microsoft]: http://www.microsoft.com/en-us/download/details.aspx?id=39379
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
