<properties
   pageTitle="Copiar dados de Blobs de Armazenamento do Azure para Repositório Data Lake| Microsoft Azure"
   description="Use a ferramenta AdlCopy para copiar dados de Blobs do Armazenamento do Azure para o Repositório Data Lake"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/05/2016"
   ms.author="nitinme"/>

# Copiar dados de Blobs do Armazenamento do Azure para o Repositório Data Lake

O Repositório Azure Data Lake fornece uma ferramenta de linha de comando, [AdlCopy](http://aka.ms/downloadadlcopy), para copiar dados **de Blobs de Armazenamento do Azure para o Repositório Data Lake**. Você não pode usar o AdlCopy para copiar dados do Repositório Data Lake para os blobs de Armazenamento do Azure.

Você pode usar a ferramenta AdlCopy de duas maneiras:

* **Autonomamente**, em que a ferramenta usa recursos do Repositório Data Lake para executar a tarefa.
* **Usando uma conta da Análise Data Lake**, em que as unidades atribuídas à sua conta da Análise Data Lake são usadas para executar a operação de cópia. Você pode usar essa opção quando quiser executar as tarefas de cópia de forma previsível.

##Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite sua assinatura do Azure** para a visualização pública do Repositório Data Lake. Veja [instruções](data-lake-store-get-started-portal.md#signup).
- Contêiner de **Blobs de Armazenamento do Azure** com alguns dados.
- **Conta do Azure Data Lake Analytics (opcional)**: confira [Introdução ao Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obter instruções sobre como criar uma conta do Repositório Data Lake.
- **Ferramenta AdlCopy**. Instale a ferramenta de AdlCopy de [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## Sintaxe da ferramenta AdlCopy

Use a sintaxe a seguir para trabalhar com a ferramenta AdlCopy

	AdlCopy /Source <Blob source> /Dest <ADLS destination> /SourceKey <Key for Blob account> /Account <ADLA account> /Unit <Number of Analytics units>

Os parâmetros na sintaxe estão descritos abaixo:

| Opção | Descrição |
|-----------|------------|
| Fonte | Especifica o local da fonte de dados no blob de armazenamento do Azure. A origem pode ser um contêiner de blob ou um blob |
| Dest | Especifica o destino do Repositório Data Lake para onde copiar. |
| SourceKey | Especifica a chave de acesso de armazenamento para a fonte de blob de armazenamento do Azure. |
| Conta | **Opcional**. Use essa opção se você deseja usar a conta do Azure Data Lake Analytics para executar o trabalho de cópia. Se você usar a opção /Account na sintaxe, mas não especificar uma conta do Data Lake Analytics, o AdlCopy usará uma conta padrão para executar o trabalho. Além disso, se você usar essa opção, deverá adicionar a origem (Blob de Armazenamento do Azure) e o destino (Repositório Azure Data Lake) como fontes de dados para sua conta do Data Lake Analytics. |
| Unidades | Especifica o número de unidades do Data Lake Analytics que será usado para o trabalho de cópia. Essa opção é obrigatória se você usar a opção **/Account** para especificar a conta do Data Lake Analytics.                                                                                                                                                                                                                                                                                                                                               



## Use a ferramenta AdlCopy autonomamente

1. Abra um prompt de comando e navegue até o diretório onde AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Execute o seguinte comando para copiar um blob específico do contêiner de origem para um Repositório Data Lake:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	Por exemplo:

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

	Você precisará inserir as credenciais da assinatura do Azure vinculada à conta do Repositório Data Lake. Você verá uma saída semelhante ao seguinte:

		Initializing Copy.
		Copy Started.
		...............
		0.00% data copied.
		. . .
		. . .
		100% data copied.
		Finishing copy.
		....
		Copy Completed.

1. Você também pode copiar todos os blobs de um contêiner para a conta do Repositório Data Lake usando o seguinte comando:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>		

	Por exemplo:

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==



## Usar AdlCopy com a conta do Data Lake Analytics

Você também pode usar sua conta da Análise Data Lake a fim de executar o trabalho AdlCopy para copiar dados de blobs do armazenamento do Azure para o Repositório Data Lake. Você normalmente usará essa opção quando os dados a serem movidos estiverem no intervalo de gigabytes e terabytes e desejar uma taxa de transferência com desempenho melhor e previsível.

Para usar a conta da Análise Data Lake com AdlCopy, tanto a origem (Blob de Armazenamento do Azure) quanto o destino (Repositório Azure Data Lake) devem ser adicionados como fontes de dados para sua conta da Análise Data Lake. Para obter instruções sobre como adicionar fontes de dados adicionais para sua conta da Análise Data Lake, confira [Gerenciar fontes de dados de conta da Análise Data Lake](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

Execute o comando a seguir:

	AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Por exemplo:

	AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeaccount /Units 2

## Cobrança

* Se você usar a ferramenta AdlCopy autonomamente, será cobrado pelos custos de egresso para movimentação de dados se a conta de armazenamento do Azure de origem não estiver na mesma região que o Repositório Data Lake.

* Se você usar a ferramenta AdlCopy com sua conta da Análise Data Lake, as [taxas de cobrança da Análise Data Lake](https://azure.microsoft.com/pricing/details/data-lake-analytics/) padrão serão aplicadas.

## Considerações para o uso do AdlCopy

* O AdlCopy não dá suporte a cópia de dados de fontes que têm mais de 1000 arquivos e pastas, coletivamente. Uma abordagem alternativa seria distribuir os arquivos/pastas em subpastas diferentes e usar o caminho para as subpastas como a origem em vez disso.

## Próximas etapas

- [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
- [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0413_2016-->