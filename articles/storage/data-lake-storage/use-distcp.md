---
title: Copiar dados no Azure Data Lake Store Gen2 usando Distcp Versão Prévia | Microsoft Docs
description: Use a ferramenta Distcp para copiar dados do Data Lake Storage Gen2 Versão Prévia
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: seguler
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: how-to
ms.date: 06/27/2018
ms.author: seguler
ms.openlocfilehash: 2a958ceb0b3a1db9d06d045a8161fa6cd3ef5aba
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059919"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-storage-gen2-preview"></a>Usar Distcp para copiar dados entre Blobs do Armazenamento do Azure e Data Lake Storage Gen2 Versão Prévia

Se você tem um cluster HDInsight com acesso ao Azure Data Lake Storag Gen2 Versão Prévia, use as ferramentas do ecossistema do Hadoop, como o Distcp, para copiar dados **bidirecionalmente** em um armazenamento de cluster HDInsight (WASB) para uma conta do Data Lake Storage Gen2. Este artigo fornece instruções de como usar a ferramenta Distcp.

## <a name="prerequisites"></a>pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Armazenamento do Azure com o recurso Azure Data Lake Storage (Versão Prévia) habilitado**. Para obter instruções sobre como criar uma, consulte [TODO](quickstart-create-account.md)
* **Cluster HDInsight do Azure** com acesso a uma conta do Data Lake Storage. Consulte [Usar o Azure Data Lake Storage Gen2 com clusters de HDInsight do Azure](use-hdi-cluster.md). Certifique-se de habilitar a área de trabalho remota para o cluster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usar Distcp de um cluster HDInsight do Linux

Um cluster do HDInsight é fornecido com o utilitário Distcp, que pode ser usado para copiar dados de fontes diferentes em um cluster do HDInsight. Se você tiver configurado o cluster HDInsight para usar o Azure Blob Storage e o Azure Data Lake Storage juntos, o utilitário pode ser usado prontamente para copiar dados entre eles também. Nesta seção, vamos examinar como usar o utilitário Distcp.

1. Da sua área de trabalho, use o SSH para se conectar ao cluster. Confira [Conectar-se a um cluster HDInsight baseado em Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Execute os comandos do prompt de SSH.

2. Verifique se você pode acessar os WASB (Blobs de Armazenamento do Azure). Execute o comando a seguir:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    A saída deve fornecer uma lista de conteúdo no blob de armazenamento.

3. Da mesma forma, verifique se você pode acessar a conta de do Data Lake Storage do cluster. Execute o comando a seguir:

        hdfs dfs -ls abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/

    A saída deve fornecer uma lista de arquivos/pastas na conta do Data Lake Storage.

4. Use Distcp para copiar dados do WASB para uma conta do Data Lake Storage.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/myfolder

    O comando copia os conteúdos da pasta **/example/data/gutenberg/** do Armazenamento de Blobs em **/myfolder** na conta do Data Lake Storage.

5. De modo semelhante, use Distcp para copiar dados da conta do Data Lake Storage para o Armazenamento de Blobs (WASB).

        hadoop distcp abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    O comando copia o conteúdo de **/myfolder** da conta do Data Lake Store na pasta **/example/data/gutenberg/** no WASB.

## <a name="performance-considerations-while-using-distcp"></a>Considerações de desempenho ao usar o DistCp

Como a granularidade mais baixa do DistCp é um único arquivo, definir o número máximo de cópias simultâneas é o parâmetro mais importante para otimizá-lo em relação ao Data Lake Storage. O número de cópias simultâneas é controlado pela configuração do parâmetro de número de mapeadores (“m”) na linha de comando. Esse parâmetro especifica o número máximo de mapeadores que são usados para copiar dados. O valor padrão é 20.

**Exemplo**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Como determino o número de mapeadores que serão usados?

Aqui estão algumas diretrizes que podem ser usadas.

* **Etapa 1: Determinar a memória total de YARN** ‑ A primeira etapa é determinar a memória YARN disponível para o cluster em que você executa o trabalho DistCp. Essas informações estão disponíveis no portal do Ambari associado ao cluster. Navegue até YARN e exiba a guia Configurações para ver a memória YARN. Para obter a memória YARN total, multiplique a memória YARN por nó pelo número de nós que você tem no cluster.

* **Etapa 2: Calcular o número de mapeadores** ‑ O valor de **m** é igual ao quociente de memória total de YARN dividida pelo tamanho do contêiner YARN. As informações de tamanho do contêiner YARN também estão disponíveis no portal do Ambari. Navegue até YARN e exiba a guia Configurações. O tamanho do contêiner YARN é exibido nessa janela. A equação para chegar até o número de mapeadores (**m**) é

        m = (number of nodes * YARN memory for each node) / YARN container size

**Exemplo**

Suponhamos que você tenha 4 nós D14v2s no cluster e está tentando transferir 10 TB de dados de 10 pastas diferentes. Cada uma das pastas contêm volumes diversos de dados e os tamanhos dos arquivos em cada pasta também são diferentes.

* Total de memória YARN – No portal do Ambari você determina que a memória YARN é 96 GB para um nó D14. Portanto, o total de memória YARN para um cluster de quatro nós é: 

        YARN memory = 4 * 96GB = 384GB

* Número de mapeadores ‑ Do portal do Ambari a você determina que o tamanho do contêiner YARN é 3072 para um nó de cluster D14. Portanto, o número de mapeadores é:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Se outros aplicativos estiverem usando memória, então você poderá usar somente uma parte da memória YARN do cluster para o DistCp.

### <a name="copying-large-datasets"></a>Copiando grandes conjuntos de dados

Quando o tamanho do conjunto de dados a ser movido for grande (por exemplo, maior que 1 TB) ou se você tiver muitas pastas diferentes, considere usar vários trabalhos DistCp. Provavelmente, não haverá nenhum ganho de desempenho, porém isso espalhará os trabalhos para que, se algum deles falhar, você só precise reiniciar esse trabalho específico em vez de todo o trabalho.

### <a name="limitations"></a>Limitações

* O DistCp tenta criar mapeadores de tamanho semelhantes para otimizar o desempenho. Aumentar o número de mapeadores nem sempre melhora o desempenho.

* O DistCp é limitado a apenas um mapeador por arquivo. Portanto, você não deve ter mais mapeadores do que arquivos. Como DistCp só pode atribuir um mapeador para um arquivo, isso limita a quantidade de simultaneidade que pode ser usada para copiar grandes arquivos.

* Se você tiver um pequeno número de grandes arquivos, divida-os em partes de arquivo de 256 MB para proporcionar um maior potencial de simultaneidade. 
