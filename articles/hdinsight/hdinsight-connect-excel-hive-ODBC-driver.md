<properties
   pageTitle="Conectar o Excel ao Hadoop com o driver ODBC do Hive | Microsoft Azure"
   description="Saiba como configurar e usar o driver ODBC do Microsoft Hive para Excel para consultar dados em um cluster HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="bradsev"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/08/2015"
   ms.author="bradsev"/>

#Conectar o Excel ao Hadoop com o driver ODBC do Microsoft Hive


A solução de Big Data da Microsoft integra componentes do Microsoft Business Intelligence (BI) com os clusters do Apache Hadoop que foram implantados pelo Azure HDInsight. Um exemplo dessa integração é a capacidade de conectar o Excel ao data warehouse do Hive de um cluster Hadoop no HDInsight usando o driver ODBC do Microsoft Hive.

Também é possível conectar os dados associados a um cluster HDInsight e outras fontes de dados, incluindo outros clusters Hadoop (não HDInsight), do Excel usando o suplemento Microsoft Power Query para Excel. Para obter informações sobre como instalar e usar o Power Query, consulte [Conectar o Excel ao HDInsight com o Power Query][hdinsight-power-query].

> [AZURE.NOTE]Embora as etapas deste artigo possam ser usadas com um cluster HDInsight com base no Linux ou Windows, é preciso ter o Windows para a estação de trabalho cliente.

**Pré-requisitos**:

Antes de começar este artigo, você deve ter o seguinte:

- **Um cluster HDInsight**. Para configurar um, consulte [Introdução ao Azure HDInsight][hdinsight-get-started].
- Uma **estação de trabalho** com Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone ou Office 2010 Professional Plus.


##<a id="InstallHiveODBCDriver"></a>Instalar o driver ODBC do Microsoft Hive

Baixe e instale o driver ODBC do Microsoft Hive no [Centro de download][hive-odbc-driver-download]

Esse driver pode ser instalado em versões de 32 ou de 64 bits do Windows 7, Windows 8, Windows Server 2008 R2 e Windows Server 2012 e permitirá a conexão ao Azure HDInsight (versão 1.6 ou posterior) e ao Emulador do Azure HDInsight (v.1.0.0.0 e posterior). Você deve instalar a versão que corresponde à versão do aplicativo onde você usará o driver ODBC. Para este tutorial, o driver será usado do Office Excel.

##<a id="CreateHiveODBCDataSource"></a>Criar uma fonte de dados ODBC do Hive

As etapas a seguir mostram como criar uma fonte de dados ODBC do Hive.

1. No Windows 8, pressione a tecla Windows para abrir a tela Iniciar, digite **fontes de dados**.
2. Clique em **Configurar fontes de dados ODBC (32 bits)** ou **Configurar fontes de dados ODBC (64 bits)**, dependendo da sua versão do Office. Se estiver usando o Windows 7, escolha **Fontes de Dados ODBC (32 bits)** ou **Fontes de Dados ODBC (64 bits)** em **Ferramentas Administrativas**. Isso iniciará a caixa de diálogo **Administrador de Fonte de Dados ODBC**.

	![Administrador de fonte de dados ODBC][img-hdi-simbahiveodbc-datasource-admin]

3. No DNS do Usuário, clique em **Adicionar** para abrir o assistente **Criar Nova Fonte de Dados**.
4. Selecione **Driver ODBC do Microsoft Hive** e clique em **Concluir**. Isso iniciará a caixa de diálogo **Configuração DNS do Driver ODBC do Microsoft Hive**.

5. Digite ou selecione os valores a seguir:

	<table border="1">
<tr><td><strong>Propriedade</strong></td><td><strong>Descrição</strong></td></tr>
<tr><td>Nome da fonte de dados</td><td>Forneça um nome para a sua fonte de dados</td></tr>
<tr><td>Host</td><td>Digite <HDInsightClusterName>.azurehdinsight.net. Por exemplo, meu_Cluster_HDI.azurehdinsight.net</td></tr>
<tr><td>Port</td><td>Use <strong>443</strong>. (Essa porta foi alterada de 563 para 443.)</td></tr>
<tr><td>Banco de dados</td><td>Use <strong>Padrão</strong>.</td></tr>
<tr><td>Tipo de servidor Hive</td><td>Selecione <strong>Servidor Hive 2</strong></td></tr>
<tr><td>Mecanismo</td><td>Selecione <strong>Serviço do Azure HDInsight</strong></td></tr>
<tr><td>Caminho HTTP</td><td>Deixe em branco.</td></tr>
<tr><td>Nome de usuário</td><td>Insira o nome de usuário do cluster HDInsight. Esse é o nome de usuário criado durante o processo de provisionamento do cluster. Se você tiver usado a opção de criação rápida, o nome de usuário padrão será <strong>admin</strong>.</td></tr>
<tr><td>Password</td><td>Insira a senha do usuário do cluster HDInsight.</td></tr>
</table>Há alguns parâmetros importantes a serem lembrados ao clicar em **Opções Avançadas**.

	<table border="1">
<tr><td>Use Consulta Nativa</td><td>Quando estiver selecionada, o driver ODBC NÃO tentará converter TSQL em HiveQL. Você deverá usar essa opção somente se estiver 100% certo de que está enviando instruções HiveQL puras. Ao conectar-se ao SQL Server ou ao Banco de Dados SQL do Azure, deixe-a desmarcada.</td></tr>
<tr><td>Linhas buscadas por bloco</td><td>Ao buscar uma grande quantidade de registros, o ajuste desse parâmetro poderá ser necessário para garantir o desempenho ideal.</td></tr>
<tr><td>Tamanho da coluna de cadeia de caracteres padrão, <br/>
		Comprimento da coluna binária,  <br/>
		Escala da coluna decimal</td><td>Os tamanhos e as precisões dos tipos de dados podem afetar a maneira como os dados são retornados. Eles farão com que informações incorretas sejam retornadas devido à perda de precisão e/ou truncamento.</td></tr>
</table>![Opções avançadas][img-HiveOdbc-DataSource-AdvancedOptions]

6. Clique em **Testar** para testar a fonte de dados. Quando a fonte de dados estiver configurada corretamente, será mostrado *TESTES CONCLUÍDOS COM ÊXITO!*.
7. Clique em **OK** para fechar o diálogo Testar. A nova fonte de dados deve agora estar listada no **Administrador de Fonte de Dados ODBC**.
8. Clique em **OK** para sair do assistente.

##<a id="ImportData"></a>Importar dados para o Excel de um cluster HDInsight

As etapas a seguir descrevem a maneira de importar dados de uma tabela hive para uma pasta de trabalho do Excel usando a fonte de dados ODBC que você criou nas etapas acima.

1. Abra uma pasta de trabalho nova ou existente no Excel.
2. Na guia **Dados**, clique em **De Outras Fontes de Dados**, então clique em **Do Assistente de Conexão de Dados** para iniciar o **Assistente de Conexão de Dados**.

	![Abrir o assistente de conexão de dados][img-hdi-simbahiveodbc.excel.dataconnection]

3. Selecione **DSN ODBC** como a fonte de dados e clique em **Avançar**.
4. Em Fontes de dados ODBC, selecione o nome da fonte de dados criada na etapa anterior e clique em **Avançar**.
5. Insira novamente a senha do cluster no assistente e clique em **Testar** para verificar a configuração novamente, se necessário.
6. Clique em **OK** para fechar o diálogo Testar.
7. Clique em **OK**. Aguarde até que a caixa de diálogo **Selecionar Banco de Dados e Tabela** seja aberta. Isso pode levar alguns segundos.
8. Selecione a tabela que deseja importar e clique em **Avançar**. A *hivesampletable* é uma tabela hive de exemplo que é fornecida com os clusters HDInsight. Você pode escolhê-la se ainda não tiver criado uma. Para obter mais informações sobre como executar consultas Hive e criar tabelas Hive, consulte [Usar o Hive com o HDInsight][hdinsight-use-hive].
8. Clique em **Concluir**.
9. No diálogo **Importar Dados**, você pode alterar ou especificar a consulta. Para fazer isso, clique em **Propriedades**. Isso pode levar alguns segundos.
10. Clique na guia **Definição** e acrescente **LIMIT 200** à instrução de seleção Hive na caixa de texto **Texto do comando**. A modificação limitará o conjunto de registros retornado para 200.

	![Propriedades da conexão][img-hdi-simbahiveodbc-excel-connectionproperties]

11. Clique em **OK** para fechar o diálogo Propriedades da Conexão.
12. Clique em **OK** para fechar o diálogo **Importar Dados**.  
13. Insira a senha novamente e clique em **OK**. Leva alguns segundos para que os dados sejam importados para o Excel.

##<a id="nextsteps"></a>Próximas etapas

Neste artigo você aprendeu a usar o driver ODBC do Microsoft Hive para recuperar dados do Serviço do HDInsight no Excel. Da mesma forma, você pode recuperar dados do Serviço do HDInsight no Banco de Dados SQL. Também é possível carregar dados em um Serviço do HDInsight. Para obter mais informações, consulte:

- [Analisar dados de atraso de voo usando o HDInsight][hdinsight-analyze-flight-data]
- [Carregar dados no HDInsight][hdinsight-upload-data]
- [Usar o Sqoop com o HDInsight][hdinsight-use-sqoop]


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png
 

<!---HONumber=July15_HO2-->