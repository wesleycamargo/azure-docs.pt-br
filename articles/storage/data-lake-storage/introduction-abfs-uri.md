---
title: Use a visualização de Gen2 do Azure Data Lake armazenamento URI
description: Use a visualização de Gen2 do Azure Data Lake armazenamento URI
services: storage
keywords: ''
author: jamesbak
ms.topic: article
ms.author: jamesbak
manager: jahogg
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 75edf6dc7382a8a2ece7c25edd09aeacfe1c5189
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060052"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Usar o Azure Data Lake armazenamento Gen2 URI

O [Hadoop Filesystem](http://www.aosabook.org/en/hdfs.html) driver é compatível com o Azure Data Lake armazenamento Gen2 Preview é conhecido por seu identificador de esquema `abfs` (sistema de arquivos de Blob do Azure). Consistente com outros drivers de Hadoop Filesystem, o driver ABFS utiliza um formato URI para arquivos de endereço e diretórios dentro de uma conta com capacidade Gen2 de armazenamento do Data Lake.

## <a name="uri-syntax"></a>Sintaxe URI

A sintaxe URI para o Data Lake armazenamento Gen2 depende se ou não sua conta de armazenamento é configurada para ter dados Lake armazenamento Gen2 como o sistema de arquivos padrão.

Se a conta de capaz de Data Lake armazenamento Gen2 que deseja endereço é definida como o sistema de arquivos padrão durante a criação da conta, é a abreviação de sintaxe do URI:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Caminho**: uma barra invertida delimitada (`/`) representação da estrutura de diretório.

2. **Nome do arquivo**: O nome do arquivo individual.

Se a conta capaz de Data Lake armazenamento Gen2 desejar endereço *não* o sistema de arquivos padrão, a sintaxe URI é:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.widows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identificador do esquema**: O `abfs` protocolo é usado como o identificador do esquema. Você tem a opção para conectar-se com ou sem uma conexão de protocolo SSL segura de soquete. Use `abfss` para se conectar com uma conexão de camada de soquete seguro.

2. **Sistema de arquivos**: O local do pai que contém os arquivos e pastas. Isso é o mesmo que contêineres no serviço de Blobs de armazenamento do Azure.

3. **Nome da conta**: O nome dado à sua conta de armazenamento durante a criação.

4. **Caminhos**: uma barra invertida delimitada (`/`) representação da estrutura de diretório.

5. **Nome do arquivo**: O nome do arquivo individual. Esse parâmetro é opcional se você está abordando um diretório.

## <a name="next-steps"></a>Próximas etapas

- [Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](use-hdi-cluster.md)