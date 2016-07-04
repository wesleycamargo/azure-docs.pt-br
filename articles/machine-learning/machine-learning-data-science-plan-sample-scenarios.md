<properties
	pageTitle="Cenários para o Processo e Tecnologia de Análise Avançada no Aprendizado de Máquina do Azure | Microsoft Azure"
	description="Escolha os cenários apropriados para realizar a análise preditiva avançada com o Processo de Ciência de Dados de Equipe."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na" 
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016" 
	ms.author="bradsev" />


# Cenários para análises avançadas no Aprendizado de Máquina do Azure

Este artigo descreve as diversas fontes de dados de exemplo e os cenários de destino que podem ser manipulados pelo TDSP (Processo de Ciência de Dados de Equipe). O TDSP fornece uma abordagem sistemática para que as equipes colaborem na criação de aplicativos inteligentes. Os cenários apresentados aqui ilustram as opções disponíveis no fluxo de trabalho de processamento de dados que dependem das características de dados, de locais de origem e de repositórios de destino no Azure.

A **árvore de decisão** para selecionar os cenários de exemplo apropriados para seus dados e objetivo é apresentada na seção anterior.

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Cada uma das seções a seguir apresenta um cenário de exemplo. Para cada cenário, são listados uma possível ciência de dados ou fluxo de análise avançada e os recursos de suporte do Azure.

>[AZURE.NOTE] **Para todos os cenários a seguir, você precisa:**

*   [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md)
*   [Criar um espaço de trabalho de AM do Azure](machine-learning-create-workspace.md)


## <a name="smalllocal"></a>Cenário 1: conjunto de dados tabular pequeno a médio em arquivos locais

![Arquivos locais pequenos a médios][1]

#### Recursos adicionais do Azure: nenhum

1.  Faça logon no [Estúdio de Aprendizado de Máquina do Azure](https://studio.azureml.net/).

2.  Carregue um conjunto de dados.

3.  Crie um fluxo experimental do Aprendizado de Máquina do Azure começando com os conjuntos de dados carregados.

## <a name="smalllocalprocess"></a>Cenário 2: conjunto de dados pequeno a médio em arquivos locais, que exige processamento

![Arquivos locais pequenos a médios com processamento][2]

#### Recursos adicionais do Azure: Máquina Virtual do Azure (servidor IPython Notebook)

1.  Crie uma Máquina Virtual do Azure que executa o IPython Notebook.

2.  Carregue dados para um contêiner de armazenamento do Azure.

3.  Pré-processe e limpe dados no IPython Notebook, acessando dados no contêiner de armazenamento do Azure.

4.  Transforme os dados para o formato limpo e em tabela.

5.  Salve os dados transformados nos blobs do Azure.

6.  Faça logon no [Estúdio de Aprendizado de Máquina do Azure](https://studio.azureml.net/).

7.  Leia os dados dos blobs do Azure usando o módulo [Importar Dados][import-data].

8. Crie um fluxo experimental do Aprendizado de Máquina do Azure começando com os conjuntos de dados ingeridos.

## <a name="largelocal"></a>Cenário 3: conjunto de dados grande em arquivos locais, com blobs do Azure como destino

![Arquivos locais grandes][3]

#### Recursos adicionais do Azure: Máquina Virtual do Azure (servidor IPython Notebook)

1.  Crie uma Máquina Virtual do Azure que executa o IPython Notebook.

2.  Carregue dados para um contêiner de armazenamento do Azure.

3.  Pré-processe e limpe dados no IPython Notebook, acessando dados nos blobs do Azure.

4.  Transforme os dados para o formato limpo e em tabela, se necessário.

5.  Explore dados e crie recursos conforme for necessário.

6.  Extraia um exemplo de dados de pequeno a médio porte.

7.  Salve os dados do exemplo nos blobs do Azure.

8. Faça logon no [Estúdio de Aprendizado de Máquina do Azure](https://studio.azureml.net/).

9. Leia os dados dos blobs do Azure usando o módulo [Importar Dados][import-data].

10. Crie um fluxo experimental do AM do Azure, começando com os conjuntos de dados ingeridos.


## <a name="smalllocaltodb"></a>Cenário 4: conjunto de dados pequeno a médio em arquivos locais, com o SQL Server na Máquina Virtual do Azure como destino

![Arquivos locais pequenos a médios para o Banco de Dados SQL no Azure][4]

#### Recursos adicionais do Azure: Máquina Virtual do Azure (SQL Server/ servidor IPython Notebook)

1.  Crie uma Máquina Virtual do Azure que executa SQL Server + IPython Notebook.

2.  Carregue dados para um contêiner de armazenamento do Azure.

3.  Pré-processe e limpe dados no contêiner de armazenamento do Azure usando o IPython Notebook.

4.  Transforme os dados para o formato limpo e em tabela, se necessário.

5.  Salve dados em arquivos locais da VM (o IPython Notebook está em execução na VM, as unidades locais se referem às unidades da VM).

6.  Carregue dados para o banco de dados do SQL Server em execução em uma VM do Azure.

    a. Opção 1: usar o SQL Server Management Studio.

		i.  Login to SQL Server VM
        ii. Run SQL Server Management Studio.
        iii. Create database and target tables.
        iv. Use one of the bulk import methods to load the data from VM-local files.

    b. Opção 2: usar o IPython Notebook – não é aconselhável para conjuntos de dados médios e maiores

        i.  Use ODBC connection string to access SQL Server on VM.
        ii. Create database and target tables.
        iii. Use one of the bulk import methods to load the data from VM-local files.

7.  Explore dados e crie recursos conforme necessário. Observe que os recursos não precisam ser materializado nas tabelas do banco de dados. Anote somente a consulta necessária para criá-los.

8. Escolha um tamanho de amostra de dados, se necessário e/ou desejado.

9. Faça logon no [Estúdio de Aprendizado de Máquina do Azure](https://studio.azureml.net/).

10. Leia os dados diretamente do SQL Server usando o módulo [Importar Dados][import-data]. Cole a consulta necessária que extrai os campos, cria recursos e amostras de dados, se necessário, diretamente na consulta [Importar Dados][import-data].

11. Crie um fluxo experimental do AM do Azure, começando com os conjuntos de dados ingeridos.

## <a name="largelocaltodb"></a>Cenário 5: conjunto de dados grande em arquivos locais, com o SQL Server na VM do Azure

![Arquivos locais grandes para o Banco de Dados SQL no Azure][5]

#### Recursos adicionais do Azure: Máquina Virtual do Azure (SQL Server/ servidor IPython Notebook)

1.  Crie uma Máquina Virtual do Azure que executa SQL Server e o servidor IPython Notebook.

2.  Carregue dados para um contêiner de armazenamento do Azure.

3.  (Opcional) Pré-processe e limpe os dados.

    a. Pré-processe e limpe dados no IPython Notebook, acessando dados nos blobs do Azure.

    b. Transforme os dados para o formato limpo e em tabela, se necessário.

    c. Salve dados em arquivos locais da VM (o IPython Notebook está em execução na VM, as unidades locais se referem às unidades da VM).

4.  Carregue dados para o banco de dados do SQL Server em execução em uma VM do Azure.

    a. Faça logon na VM do SQL Server.

    b. Se os dados já não estiverem salvos, baixe arquivos de dados do contêiner de armazenamento do Azure para a pasta da VM local.

    c. Execute o SQL Server Management Studio.

    d. Crie o banco de dados e as tabelas de destino.

	e. Use um dos métodos de importação em massa para carregar os dados.

    f. Se junções de tabelas forem necessárias, crie índices para agilizá-las.

 > [AZURE.NOTE] Para um carregamento mais rápido de grandes quantidades de dados, é recomendável criar tabelas particionadas e importar em massa os dados em paralelo. Para saber mais, consulte [Importação de Dados em Paralelo para Tabelas Particionadas do SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Explore dados e crie recursos conforme necessário. Observe que os recursos não precisam ser materializado nas tabelas do banco de dados. Anote somente a consulta necessária para criá-los.

6.  Escolha um tamanho de amostra de dados, se necessário e/ou desejado.

7.  Faça logon no [Estúdio de Aprendizado de Máquina do Azure](https://studio.azureml.net/).

8. Leia os dados diretamente do SQL Server usando o módulo [Importar Dados][import-data]. Cole a consulta necessária que extrai os campos, cria recursos e amostras de dados, se necessário, diretamente na consulta [Importar Dados][import-data].

9. Fluxo experimental simples do AM do Azure, começando com os conjuntos de dados ingeridos

## <a name="largedbtodb"></a>Cenário 6: conjunto de dados grande em um banco de dados do SQL Server local, com o SQL Server em uma Máquina Virtual do Azure como destino

![Banco de Dados SQL local grande para o Banco de Dados SQL no Azure][6]

#### Recursos adicionais do Azure: Máquina Virtual do Azure (SQL Server/ servidor IPython Notebook)

1.  Crie uma Máquina Virtual do Azure que executa SQL Server e o servidor IPython Notebook.

2.  Use um dos métodos de exportação de dados para exportar os dados do SQL Server para arquivos de despejo.

    a. Observação: se você decidir mover todos os dados do banco de dados local, há um método alternativo (mais rápido) para mover o banco de dados completo para a instância do SQL Server no Azure. Ignore as etapas para exportar dados, criar o banco de dados e carregar/importar dados para o banco de dados de destino e execute o método alternativo.

3.  Carregue os arquivos de despejo no contêiner de armazenamento do Azure.

4.  Carregue os dados para um banco de dados do SQL Server em execução em uma Máquina Virtual do Azure.

    a. Faça logon na VM do SQL Server.

    b. Baixe os arquivos de dados de um contêiner de armazenamento do Azure para a pasta da VM local.

    c. Execute o SQL Server Management Studio.

    d. Crie o banco de dados e as tabelas de destino.

	e. Use um dos métodos de importação em massa para carregar os dados.

	f. Se junções de tabelas forem necessárias, crie índices para agilizá-las.

> [AZURE.NOTE] Para um carregamento mais rápido de grandes quantidades de dados, crie tabelas particionadas e importe em massa os dados em paralelo. Para saber mais, consulte [Importação de Dados em Paralelo para Tabelas Particionadas do SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Explore dados e crie recursos conforme necessário. Observe que os recursos não precisam ser materializado nas tabelas do banco de dados. Anote somente a consulta necessária para criá-los.

6.  Escolha um tamanho de amostra de dados, se necessário e/ou desejado.

7.  Faça logon no [Estúdio de Aprendizado de Máquina do Azure](https://studio.azureml.net/).

8. Leia os dados diretamente do SQL Server usando o módulo [Importar Dados][import-data]. Cole a consulta necessária que extrai os campos, cria recursos e amostras de dados, se necessário, diretamente na consulta [Importar Dados][import-data].

9. Fluxo experimental simples do AM do Azure, começando com os conjuntos de dados carregados.

### Método alternativo para copiar um banco de dados completo de um SQL Server local para o Banco de Dados SQL do Azure

![Desanexar o banco de dados local e anexar ao Banco de Dados SQL no Azure][7]

#### Recursos adicionais do Azure: Máquina Virtual do Azure (SQL Server/ servidor IPython Notebook)

Para replicar todo o banco de dados do SQL Server na sua VM do SQL Server, você deve copiar um banco de dados de um local/servidor para outro, supondo que o banco de dados possa ficar offline temporariamente. Você pode fazer isso no GUI do Pesquisador de Objetos do SQL Server Management Studio ou usando os comandos Transact-SQL equivalentes.

1. Desanexe o banco de dados no local de origem. Para saber mais, consulte [Desanexar um banco de dados](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx).
2. Na janela de prompt de comando do Windows Explorer ou Windows, copie os arquivos do banco de dados e os arquivos de log desanexados para o local de destino na VM do SQL Server no Azure.
3. Anexe os arquivos copiados à instância de destino do SQL Server. Para saber mais, consulte [Anexar um banco de dados](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx).

[Mover um Banco de Dados utilizando Desanexar e Anexar (Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>Cenário 7: Big Data em arquivos locais, com um banco de dados Hive em clusters Hadoop do Azure HDInsight como destino

![Big Data no Hive local de destino][9]

#### Recursos adicionais do Azure: cluster Hadoop do Azure HDInsight e Máquina Virtual do Azure (servidor IPython Notebook)

1.  Crie uma Máquina Virtual do Azure que executa o servidor IPython Notebook.

2.  Personalize um cluster Hadoop do Azure HDInsight.

3.  (Opcional) Pré-processe e limpe os dados.

    a. Pré-processe e limpe dados no IPython Notebook, acessando dados nos blobs do Azure.

    b. Transforme os dados para o formato limpo e em tabela, se necessário.

    c. Salve dados em arquivos locais da VM (o IPython Notebook está em execução na VM, as unidades locais se referem às unidades da VM).

4.  Carregue dados no contêiner padrão do cluster Hadoop selecionado na etapa 2.

5.  Carregue dados para o banco de dados Hive no cluster Hadoop do Azure HDInsight.

    a. Faça logon no nó principal do cluster Hadoop

    b. Abra a linha de comando do Hadoop.

    c. Insira o diretório raiz do Hive pelo comando `cd %hive_home%\bin` na linha de comando do Hadoop.

    d. Execute as consultas do Hive para criar o banco de dados e tabelas e carregue dados do armazenamento de blob para as tabelas do Hive.

 	> [AZURE.NOTE] Se o volume de dados for grande, os usuários podem criar a tabela do Hive com partições. Em seguida, os usuários podem usar um loop `for` na linha de comando do Hadoop no nó principal para carregar dados na tabela do Hive particionada, por partição.

6.  Explore dados e crie recursos conforme necessário na linha de comando do Hadoop. Observe que os recursos não precisam ser materializado nas tabelas do banco de dados. Anote somente a consulta necessária para criá-los.

	a. Faça logon no nó principal do cluster Hadoop

    b. Abra a linha de comando do Hadoop.

    c. Insira o diretório raiz do Hive pelo comando `cd %hive_home%\bin` na linha de comando do Hadoop.

	d. Execute as consultas do Hive na linha de comando do Hadoop no nó principal do cluster do Hadoop para explorar os dados e criar recursos conforme necessário.

7.  Se necessário e/ou desejado, faça amostras dos dados para que eles caibam no Estúdio de Aprendizado de Máquina do Azure.

8.  Faça logon no [Estúdio de Aprendizado de Máquina do Azure](https://studio.azureml.net/).

9. Leia os dados diretamente do `Hive Queries` usando o módulo [Importar Dados][import-data]. Cole a consulta necessária que extrai os campos, cria recursos e amostras de dados, se necessário, diretamente na consulta [Importar Dados][import-data].

10. Fluxo experimental simples do AM do Azure, começando com os conjuntos de dados carregados.

## <a name="decisiontree"></a>Árvore de decisão para a seleção do cenário
------------------------

O diagrama a seguir resume os cenários descritos acima e as opções do Processo e Tecnologia de Análise Avançada selecionadas que o levam a cada um dos cenários detalhados. Observe que o processamento de dados, exploração, engenharia de recursos e amostragem podem ocorrer em um ou mais métodos/ambientes – nos ambientes de origem, intermediário e/ou de destino – e podem continuar iterativamente conforme necessário. O diagrama só serve para ilustrar alguns dos fluxos de possíveis e não fornece uma enumeração completa.

![Cenários de passo a passo de exemplo do processo de Ciência de Dados][8]

### Exemplos de análise avançada em ação

Para obter um passo a passo do Aprendizado de Máquina do Azure que emprega o Processo e Tecnologia de Análise Avançada usando conjuntos de dados públicos, consulte:


* [Processo de Ciência de Dados de Equipe em ação: usando o SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
* [Processo de Ciência de Dados de Equipe em ação: usando clusters Hadoop do HDInsight](machine-learning-data-science-process-hive-walkthrough.md).


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0622_2016-->