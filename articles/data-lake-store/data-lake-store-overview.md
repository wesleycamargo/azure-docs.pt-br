---
title: Visão geral do Azure Data Lake Storeage Gen1 | Microsoft Docs
description: Entenda o que é o Azure Data Lake Storage Gen1 (anteriormente conhecido como Azure Data Lake Store) e o valor que ele fornece com relação a outros armazenamentos de dados
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: b733a0bec20c7abae5df41acd74284bdf75bca2c
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124126"
---
# <a name="overview-of-azure-data-lake-storage-gen1"></a>Visão Geral do Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

O Azure Data Lake Storage Gen1 é um repositório de hiperescala em toda a empresa para cargas de trabalho analíticas de big data. O Azure Data Lake permite que você capture dados de qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análises operacionais e exploratórias.

> [!TIP]
> Use o [caminho de aprendizado do Data Lake Storage Gen1](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) para começar a explorar o serviço Data Lake Storage Gen1.
> 
> 

O Data Lake Storage Gen1 pode ser acessado a partir do Hadoop (disponível com o cluster HDInsight) usando as APIs REST compatíveis com WebHDFS. Ele foi especificamente desenvolvido para permitir a análise dos dados armazenados e está ajustado para trabalhar em cenários de análise de dados. Ele já inclui todos os recursos corporativos — segurança, capacidade de gerenciamento, escalabilidade, confiabilidade e disponibilidade — essenciais para casos de uso reais.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Alguns dos principais recursos do Data Lake Storage Gen1 incluem o seguinte.

### <a name="built-for-hadoop"></a>Desenvolvido para Hadoop
Data Lake Storage O Gen1 é um sistema de arquivos Apache Hadoop compatível com o Hadoop Distributed File System (HDFS) e funciona com o ecossistema Hadoop.  Seus aplicativos ou serviços existentes do HDInsight que usam a API WebHDFS podem ser facilmente integrados ao Data Lake Storage Gen1. O Data Lake Storage Gen1 também expõe uma interface REST compatível com WebHDFS para aplicativos

Os dados armazenados no Data Lake Storage Gen1 podem ser facilmente analisados usando estruturas analíticas do Hadoop, como MapReduce ou Hive. Os clusters do Microsoft Azure HDInsight podem ser provisionados e configurados para acessar diretamente os dados armazenados no Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Armazenamento ilimitado, arquivos em petabytes
O Data Lake Storage Gen1 fornece armazenamento ilimitado e é adequado para armazenar uma variedade de dados para análise. Ele não impõe qualquer limite de tamanho de conta, de tamanho de arquivo ou de quantidade de dados que podem ser armazenados em um Data Lake. Os arquivos individuais podem variar de quilobytes a petabytes, tornando-os uma ótima opção para armazenar qualquer tipo de dados. Os dados são armazenados permanentemente por meio de várias cópias, e não há limite de tempo de armazenamento dos dados no Data Lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Desempenho ajustado para a análise de big data
O Data Lake Storage Gen1 foi desenvolvido para executar sistemas analíticos de larga escala que exigem um throughput massivo para consultar e analisar grandes quantidades de dados. O Data Lake espalha partes de um arquivo por vários servidores de armazenamento individuais. Isso melhora a produtividade da leitura do arquivo em paralelo para a realização de análises de dados.

### <a name="enterprise-ready-highly-available-and-secure"></a>Pronto para a empresa: altamente disponível e seguro
O Data Lake Storage Gen1 fornece disponibilidade e confiabilidade padrão do setor. Seus ativos de dados são armazenados permanentemente por meio de cópias redundantes, a fim de se proteger contra quaisquer falhas inesperadas. As empresas podem usar o Data Lake Storage Gen1 em suas soluções como parte importante de sua plataforma de dados existente.

O Data Lake Storage Gen1 também fornece segurança de nível corporativo para os dados armazenados. Para obter mais informações, consulte [Protegendo dados no Armazenamento de dados do Lake Azure Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Todos os dados
O Data Lake Storage Gen1 pode armazenar qualquer dado em seu formato nativo, sem necessidade de transformações anteriores. O Data Lake Storage Gen1 não exige que um esquema seja definido antes de os dados serem carregados, deixando a estrutura analítica individual interpretar os dados e definir um esquema no momento da análise. A capacidade de armazenar arquivos de tamanhos e formatos arbitrários possibilita que o Data Lake Storage Gen1 manipule dados estruturados, semi-estruturados e não estruturados.

Os contêineres Data Lake Storage Gen1 para dados são essencialmente pastas e arquivos. Você opera nos dados armazenados usando SDKs, o Portal do Azure e o Azure PowerShell. Desde que você coloque os dados no repositório usando essas interfaces e os contêineres apropriados, é possível armazenar qualquer tipo de dados. O Data Lake Storage Gen1 não executa nenhum tratamento especial de dados com base no tipo de dados que armazena.

## <a name="DataLakeStoreSecurity"></a> Protegendo dados no Data Lake Storage Gen1
O Data Lake Storage Gen1 usa o Azure Active Directory para autenticação e listas de controle de acesso (ACLs) para gerenciar o acesso aos seus dados.

| Recurso | DESCRIÇÃO |
| --- | --- |
| Autenticação |O Data Lake Storage Gen1 integra-se ao Azure Active Directory (AAD) para gerenciamento de identidade e acesso para todos os dados armazenados no Data Lake Storage Gen1. Como resultado da integração, o Data Lake Storage Gen1 se beneficia de todos os recursos do AAD, incluindo autenticação multifator, acesso condicional, controle de acesso baseado em função, monitoramento do uso do aplicativo, monitoramento e alerta de segurança, etc. O Data Lake Storage Gen1 suporta o protocolo OAuth 2.0 para autenticação na interface REST. Consulte [autenticação Gen1 de armazenamento do Data Lake](data-lakes-store-authentication-using-azure-active-directory.md)|
| Controle de acesso |O Data Lake Storage Gen1 fornece controle de acesso, suportando permissões no estilo POSIX expostas pelo protocolo WebHDFS. As ACLs podem ser habilitadas na pasta raiz, nas subpastas e nos arquivos individuais. Para obter mais informações sobre como as ACLs funcionam no contexto do Data Lake Storage Gen1, consulte [Controle de acesso no Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Criptografia |O Data Lake Storage Gen1 também fornece criptografia para dados armazenados na conta. Você especifica as configurações de criptografia ao criar uma conta do Data Lake Storage Gen1. Você pode optar por ter seus dados criptografados ou optar por nenhuma criptografia. Para obter mais informações, consulte [Criptografia no Data Lake Storage Gen1](data-lake-store-encryption.md). Para obter instruções sobre como fornecer configuração relacionada à criptografia, consulte [Introdução ao Armazenamento de Dados do Azure Data Lake Gen1 usando o Portal do Azure ](data-lake-store-get-started-portal.md). |

Deseja saber mais sobre como proteger dados no Data Lake Storage Gen1? Siga os links abaixo.

* Para obter instruções sobre como proteger dados no Data Lake Storage Gen1, consulte [Protegendo dados no Armazenamento de dados do Azure Data Lake Gen1](data-lake-store-secure-data.md).
* Prefere vídeos? [Assista a este vídeo](https://mix.office.com/watch/1q2mgzh9nn5lx) sobre como proteger os dados armazenados no Data Lake armazenamento Gen1.

## <a name="applications-compatible-with-data-lake-storage-gen1"></a>Aplicativos compatíveis com o Data Lake armazenamento Gen1
Gen1 de armazenamento do data Lake é compatível com mais componentes de software livre no ecossistema do Hadoop. Ele também se integra perfeitamente com outros serviços do Azure. Isso torna o Data Lake Storage Gen1 uma opção perfeita para suas necessidades de armazenamento de dados. Siga os links abaixo para saber mais sobre como o Data Lake Storage Gen1 pode ser usado tanto com componentes de código aberto quanto com outros serviços do Azure.

* Consulte [Aplicativos e serviços compatíveis com o Armazenamento de dados do Azure Data Lake Gen1](data-lake-store-compatible-oss-other-applications.md) para obter uma lista de aplicativos de software livre interoperáveis com o Data Lake Storage Gen1.
* Consulte [Integração com outros serviços do Azure](data-lake-store-integrate-with-other-services.md) para entender como o Data Lake Storage Gen1 pode ser usado com outros serviços do Azure para permitir uma gama mais ampla de cenários.
* Veja [Cenários para usar o Data Lake Storage Gen1](data-lake-store-data-scenarios.md) para aprender a usar o Data Lake Storage Gen1 em cenários como a ingestão de dados, o processamento de dados, o download de dados e a visualização de dados.

## <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>O que é o sistema de arquivos Data Lake Storage Gen1 (adl://)?
O Data Lake Storage Gen1 pode ser acessado por meio do novo sistema de arquivos, o AzureDataLakeFilesystem (adl: //), em ambientes do Hadoop (disponíveis com o cluster HDInsight). Os aplicativos e os serviços que usam adl:// podem aproveitar a otimização adicional de desempenho que não está disponível no momento no WebHDFS. Como resultado, o Data Lake Storage Gen1 oferece a flexibilidade de aproveitar o melhor desempenho com a opção recomendada de usar adl:// ou manter o código existente, continuando a usar a API WebHDFS diretamente. O Azure HDInsight aproveita totalmente o AzureDataLakeFilesystem para fornecer o melhor desempenho no Data Lake Storage Gen1.

Você pode acessar seus dados no Data Lake Storage Gen1 usando `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Para obter mais informações sobre como acessar os dados no Data Lake Storage Gen1, consulte [Exibir propriedades dos dados armazenados](data-lake-store-get-started-portal.md#properties)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Data Lake armazenamento Gen1 usando o Portal do Azure](data-lake-store-get-started-portal.md)
* [Introdução ao Azure Data Lake armazenamento Gen1 usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)