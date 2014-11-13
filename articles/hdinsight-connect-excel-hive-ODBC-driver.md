<properties urlDisplayName="Connect Excel to HDInsight" pageTitle="Conectar o Excel ao Hadoop com o driver ODBC do Hive | Azure" metaKeywords="" description="Saiba como configurar e usar o driver ODBC do Microsoft Hive para Excel para consultar dados em um cluster HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Conectar o Excel ao Hadoop com o driver ODBC do Microsoft Hive" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Conectar o Excel ao Hadoop com o driver ODBC do Microsoft Hive

Um recurso principal da Solução de Big Data da Microsoft é a integração do Microsoft Business Intelligence (BI) com os clusters do Apache Hadoop que foram implantados pelo Azure HDInsight. Um exemplo dessa integração é a capacidade de conectar o Excel ao data warehouse do Hive de um cluster Hadoop no HDInsight usando o driver ODBC do Microsoft Hive.

Também é possível conectar os dados associados a um cluster HDInsight e outras fontes de dados, incluindo outros clusters Hadoop (não HDInsight), do Excel usando o suplemento Microsoft Power Query para Excel. Para obter informações sobre como instalar e usar o Power Query, consulte [Conectar o Excel ao HDInsight com o Power Query][Conectar o Excel ao HDInsight com o Power Query].

**Pré-requisitos**:

Antes de começar este artigo, você deve ter o seguinte:

-   Um cluster HDInsight. Para configurar um, consulte [Introdução ao Azure HDInsight][Introdução ao Azure HDInsight].
-   Um computador que esteja executando o Windows 8, Windows 7, Windows Server 2012 ou Windows Server 2008 R2.
-   Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone ou Office 2010 Professional Plus.

## Neste artigo

1.  [Instalar o driver ODBC do Microsoft Hive][Instalar o driver ODBC do Microsoft Hive]
2.  [Criar uma fonte de dados ODBC do Hive][Criar uma fonte de dados ODBC do Hive]
3.  [Importar dados para o Excel de um cluster HDInsight][Importar dados para o Excel de um cluster HDInsight]
4.  [Próximas etapas][Próximas etapas]

## <span id="InstallHiveODBCDriver"></span></a>Instalar o driver ODBC do Microsoft Hive

Baixe e instale o driver ODBC do Microsoft Hive no [Centro de download][Centro de download]

Esse driver pode ser instalado em versões de 32 ou de 64 bits do Windows 7, Windows 8, Windows Server 2008 R2 e Windows Server 2012 e permitirá a conexão ao Azure HDInsight (versão 1.6 ou posterior) e ao Emulador do Azure HDInsight (v.1.0.0.0 e posterior). Você deve instalar a versão que corresponde à versão do aplicativo onde você usará o driver ODBC. Para este tutorial, o driver será usado do Office Excel.

## <span id="CreateHiveODBCDataSource"></span></a>Criar uma fonte de dados ODBC do Hive

As etapas a seguir mostram como criar uma fonte de dados ODBC do Hive.

1.  No Windows 8, pressione a tecla Windows para abrir a tela Iniciar, digite **fontes de dados**.
2.  Clique em **Configurar fontes de dados ODBC (32 bits)** ou **Configurar fontes de dados ODBC (64 bits)**, dependendo da sua versão do Office. Se estiver usando o Windows 7, escolha **Fontes de Dados ODBC (32 bits)** ou **Fontes de Dados ODBC (64 bits)** em **Ferramentas Administrativas**. Isso iniciará a caixa de diálogo **Administrador de Fonte de Dados ODBC**.

    ![Administrador de fonte de dados ODBC][Administrador de fonte de dados ODBC]

3.  No DNS do Usuário, clique em **Adicionar** para abrir o assistente **Criar Nova Fonte de Dados**.
4.  Selecione **Driver ODBC do Microsoft Hive** e clique em **Concluir**. Isso iniciará a caixa de diálogo **Configuração DNS do Driver ODBC do Microsoft Hive**.

5.  Digite ou selecione os valores a seguir:

    |------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Propriedade**        | **Descrição**                                                                                                                                                                                                            |
    | Nome da fonte de dados | Forneça um nome para a sua fonte de dados                                                                                                                                                                                |
    | Host                   | Insira <hdinsightclustername>.azurehdinsight.net. Por exemplo, myHDICluster.azurehdinsight.net                                                                                                                           |
    | Port                   | Use **443**. (Essa porta foi alterada de 563 para 443.)                                                                                                                                                                  |
    | Banco de dados         | Use **Padrão**.                                                                                                                                                                                                          |
    | Tipo de servidor Hive  | Selecione **Servidor Hive 2**                                                                                                                                                                                            |
    | Mecanismo              | Selecione **Serviço do Azure HDInsight**                                                                                                                                                                                 |
    | Caminho HTTP           | Deixe em branco.                                                                                                                                                                                                         |
    | Nome de usuário        | Insira o nome de usuário do cluster HDInsight. Esse é o nome de usuário criado durante o processo de provisionamento do cluster. Se você tiver usado a opção de criação rápida, o nome de usuário padrão será **admin**. |
    | Password               | Insira a senha do usuário do cluster HDInsight.                                                                                                                                                                          |

    Há alguns parâmetros importantes a serem lembrados ao clicar em **Opções Avançadas**.

    |---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Use Consulta Nativa                               | Quando estiver selecionada, o driver ODBC NÃO tentará converter TSQL em HiveQL. Você deverá usar essa opção somente se estiver 100% certo de que está enviando instruções HiveQL puras. Ao conectar-se ao SQL Server ou ao Banco de Dados SQL do Azure, deixe-a desmarcada. |
    | Linhas buscadas por bloco                         | Ao buscar uma grande quantidade de registros, o ajuste desse parâmetro poderá ser necessário para garantir o desempenho ideal.                                                                                                                                              |
    | Tamanho da coluna de cadeia de caracteres padrão, 
      Comprimento da coluna binária,                    
      Escala da coluna decimal                          | Os tamanhos e as precisões dos tipos de dados podem afetar a maneira como os dados são retornados. Eles farão com que informações incorretas sejam retornadas devido à perda de precisão e/ou truncamento.                                                                  |

    ![Opções avançadas][Opções avançadas]

6.  Clique em **Testar** para testar a fonte de dados. Quando a fonte de dados estiver configurada corretamente, será mostrado *TESTES CONCLUÍDOS COM ÊXITO!*.
7.  Clique em **OK** para fechar o diálogo Testar. A nova fonte de dados deve agora estar listada no **Administrador de Fonte de Dados ODBC**.
8.  Clique em **OK** para sair do assistente.

## <span id="ImportData"></span></a>Importar dados para o Excel de um cluster HDInsight

As etapas a seguir descrevem a maneira de importar dados de uma tabela hive para uma pasta de trabalho do Excel usando a fonte de dados ODBC que você criou nas etapas acima.

1.  Abra uma pasta de trabalho nova ou existente no Excel.
2.  Na guia **Dados**, clique no bloco **Obter Dados Externos**, clique em **De Outras Fontes de Dados** e em **Do Assistente de Conexão de Dados** para iniciar o **Assistente de Conexão de Dados**.

    ![Abrir o assistente de conexão de dados][Abrir o assistente de conexão de dados]

3.  Selecione **DSN ODBC** como a fonte de dados e clique em **Avançar**.
4.  Em Fontes de dados ODBC, selecione o nome da fonte de dados criada na etapa anterior e clique em **Avançar**.
5.  Insira novamente a senha do cluster no assistente e clique em **Testar** para verificar a configuração
6.  Clique em **OK** para fechar o diálogo Testar.
7.  Clique em **OK**. Aguarde até que a caixa de diálogo **Selecionar Banco de Dados e Tabela** seja aberta. Isso pode levar alguns segundos.
8.  Selecione a tabela que deseja importar e clique em **Avançar**. A *hivesampletable* é uma tabela hive de exemplo que é fornecida com os clusters HDInsight. Você pode escolhê-la se ainda não tiver criado uma. Para obter mais informações sobre como executar consultas Hive e criar tabelas Hive, consulte [Usar o Hive com o HDInsight][Usar o Hive com o HDInsight].
9.  Clique em **Concluir**.
10. No diálogo **Importar Dados**, você pode alterar ou especificar a consulta. Para fazer isso, clique em **Propriedades**. Isso pode levar alguns segundos.
11. Clique na guia **Definição** e acrescente **LIMIT 200** à instrução de seleção Hive na caixa de texto **Texto do comando**. A modificação limitará o conjunto de registros retornado para 200.

    ![Propriedades da conexão][Propriedades da conexão]

12. Clique em **OK** para fechar o diálogo Propriedades da Conexão.
13. Clique em **OK** para fechar o diálogo **Importar Dados**.
14. Insira a senha novamente e clique em **OK**. Leva alguns segundos para que os dados sejam importados para o Excel.

## <span id="nextsteps"></span></a>Próximas etapas

Neste artigo você aprendeu a usar o driver ODBC do Microsoft Hive para recuperar dados do Serviço do HDInsight no Excel. Da mesma forma, você pode recuperar dados do Serviço do HDInsight no Banco de Dados SQL. Também é possível carregar dados em um Serviço do HDInsight. Para obter mais informações, consulte:

-   [Analisar dados de atraso de voo usando o HDInsight][Analisar dados de atraso de voo usando o HDInsight]
-   [Carregar dados no HDInsight][Carregar dados no HDInsight]
-   [Use o Sqoop com o HDInsight][Use o Sqoop com o HDInsight]

  [Conectar o Excel ao HDInsight com o Power Query]: ../hdinsight-connect-excel-power-query/
  [Introdução ao Azure HDInsight]: ../hdinsight-get-started/
  [Instalar o driver ODBC do Microsoft Hive]: #InstallHiveODBCDriver
  [Criar uma fonte de dados ODBC do Hive]: #CreateHiveODBCDataSource
  [Importar dados para o Excel de um cluster HDInsight]: #ImportData
  [Próximas etapas]: #nextsteps
  [Centro de download]: http://go.microsoft.com/fwlink/?LinkID=286698
  [Administrador de fonte de dados ODBC]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
  [Opções avançadas]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
  [Abrir o assistente de conexão de dados]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png
  [Usar o Hive com o HDInsight]: ../hdinsight-use-hive/
  [Propriedades da conexão]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
  [Analisar dados de atraso de voo usando o HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Carregar dados no HDInsight]: ../hdinsight-upload-data/
  [Use o Sqoop com o HDInsight]: ../hdinsight-use-sqoop/
