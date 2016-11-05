---
title: Conectar o Excel ao Hadoop com o driver ODBC do Hive | Microsoft Docs
description: Saiba como configurar e usar o driver ODBC do Microsoft Hive para Excel para consultar dados em um cluster HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
tags: azure-portal
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/25/2016
ms.author: jgao

---
# Conectar o Excel ao Hadoop com o driver ODBC do Microsoft Hive
[!INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

A solução de Big Data da Microsoft integra componentes do Microsoft Business Intelligence (BI) com os clusters do Apache Hadoop que foram implantados pelo Azure HDInsight. Um exemplo dessa integração é a capacidade de conectar o Excel ao data warehouse do Hive de um cluster Hadoop no HDInsight usando o driver ODBC do Microsoft Hive.

Também é possível conectar os dados associados a um cluster HDInsight e outras fontes de dados, incluindo outros clusters Hadoop (não HDInsight), do Excel usando o suplemento Microsoft Power Query para Excel. Para obter informações sobre como instalar e usar o Power Query, consulte [Conectar o Excel ao HDInsight com o Power Query][hdinsight-power-query].

> [!NOTE]
> Embora as etapas deste artigo possam ser usadas com um cluster HDInsight com base no Linux ou Windows, é preciso ter o Windows para a estação de trabalho cliente.
> 
> 

**Pré-requisitos**:

Antes de começar este artigo, você deve ter o seguinte:

* **Um cluster HDInsight**. Para criar um, consulte [Introdução ao Azure HDInsight][hdinsight-get-started].
* Uma **estação de trabalho** com Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone ou Office 2010 Professional Plus.

## Instalar o driver ODBC do Microsoft Hive
Baixe e instale o driver ODBC do Microsoft Hive no [Centro de download][hive-odbc-driver-download]

Esse driver pode ser instalado em versões de 32 ou de 64 bits do Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 e Windows Server 2012, e permitirá a conexão ao Azure HDInsight (versão 1.6 ou posterior) e ao Emulador do Azure HDInsight (v.1.0.0.0 e posterior). Você deve instalar a versão que corresponde à versão do aplicativo onde você usará o driver ODBC. Para este tutorial, o driver será usado do Office Excel.

## Criar uma fonte de dados ODBC do Hive
As etapas a seguir mostram como criar uma fonte de dados ODBC do Hive.

1. No Windows 8 ou no Windows 10, pressione a tecla Windows para abrir a tela Iniciar e digite **fontes de dados**.
2. Clique em **Configurar fontes de dados ODBC (32 bits)** ou **Configurar fontes de dados ODBC (64 bits)**, dependendo da sua versão do Office. Se estiver usando o Windows 7, escolha **Fontes de Dados ODBC (32 bits)** ou **Fontes de Dados ODBC (64 bits)** em **Ferramentas Administrativas**. Isso iniciará a caixa de diálogo **Administrador de Fonte de Dados ODBC**.
   
    ![Administrador de fonte de dados ODBC][img-hdi-simbahiveodbc-datasource-admin]
3. No DNS do Usuário, clique em **Adicionar** para abrir o assistente **Criar Nova Fonte de Dados**.
4. Selecione **Driver ODBC do Microsoft Hive** e clique em **Concluir**. Isso iniciará a caixa de diálogo **Configuração DNS do Driver ODBC do Microsoft Hive**.
5. Digite ou selecione os valores a seguir:
   
   | Propriedade | Descrição |
   | --- | --- |
   |  Nome da fonte de dados |Forneça um nome para a sua fonte de dados |
   |  Host |Digite &lt;HDInsightClusterName>.azurehdinsight.net. Por exemplo, meu\_Cluster\_HDI.azurehdinsight.net |
   |  Port |Use <strong>443</strong>. (Essa porta foi alterada de 563 para 443.) |
   |  Banco de dados |Use <strong>Padrão</strong>. |
   |  Tipo de servidor Hive |Selecione <strong>Servidor Hive 2</strong> |
   |  Mecanismo |Selecione <strong>Serviço do Azure HDInsight</strong> |
   |  Caminho HTTP |Deixe em branco. |
   |  Nome de usuário |Insira o nome de usuário do cluster HDInsight. Esse é o nome de usuário criado durante o processo de provisionamento do cluster. Se você tiver usado a opção de criação rápida, o nome de usuário padrão será <strong>admin</strong>. |
   |  Senha |Insira a senha do usuário do cluster HDInsight. |
   
    </table>
   
    Há alguns parâmetros importantes a serem lembrados ao clicar em **Opções Avançadas**.
   
   | Parâmetro | Descrição |
   | --- | --- |
   |  Use Consulta Nativa |Quando estiver selecionada, o driver ODBC NÃO tentará converter TSQL em HiveQL. Você deverá usar essa opção somente se estiver 100% certo de que está enviando instruções HiveQL puras. Ao conectar-se ao SQL Server ou ao Banco de Dados SQL do Azure, deixe-a desmarcada. |
   |  Linhas buscadas por bloco |Ao buscar uma grande quantidade de registros, o ajuste desse parâmetro poderá ser necessário para garantir o desempenho ideal. |
   |  Comprimento de coluna de cadeia de caracteres padrão, Comprimento da coluna binária e Escala da coluna decimal |Os tamanhos e as precisões dos tipos de dados podem afetar a maneira como os dados são retornados. Eles farão com que informações incorretas sejam retornadas devido à perda de precisão e/ou truncamento. |

    ![Opções avançadas][img-HiveOdbc-DataSource-AdvancedOptions]

1. Clique em **Testar** para testar a fonte de dados. Quando a fonte de dados estiver configurada corretamente, será mostrado *TESTES CONCLUÍDOS COM ÊXITO!*.
2. Clique em **OK** para fechar o diálogo Testar. A nova fonte de dados deve agora estar listada no **Administrador de Fonte de Dados ODBC**.
3. Clique em **OK** para sair do assistente.

## Importar dados do HDInsight para o Excel
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
9. Clique em **Concluir**.
10. No diálogo **Importar Dados**, você pode alterar ou especificar a consulta. Para fazer isso, clique em **Propriedades**. Isso pode levar alguns segundos.
11. Clique na guia **Definição** e acrescente **LIMIT 200** à instrução de seleção Hive na caixa de texto **Texto do comando**. A modificação limitará o conjunto de registros retornado para 200.
    
    ![Propriedades da conexão][img-hdi-simbahiveodbc-excel-connectionproperties]
12. Clique em **OK** para fechar o diálogo Propriedades da Conexão.
13. Clique em **OK** para fechar o diálogo **Importar Dados**.
14. Insira a senha novamente e clique em **OK**. Leva alguns segundos para que os dados sejam importados para o Excel.

## Próximas etapas
Neste artigo você aprendeu a usar o driver ODBC do Microsoft Hive para recuperar dados do Serviço do HDInsight no Excel. Da mesma forma, você pode recuperar dados do Serviço do HDInsight no Banco de Dados SQL. Também é possível carregar dados em um Serviço do HDInsight. Para obter mais informações, consulte:

* [Analisar dados de atraso de voo usando o HDInsight][hdinsight-analyze-flight-data]
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Usar o Sqoop com o HDInsight][hdinsight-use-sqoop]

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

<!---HONumber=AcomDC_0914_2016-->