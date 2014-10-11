<properties linkid="storage-introduction" urlDisplayName="Introduction to Azure Storage" pageTitle="Introduction to Storage | Microsoft Azure" metaKeywords="Get started  Azure storage introduction  Azure storage overview  Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage  Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage  Azure file storage  Azure file  Azure file share  Azure " description="An overview of Microsoft Azure Storage." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="Introduction to Microsoft Azure Storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# Introdução ao Armazenamento do Microsoft Azure

Este artigo fornece uma introdução ao Armazenamento do Microsoft Azure para desenvolvedores, profissionais de TI e tomadores de decisões de negócios. Lendo-o, você aprenderá sobre:

-   O que é o Armazenamento do Azure e como você pode tirar proveito dele em aplicativos de nuvem, móveis, de servidor e de área de trabalho
-   Quais os tipos de dados que você pode armazenar com os serviços de Armazenamento do Azure: armazenamento de Blob, Tabela e Fila
-   Como o acesso aos seus dados no Armazenamento do Azure é gerenciado
-   Como os dados do Armazenamento do Azure são protegidos por meio de redundância e replicação
-   Onde ir em seguida para criar seu primeiro aplicativo de Armazenamento do Azure

## O que é o armazenamento do Azure?

A computação na nuvem habilita novos cenários para aplicativos, que exigem armazenamento escalonável, durável e altamente disponível para seus dados - que é exatamente o motivo pelo qual a Microsoft desenvolveu o Armazenamento do Azure. Além de permitir que os desenvolvedores criem aplicativos de grande escala para dar suporte a novos cenários, o Armazenamento do Azure também fornece a base do armazenamento das Máquinas Virtuais do Azure, uma prova adicional de sua robustez.

O Armazenamento do Azure é extremamente escalonável para que você possa armazenar e processar centenas de terabytes de dados para oferecer suporte a cenários de big data, necessários para análise científica e financeira e aplicativos de mídia. Ou você pode armazenar pequenas quantidades de dados necessários para um site de pequena empresa. Quaisquer que sejam suas necessidades, você paga apenas pelos dados que está armazenando. O Armazenamento do Azure atualmente armazena dezenas de trilhões de objetos exclusivos de clientes e manipula milhões de solicitações por segundo em média.

O Armazenamento do Azure é elástico, portanto, você pode criar aplicativos para um grande público global e dimensionar esses aplicativos conforme necessário - tanto a quantidade de dados armazenados e o número de solicitações realizadas. Você paga apenas pelo que usa e apenas quando usa.

O Armazenamento do Azure usa um sistema de particionamento automático que faz o balanço automático da carga de seus dados com base no tráfego. Isso significa que conforme crescem as demandas por seu aplicativo, o Armazenamento do Azure aloca automaticamente os recursos apropriados para atendê-las.

O Armazenamento de fila fornece uma solução de mensagens confiáveis para comunicação assíncrona entre os componentes do aplicativo, quer estejam em execução na nuvem, na área de trabalho, em um servidor local ou em um dispositivo móvel. Você pode usar o Armazenamento do Azure em cenários móveis, onde o aplicativo armazena um subconjunto dos dados no dispositivo e os sincroniza com um conjunto completo de dados armazenados na nuvem.

O Armazenamento do Azure oferece suporte a clientes que usam um conjunto variado de sistemas operacionais (inclusive o Windows e o Linux) e uma variedade de linguagens de programação (incluindo .NET, Java e C++) para desenvolvimento conveniente. O Armazenamento do Azure também expõe recursos de dados por meio de APIs REST simples, que estão disponíveis para qualquer cliente capaz de enviar e receber dados via HTTP/HTTPS.

## Apresentando os serviços de Armazenamento do Azure

Os serviços de Armazenamento do Azure são Armazenamento de Blob, Armazenamento de Tabela e Armazenamento de Fila:

-   **O Armazenamento de Blob** armazena dados de arquivos. Um blob pode ser qualquer tipo de texto ou dados binários, como um documento, um arquivo de mídia ou um instalador do aplicativo.
-   **O Armazenamento de Tabela** armazena conjuntos de dados estruturados. O Armazenamento de Tabela é um repositório de dados de atributo de chave NoSQL, que permite desenvolvimento rápido e acesso rápido a grandes quantidades de dados.
-   **O Armazenamento de Fila** fornece sistema de mensagens confiável para processamento de fluxo de trabalho e para comunicação entre componentes dos serviços de nuvem.
-   **O Armazenamento de Arquivo** oferece armazenamento compartilhado para aplicativos herdados usando o protocolo padrão SMB 2.1. As máquinas virtuais do Azure e os serviços de nuvem podem compartilhar dados de arquivos entre componentes de aplicativos por meio de compartilhamentos montados, e aplicativos locais podem acessar dados de arquivos em um compartilhamento por meio da API REST do serviço de Arquivo.

Os armazenamentos de Blob, Tabela e Fila são incluídos em cada conta de armazenamento, enquanto o armazenamento de Arquivo está disponível mediante solicitação por meio da [Página de Exibição do Azure][].

A conta de armazenamento é um namespace exclusivo que fornece acesso ao Armazenamento do Azure. Cada conta de armazenamento pode conter até 500 TB de dados de blob, tabela, fila e arquivo combinados. Consulte [Escalabilidade e Metas de Desempenho do Armazenamento do Azure][] para obter detalhes sobre a capacidade da conta de armazenamento do Azure.

A imagem abaixo mostra as relações entre os recursos de armazenamento do Azure:

![Recursos de Armazenamento do Azure][]

Antes de criar uma conta de armazenamento, você deve ter uma assinatura do Azure, que é um plano que dá acesso a uma variedade de serviços do Azure. Você pode criar até 20 contas de armazenamento nomeadas exclusivamente com uma única assinatura.

Você pode começar com o Azure com uma [avaliação gratuita][]. Quando decidir comprar um plano, você poderá escolher entre uma variedade de [opções de compra][]. Se for um [assinante do MSDN][], você obterá créditos mensais gratuitos que podem se usados com os serviços do Azure, incluindo o Armazenamento do Azure.

## Armazenamento de Blob

Para usuários com grandes quantidades de dados não estruturados para armazenamento na nuvem, o armazenamento de Blob oferece uma solução econômica e escalonável. Você pode usar o armazenamento de Blob para armazenar conteúdo, como:

-   Documentos
-   Dados sociais, como fotos, vídeos, música e blogs
-   Backups de arquivos, computadores, bancos de dados e dispositivos
-   Imagens e texto para aplicativos web
-   Dados de configuração de aplicativos de nuvem
-   Big data, como logs e outros grandes conjuntos de dados

Cada blob é organizado em um contêiner. Os contêineres também fornecem uma maneira útil para atribuir políticas de segurança para grupos de objetos. Uma conta de armazenamento pode conter qualquer número de contêineres e um contêiner pode conter qualquer número de blobs, até o limite de 500 TB de capacidade da conta de armazenamento.

O armazenamento de blob oferece dois tipos de blobs, blobs de bloco e blobs de página (discos). Os blobs de bloco são otimizados para streaming e armazenamento de objetos de nuvem e são uma boa opção para armazenar documentos, arquivos de mídia, backups etc. Um blob de bloco pode ter até 200 GB de tamanho. Os blobs de página são otimizados para representar discos de IaaS e gravações aleatórias de suporte e podem ter até 1 TB de tamanho. Um disco de IaaS anexado a uma rede de máquinas virtuais do Azure é um VHD armazenado como um blob de página.

Para conjuntos de dados muito grandes onde as restrições de rede impossibilitam o carregamento e o download de dados por transferência eletrônica, você pode enviar uma unidade de disco rígido para a Microsoft para importar ou exportar dados diretamente do data center usando o [Serviço de importação/exportação do Azure][] Você também pode copiar dados de blob em sua conta de armazenamento ou entre contas de armazenamento.

## Armazenamento de tabela

Os aplicativos modernos geralmente demandam armazenamento de dados com maior escalabilidade e flexibilidade que as gerações anteriores de software exigiam. O armazenamento de tabela oferece armazenamento altamente disponível e altamente escalonável, para que seu aplicativo possa ser dimensionado automaticamente para atender à demanda dos usuários. O armazenamento de tabela é um repositório de chave/atributo NoSQL da Microsoft - que tem um design sem esquema, o que o torna diferente dos bancos de dados relacionais tradicionais. Com um repositório de dados sem esquema, é fácil adaptar seus dados conforme as necessidades de seu aplicativo evoluem. O armazenamento de tabela é fácil de usar para que os desenvolvedores possam criar aplicativos rapidamente. O acesso aos dados é rápido e econômico para todos os tipos de aplicativos. O armazenamento de tabela normalmente tem um custo significativamente mais baixo do que o SQL tradicional para volumes de dados semelhantes.

O armazenamento de tabela é um repositório de chave-atributo, o que significa que cada valor em uma tabela é armazenado com um nome de propriedade. Esse nome de propriedade pode ser usado para filtrar e especificar os critérios de seleção. Um conjunto de propriedades e seus valores compõem uma entidade. Como o Armazenamento de tabela é sem esquema, duas entidades na mesma tabela podem conter diferentes coleções de propriedades, e essas propriedades podem ser de tipos diferentes.

Você pode usar o armazenamento de tabela para armazenar conjuntos de dados flexíveis, como dados de usuário para aplicativos web, catálogos de endereços, informações sobre dispositivos e qualquer outro tipo de metadados que o serviço requeira. É possível armazenar qualquer número de entidades em uma tabela, e uma conta de armazenamento pode conter qualquer número de tabelas, até o limite de 200 TB de capacidade da conta de armazenamento.

Semelhantemente a Blobs e Filas, os desenvolvedores podem gerenciar e acessar o Armazenamento de Tabela usando protocolos REST padrão, no entanto, o Armazenamento de Tabela também oferece suporte a um subconjunto do protocolo OData, simplificando recursos de consulta avançados e permitindo formatos JSON e AtomPub (baseados em XML).

Para os aplicativos baseados na Internet atuais, os bancos de dados NoSQL como o Armazenamento de tabela, oferecem uma alternativa popular aos bancos de dados relacionais tradicionais.

## Armazenamento de Fila

Na criação de aplicativos para escala, os componentes do aplicativo geralmente são desassociados, para que possam ser redimensionados independentemente. O Armazenamento de fila fornece uma solução de mensagens confiáveis para comunicação assíncrona entre os componentes do aplicativo, quer estejam em execução na nuvem, na área de trabalho, em um servidor local ou em um dispositivo móvel. O Armazenamento de fila também oferece suporte ao gerenciamento de tarefas assíncronas e à criação de fluxos de trabalho do processo.

Uma conta de armazenamento pode conter qualquer número de filas. Uma fila pode conter qualquer número de mensagens, até o limite de 200 TB de capacidade da conta de armazenamento. Mensagens individuais podem ter até 64 KB de tamanho.

## Armazenamento de arquivo

Muitos aplicativos herdados dependem de compartilhamentos de arquivos, e essa dependência complicou a mudança desses aplicativos para a nuvem. O armazenamento de arquivo oferece compartilhamentos de arquivo baseados em nuvem, de modo que você pode migrar aplicativos herdados para o Azure rapidamente e sem regravações caras.

Aplicativos executados em máquinas virtuais do Azure ou em serviços de nuvem podem montar um compartilhamento de armazenamento de arquivo para acessar dados de arquivos, assim como um aplicativo de área de trabalho montaria um compartilhamento SMB típico. Qualquer quantidade de componentes de aplicativos podem montar e acessar o compartilhamento de armazenamento de arquivo simultaneamente.

Uma vez que um compartilhamento de armazenamento de Arquivo é um compartilhamento de arquivo SMB 2.1 padrão, aplicativos executados no Azure podem acessar dados no compartilhamento por meio de APIs E/S de sistemas de arquivos. Os desenvolvedores podem, portanto, utilizar seus códigos e habilidades existentes para migrar aplicativos existentes. Profissionais de TI podem usar cmdlets do PowerShell para criar, montar e gerenciar compartilhamentos de armazenamento de Arquivo como parte da administração de aplicativos Azure.

Assim como os outros serviços de armazenamento do Azure, o armazenamento de Arquivo expõe a API REST para acessar dados em um compartilhamento. Aplicativos no local podem chamar a API REST do armazenamento de arquivo para acessar dados em um armazenamento de arquivo. Dessa forma, uma empresa pode optar por migrar alguns aplicativos herdados para o Azure e continuar executando outros de dentro de sua própria organização. Observe que montar um compartilhamento de arquivo é possível somente para aplicativos em execução no Azure. Um aplicativo local pode acessar o compartilhamento de arquivo somente pela API REST.

Aplicativos distribuídos também podem usar o armazenamento de arquivo para armazenar e compartilhar dados de aplicativos úteis e ferramentas de desenvolvimento e testes. Por exemplo, um aplicativo pode armazenar arquivos de configuração e dados de diagnósticos como logs, métricas e despejos de falha para que estejam disponíveis para diversas funções ou máquinas virtuais. Desenvolvedores e administradores podem armazenar utilitários de que precisam para compilar ou gerenciar um aplicativo em um armazenamento de arquivo que esteja disponível para todos os componentes, em vez de instalá-los em todas as máquinas virtuais ou instâncias de função.

## Acesso a Recursos de Blob, Tabela, Fila e Arquivo

Por padrão, somente o proprietário da conta de armazenamento pode acessar recursos na conta de armazenamento. Para a segurança de seus dados, todas as solicitações feitas para recursos em sua conta devem ser autenticadas. A autenticação conta com um modelo de Chave Compartilhada. Os Blobs também podem ser configurados para oferecer suporte à autenticação anônima.

Sua conta de armazenamento recebe duas chaves de acesso privadas na criação, que são usadas para autenticação. Ter duas chaves garante que seu aplicativo permaneça disponível quando você regenerar regularmente as chaves como uma prática comum de gerenciamento de chaves de segurança.

Se você precisar permitir acesso controlado aos usuários a seus recursos de armazenamento, poderá criar uma [assinatura de acesso compartilhado][]. Uma assinatura de acesso compartilhado é um token que pode ser acrescentado a uma URL que permite acesso delegado a um contêiner, blob, tabela ou fila. Qualquer pessoa que possua o token pode acessar o recurso para o qual ele aponta com as permissões que ele especifica, pelo período de tempo da validade. Observe que atualmente o armazenamento de arquivo do Azure não oferece suporte para assinaturas de acesso compartilhado.

Finalmente, você pode especificar que um contêiner e seus blobs ou um blob específico estão disponíveis para acesso público. Quando você indica que um contêiner ou blob é público, qualquer pessoa pode lê-lo anonimamente. Nenhuma autenticação é necessária. Os contêineres e blobs públicos são úteis para expor recursos, como mídia e documentos, que são hospedados em sites. Para reduzir a latência de rede para um público global, você pode armazenar em cache os dados de blob usados por sites com a CDN do Azure.

## Replicação para durabilidade e alta disponibilidade

[WACOM.INCLUDE [storage-replication-options][]]

## Preços

Os clientes são cobrados pelo Armazenamento do Azure com base em quatro fatores: a capacidade de armazenamento usada, a opção de replicação selecionada, o número de solicitações feitas ao serviço e a saída de dados.

A capacidade do armazenamento refere-se a quanto de sua alocação de conta de armazenamento você está usando para armazenar dados. O custo de simplesmente armazenar seus dados é determinado pela quantidade de dados que você está armazenando e como eles são replicados. Cada operação de leitura e gravação no Armazenamento do Azure faz uma solicitação no serviço. A saída de dados refere-se aos dados transferidos para fora de uma região do Windows Azure. Quando os dados de sua conta de armazenamento são acessados por um aplicativo que não está sendo executado na mesma região, quer esse aplicativo seja um serviço de nuvem ou algum outro tipo de aplicativo, você é cobrado pela saída de dados. (Para os serviços do Windows Azure, você pode tomar medidas para agrupar seus dados e serviços nos mesmos data centers para reduzir ou eliminar encargos de processamento e de saída de dados.)

A página [Detalhes de preços de armazenamento][] fornece informações detalhadas de preços para capacidade de armazenamento, replicação e transações. A página [Detalhes de preços de transferências de dados][] fornece informações detalhadas de preços para saída de dados. Você pode usar a [Calculadora de preços do Armazenamento do Azure][] para ajudar a estimar os custos.

## Desenvolvendo no armazenamento

O Armazenamento do Azure expõe os recursos de armazenamento por meio de uma [API REST][] que pode ser chamada por qualquer linguagem que possa fazer solicitações HTTP/HTTPS. Além disso, o Armazenamento do Azure oferece bibliotecas de programação para várias linguagens populares. Essas bibliotecas simplificam muitos aspectos do trabalho com o Armazenamento do Azure manipulando detalhes, como invocação síncrona e assíncrona, processamento em lotes de operações, gerenciamento de exceções, novas tentativas automáticas, comportamento operacional e assim por diante. Atualmente, as bibliotecas estão disponíveis para as seguintes linguagens e plataformas, com outras no pipeline:

-   [.NET][]
-   [Código nativo][]
-   [Java/Android][]
-   [Node.js][]
-   [PHP][]
-   [Ruby][]
-   [Python][]
-   [PowerShell][]

## Próximas etapas

Para começar com o Armazenamento do Azure, explore estes recursos:

-   [Documentação do Armazenamento do Azure][]
-   [Metas de desempenho e escalabilidade do Armazenamento do Azure][Escalabilidade e Metas de Desempenho do Armazenamento do Azure]

### Para desenvolvedores do .NET

-   [Como usar o Armazenamento de Blob no .NET][]
-   [Como usar o Armazenamento de Tabela no .NET][]
-   [Como usar o Armazenamento de Fila no .NET][]

### Para desenvolvedores de Java/Android

-   [Como usar o Armazenamento de Blob no Java/Android][]
-   [Como usar o Armazenamento de Tabela no Java/Android][]
-   [Como usar o Armazenamento de Fila no Java/Android][]

### Para desenvolvedores do Node.js

-   [Como usar o Armazenamento de Blob no Node.js][]
-   [Como usar o Armazenamento de Tabela no Node.js][]
-   [Como usar o Armazenamento de Fila no Node.js][]

### Para desenvolvedores do PHP

-   [Como usar o Armazenamento de Blob no PHP][]
-   [Como usar o Armazenamento de Tabela no PHP][]
-   [Como usar o Armazenamento de Fila no PHP][]

### Para desenvolvedores do Ruby

-   [Como usar o Armazenamento de Blob no Ruby][]
-   [Como usar o Armazenamento de Tabela no Ruby][]
-   [Como usar o Armazenamento de Fila no Ruby][]

### Para desenvolvedores do Python

-   [Como usar o Armazenamento de Blob no Python][]
-   [Como usar o Armazenamento de Tabela no Python][]
-   [Como usar o Armazenamento de Fila no Python][]

  [Página de Exibição do Azure]: /en-us/services/preview/
  [Escalabilidade e Metas de Desempenho do Armazenamento do Azure]: http://msdn.microsoft.com/library/windowsazure/dn249410.aspx
  [Recursos de Armazenamento do Azure]: ./media/storage-introduction/storage-concepts.png
  [avaliação gratuita]: /en-us/pricing/free-trial/
  [opções de compra]: /en-us/pricing/purchase-options/
  [assinante do MSDN]: /en-us/pricing/member-offers/msdn-benefits-details/
  [Serviço de importação/exportação do Azure]: http://azure.microsoft.com/en-us/documentation/articles/storage-import-export-service/
  [assinatura de acesso compartilhado]: ../storage-dotnet-shared-access-signature-part-1/
  [storage-replication-options]: ../includes/storage-replication-options.md
  [Detalhes de preços de armazenamento]: /en-us/pricing/details/storage/
  [Detalhes de preços de transferências de dados]: /en-us/pricing/details/data-transfers/
  [Calculadora de preços do Armazenamento do Azure]: /en-us/pricing/calculator/?scenario=data-management
  [API REST]: http://msdn.microsoft.com/library/windowsazure/dd179355.aspx
  [.NET]: http://msdn.microsoft.com/library/dn495001.aspx
  [Código nativo]: http://msdn.microsoft.com/library/dn495438.aspx
  [Java/Android]: /en-us/develop/java/
  [Node.js]: /en-us/develop/nodejs/
  [PHP]: /en-us/develop/php/
  [Ruby]: /en-us/develop/ruby/
  [Python]: /en-us/develop/python/
  [PowerShell]: http://msdn.microsoft.com/library/dn495240.aspx
  [Documentação do Armazenamento do Azure]: /en-us/documentation/services/storage/
  [Como usar o Armazenamento de Blob no .NET]: ../storage-dotnet-how-to-use-blobs/
  [Como usar o Armazenamento de Tabela no .NET]: ../storage-dotnet-how-to-use-tables/
  [Como usar o Armazenamento de Fila no .NET]: ../storage-dotnet-how-to-use-queues/
  [Como usar o Armazenamento de Blob no Java/Android]: ../storage-java-how-to-use-blob-storage/
  [Como usar o Armazenamento de Tabela no Java/Android]: ../storage-java-how-to-use-table-storage/
  [Como usar o Armazenamento de Fila no Java/Android]: ../storage-java-how-to-use-queue-storage/
  [Como usar o Armazenamento de Blob no Node.js]: ../storage-nodejs-how-to-use-blob-storage/
  [Como usar o Armazenamento de Tabela no Node.js]: ../storage-nodejs-how-to-use-table-storage/
  [Como usar o Armazenamento de Fila no Node.js]: ../storage-nodejs-how-to-use-queues/
  [Como usar o Armazenamento de Blob no PHP]: ../storage-php-how-to-use-blobs/
  [Como usar o Armazenamento de Tabela no PHP]: ../storage-php-how-to-use-table-storage/
  [Como usar o Armazenamento de Fila no PHP]: ../storage-php-how-to-use-queues/
  [Como usar o Armazenamento de Blob no Ruby]: ../storage-ruby-how-to-use-blob-storage/
  [Como usar o Armazenamento de Tabela no Ruby]: ../storage-ruby-how-to-use-table-storage/
  [Como usar o Armazenamento de Fila no Ruby]: ../storage-ruby-how-to-use-queue-storage/
  [Como usar o Armazenamento de Blob no Python]: ../storage-python-how-to-use-blob-storage/
  [Como usar o Armazenamento de Tabela no Python]: ../storage-python-how-to-use-table-storage/
  [Como usar o Armazenamento de Fila no Python]: ../storage-python-how-to-use-queue-storage/
