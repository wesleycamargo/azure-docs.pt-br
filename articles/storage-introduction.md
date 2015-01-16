<properties urlDisplayName="Introduction to Azure Storage" pageTitle="Introdução ao Armazenamento | Microsoft Azure" metaKeywords="Get started  Azure storage introduction  Azure storage overview  Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage  Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage  Azure file storage  Azure file  Azure file share  Azure " description="Uma visão geral do Armazenamento do Microsoft Azure." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="Introduction to Microsoft Azure Storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="tamram" />

# Introdução ao Armazenamento do Microsoft Azure

Este artigo fornece uma introdução ao Armazenamento do Microsoft Azure para desenvolvedores, profissionais de TI e tomadores de decisões de negócios. Lendo-o, você aprenderá sobre:

- O que é o Armazenamento do Azure e como você pode tirar proveito dele em aplicativos de nuvem, móveis, de servidor e de área de trabalho
- Quais os tipos de dados que você pode armazenar com os serviços de Armazenamento do Azure: armazenamento de Blob, Tabela e Fila
- Como o acesso aos seus dados no Armazenamento do Azure é gerenciado
- Como os dados do Armazenamento do Azure são protegidos por meio de redundância e replicação 
- Onde ir em seguida para criar seu primeiro aplicativo de Armazenamento do Azure

## O que é o armazenamento do Azure? ##

A computação em nuvem habilita novos cenários para aplicativos, que exigem armazenamento escalonável, durável e altamente disponível para seus dados - que é exatamente o motivo pelo qual a Microsoft desenvolveu o Armazenamento do Azure. Além de permitir que os desenvolvedores criem aplicativos de grande escala para dar suporte a novos cenários, o Armazenamento do Azure também fornece a base do armazenamento das Máquinas Virtuais do Azure, uma prova adicional de sua robustez. 

O Armazenamento do Azure é extremamente escalonável para que você possa armazenar e processar centenas de terabytes de dados para dar suporte a cenários de big data, necessários para análise científica e financeira e aplicativos de mídia. Ou você pode armazenar pequenas quantidades de dados necessários para um site de pequena empresa. Quaisquer que sejam suas necessidades, você paga apenas pelos dados que está armazenando. O Armazenamento do Azure atualmente armazena dezenas de trilhões de objetos exclusivos de clientes e manipula milhões de solicitações por segundo em média. 

O Armazenamento do Azure é elástico, portanto, você pode criar aplicativos para um grande público global e dimensionar esses aplicativos conforme necessário - tanto a quantidade de dados armazenados e o número de solicitações realizadas. Você paga apenas pelo que usa e apenas quando usa.

O Armazenamento do Azure usa um sistema de particionamento automático que faz o balanço automático da carga de seus dados com base no tráfego. Isso significa que conforme crescem as demandas por seu aplicativo, o Armazenamento do Azure aloca automaticamente os recursos apropriados para atendê-las. 

O Armazenamento de fila fornece uma solução de mensagens confiáveis para comunicação assíncrona entre os componentes do aplicativo, quer estejam em execução na nuvem, na área de trabalho, em um servidor local ou em um dispositivo móvel. Você pode usar o Armazenamento do Azure em cenários móveis, onde o aplicativo armazena um subconjunto dos dados no dispositivo e os sincroniza com um conjunto completo de dados armazenados na nuvem.

O Armazenamento do Azure dá suporte a clientes que usam um conjunto variado de sistemas operacionais (inclusive o Windows e o Linux) e uma variedade de linguagens de programação (incluindo .NET, Java e C++) para desenvolvimento conveniente. O Armazenamento do Azure também expõe recursos de dados por meio de APIs REST simples, que estão disponíveis para qualquer cliente capaz de enviar e receber dados via HTTP/HTTPS.

O Armazenamento premium do Azure agora está disponível na visualização. O Armazenamento premium do Azure dá suporte a disco de alto desempenho e baixa latência para cargas de trabalho com uso intenso de entrada e saída em execução em máquinas virtuais do Azure. Com o Armazenamento premium do Azure, é possível anexar vários discos de dados persistentes a uma máquina virtual e configurá-los para atender suas necessidades de desempenho. Cada disco de dados é apoiado por um disco SSD no Armazenamento premium do Azure para máximo desempenho de entrada e saída. Consulte [Armazenamento premium: Armazenamento de Alto Desempenho para Cargas de Trabalho de Máquina Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=521898) para mais detalhes. 

## Apresentando os serviços de Armazenamento do Azure ##

Uma conta de armazenamento do Azure é uma conta segura que fornece acesso a serviços no Armazenamento do Azure. Sua conta de armazenamento fornece um namespace único para seus recursos de armazenamento. Existem dois tipos de contas de armazenamento:

- Uma conta de armazenamento padrão inclui armazenamento de Blob, Tabela e Fila.
- Uma conta de armazenamento premium tem suporte no momento para discos de Máquina Virtual do Azure apenas. Armazenamento premium do Azure está disponível sob solicitação por meio da [página de Visualização do Azure](/pt-br/services/preview/).

Antes de criar uma conta de armazenamento, você deve ter uma assinatura do Azure, que é um plano que dá acesso a uma variedade de serviços do Azure. Você pode criar até 100 contas de armazenamento nomeadas exclusivamente com uma única assinatura. Consulte os [Detalhes de preços de armazenamento](http://www.windowsazure.com/pt-br/pricing/details/storage/) para informações sobre preço por volume.

Você pode começar com o Azure com uma [avaliação gratuita](/pt-br/pricing/free-trial/). Quando decidir comprar um plano, você poderá escolher entre uma variedade de [opções de compra](/pt-br/pricing/purchase-options/). Se você for um [assinante do MSDN](/pt-br/pricing/member-offers/msdn-benefits-details/), obterá créditos mensais gratuitos que podem se usados com os serviços do Azure, incluindo o Armazenamento do Azure.

### Contas de armazenamento padrão

Uma conta de armazenamento padrão dá acesso a armazenamento de Blob, Tabela, Fila e Arquivo:

- **O Armazenamento de Blob** armazena dados de arquivos. Um blob pode ser qualquer tipo de texto ou dados binários, como um documento, um arquivo de mídia ou um instalador do aplicativo. 
- **O Armazenamento de Tabela** armazena conjuntos de dados estruturados.O Armazenamento de Tabela é um repositório de dados de atributo de chave NoSQL, que permite desenvolvimento rápido e acesso rápido a grandes quantidades de dados.
- **O Armazenamento de Fila** fornece sistema de mensagens confiável para processamento de fluxo de trabalho e para comunicação entre componentes dos serviços de nuvem.
- **O Armazenamento de Arquivo (visualização)** oferece armazenamento compartilhado para aplicativos herdados usando o protocolo padrão SMB 2.1. As máquinas virtuais do Azure e os serviços de nuvem podem compartilhar dados de arquivos entre componentes de aplicativos por meio de compartilhamentos montados, e aplicativos locais podem acessar dados de arquivos em um compartilhamento por meio da API REST do serviço de Arquivo.Armazenamento de arquivo está disponível sob solicitação por meio da [página de Visualização do Azure](/pt-br/services/preview/). 

Cada conta de armazenamento padrão pode conter até 500 TB de dados de blob, tabela, fila e arquivo combinados. Consulte as [Metas de desempenho e escalabilidade do Armazenamento do Azure](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx) para obter detalhes sobre a capacidade da conta de armazenamento padrão.

A imagem abaixo mostra as relações entre os recursos de armazenamento do Azure em uma conta de armazenamento padrão:

![Azure Storage Resources](./media/storage-introduction/storage-concepts.png)

Quando você estiver pronto para criar uma conta de armazenamento padrão, consulte [Criar, gerenciar ou excluir uma conta de armazenamento](../storage-create-storage-account/) para mais detalhes.

### Contas de Armazenamento premium (visualização)

Um Armazenamento premium do Azure tem suporte no momento para discos de Máquina Virtual do Azure apenas. Armazenamento Premium do Azure está disponível sob solicitação por meio da [página de Visualização do Azure](/pt-br/services/preview/). Para uma visão geral aprofundada do Armazenamento premium do Azure, consulte [Armazenamento premium: Armazenamento de Alto Desempenho para Cargas de Trabalho de Máquina Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=521898).

## Armazenamento de Blob ##

Para usuários com grandes quantidades de dados não estruturados para armazenamento na nuvem, o armazenamento de Blob oferece uma solução econômica e escalonável. Você pode usar o armazenamento de Blob para armazenar conteúdo, como:

- Documentos 
- Dados sociais, como fotos, vídeos, música e blogs
- Backups de arquivos, computadores, bancos de dados e dispositivos
- Imagens e texto para aplicativos web
- Dados de configuração de aplicativos de nuvem
- Big data, como logs e outros grandes conjuntos de dados

Cada blob é organizado em um contêiner. Os contêineres também fornecem uma maneira útil para atribuir políticas de segurança para grupos de objetos. Uma conta de armazenamento pode conter qualquer número de contêineres e um contêiner pode conter qualquer número de blobs, até o limite de 500 TB de capacidade da conta de armazenamento.  

O armazenamento de blob oferece dois tipos de blobs, blobs de bloco e blobs de página (discos). Os blobs de bloco são otimizados para streaming e armazenamento de objetos de nuvem e são uma boa opção para armazenar documentos, arquivos de mídia, backups etc. Um blob de blocos pode ter até 200 GB de tamanho. Os blobs de página são otimizados para representar discos de IaaS e gravações aleatórias de suporte e podem ter até 1 TB de tamanho. Um disco de IaaS anexado a uma rede de máquinas virtuais do Azure é um VHD armazenado como um blob de páginas.

Para conjuntos de dados muito grandes onde as restrições de rede impossibilitam o carregamento e o download de dados por transferência eletrônica, você pode enviar uma unidade de disco rígido para a Microsoft para importar ou exportar dados diretamente do data center usando o [Serviço de importação/exportação do Azure](http://azure.microsoft.com/pt-br/documentation/articles/storage-import-export-service/). Você também pode copiar dados de blob em sua conta de armazenamento ou entre contas de armazenamento. 

## Armazenamento de tabela ##

Os aplicativos modernos geralmente demandam armazenamento de dados com maior escalabilidade e flexibilidade que as gerações anteriores de software exigiam. O armazenamento de tabela oferece armazenamento altamente disponível e altamente escalonável, para que seu aplicativo possa ser dimensionado automaticamente para atender à demanda dos usuários. O armazenamento de tabela é um repositório de chave/atributo NoSQL da Microsoft - que tem um design sem esquema, o que o torna diferente dos bancos de dados relacionais tradicionais. Com um repositório de dados sem esquema, é fácil adaptar seus dados conforme as necessidades de seu aplicativo evoluem. O armazenamento de tabela é fácil de usar para que os desenvolvedores possam criar aplicativos rapidamente. O acesso aos dados é rápido e econômico para todos os tipos de aplicativos.  O armazenamento de tabela normalmente tem um custo significativamente mais baixo do que o SQL tradicional para volumes de dados semelhantes.

O armazenamento de tabela é um repositório de chave-atributo, o que significa que cada valor em uma tabela é armazenado com um nome de propriedade. Esse nome de propriedade pode ser usado para filtrar e especificar os critérios de seleção. Um conjunto de propriedades e seus valores compõem uma entidade. Como o Armazenamento de tabela é sem esquema, duas entidades na mesma tabela podem conter diferentes coleções de propriedades, e essas propriedades podem ser de tipos diferentes. 

Você pode usar o armazenamento de tabela para armazenar conjuntos de dados flexíveis, como dados de usuário para aplicativos web, catálogos de endereços, informações sobre dispositivos e qualquer outro tipo de metadados que o serviço requeira.  Você pode armazenar qualquer número de entidades em uma tabela e uma conta de armazenamento pode conter um número ilimitado de tabelas, até o limite de capacidade da conta de armazenamento.

Semelhantemente a Blobs e Filas, os desenvolvedores podem gerenciar e acessar o Armazenamento de Tabela usando protocolos REST padrão, no entanto, o Armazenamento de Tabela também dá suporte a um subconjunto do protocolo OData, simplificando recursos de consulta avançados e permitindo formatos JSON e AtomPub (baseados em XML).

Para os aplicativos baseados na Internet atuais, os bancos de dados NoSQL como o Armazenamento de tabela, oferecem uma alternativa popular aos bancos de dados relacionais tradicionais. 

## Armazenamento de Fila ##

Na criação de aplicativos para escala, os componentes do aplicativo geralmente são desassociados, para que possam ser redimensionados independentemente. O Armazenamento de fila fornece uma solução de mensagens confiáveis para comunicação assíncrona entre os componentes do aplicativo, quer estejam em execução na nuvem, na área de trabalho, em um servidor local ou em um dispositivo móvel. O Armazenamento de fila também dá suporte ao gerenciamento de tarefas assíncronas e à criação de fluxos de trabalho do processo. 

Uma conta de armazenamento pode conter qualquer número de filas. Uma fila pode conter um número ilimitado de mensagens, até o limite de capacidade da conta de armazenamento. Mensagens individuais podem ter até 64 KB de tamanho.

## Armazenamento de arquivo (visualização) ##

Muitos aplicativos herdados dependem de compartilhamentos de arquivos, e essa dependência complicou a mudança desses aplicativos para a nuvem. O armazenamento de arquivo oferece compartilhamentos de arquivo baseados em nuvem, de modo que você pode migrar aplicativos herdados para o Azure rapidamente e sem regravações caras. 

Aplicativos executados em máquinas virtuais ou em serviços de nuvem do Azure podem montar um compartilhamento de armazenamento de arquivos para acessar dados de arquivos, da mesma forma que um aplicativo de área de trabalho montaria um compartilhamento SMB típico. Qualquer quantidade de componentes de aplicativos podem montar e acessar o compartilhamento de armazenamento de arquivo simultaneamente.

Uma vez que um compartilhamento de armazenamento de Arquivo é um compartilhamento de arquivo SMB 2.1 padrão, aplicativos executados no Azure podem acessar dados no compartilhamento por meio de APIs E/S de sistemas de arquivos. Os desenvolvedores podem, portanto, utilizar seus códigos e habilidades existentes para migrar aplicativos existentes. Os profissionais de TI podem usar cmdlets do PowerShell para criar, montar e gerenciar compartilhamentos de armazenamento de arquivos como parte da administração de aplicativos do Azure.

Assim como os outros serviços de armazenamento do Azure, o armazenamento de Arquivo expõe a API REST para acessar dados em um compartilhamento. Aplicativos no local podem chamar a API REST do armazenamento de arquivo para acessar dados em um armazenamento de arquivo. Dessa forma, uma empresa pode optar por migrar alguns aplicativos herdados para o Azure e continuar executando outros de dentro de sua própria organização. Observe que montar um compartilhamento de arquivo é possível somente para aplicativos em execução no Azure. Um aplicativo local pode acessar o compartilhamento de arquivo somente pela API REST.

Aplicativos distribuídos também podem usar o armazenamento de arquivo para armazenar e compartilhar dados de aplicativos úteis e ferramentas de desenvolvimento e testes. Por exemplo, um aplicativo pode armazenar arquivos de configuração e dados de diagnósticos como logs, métricas e despejos de falha para que estejam disponíveis para diversas funções ou máquinas virtuais. Desenvolvedores e administradores podem armazenar utilitários de que precisam para compilar ou gerenciar um aplicativo em um armazenamento de arquivo que esteja disponível para todos os componentes, em vez de instalá-los em todas as máquinas virtuais ou instâncias de função.


## Acesso a Recursos de Blob, Tabela, Fila e Arquivo ##

Por padrão, somente o proprietário da conta de armazenamento pode acessar recursos na conta de armazenamento. Para a segurança de seus dados, todas as solicitações feitas para recursos em sua conta devem ser autenticadas. A autenticação conta com um modelo de Chave Compartilhada. Os Blobs também podem ser configurados para dar suporte à autenticação anônima. 

Sua conta de armazenamento recebe duas chaves de acesso privadas na criação, que são usadas para autenticação. Ter duas chaves garante que seu aplicativo permaneça disponível quando você regenerar regularmente as chaves como uma prática comum de gerenciamento de chaves de segurança.

Se você precisar permitir acesso controlado aos usuários a seus recursos de armazenamento, poderá criar uma [assinatura de acesso compartilhado](../storage-dotnet-shared-access-signature-part-1/). Uma assinatura de acesso compartilhado é um token que pode ser acrescentado a uma URL que permite acesso delegado a um contêiner, blob, tabela ou fila. Qualquer pessoa que possua o token pode acessar o recurso para o qual ele aponta com as permissões que ele especifica, pelo período de tempo da validade. Observe que atualmente o armazenamento de arquivo do Azure não dá suporte para assinaturas de acesso compartilhado.

Finalmente, você pode especificar que um contêiner e seus blobs ou um blob específico estão disponíveis para acesso público. Quando você indica que um contêiner ou blob é público, qualquer pessoa pode lê-lo anonimamente. Nenhuma autenticação é necessária.  Os contêineres e blobs públicos são úteis para expor recursos, como mídia e documentos, que são hospedados em sites.  Para reduzir a latência de rede para um público global, você pode armazenar em cache os dados de blob usados por sites com a CDN do Azure.

## Replicação para durabilidade e alta disponibilidade ##

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

## Preço ##

Os clientes são cobrados pelo Armazenamento do Azure com base em quatro fatores: a capacidade de armazenamento usada, a opção de replicação selecionada, o número de solicitações feitas ao serviço e a saída de dados. 

A capacidade do armazenamento refere-se a quanto de sua alocação de conta de armazenamento você está usando para armazenar dados. O custo de simplesmente armazenar seus dados é determinado pela quantidade de dados que você está armazenando e como eles são replicados. Cada operação de leitura e gravação no Armazenamento do Azure faz uma solicitação no serviço. A saída de dados refere-se aos dados transferidos para fora de uma região do Microsoft Azure. Quando os dados de sua conta de armazenamento são acessados por um aplicativo que não está sendo executado na mesma região, quer esse aplicativo seja um serviço de nuvem ou algum outro tipo de aplicativo, você é cobrado pela saída de dados. (Para os serviços do Microsoft Azure, você pode tomar medidas para agrupar seus dados e serviços nos mesmos data centers para reduzir ou eliminar encargos de processamento e de saída de dados.) 

Os [Detalhes de preços de armazenamento](/pt-br/pricing/details/storage/) fornece informações detalhadas de preços para capacidade de armazenamento, replicação e transações. Os [Detalhes de preços de transferências de dados](/pt-br/pricing/details/data-transfers/) fornecem informações detalhadas de preços para saída de dados. É possível usar a [Calculadora de preço de armazenamento do Azure](/pt-br/pricing/calculator/?scenario=data-management) to help estimate your costs.

## Desenvolvendo no armazenamento ##

O Armazenamento do Azure expõe os recursos de armazenamento por meio de uma [API REST](http://msdn.microsoft.com/library/windowsazure/dd179355.aspx) que pode ser chamada por qualquer linguagem que possa fazer solicitações HTTP/HTTPS. Além disso, o Armazenamento do Azure oferece bibliotecas de programação para várias linguagens populares. Essas bibliotecas simplificam muitos aspectos do trabalho com o Armazenamento do Azure manipulando detalhes, como invocação síncrona e assíncrona, processamento em lotes de operações, gerenciamento de exceções, novas tentativas automáticas, comportamento operacional e assim por diante. Atualmente, as bibliotecas estão disponíveis para as seguintes linguagens e plataformas, com outras no pipeline:

- [.NET](http://go.microsoft.com/fwlink/?LinkID=390731)
- [Código nativo](http://msdn.microsoft.com/library/dn495438.aspx)
- [Java/Android](/pt-br/develop/java/)
- [Node.js](/pt-br/develop/nodejs/)
- [PHP](/pt-br/develop/php/)
- [Ruby](/pt-br/develop/ruby/)
- [Python](/pt-br/develop/python/)
- [PowerShell](http://msdn.microsoft.com/library/dn495240.aspx)

## Próximas Etapas ##

Para começar com o Armazenamento do Azure, explore estes recursos:

### Downloads

- [Pacote NuGet de armazenamento do Azure - Bibliotecas do cliente para .NET, Windows Phone e Tempo de Execução do Windows](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [SDKs e Ferramentas do Azure](http://azure.microsoft.com/pt-br/downloads/)
- [Emulador de Armazenamento do Azure](http://www.microsoft.com/en-in/download/details.aspx?id=43709)

### Código-fonte

- [Bibliotecas de Armazenamento do Microsoft Azure para .NET](https://github.com/Azure/azure-storage-net)

### Documentação

- [Documentação do Armazenamento do Azure](/pt-br/documentation/services/storage/)
- [Referência de API REST de Serviços de Armazenamento do Azure](http://msdn.microsoft.com/pt-br/library/dd179355.aspx)
- [Referência da Ferramenta de Linha de Comando AzCopy](http://azure.microsoft.com/pt-br/documentation/articles/storage-use-azcopy/)

<h3>Para usuários do PowerShell</h3>
- [Cmdlets do Armazenamento do Azure](http://msdn.microsoft.com/pt-br/library/azure/dn806401.aspx)

<h3>Para desenvolvedores do .NET</h3>

- [Referência à Biblioteca cliente do .NET](http://msdn.microsoft.com/pt-br/library/wa_storage_30_reference_home.aspx)
- [Como usar o Armazenamento de Blob no .NET](../storage-dotnet-how-to-use-blobs/)
- [Como usar o Armazenamento de Tabela no .NET](../storage-dotnet-how-to-use-tables/)
- [Como usar o Armazenamento de Fila no .NET](../storage-dotnet-how-to-use-queues/)

<h3>Para desenvolvedores de Java/Android</h3>

- [Referência da Biblioteca do Cliente Java]()
- [Como usar o Armazenamento de Blob no Java/Android](../storage-java-how-to-use-blob-storage/)
- [Como usar o Armazenamento de Tabela no Java/Android](../storage-java-how-to-use-table-storage/)
- [Como usar o Armazenamento de Fila no Java/Android](../storage-java-how-to-use-queue-storage/)

<h3>Para desenvolvedores do Node.js</h3>

- [Como usar o Armazenamento de Blob no Node.js](../storage-nodejs-how-to-use-blob-storage/)
- [Como usar o Armazenamento de Tabela no Node.js](../storage-nodejs-how-to-use-table-storage/)
- [Como usar o Armazenamento de Fila no Node.js](../storage-nodejs-how-to-use-queues/)

<h3>Para desenvolvedores do PHP</h3>

- [Como usar o Armazenamento de Blob no PHP](../storage-php-how-to-use-blobs/)
- [Como usar o Armazenamento de Tabela no PHP](../storage-php-how-to-use-table-storage/)
- [Como usar o Armazenamento de Fila no PHP](../storage-php-how-to-use-queues/)

<h3>Para desenvolvedores do Ruby</h3>

- [Como usar o Armazenamento de Blob no Ruby](../storage-ruby-how-to-use-blob-storage/)
- [Como usar o Armazenamento de Tabela no Ruby](../storage-ruby-how-to-use-table-storage/)
- [Como usar o Armazenamento de Fila no Ruby](../storage-ruby-how-to-use-queue-storage/)

<h3>Para desenvolvedores do Python</h3>

- [Como usar o Armazenamento de Blob no Python](../storage-python-how-to-use-blob-storage/)
- [Como usar o Armazenamento de Tabela no Python](../storage-python-how-to-use-table-storage/)
- [Como usar o Armazenamento de Fila no Python](../storage-python-how-to-use-queue-storage/)

<!--HONumber=35.1-->
