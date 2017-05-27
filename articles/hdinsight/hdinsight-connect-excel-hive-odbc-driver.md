---
title: "Conectar o Excel ao Hadoop com o driver ODBC do Hive – Azure HDInsight | Microsoft Docs"
description: Saiba como configurar e usar o driver ODBC do Microsoft Hive para Excel para consultar dados em clusters HDInsight no Microsoft Excel.
keywords: hadoop excel,hive excel,hive odbc
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
tags: azure-portal
editor: cgronlun
ms.assetid: a7665a14-0211-4521-b3e7-3b26e8029cc0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/12/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 62a5b47d3b3fe452bfdff3005192e5066bb7c7da
ms.contentlocale: pt-br
ms.lasthandoff: 05/12/2017


---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Conectar o Excel ao Hadoop no Azure HDInsight com o driver ODBC do Microsoft Hive

[!INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

A solução de Big Data da Microsoft integra componentes do Microsoft Business Intelligence (BI) com os clusters do Apache Hadoop que foram implantados pelo Azure HDInsight. Um exemplo dessa integração é a capacidade de conectar o Excel ao data warehouse do Hive de um cluster Hadoop no HDInsight usando o driver ODBC do Microsoft Hive.

Também é possível conectar os dados associados a um cluster HDInsight e outras fontes de dados, incluindo outros clusters Hadoop (não HDInsight), do Excel usando o suplemento Microsoft Power Query para Excel. Para obter informações sobre como instalar e usar o Power Query, confira [Conectar o Excel ao HDInsight com o Power Query][hdinsight-power-query].

> [!NOTE]
> Embora as etapas deste artigo possam ser usadas com um cluster HDInsight com base no Linux ou Windows, é preciso ter o Windows para a estação de trabalho cliente.
> 
> 

**Pré-requisitos**:

Antes de começar este artigo, você deve ter os seguintes itens:

* **Um cluster HDInsight**. Para criar um, confira [Introdução ao Azure HDInsight][hdinsight-get-started].
* **estação de trabalho** com Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone ou Office 2010 Professional Plus.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalar o driver ODBC do Microsoft Hive
Baixe e instale o driver ODBC do Microsoft Hive no [Centro de download][hive-odbc-driver-download].

Este driver pode ser instalado em versões de 32 ou 64 bits do Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 e Windows Server 2012. O driver permite a conexão com o Azure HDInsight (versão 1.6 ou posterior) e o Emulador do Azure HDInsight (1.0.0.0 e posterior). Você deve instalar a versão que corresponda à versão do aplicativo onde você usa o driver ODBC. Para este tutorial, o driver é usado do Office Excel.

## <a name="create-hive-odbc-data-source"></a>Criar uma fonte de dados ODBC do Hive
As etapas a seguir mostram como criar uma fonte de dados ODBC do Hive.

1. No Windows 8 ou no Windows 10, pressione a tecla Windows para abrir a tela Iniciar e digite **fontes de dados**.
2. Clique em **Configurar fontes de dados ODBC (32 bits)** ou **Configurar fontes de dados ODBC (64 bits)**, dependendo da sua versão do Office. Se estiver usando o Windows 7, escolha **Fontes de Dados ODBC (32 bits)** ou **Fontes de Dados ODBC (64 bits)** em **Ferramentas Administrativas**. Você deverá ver a caixa de diálogo **Administrador de Fonte de Dados ODBC**.
   
    ![Administrador de fonte de dados ODBC](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "Configurar um DSN usando o Administrador de Fonte de Dados ODBC")

3. No DNS do Usuário, clique em **Adicionar** para abrir o assistente **Criar Nova Fonte de Dados**.
4. Selecione **Driver ODBC do Microsoft Hive** e clique em **Concluir**. Você deverá ver a caixa de diálogo **Configuração DNS do Driver ODBC do Microsoft Hive**.
5. Digite ou selecione os valores a seguir:
   
   | Propriedade | Descrição |
   | --- | --- |
   |  Nome da fonte de dados |Forneça um nome para a sua fonte de dados |
   |  Host |Digite &lt;HDInsightClusterName>.azurehdinsight.net. Por exemplo, meu_Cluster_HDI.azurehdinsight.net |
   |  Port |Use <strong>443</strong>. (Essa porta foi alterada de 563 para 443.) |
   |  Banco de dados |Use <strong>Padrão</strong>. |
   |  Tipo de servidor Hive |Selecione <strong>Servidor Hive 2</strong> |
   |  Mecanismo |Selecione <strong>Serviço do Azure HDInsight</strong> |
   |  Caminho HTTP |Deixe em branco. |
   |  Nome de usuário |Insira o nome de usuário HTTP do cluster HDInsight. O nome de usuário padrão é <strong>admin</strong>. |
   |  Senha |Insira a senha do usuário do cluster HDInsight. |
   
    </table>
   
    Há alguns parâmetros importantes a serem lembrados ao clicar em **Opções Avançadas**.
   
   | Parâmetro | Descrição |
   | --- | --- |
   |  Use Consulta Nativa |Quando estiver selecionada, o driver ODBC NÃO tentará converter TSQL em HiveQL. Você deverá usar essa opção somente se estiver 100% certo de que está enviando instruções HiveQL puras. Ao conectar-se ao SQL Server ou ao Banco de Dados SQL do Azure, deixe-a desmarcada. |
   |  Linhas buscadas por bloco |Ao buscar uma grande quantidade de registros, o ajuste desse parâmetro poderá ser necessário para garantir o desempenho ideal. |
   |  Comprimento de coluna de cadeia de caracteres padrão, Comprimento da coluna binária e Escala da coluna decimal |Os tamanhos e as precisões dos tipos de dados podem afetar a maneira como os dados são retornados. Eles farão com que informações incorretas sejam retornadas devido à perda de precisão e/ou truncamento. |

    ![Opções avançadas](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "Opções de configuração de DSN avançadas")

1. Clique em **Testar** para testar a fonte de dados. Quando a fonte de dados estiver configurada corretamente, será mostrado *TESTES CONCLUÍDOS COM ÊXITO!*.
2. Clique em **OK** para fechar o diálogo Testar. A nova fonte de dados deve agora estar listada no **Administrador de Fonte de Dados ODBC**.
3. Clique em **OK** para sair do assistente.

## <a name="import-data-into-excel-from-hdinsight"></a>Importar dados do HDInsight para o Excel
As etapas a seguir descrevem a maneira de importar dados de uma tabela hive para uma pasta de trabalho do Excel usando a fonte de dados ODBC que você criou nas etapas acima.

1. Abra uma pasta de trabalho nova ou existente no Excel.
2. Na guia **Dados**, clique em **De Outras Fontes de Dados** e clique em **Do Assistente de Conexão de Dados** para iniciar o **Assistente de Conexão de Dados**.
   
    ![Abrir assistente de conexão de dados](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Abrir assistente de conexão de dados")
3. Selecione **ODBC DSN** como a fonte de dados e clique em **Avançar**.
4. Em Fontes de dados ODBC, selecione o nome da fonte de dados criada na etapa anterior e clique em **Avançar**.
5. Insira novamente a senha do cluster no assistente e clique em **Testar** para verificar a configuração novamente.
6. Clique em **OK** para fechar o diálogo Testar.
7. Clique em **OK**. Aguarde até que a caixa de diálogo **Selecionar Banco de Dados e Tabela** seja aberta. Esta etapa pode demorar alguns segundos.
8. Selecione a tabela que deseja importar e clique em **Avançar**. A *hivesampletable* é uma tabela hive de exemplo que é fornecida com os clusters HDInsight.  Você pode escolhê-la se ainda não tiver criado uma. Para obter mais informações sobre como executar consultas Hive e criar tabelas Hive, confira [Usar o Hive com o HDInsight][hdinsight-use-hive].
9. Clique em **Concluir**.
10. No diálogo **Importar Dados** , você pode alterar ou especificar a consulta. Para fazer isso, clique em **Propriedades**. Esta etapa pode demorar alguns segundos.
11. Clique na guia **Definição** e acrescente **LIMIT 200** à instrução de seleção Hive na caixa de texto **Texto do comando**. A modificação limita o conjunto de registros retornado para 200.
    
    ![Propriedades da Conexão](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png "Configurar propriedades de conexão do DSN")
12. Clique em **OK** para fechar o diálogo Propriedades da Conexão.
13. Clique em **OK** para fechar a caixa de diálogo **Importar Dados**.  
14. Insira a senha novamente e clique em **OK**. Leva alguns segundos para que os dados sejam importados para o Excel.

## <a name="next-steps"></a>Próximas etapas
Neste artigo você aprendeu a usar o driver ODBC do Microsoft Hive para recuperar dados do Serviço do HDInsight no Excel. Da mesma forma, você pode recuperar dados do Serviço do HDInsight no Banco de Dados SQL. Também é possível carregar dados em um Serviço do HDInsight. Para obter mais informações, consulte:

* [Analisar dados de atraso de voo usando o HDInsight][hdinsight-analyze-flight-data]
* [Carregar Dados no HDInsight][hdinsight-upload-data]
* [Use o Sqoop com o HDInsight][hdinsight-use-sqoop]

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698



