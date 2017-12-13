---
title: "Fontes de dados com suporte disponíveis com a preparação de dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece uma lista completa das fontes de dados com suporte disponíveis para a preparação de dados do Azure Machine Learning."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 458338cd23c704c40c512dd96b22a4790f27d017
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>Fontes de dados com suporte para preparação de dados de do Azure Machine Learning 
Este artigo descreve as fontes de dados com suporte no momento para preparação de dados do Azure Machine Learning.

As fontes de dados com suporte para essa versão são as seguintes.

## <a name="types"></a>Tipos 

### <a name="sql-server"></a>SQL Server
Leia no SQL Server local ou no banco de dados SQL do Azure.

#### <a name="options"></a>Opções
- Endereço do servidor
- Servidor de confiança (mesmo quando o certificado no servidor não é válido. Use com cuidado)
- Tipo de autenticação (Windows, servidor)
- Nome de usuário
- Senha
- Banco de dados ao qual se conectar
- Consulta SQL

#### <a name="notes"></a>Observações
- As colunas Sql-variant não são compatíveis
- Coluna de hora é convertida em datetime anexando a horado banco de dados à data 1970/1/1
- Quando executado em um cluster Spark, todas as colunas relacionadas a dados (date, datetime, datetime2, datetimeoffset) avaliarão valores incorretos para datas anteriores a 1583
- Os valores em colunas decimais podem perder a precisão devido à conversão para decimal

### <a name="directory-vs-file"></a>Diretório vs. arquivo
Escolha um único arquivo e leia-o na preparação de dados. O tipo de arquivo é analisado para determinar os parâmetros padrão para a conexão de arquivo mostrada na próxima tela.

Escolha um diretório ou conjunto de arquivos em um diretório (o seletor de arquivos é de seleção múltipla). Com qualquer uma das abordagem, os arquivos são lidos como um fluxo de dados único e são acrescentados um ao outro, com cabeçalhos eliminados, se necessário.

Os tipos de arquivo com suporte são:
- Delimitado (csv, tsv, txt etc.)
- Largura fixa
- Texto sem formatação
- Arquivo JSON

### <a name="csv-file"></a>Arquivo CSV
Ler um arquivo de valores separados por vírgula do armazenamento.

#### <a name="options"></a>Opções
- Separador
- Comentário
- Cabeçalhos
- Símbolo decimal
- Codificação de arquivo
- Linhas a serem ignoradas

### <a name="tsv-file"></a>Arquivo TSV
Ler um arquivo de valores separados por tabulação do armazenamento.

#### <a name="options"></a>Opções
- Comentário
- Cabeçalhos
- Codificação de arquivo
- Linhas a serem ignoradas

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Ler um arquivo do Excel, uma planilha por vez, especificando o nome ou o número da planilha.

#### <a name="options"></a>Opções
- Nome ou número da planilha
- Cabeçalhos
- Linhas a serem ignoradas

### <a name="json-file"></a>Arquivo JSON
Lê um arquivo JSON do armazenamento. O arquivo é "nivelado" na leitura.

#### <a name="options"></a>Opções
- Nenhum

### <a name="parquet"></a>Parquet
Lê um conjunto de dados Parquet, seja um único arquivo ou uma pasta.

O Parquet como um formato pode assumir vários formatos no armazenamento. Para conjuntos de dados menores, às vezes um único arquivo .parquet é usado. Várias bibliotecas Python dão suporte à leitura ou à gravação em arquivos .parquet únicos. Por enquanto, a preparação de dados do Azure Machine Learning se baseia na biblioteca do PyArrow Python para ler o Parquet durante o uso de interativo local. Ele dá suporte a arquivos .parquet únicos (contanto que sejam escritos assim e não como uma parte de um conjunto de dados maior), bem como conjuntos de dados Parquet.

Um conjunto de dados Parquet é uma coleção de mais de um arquivo .parquet, em que cada um representa uma partição menor de um conjunto de dados maior. Os conjuntos de dados geralmente estão contidos em uma pasta e são o formato de saída padrão parquet para plataformas como Spark e Hive.

>[!NOTE]
>Ao ler os dados do Parquet que estão em uma pasta com vários arquivos .parquet, é mais seguro selecionar o diretório para leitura e a opção **Conjunto de dados Parquet**. Isso faz o PyArrow ler toda a pasta em vez dos arquivos individuais. Isso garante o suporte à leitura de modos mais complicados de armazenar Parquet em disco, como o particionamento de pasta.

A execução de escalar horizontalmente depende das funcionalidades de leitura do Parquet do Spark e dá suporte a arquivos únicos, bem como a pastas, da mesma maneira que o uso local interativo.

#### <a name="options"></a>Opções
- Conjunto de dados parquet. Essa opção determina se preparação de dados do Azure Machine Learning expande um determinado diretório e tenta ler cada arquivo individualmente (o modo não selecionado) ou trata o diretório como todo o conjunto de dados (o modo selecionado). Com o modo selecionado, PyArrow escolhe a melhor maneira de interpretar os arquivos.


## <a name="locations"></a>Locais
### <a name="local"></a>Local
Um disco rígido local ou local de armazenamento de rede mapeado.

### <a name="sql-server"></a>SQL Server
Servidor SQL local ou banco de dados SQL do Azure.

### <a name="azure-blob-storage"></a>Armazenamento do Blob do Azure
O armazenamento de Blobs do Azure requer uma assinatura do Azure.

