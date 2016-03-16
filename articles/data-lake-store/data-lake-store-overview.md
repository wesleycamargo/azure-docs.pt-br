<properties 
   pageTitle="Visão geral do Repositório Azure Data Lake | Azure" 
   description="Entenda o que é o Repositório Azure Data Lake e o valor que ele fornece com relação a outros armazenamentos de dados" 
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
   ms.date="01/22/2016"
   ms.author="nitinme"/>

# Visão geral do Repositório Azure Data Lake

O Repositório Azure Data Lake é um repositório em hiper-escala corporativo para cargas de trabalho de análise de big data. O Azure Data Lake permite que você capture dados de qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análises operacionais e exploratórias.

> [AZURE.TIP] Use o [Roteiro de aprendizagem do Repositório do Data Lake](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) para começar a explorar o serviço Repositório do Azure Data Lake.

O Repositório Azure Data Lake pode ser acessado no Hadoop (disponível com o cluster HDInsight) usando as APIs REST compatíveis com WebHDFS. Ele foi especificamente desenvolvido para permitir a análise dos dados armazenados e está ajustado para trabalhar em cenários de análise de dados. Ele já inclui todos os recursos corporativos — segurança, capacidade de gerenciamento, escalabilidade, confiabilidade e disponibilidade — essenciais para casos de uso reais.


![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Veja alguns dos principais recursos do Azure Data Lake a seguir.

### Desenvolvido para Hadoop

O Repositório Azure Data Lake é um sistema de arquivos Apache Hadoop compatível com HDFS (Sistema de Arquivos Distribuídos Hadoop) que funciona no ecossistema Hadoop. Seus aplicativos ou serviços atuais do HDInsight que usam a API WebHDFS podem ser facilmente integrados ao Repositório Data Lake. O Repositório Data Lake também expõe uma interface REST compatível com WebHDFS para os aplicativos

Os dados armazenados no Repositório Data Lake podem ser facilmente analisados usando estruturas de análise Hadoop, como o MapReduce ou o Hive. Os clusters do Microsoft Azure HDInsight podem ser provisionados e configurados para acessar diretamente os dados armazenados no Repositório Data Lake.

### Armazenamento ilimitado, arquivos em petabytes

O Repositório Azure Data Lake fornece armazenamento ilimitado e é adequado para armazenar vários dados para análise. Ele não impõe qualquer limite de tamanho de conta, de tamanho de arquivo ou de quantidade de dados que podem ser armazenados em um Data Lake. Os arquivos individuais podem variar de quilobytes a petabytes, tornando-os uma ótima opção para armazenar qualquer tipo de dados. Os dados são armazenados permanentemente por meio de várias cópias, e não há limite de tempo de armazenamento dos dados no Data Lake.

### Desempenho ajustado para a análise de big data

O Repositório Azure Data Lake foi criado para executar grandes sistemas de análise que exijam uma alta taxa de transferência de consulta e de análise de grandes quantidades de dados. O Data Lake espalha partes de um arquivo por vários servidores de armazenamento individuais. Isso melhora a produtividade da leitura do arquivo em paralelo para a realização de análises de dados.


### Pronto para a empresa: altamente disponível e seguro

O Repositório Azure Data Lake fornece confiabilidade e disponibilidade padrão do setor. Seus ativos de dados são armazenados permanentemente por meio de cópias redundantes, a fim de se proteger contra quaisquer falhas inesperadas. As empresas podem usar o Azure Data Lake em suas soluções como uma parte importante de suas plataformas de dados atuais.

O Repositório Data Lake também fornece segurança de classe empresarial para os dados armazenados. Para saber mais, consulte [Protegendo dados no Repositório Azure Data Lake](#DataLakeStoreSecurity).


### Todos os dados

O Repositório Azure Data Lake pode armazenar quaisquer dados em formato nativo, como estão, sem exigir transformações prévias. O Repositório Data Lake não exige a definição de um esquema antes do carregamento dos dados, deixando por conta da estrutura de análise individual interpretar os dados e definir um esquema no momento da análise. Ser capaz de armazenar arquivos de formatos e tamanhos arbitrários possibilita ao Repositório Data Lake lidar com dados estruturados, semiestruturados e não estruturados.

Os contêineres para dados do Repositório Azure Data Lake são basicamente pastas e arquivos. Você opera nos dados armazenados usando SDKs, o Portal do Azure e o Azure PowerShell. Desde que você coloque os dados no repositório usando essas interfaces e os contêineres apropriados, é possível armazenar qualquer tipo de dados. O Repositório Data Lake não realiza qualquer manipulação especial de dados com base no tipo de dados que armazena.


## <a name="DataLakeStoreSecurity"></a>Protegendo os dados no Repositório Azure Data Lake

O Repositório Azure Data Lake usa o Active Directory do Azure para autenticação e listas de controle de acesso (ACLs) para gerenciar o acesso aos dados.

| Recurso | Descrição |
|-----------------------------------------|------------------------------------------|
| Autenticação | O Repositório Azure Data Lake integra-se ao Active Directory do Azure (AAD) para gerenciamento de identidades e de acesso a todos os dados armazenados no Repositório Azure Data Lake. Como resultado dessa integração, o Azure Data Lake se beneficia de todos os recursos do AAD, incluindo a autenticação multifator, acesso condicional, controle de acesso baseado em função, monitoramento de uso do aplicativo, monitoramento de segurança e alertas etc. O Repositório Azure Data Lake oferece suporte ao protocolo OAuth 2.0 para autenticação na interface REST. |
| Controle de acesso | O Repositório Azure Data Lake fornece controle de acesso por meio do suporte a permissões no estilo POSIX expostas pelo protocolo WebHDFS. Na versão atual, as permissões podem ser especificadas no nível do Data Lake e serão aplicadas a todos os arquivos e pastas no Data Lake. Em atualizações futuras, habilitaremos o controle de acesso refinado ao permitirmos a especificação de permissões em arquivos e pastas individuais.|

Para obter instruções sobre como proteger os dados no Repositório Data Lake, consulte [Protegendo dados no Repositório Azure Data Lake](data-lake-store-secure-data.md).

## Aplicativos compatíveis com o Repositório Azure Data Lake

Consulte [Aplicativos e serviços compatíveis com o Azure Data Lake](data-lake-store-compatible-oss-other-applications.md) para obter uma lista de aplicativos de software livre interoperáveis com o Repositório Azure Data Lake. Consulte [Integração com outros serviços do Azure](data-lake-store-integrate-with-other-services.md) para entender como o Repositório Data Lake pode ser usado com outros serviços do Azure para permitir uma variedade maior de cenários.

## O que é o sistema de arquivos do Repositório Azure Data Lake (adl://)?)

O Repositório Data Lake pode ser acessado por meio do novo sistema de arquivos, o AzureDataLakeFilesystem (adl://), em ambientes Hadoop (disponíveis no cluster HDInsight). Os aplicativos e os serviços que usam adl:// podem aproveitar a otimização adicional de desempenho que não está disponível no momento no WebHDFS. Como resultado, o Repositório Data Lake oferece a flexibilidade de avaliar o melhor desempenho com a recomendação de uso da opção adl:// ou de manter o código existente caso você continue a usar a API do WebHDFS de forma direta. O Azure HDInsight aproveita o AzureDataLakeFilesystem por completo para fornecer o melhor desempenho no Repositório Data Lake.

Você pode acessar seus dados no Repositório Data Lake usando `adl://<data_lake_store_name>.azuredatalakestore.net`. Para saber mais sobre como acessar os dados no Repositório Data Lake, consulte [Exibir as propriedades dos dados armazenados](data-lake-store-get-started-portal.md#properties)

## Como posso começar a usar o Repositório Azure Data Lake?

Veja [Introdução ao Repositório do Data Lake usando o Portal do Azure](data-lake-store-get-started-portal.md), para obter informações sobre como provisionar um Repositório do Data Lake usando o Portal do Azure. Depois de provisionar o Azure Data Lake, você aprenderá a usar ofertas de big data, como a Análise do Azure Data Lake ou o Azure HDInsight com o Repositório Data Lake. Você também pode criar um aplicativo .NET a fim de criar uma conta do Repositório Azure Data Lake e executar operações como carregar dados, baixar dados etc.

- [Introdução à Análise Data Lake do Azure](data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Introdução ao Repositório Azure Data Lake usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
  

<!---HONumber=AcomDC_0128_2016-->