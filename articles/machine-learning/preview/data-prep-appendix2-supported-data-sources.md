---
title: "Fontes de dados com suporte disponíveis com a Preparação de Dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece uma lista completa das Fontes de Dados com suporte disponíveis para a Preparação de Dados do Azure Machine Learning"
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
ms.openlocfilehash: 1ef4c5c33d98cfeb566e8fe23bda9e0d3f041781
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="supported-data-sources-for-this-release"></a>Fontes de dados com suporte para esta versão 
O documento a seguir descreve a lista de Fontes de Dados com suporte no momento na Preparação de Dados do Azure Machine Learning.

As Fontes de Dados com suporte para essa versão são as seguintes.

## <a name="types"></a>Tipos 
### <a name="directory-versus-file"></a>Diretório versus arquivo
*Arquivos/Diretórios*: escolha um único arquivo e leia-o na Preparação de Dados. O tipo de arquivo é analisado para determinar os parâmetros padrão para a conexão de arquivo que está na próxima tela. Escolha um diretório ou conjunto de arquivos em um diretório (o seletor de arquivos aceita seleção múltipla). Trate os resultados nos arquivos lidos como um único fluxo de dados com os arquivos anexados entre si (com cabeçalhos eliminados, se necessário).

Os tipos de arquivo são os seguintes:
- Delimitados (.csv, .tsv, .txt etc.) 
- Largura fixa
- Texto sem formatação
- Arquivo JSON

### <a name="csv-file"></a>Arquivo CSV
Lê um arquivo CSV do armazenamento.

#### <a name="options"></a>Opções
- Separador
- Comentário
- Cabeçalhos
- Símbolo decimal
- Codificação de arquivo
- Linhas a serem ignoradas

### <a name="tsv-file"></a>Arquivo TSV
Lê um arquivo com valores TSV do armazenamento.

#### <a name="options"></a>Opções
- Comentário
- Cabeçalhos
- Codificação de arquivo
- Linhas a serem ignoradas

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Lê um arquivo do Excel, uma planilha por vez, especificando o nome ou o número da planilha.

#### <a name="options"></a>Opções
- Nome/número da planilha
- Cabeçalhos
- Linhas a serem ignoradas

### <a name="json-file"></a>Arquivo JSON
Lê um arquivo JSON do armazenamento. Observe que o arquivo é “nivelado” na leitura.

#### <a name="options"></a>Opções
Nenhum

### <a name="parquet"></a>Parquet
Lê um conjunto de dados Parquet, um único arquivo ou pasta.

O Parquet como um formato pode assumir vários formatos no armazenamento. Para conjuntos de dados menores, um único arquivo .parquet pode ser usado. Várias bibliotecas de Python dão suporte à leitura ou gravação em um único arquivo .parquet. No momento, o Azure Machine Learning Workbench se baseia na biblioteca do PyArrow Python para ler o Parquet durante o uso interativo local. Ele dá suporte a arquivos .parquet únicos (desde que sejam escritos de tal forma, não como uma parte de um conjunto de dados maior). Ele também dá suporte a conjuntos de dados Parquet. 

Um conjunto de dados Parquet é uma coleção de mais de um arquivo .parquet, em que cada um representa uma partição menor de um conjunto de dados maior. Conjuntos de dados geralmente estão contidos em uma pasta. Eles são o formato de saída padrão de Parquet para plataformas comuns como Spark e Hive.

>[!NOTE]
>Ao ler dados Parquet que estão em uma pasta com vários arquivos .parquet, é mais seguro selecionar o diretório para leitura e marcar a opção **Conjunto de dados Parquet**. Isso faz o PyArrow ler toda a pasta em vez dos arquivos individuais. Isso garante o suporte à leitura de formais mais complicadas de armazenar Parquet em disco (tais como o particionamento de pasta).

A execução de expansão depende dos recursos de leitura do Parquet do Spark e dá suporte a arquivos únicos e a pastas.

#### <a name="options"></a>Opções
*Conjunto de dados Parquet*: essa opção determina se o Azure Machine Learning Workbench usa o modo marcado ou desmarcado. O modo desmarcado expande determinado diretório e tenta ler cada arquivo nele individualmente. O modo marcado trata o diretório como o conjunto de dados completo e deixa o PyArrow descobrir a melhor maneira de interpretar os arquivos.


## <a name="locations"></a>Locais
### <a name="local"></a>Local
Disco rígido local ou local de armazenamento de rede mapeado.

### <a name="azure-blob-storage"></a>Armazenamento do Blob do Azure
Requer uma assinatura do Azure.

