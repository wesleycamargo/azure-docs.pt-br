<properties linkid="storage-introduction" urlDisplayName="Introdução ao Armazenamento do Azure" pageTitle="Introdução ao armazenamento | Microsoft Azure" metaKeywords="introdução introdução ao armazenamento do Azure visão geral do armazenamento do Azure blob do Azure dados não estruturados do Azure armazenamento não estruturado do Azure blob do Azure armazenamento de blob do Azure fila do Azure processamento assíncrono do Azure fila do Azure armazenamento de filas do Azure tabela do Azure nosql do Azure armazenamento de dados estruturados grandes do Azure tabela do Azure armazenamento de tabelas do Azure Azure " description="Uma visão geral do Armazenamento do Microsoft Azure." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="Introdução ao Armazenamento do Microsoft Azure" authors="tamram" />

# Introdução ao Armazenamento do Microsoft Azure

Este artigo fornece uma introdução ao Armazenamento do Microsoft Azure para desenvolvedores, profissionais de TI e tomadores de decisões de negócios. Lendo-o, você aprenderá sobre:

- O que é o Armazenamento do Azure e como você pode tirar proveito dele em aplicativos de nuvem, móveis, de servidor e de área de trabalho
- Quais os tipos de dados que você pode armazenar com os serviços de Armazenamento do Azure: armazenamento de Blob, Tabela e Fila.
- Como o acesso aos seus dados no Armazenamento do Azure é gerenciado
- Como os dados do Armazenamento do Azure são protegidos por meio de redundância e replicação 
- Onde ir em seguida para criar seu primeiro aplicativo de Armazenamento do Azure

## O que é o armazenamento do Azure? ##

A computação na nuvem habilita novos cenários para aplicativos, que exigem armazenamento escalonável, durável e altamente disponível para seus dados â€" que é exatamente o motivo pelo qual a Microsoft desenvolveu o Armazenamento do Azure. Além de permitir que os desenvolvedores criem aplicativos de grande escala para dar suporte a novos cenários, o Armazenamento do Azure também fornece a base da funcionalidade da Infraestrutura como Serviço da Microsoft, uma prova adicional de sua robustez. 

O Armazenamento do Azure é extremamente escalonável para que você possa armazenar e processar centenas de terabytes de dados para oferecer suporte a cenários de big data, necessários para análise científica e financeira e aplicativos de mídia. Ou você pode armazenar pequenas quantidades de dados necessários para um site de pequena empresa. Quaisquer que sejam suas necessidades, você paga apenas pelos dados que está armazenando. O Armazenamento do Azure atualmente armazena dezenas de trilhões de objetos exclusivos de clientes e manipula milhões de solicitações por segundo em média. 

O Armazenamento do Azure é elástico, portanto, você pode criar aplicativos para um grande público global e dimensionar esses aplicativos conforme necessário - tanto a quantidade de armazenamento como o número de transações necessárias. Você paga apenas pelo que usa e apenas quando usa.

O Armazenamento do Azure usa um sistema de particionamento automático que faz o balanço automático da carga de seus dados com base no tráfego. Isso significa que conforme crescem as demandas por seu aplicativo, o Armazenamento do Azure aloca automaticamente os recursos apropriados para atendê-las. 

O Armazenamento do Azure é acessível a partir de qualquer lugar no mundo, em qualquer tipo de aplicativo, quer ele esteja em execução na nuvem, na área de trabalho, em um servidor local ou em um celular ou dispositivo tablet. Você pode usar o Armazenamento do Azure em cenários móveis, onde o aplicativo armazena um subconjunto dos dados no dispositivo e os sincroniza com um conjunto completo de dados armazenados na nuvem.

O Armazenamento do Azure oferece suporte a clientes que usam um conjunto variado de sistemas operacionais (inclusive o Windows e o Linux) e uma variedade de linguagens de programação (incluindo .NET, Java e C++) para desenvolvimento conveniente. O Armazenamento do Azure também expõe recursos de dados por meio de APIs REST simples, que estão disponíveis para qualquer cliente capaz de enviar e receber dados via HTTP/HTTPS. 

## Apresentando os serviços de Armazenamento do Azure ##

Os serviços de Armazenamento do Azure são Armazenamento de Blob, Armazenamento de Tabela e Armazenamento de Fila:

- **O Armazenamento de Blob** armazena dados de arquivos. Um blob pode ser qualquer tipo de texto ou dados binários, como um documento, um arquivo de mídia ou um instalador do aplicativo. 
- **O Armazenamento de Tabela** armazena conjuntos de dados estruturados. O Armazenamento de Tabela é um repositório de dados de atributo de chave NoSQL, que permite desenvolvimento rápido e acesso rápido a grandes quantidades de dados.
- **O Armazenamento de Fila** fornece sistema de mensagens confiável para processamento de fluxo de trabalho e para comunicação entre componentes dos serviços de nuvem.

Esses três serviços são incluídos em todas as contas de armazenamento. Uma conta de armazenamento é um namespace exclusivo que fornece acesso ao Armazenamento do Azure. Cada conta de armazenamento pode conter até 200 TB de dados de blob, tabela e fila combinados.

Antes de criar uma conta de armazenamento, você deve ter uma assinatura do Azure, que é um plano que dá acesso a uma variedade de serviços do Azure. Você pode criar até 20 contas de armazenamento nomeadas exclusivamente com uma única assinatura.

Você pode começar com o Azure com uma [avaliação gratuita](/pt-br/pricing/free-trial/). Quando decidir comprar um plano, você poderá escolher entre uma variedade de [opções de compra](/pt-br/pricing/purchase-options/). Se for um [assinante do MSDN](/pt-br/pricing/member-offers/msdn-benefits-details/), você obterá créditos mensais gratuitos que podem se usados com os serviços do Azure, incluindo o Armazenamento do Azure.

## Armazenamento de Blob ##

Para usuários com grandes quantidades de dados não estruturados para armazenamento na nuvem, o armazenamento de Blob oferece uma solução econômica e escalonável. Você pode usar o armazenamento de Blob para armazenar conteúdo, como:

- Documentos 
- Dados sociais, como fotos, vídeos, música e blogs
- Backups de arquivos, computadores, bancos de dados e dispositivos
- Imagens e texto para aplicativos web
- Dados de configuração de aplicativos de nuvem
- Big data, como logs e outros grandes conjuntos de dados

Cada blob é organizado em um contêiner. Os contêineres também fornecem uma maneira útil para atribuir políticas de segurança para grupos de objetos. Uma conta de armazenamento pode conter qualquer número de contêineres e um contêiner pode conter qualquer número de blobs, até o limite de 200 TB de capacidade da conta de armazenamento.  

O armazenamento de blob oferece dois tipos de blobs, blobs de bloco e blobs de página (discos). Os blobs de bloco são otimizados para streaming e armazenamento de objetos de nuvem e são uma boa opção para armazenar documentos, arquivos de mídia, backups etc. Um blob de bloco pode ter até 200 GB de tamanho. Os blobs de página são otimizados para representar discos de IaaS e gravações aleatórias de suporte e podem ter até 1 TB de tamanho. Um disco de IaaS anexado a uma rede de máquinas virtuais do Azure é um VHD armazenado como um blob de página.

Para conjuntos de dados muito grandes onde as restrições de rede impossibilitam o carregamento e o download de dados por transferência eletrônica, você pode enviar uma unidade de disco rígido para a Microsoft para importar ou exportar dados diretamente do data center usando o [Serviço de importação/exportação do Azure](http://www.windowsazure.com/documentation/articles/storage-import-export-service/) Você também pode copiar dados de blob em sua conta de armazenamento ou entre contas de armazenamento. 

## Armazenamento de tabela ##

Os aplicativos modernos geralmente demandam armazenamento de dados com maior escalabilidade e flexibilidade que as gerações anteriores de software exigiam. O armazenamento de tabela oferece armazenamento altamente disponível e altamente escalonável, para que seu aplicativo possa ser dimensionado automaticamente para atender à demanda dos usuários. O armazenamento de tabela é um repositório de chave/atributo NoSQL da Microsoft â€" que tem um design sem esquema, o que o torna diferente dos bancos de dados relacionais tradicionais. Com um repositório de dados sem esquema, é fácil adaptar seus dados conforme as necessidades de seu aplicativo evoluem. O armazenamento de tabela é fácil de usar para que os desenvolvedores possam criar aplicativos rapidamente. O acesso aos dados é rápido e econômico para todos os tipos de aplicativos.  O armazenamento de tabela normalmente tem um custo significativamente mais baixo do que o SQL tradicional para volumes de dados semelhantes.

O armazenamento de tabela é um repositório de chave-atributo, o que significa que cada entidade em uma tabela é armazenada com um nome de propriedade. Esse nome de propriedade, a chave, pode ser usado para filtrar e especificar os critérios de seleção. A chave junto com um conjunto de propriedades e seus valores compõem uma entidade. Como o Armazenamento de tabela é sem esquema, as entidades na mesma tabela podem conter diferentes coleções de propriedades, e essas propriedades podem ser de tipos diferentes. 

Você pode usar o armazenamento de tabela para armazenar conjuntos de dados flexíveis, como dados de usuário para aplicativos web, catálogos de endereços, informações sobre dispositivos e qualquer outro tipo de metadados que o serviço requeira.  É possível armazenar qualquer número de entidades em uma tabela, e uma conta de armazenamento pode conter qualquer número de tabelas, até o limite de 200 TB de capacidade da conta de armazenamento.

Semelhantemente a Blobs e Filas, os desenvolvedores podem gerenciar e acessar o Armazenamento de Tabela usando protocolos REST padrão, no entanto, o Armazenamento de Tabela também oferece suporte a um subconjunto do protocolo OData, simplificando recursos de consulta avançados e permitindo formatos JSON e AtomPub (baseados em XML).

Para os aplicativos baseados na Internet atuais, os bancos de dados NoSQL como o Armazenamento de tabela, oferecem uma alternativa popular aos bancos de dados relacionais tradicionais. 

## Armazenamento de Fila ##

Na criação de aplicativos para escala, os componentes do aplicativo geralmente são desassociados, para que possam ser redimensionados independentemente. O Armazenamento de fila fornece uma solução de mensagens confiáveis para comunicação assíncrona entre os componentes do aplicativo, quer estejam em execução na nuvem, na área de trabalho, em um servidor local ou em um dispositivo móvel. O Armazenamento de fila também oferece suporte ao gerenciamento de tarefas assíncronas e à criação de fluxos de trabalho do processo. 

Uma conta de armazenamento pode conter qualquer número de filas. Uma fila pode conter qualquer número de mensagens, até o limite de 200 TB de capacidade da conta de armazenamento. Mensagens individuais podem ter até 64 KB de tamanho.

## Acesso a recursos de Blob, Tabela e Fila ##

Por padrão, somente o proprietário da conta de armazenamento pode acessar recursos na conta de armazenamento. Para a segurança de seus dados, todas as solicitações feitas para recursos em sua conta devem ser autenticadas. A autenticação conta com um modelo de Chave Compartilhada. Os Blobs também podem ser configurados para oferecer suporte à autenticação anônima. 

Sua conta de armazenamento recebe duas chaves de acesso privadas na criação, que são usadas para autenticação. Ter duas chaves garante que seu aplicativo permaneça disponível quando você regenerar regularmente as chaves como uma prática comum de gerenciamento de chaves de segurança.

Se você precisar permitir acesso controlado aos usuários a seus recursos de armazenamento, poderá criar uma [assinatura de acesso compartilhado](../storage-dotnet-shared-access-signature-part-1/). Uma assinatura de acesso compartilhado é um token que pode ser acrescentado a uma URL que permite acesso delegado a um contêiner, blob, tabela ou fila. Qualquer pessoa que possua o token pode acessar o recurso para o qual ele aponta com as permissões que ele especifica, pelo período de tempo da validade.

Finalmente, você pode especificar que um contêiner e seus blobs ou um blob específico estão disponíveis para acesso público. Quando você indica que um contêiner ou blob é público, qualquer pessoa pode lê-lo anonimamente. Nenhuma autenticação é necessária.  Os contêineres e blobs públicos são úteis para expor recursos, como mídia e documentos, que são hospedados em sites.  Para reduzir a latência de rede para um público global, você pode armazenar em cache os dados de blob usados por sites com a CDN do Azure.

## Replicação para durabilidade e alta disponibilidade ##

Os dados de sua conta de armazenamento são replicados para garantir durabilidade que também é altamente disponível, atendendo ao [Contrato de Nível de Serviço do armazenamento do Azure ](/pt-br/support/legal/sla/)mesmo no caso de falhas transitórias de hardware. Você tem três opções para replicar os dados em sua conta de armazenamento:

- *O LRS* (armazenamento com redundância local) é replicado três vezes em um único data center. Ao gravar dados em um blob, fila ou tabela, a operação de gravação é executada em sincronia em todas as três réplicas. O LRS protege os dados contra falhas normais de hardware.
- *O GRS (armazenamento com redundância geográfica)* é replicado três vezes dentro de uma única região e também é replicado de maneira assíncrona para uma segunda região a centenas de quilômetros de distância da região primária. O GRS mantém um equivalente de seis cópias (réplicas) de seus dados (três em cada região). O GRS permite que a Microsoft faça failover em uma segunda região se não for possível restaurar a primeira região devido a uma grande interrupção ou a um desastre.  O GRS é recomendado sobre o armazenamento com redundância local.
- *O RA-GRS (armazenamento com acesso de leitura geograficamente redundante)* fornece todos os benefícios do armazenamento com redundância geográfica e também permite acesso de leitura dos dados na região secundária caso a região primária fique indisponível. O armazenamento com acesso de leitura geograficamente redundante é recomendado para disponibilidade máxima além de durabilidade.  

As diferenças de preço entre LRS, GRS e RA-GRS podem ser encontradas na página [Detalhes de preços de armazenamento](/pt-br/pricing/details/storage/).

## Preços ##

Os clientes são cobrados pelo Armazenamento do Azure com base em quatro fatores: a capacidade de armazenamento usada, a opção de replicação selecionada, o número de solicitações feitas ao serviço e a saída de dados. 

A capacidade do armazenamento refere-se a quanto de sua alocação de conta de armazenamento você está usando para armazenar dados. O custo de simplesmente armazenar seus dados é determinado pela quantidade de dados que você está armazenando e como eles são replicados. Cada operação de leitura e gravação no Armazenamento do Azure faz uma solicitação no serviço. A saída de dados refere-se aos dados transferidos para fora de uma região do Windows Azure. Quando os dados de sua conta de armazenamento são acessados por um aplicativo que não está sendo executado na mesma região, quer esse aplicativo seja um serviço de nuvem ou algum outro tipo de aplicativo, você é cobrado pela saída de dados. (Para os serviços do Windows Azure, você pode tomar medidas para agrupar seus dados e serviços nos mesmos data centers para reduzir ou eliminar encargos de processamento e de saída de dados.) 

A página [Detalhes de preços de armazenamento](/pt-br/pricing/details/storage/) fornece informações detalhadas de preços para capacidade de armazenamento, replicação e transações. A página [Detalhes de preços de transferências de dados](/pt-br/pricing/details/data-transfers/) fornece informações detalhadas de preços para saída de dados. Você pode usar a [Calculadora de preços do Armazenamento do Azure](/pt-br/pricing/calculator/?scenario=data-management) para ajudar a estimar os custos.

## Desenvolvendo no armazenamento ##

O Armazenamento do Azure expõe os recursos de armazenamento por meio de uma [API REST](http://msdn.microsoft.com/library/windowsazure/dd179355.aspx) que pode ser chamada por qualquer linguagem que possa fazer solicitações HTTP/HTTPS. Além disso, o Armazenamento do Azure oferece bibliotecas de programação para várias linguagens populares. Essas bibliotecas simplificam muitos aspectos do trabalho com o Armazenamento do Azure manipulando detalhes, como invocação síncrona e assíncrona, processamento em lotes de operações, gerenciamento de exceções, novas tentativas automáticas, comportamento operacional e assim por diante. Atualmente, as bibliotecas estão disponíveis para as seguintes linguagens e plataformas, com outras no pipeline:

- [.NET](http://msdn.microsoft.com/library/dn495001(v=azure.10).aspx)
- [Código nativo](http://msdn.microsoft.com/library/dn495438.aspx)
- [Java](/pt-br/develop/java/)
- [Node.js](../storage/#node)
- [PHP](../storage/#php)
- [Ruby](../storage/#ruby)
- [Python](../storage/#python)
- [PowerShell](http://msdn.microsoft.com/library/dn495240.aspx)

## Próximas etapas ##

Para começar com o Armazenamento do Azure, explore estes recursos:

- [Documentação do Armazenamento do Azure](/pt-br/documentation/services/storage/)
- [Metas de desempenho e escalabilidade do Armazenamento do Azure](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx)

<h3>Para desenvolvedores do .NET</h3>

- [Como usar o Armazenamento de Blob no .NET](../storage-dotnet-how-to-use-blobs-20/)
- [Como usar o Armazenamento de Tabela no .NET](../storage-dotnet-how-to-use-tables-20/)
- [Como usar o Armazenamento de Fila no .NET](../storage-dotnet-how-to-use-queues-20/)

<h3>Para desenvolvedores de Java</h3>

- [Como usar o Armazenamento de Blob no Java](../storage-java-how-to-use-blob-storage/)
- [Como usar o Armazenamento de Tabela no Java](..storage-java-how-to-use-table-storage/)
- [Como usar o Armazenamento de Fila no Java](..storage-java-how-to-use-queue-storage/)

<h3>Para desenvolvedores do Node.js</h3>

- [Como usar o Armazenamento de Blob no Node.js](../storage-nodejs-how-to-use-blob-storage/)
- [Como usar o Armazenamento de Tabela no Node.js](../storage-nodejs-how-to-use-table-storage/)
- [Como usar o Armazenamento de Fila no Node.js](../storage-nodejs-how-to-use-queue-storage/)

<h3>Para desenvolvedores do PHP</h3>

- [Como usar o Armazenamento de Blob no PHP](../storage-php-how-to-use-blob-storage/)
- [Como usar o Armazenamento de Tabela no PHP](..storage-php-how-to-use-table-storage/)
- [Como usar o Armazenamento de Fila no PHP](..storage-php-how-to-use-queue-storage/)

<h3>Para desenvolvedores do Ruby</h3>

- [ Como usar o Armazenamento de Blob no Ruby](../storage-ruby-how-to-use-blob-storage/)
- [Como usar o Armazenamento de Tabela no Ruby](..storage-ruby-how-to-use-table-storage/)
- [Como usar o Armazenamento de Fila no Ruby](..storage-ruby-how-to-use-queue-storage/)

<h3>Para desenvolvedores do Python</h3>

- [Como usar o Armazenamento de Blob no Python](../storage-python-how-to-use-blob-storage/)
- [Como usar o Armazenamento de Tabela no Python](..storage-python-how-to-use-table-storage/)
- [Como usar o Armazenamento de Fila no Python](..storage-python-how-to-use-queue-storage/)

