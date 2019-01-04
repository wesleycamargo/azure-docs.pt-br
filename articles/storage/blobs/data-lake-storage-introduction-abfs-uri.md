---
title: Use a visualização de Gen2 do Azure Data Lake armazenamento URI
description: Use a visualização de Gen2 do Azure Data Lake armazenamento URI
services: storage
author: jamesbak
ms.topic: conceptual
ms.author: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: e596123cb218a542166d80b53916a73034f71760
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976109"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Usar o Azure Data Lake armazenamento Gen2 URI

O [Hadoop Filesystem](http://www.aosabook.org/en/hdfs.html) driver é compatível com o Azure Data Lake armazenamento Gen2 Preview é conhecido por seu identificador de esquema `abfs` (sistema de arquivos de Blob do Azure). Consistente com outros drivers de Hadoop Filesystem, o driver ABFS utiliza um formato URI para arquivos de endereço e diretórios dentro de uma conta com capacidade Gen2 de armazenamento do Data Lake.

## <a name="uri-syntax"></a>Sintaxe URI

A sintaxe URI para o Data Lake armazenamento Gen2 depende se ou não sua conta de armazenamento é configurada para ter dados Lake armazenamento Gen2 como o sistema de arquivos padrão.

Se a conta de capaz de Data Lake armazenamento Gen2 que deseja endereço **não** é definida como o sistema de arquivos padrão durante a criação da conta, é a abreviação de sintaxe do URI:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identificador do esquema**: o protocolo `abfs` é usado como o identificador do esquema. Você tem a opção para conectar-se com ou sem uma conexão de protocolo SSL segura de soquete. Use `abfss` para se conectar com uma conexão de camada de soquete seguro.

2. **Sistema de arquivos**: a localização pai que contém os arquivos e pastas. Isso é o mesmo que contêineres no serviço de Blobs de armazenamento do Azure.

3. **Nome da conta**: o nome dado à sua conta de armazenamento durante a criação.

4. **Caminhos**: uma representação delimitada por uma barra invertida (`/`) da estrutura de diretório.

5. **Nome do arquivo**: o nome do arquivo individual. Esse parâmetro é opcional se você está abordando um diretório.

Porém, se a conta que deseja endereço não for definida como o sistema de arquivos padrão durante a criação da conta, é a abreviação de sintaxe do URI:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Caminho**: uma representação delimitada por uma barra invertida (`/`) da estrutura de diretório.

2. **Nome do arquivo**: o nome do arquivo individual.


## <a name="next-steps"></a>Próximas etapas

- [Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](data-lake-storage-use-hdi-cluster.md)
