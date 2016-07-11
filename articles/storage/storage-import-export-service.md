<properties
	pageTitle="Usando importação/exportação para transferir dados para o Armazenamento de Blob | Microsoft Azure"
	description="Saiba como criar os trabalhos de importação e exportação no portal Clássico do Azure para transferir dados para o armazenamento de blobs."
	authors="renashahmsft"
	manager="aungoo"
	editor="tysonn"
	services="storage"
	documentationCenter=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/22/2016"
	ms.author="renash"/>


# Usar o serviço de Importação/Exportação do Microsoft Azure para transferir dados para o Armazenamento de Blobs

## Visão geral

O Serviço de Importação/Exportação do Azure permite a você transferir com segurança grandes quantidades de dados para o armazenamento de blobs do Azure por meio do envio de unidades de disco rígido para um data center do Azure. Você também pode usar esse serviço para transferir dados do armazenamento de Blobs do Azure para as unidades de disco rígido e enviar para seu site local. Esse serviço é adequado em situações em que você deseja transferir vários TBs de dados de ou para o Azure, mas carregar ou baixar pela rede não é viável devido à largura de banda limitada ou aos os altos custos de rede.

O serviço requer que as unidades de disco rígido sejam criptografadas com bitlocker para a segurança dos seus dados. O serviço dá suporte a contas de armazenamento clássicas presentes em todas as regiões do Azure Público. Você deve enviar as unidades de disco rígido para um dos locais com suporte especificados posteriormente neste artigo.
 
Neste artigo, você aprenderá mais sobre o serviço de Importação/Exportação do Azure e como enviar as unidades para copiar os dados para e a partir do armazenamento de Blobs do Azure.

> [AZURE.IMPORTANT] Você pode criar e gerenciar a importação e exportação dos trabalhos para o armazenamento clássico usando o portal Clássico ou as [APIs REST do serviço de Importação/Exportação](http://go.microsoft.com/fwlink/?LinkID=329099). As contas de armazenamento do Gerenciador de Recursos não têm suporte no momento.

## Quando devo usar o serviço de Importação/Exportação do Azure?

Você pode considerar usar o serviço de Importação/Exportação do Azure quando carregar ou baixar os dados pela rede estiver lento ou quando a largura de banda de rede adicional for dispendioso.

Você pode usar esse serviço em cenários como:

- Migrando dados para a nuvem: mover grandes quantidades de dados para Azure de forma rápida e econômica.
- Distribuição de conteúdo: enviar rapidamente dados para seus sites de clientes.
- Backup: fazer backups dos seus dados locais para guardar no armazenamento de blobs do Azure.
- Recuperação de dados: recuperar uma grande quantidade de dados guardados no armazenamento de blobs e enviá-los para seu local.


## Pré-requisitos

Nesta seção, listamos os pré-requisitos necessários para usar este serviço. Examine-os cuidadosamente antes de enviar suas unidades.

### Contas de armazenamento

Você deve ter uma assinatura do Azure existente e uma ou mais contas de armazenamento **clássicas** para usar o serviço de Importação/Exportação. Cada trabalho pode ser usado para transferir dados para apenas uma conta de armazenamento clássica, ou por meio dela. Em outras palavras, um único trabalho de importação/exportação não pode estender-se por várias contas de armazenamento. Para obter informações sobre como criar uma nova conta de armazenamento, consulte [Como criar uma conta de armazenamento](storage-create-storage-account.md#create-a-storage-account).

### Tipos de blob

Você pode usar o serviço de Importação/Exportação do Azure para copiar os dados para blobs de **Bloco** ou blobs de **Página**. Por outro lado, você pode exportar apenas os blobs de **Bloco**, blobs de **Página** ou blobs de **Acréscimo** a partir do armazenamento do Azure usando esse serviço.

### Trabalho

Para começar o processo de importação para o armazenamento de Blob ou de exportação desse armazenamento, primeiro crie um trabalho, que pode ser um trabalho de importação ou um trabalho de exportação:

- Crie um trabalho de importação quando desejar transferir dados locais para blobs em sua conta de armazenamento do Azure.
- Crie um trabalho de exportação quando desejar transferir os dados armazenados atualmente como blobs na sua conta de armazenamento para discos rígidos enviados a você.

Ao criar um trabalho, você notifica o serviço de Importação/Exportação de que enviará um ou mais discos rígidos para um data center do Azure.

- Para um trabalho de importação, você enviará discos rígidos contendo seus dados.
- Para um trabalho de exportação, você enviará discos rígidos vazios.
- Você pode fornecer até 10 unidades de disco rígido por trabalho.

Você pode criar um trabalho de importação ou exportação usando o [portal Clássico](https://manage.windowsazure.com/) ou a [API REST de Importação/Exportação do Armazenamento do Azure](http://go.microsoft.com/fwlink/?LinkID=329099).

### Ferramenta de cliente

A primeira etapa na criação de um trabalho de **importação** é preparar a unidade que será enviada para a importação. Para preparar a unidade, você deve conectar-se a um servidor local e executar a Ferramenta de Cliente de Importação/Exportação do Azure no servidor local. Essa ferramenta de cliente facilita a cópia dos seus dados para a unidade, criptografando os dados na unidade com BitLocker e gerando os arquivos de diário de unidade.

Os arquivos de diário armazenam informações básicas sobre o trabalho e a unidade, como o número de série da unidade e o nome da conta de armazenamento. Este arquivo de diário não é armazenado na unidade. Ele é usado durante a criação do trabalho de importação. Detalhes do passo a passo sobre a criação de trabalho são fornecidos posteriormente neste artigo.

A ferramenta de cliente só é compatível com o sistema de operacional do Windows de 64 bits. Consulte a seção [Sistema Operacional](#operating-system) para obter as versões específicas do SO com suporte.

Baixe a versão mais recente da [ferramenta do cliente de Importação/Exportação do Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409). Para obter mais detalhes sobre como usar a Ferramenta de Importação/Exportação do Azure, consulte a [Referência da Ferramenta de Importação/Exportação do Azure](http://go.microsoft.com/fwlink/?LinkId=329032).

### Unidades de disco rígido

Somente discos rígidos SATA II/III internos de 3,5 polegadas têm suporte para uso com o serviço de Importação/Exportação. Você pode usar discos rígidos de até 8 TB. Para trabalhos de importação, somente o primeiro volume de dados na unidade será processado. O volume de dados deve ser formatado com NTFS. Ao copiar dados para o disco rígido, você poderá anexá-lo diretamente usando um conector SATA ou anexá-lo externamente usando um adaptador USB SATA II/III. Recomendamos usar um dos seguintes adaptadores USB SATA II/III externos:

- Anker 68UPSATAA-02BU
- Anker 68UPSHHDS-BU
- Startech SATADOCK22UE
- Sharkoon QuickPort XT HC

Se tiver um conversor que não está relacionado acima, você pode tentar executar a Ferramenta de Importação/Exportação do Azure usando seu conversor para preparar a unidade e ver se funciona, antes de adquirir um conversor com suporte.

> [AZURE.IMPORTANT] As unidades de disco rígido externas que vêm com um adaptador USB interno não são suportadas por esse serviço. Além disso, o disco dentro da proteção de um HDD externo não pode ser usado; não envie HDDs externos.

### Criptografia

Os dados na unidade devem ser criptografados com a Criptografia de Unidade de Disco BitLocker. Isso protegerá os dados enquanto eles estiverem em trânsito.

Para os trabalhos de importação, há duas maneiras de executar a criptografia. A primeira maneira é usar o parâmetro /encrypt ao executar a ferramenta do cliente durante a preparação da unidade. A segunda é habilitar manualmente a criptografia BitLocker na unidade e especificar a chave de criptografia na linha de comando da ferramenta do cliente durante a preparação da unidade.

Para os trabalhos de exportação, depois que seus dados forem copiados para as unidades, o serviço fará a criptografia da unidade usando o BitLocker antes de enviar para você. A chave de criptografia será fornecida a você por meio do portal Clássico.

### Sistema operacional

Você pode usar um dos seguintes Sistemas Operacionais de 64 bits para preparar o disco rígido usando a Ferramenta de Importação/Exportação do Azure antes de enviar a unidade para o Azure:

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. Todos esses sistemas operacionais dão suporte à Criptografia de Unidade de Disco BitLocker.

> [AZURE.IMPORTANT] <sup>1</sup>Se você estiver usando uma máquina Windows 10 para preparar seu disco rígido, baixe a versão mais recente da Ferramenta de Importação/Exportação do Azure.

### Locais

O serviço de Importação/Exportação do Azure dá suporte à cópia dos dados para e a partir de todas as contas de armazenamento do Azure Públicas. Você pode enviar discos rígidos para um dos seguintes locais. Se sua conta de armazenamento estiver em um local público do Azure não especificado aqui, um local alternativo de envio será fornecido quando você estiver criando o trabalho no portal Clássico ou por meio da API REST de Importação/Exportação.

Locais de envio com suporte:

- Leste dos EUA

- Oeste dos EUA

- Leste dos EUA 2

- Centro dos EUA

- Centro-Norte dos EUA

- Centro-Sul dos Estados Unidos

- Norte da Europa

- Europa Ocidental

- Ásia Oriental

- Sudeste Asiático

- Leste da Austrália

- Sudeste da Austrália

- Oeste do Japão

- Leste do Japão

- Índia Central


### Remessa

**Unidades de envio para o data center:**

Ao criar um trabalho de importação ou exportação, você receberá um endereço de envio de um dos locais com suporte para enviar suas unidades. O endereço de envio fornecido depende do local de sua conta de armazenamento, mas não pode ser o mesmo que o local da conta de armazenamento.

Você pode usar operadoras como FedEx, DHL, UPS ou o Serviço Postal para enviar suas unidades para o endereço de envio.

**Enviando unidades a partir do data center:**

Ao criar um trabalho de importação ou exportação, você deve fornecer um endereço de retorno para a Microsoft usar ao enviar de volta as unidades após a conclusão do trabalho. Certifique-se de que fornecer um endereço de retorno válido para evitar atrasos no processamento.

Você também deve fornecer um número de conta da transportadora FedEx ou DHL válido para ser usado pela Microsoft para enviar de volta as unidades. Um número de conta FedEx é necessário para enviar de volta as unidades de locais nos Estados Unidos e na Europa. Um número de conta DHL é necessário para enviar de volta as unidades de locais na Ásia e na Austrália. Você poderá criar uma conta da transportadora [FedEx](http://www.fedex.com/us/oadr/) (para os EUA e a Europa) ou [DHL](http://www.dhl.com/) (Ásia e Austrália) se não tiver uma. Se você já tiver um número de conta da transportadora, verifique se ele é válido.

Ao enviar seus pacotes, você deve seguir os [Termos de Serviço do Microsoft Azure](https://azure.microsoft.com/support/legal/services-terms/).

> [AZURE.IMPORTANT] Observe que a mídia física que está enviando talvez precise cruzar fronteiras internacionais. Você é responsável por garantir que seus dados e mídia física sejam importados e/ou exportados de acordo com as leis aplicáveis. Antes de enviar a mídia física, peça a seus consultores para verificar se a mídia e os dados podem ser enviados legalmente ao data center identificado. Isso ajudará a garantir que eles cheguem à Microsoft pontualmente.

## Como funciona o serviço de Importação/Exportação do Azure?

Você pode transferir dados entre o site local e o armazenamento de blobs do Azure usando o serviço de Importação/Exportação do Azure criando trabalhos e enviando as unidades de disco rígido para um data center do Azure. Cada unidade de disco rígido que você enviar estará associada a um único trabalho. Cada trabalho é associado uma única conta de armazenamento. Examine a [seção de pré-requisitos](#pre-requisites) cuidadosamente para conhecer as especificações desse serviço, como tipos de blob com suporte, tipos de disco, locais e envio.

Nesta seção, descreveremos em um nível alto, as etapas envolvidas na importação e exportação de trabalhos. Posteriormente na [seção Início Rápido](#quick-start), forneceremos instruções passo a passo para criar um trabalho de importação e exportação.

### Dentro de um trabalho de importação

Em um alto nível, um trabalho de importação envolve as seguintes etapas:

- Determine os dados a serem importados e o número de unidades necessárias.
- Identifique os blobs de destino de seus dados no Armazenamento de Blobs.
- Use a Ferramenta de Importação/Exportação do Azure para copiar seus dados para uma ou mais unidades de disco rígido e criptografá-los com o BitLocker.
- Crie um trabalho de importação na sua conta de armazenamento clássica de destino usando o portal Clássico ou a API REST de Importação/Exportação. Se você usar o portal Clássico, carregue os arquivos de diário da unidade.
- Forneça o endereço de retorno e o número da conta da transportadora a ser usado para enviar de volta as unidades para você.
- Envie as unidades de disco rígido para o endereço de envio fornecido durante a criação do trabalho.
- Atualize o número de acompanhamento de entrega nos detalhes do trabalho de importação de acompanhamento e envie o trabalho de importação.
- As unidades são recebidas e processadas no data center do Azure.
- Unidades são enviadas usando sua conta da transportadora para o endereço de retorno fornecido no trabalho de importação.

	![Figura 1: Importar o fluxo de trabalho](./media/storage-import-export-service/importjob.png)


### Dentro de um trabalho de Exportação

Em um alto nível, um trabalho de exportação envolve as seguintes etapas:

- Determine os dados a serem exportados e o número de unidades necessárias.
- Identifique os blobs de origem ou os caminhos do contêiner de seus dados no armazenamento de Blobs.
- Crie um trabalho de exportação na conta de armazenamento de origem usando o portal Clássico ou a API REST de Importação/Exportação.
- Especifique os blobs de origem ou os caminhos do contêiner de seus dados no trabalho de exportação.
- Forneça o endereço de retorno e o número da conta da transportadora a serem usados para enviar de volta as unidades para você.
- Envie as unidades de disco rígido para o endereço de envio fornecido durante a criação do trabalho.
- Atualize o número de acompanhamento de entrega nos detalhes do trabalho de exportação.
- As unidades são recebidas e processadas no data center do Azure.
- As unidades são criptografadas com o BitLocker e as chaves estão disponíveis no portal Clássico.
- As unidades são enviadas usando sua conta da transportadora para o endereço de retorno fornecido no trabalho de importação.

	![Figura 2: Exportar o fluxo de trabalho](./media/storage-import-export-service/exportjob.png)

### Exibindo o status do trabalho

Você pode acompanhar o status de seus trabalhos de importação ou exportação no portal Clássico. Navegue até sua conta de armazenamento no portal Clássico e clique na guia **Importação/Exportação**. Uma lista dos seus trabalhos será exibida na página. Você pode filtrar a lista por status do trabalho, nome do trabalho, tipo de trabalho ou número de controle.

Você verá um dos seguintes status de trabalho, dependendo de onde a unidade está no processo.

Status do Trabalho|Descrição
---|---
Criando|Seu trabalho foi criado, mas você ainda não forneceu as informações de remessa.
Remessa|Seu trabalho foi criado e você forneceu as informações de remessa. **Observação**: quando a unidade é entregue ao data center do Azure, o status ainda pode mostrar "Envio" por algum tempo. Depois que o serviço inicia a cópia dos seus dados, o status será alterado para "Transferir". Se você quiser ver o status mais específico de sua unidade, poderá usar a API REST de Importação/Exportação. 
Transferindo|Os dados estão sendo transferidos do seu disco rígido (para um trabalho de importação) ou para o seu disco rígido (para um trabalho de exportação).
Empacotamento|A transferência dos dados foi concluída e o seu disco rígido está sendo preparado para enviar de volta para você.
Concluído|Seu disco rígido foi enviado de volta para você.

### Tempo para processar o trabalho 

O tempo necessário para processar um trabalho de importação/exportação varia de acordo com diversos fatores, como tempo de envio, tipo do trabalho, tipo e tamanho dos dados sendo copiados, e tamanho dos discos fornecidos. O Serviço de Importação/Exportação não tem um SLA. Você pode usar a API REST para acompanhar o andamento do trabalho mais de perto. Há um parâmetro de porcentagem completa na operação Listar Trabalhos que fornece uma indicação do andamento da cópia. Contate-nos se precisar de uma estimativa para concluir um trabalho de importação/exportação de tempo crítico.

### Preços 

**Taxa de manuseio de unidade**

Há uma taxa de manuseio de unidade para cada unidade processada como parte do trabalho de importação ou exportação. Consulte os detalhes sobre o [Preços de Importação/Exportação do Azure](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Custos de envio**

Quando você envia unidades do Azure, você paga pelo custo de envio para a transportadora. Quando a Microsoft devolver as unidades, o custo do envio será cobrado na conta da transportadora que você forneceu no momento da criação do trabalho.

**Custos de transação**

Não há nenhum custo de transação ao importar dados para o armazenamento de blobs. Os encargos de saída padrão são aplicáveis quando dados são exportados do armazenamento de blobs. Para obter mais detalhes sobre os custos da transação, consulte [Preços da transferência de dados.](https://azure.microsoft.com/pricing/details/data-transfers/)

## Início rápido

Nesta seção, forneceremos instruções passo a passo para criar um trabalho de importação e exportação. Atenda a todos os [pré-requisitos](#pre-requisites) antes de prosseguir.

## Como faço para criar um Trabalho de Importação?

Crie um trabalho de importação para copiar os dados para sua conta de armazenamento do Azure enviando uma ou mais unidades contendo dados para o data center especificado. O trabalho de importação transmite detalhes sobre os discos rígidos, dados a serem copiados, conta de armazenamento de destino e envio de informações para o serviço de Importação/Exportação do Azure. A criação de um trabalho de importação é um processo de três etapas. Primeiro, prepare suas unidades usando a ferramenta de cliente de Importação/Exportação do Azure. Segundo, envie um trabalho de importação usando o portal Clássico. Terceiro, envie as unidades para o endereço de envio fornecido durante a criação do trabalho e atualize as informações de envio nos detalhes do seu trabalho.

> [AZURE.IMPORTANT] Você pode enviar somente um trabalho por conta de armazenamento. Cada unidade que você enviar pode ser importada para uma conta de armazenamento. Por exemplo, digamos que você deseja importar dados para duas contas de armazenamento. Você deve usar unidades de disco rígido separados para cada conta de armazenamento e criar trabalhos separados por conta de armazenamento.

### Preparar suas unidades	

A primeira etapa ao importar os dados usando o serviço de Importação/Exportação do Azure é preparar suas unidades usando a ferramenta do cliente de Importação/Exportação do Azure. Siga as etapas abaixo para preparar suas unidades.

1.	Identifique os dados a serem importados. Isso pode ser diretórios e arquivos autônomos no servidor local ou em um compartilhamento de rede.

2.	Determine o número de unidades que você precisará, dependendo do tamanho total dos dados. Adquira o número necessário de unidades de disco rígido SATA II/III de 3,5 polegadas.

3.	Identifique a conta de armazenamento de destino, contêiner, diretórios virtuais e blobs.

4.	Determine os diretórios e/ou os arquivos independentes que serão copiados para cada unidade de disco rígido.

5.	Use a [Ferramenta de Importação/Exportação do Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) para copiar seus dados para um ou mais discos rígidos.
	
	- A ferramenta de Importação/Exportação do Azure cria sessões de cópia para copiar os dados de origem para as unidades de disco rígido. Em uma sessão de cópia única, a ferramenta pode copiar um único diretório junto com seus subdiretórios, ou um único arquivo.

	- Talvez seja necessário diversas sessões de cópia se os dados de origem abrangerem vários diretórios.

	- Cada unidade de disco rígido que você preparar exige pelo menos uma sessão de cópia.

6.	Você pode especificar o parâmetro /encrypt para habilitar a criptografia Bitlocker na unidade de disco rígido. Como alternativa, você também pode habilitar a criptografia Bitlocker manualmente na unidade de disco rígido e fornecer a chave durante a execução da ferramenta.

7.	A Ferramenta de Importação/Exportação do Azure gera um arquivo de diário de unidade para cada unidade à medida que ela for preparada. O arquivo de diário de unidade é armazenado no seu computador local, e não na própria unidade. Você poderá carregar o arquivo de diário ao criar o trabalho de importação. Um arquivo de diário de unidade inclui a ID de unidade e a chave do BitLocker, bem como outras informações sobre a unidade. **Importante**: cada unidade de disco rígido preparada resultará em um arquivo de diário. Ao criar o trabalho de importação usando o portal Clássico, você deverá carregar todos os arquivos de diário das unidades que fazem parte do trabalho de importação. Unidades sem arquivos de diário não serão processadas.

8.	Não modifique os dados em unidades de disco rígido ou o arquivo de diário depois de concluir a preparação do disco.

Abaixo estão os comandos e exemplos para preparar o disco rígido usando a ferramenta de cliente de Importação/Exportação do Azure.

O comando PrepImport da ferramenta de cliente de Importação/Exportação do Azure para a primeira sessão de cópia para copiar um diretório:

	WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Exemplo:**

No exemplo abaixo, estamos copiando todos os arquivos e subdiretórios de H:\\Video para a unidade de disco rígido montada no X:. Os dados serão importados para a conta de armazenamento de destino especificada pela chave da conta de armazenamento e no contêiner de armazenamento denominado video. Se o contêiner de armazenamento não estiver presente, ele será criado. Esse comando também formatará e criptografará a unidade do disco rígido de destino.

	WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:storageaccountkey /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

O comando PrepImport da ferramenta de cliente de Importação/Exportação do Azure para as sessões de cópia subsequentes para copiar um diretório:

	WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

Para as sessões de cópia subsequentes para a mesma unidade de disco rígido, especifique o mesmo nome do arquivo de diário e forneça uma nova ID de sessão. Não é preciso fornecer a chave da conta de armazenamento a unidade de destino novamente, nem formate ou criptografar a unidade. Neste exemplo, estamos copiando a pasta H:\\Photo e seus subdiretórios para a mesma unidade de destino, no contêiner de armazenamento denominado photo.

	WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt

O comando PrepImport da ferramenta do cliente de Importação/Exportação do Azure para a primeira sessão de cópia para copiar um arquivo:

	WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

O comando PrepImport da ferramenta do cliente de Importação/Exportação do Azure para as sessões de cópia subsequentes para copiar um arquivo:

	WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]

**Lembre-se**: por padrão, os dados serão importados como Blobs de Blocos. Você pode usar o parâmetro /BlobType para importar dados como os Blobs de Página. Por exemplo, se você estiver importando arquivos VHD que serão montados como discos em uma VM do Azure, deverá importá-los como Blobs de Página. Se você não tiver certeza sobre qual tipo de blob usar, poderá especificar /blobType:auto e ajudaremos a determinar o tipo correto. Nesse caso, todos os arquivos vhd e vhdx serão importados como Blobs de Página e o restante será importado como Blobs de Bloco.

Veja mais detalhes sobre como usar a ferramenta do cliente de Importação/Exportação do Azure em [Preparando Discos Rígidos para a Importação](https://msdn.microsoft.com/library/dn529089.aspx).

Além disso, consulte o [Fluxo de Trabalho de Exemplo para Preparar os Discos Rígidos para um Trabalho de Importação](https://msdn.microsoft.com/library/dn529097.aspx) para obter instruções passo a passo mais detalhadas.

### Criar o trabalho de importação

1.	Depois de preparar a unidade, navegue até a conta de armazenamento no [portal Clássico](https://manage.windowsazure.com) e exiba o Painel. Em **Visão Rápida**, clique em **Criar um Trabalho de Importação**. Examine as etapas e marque a caixa de seleção para indicar que você preparou a unidade e que o arquivo de diário de unidade está disponível.

2.	Na Etapa 1, forneça as informações de contato da pessoa responsável por esse trabalho de importação e um endereço de retorno válido. Se desejar salvar dados de log detalhados para o trabalho de importação, marque a opção para **Salvar o log detalhado no meu contêiner de blob 'waimportexport'**.

3.	Na Etapa 2, carregue os arquivos de diário de unidade obtidos durante a etapa de preparação de unidade. Você precisa carregar um arquivo para cada unidade preparada.

	![Criar o trabalho de importação - Etapa 3](./media/storage-import-export-service/import-job-03.png)

4.	Na Etapa 3, digite um nome descritivo para o trabalho de importação. Observe que o nome fornecido pode conter somente letras minúsculas, números, hifens e sublinhados, deve começar com letra e não pode conter espaços. Você usará o nome escolhido para acompanhar os trabalhos enquanto eles estiverem em andamento e quando eles estiverem concluídos.

	Em seguida, selecione a região do data center na lista. A região do data center indica o data center e o endereço para o qual você deve enviar seu pacote. Consulte as Perguntas Frequentes abaixo para obter mais informações.

5. 	Na Etapa 4, selecione sua operadora de retorno na lista e insira o número da conta da operadora. A Microsoft usará essa conta para enviar de volta as unidades para você após a conclusão do seu trabalho de importação.

	Se você tiver um número de controle, selecione a transportadora de entrega na lista e insira seu número de controle.

	Se ainda não tiver um número de controle, escolha **Fornecerei minhas informações de remessa para este trabalho de importação após enviar meu pacote** e, em seguida, conclua o processo de importação.

6. Para inserir o número de controle após enviar o pacote, volte à página **Importação/Exportação** da sua conta de armazenamento no portal Clássico, selecione seu trabalho na lista e escolha **Informações do Envio**. Navegue pelo assistente e insira o número de acompanhamento na Etapa 2.

	Se o número de acompanhamento não está atualizado em 2 semanas após a criação do trabalho, este irá expirar.

	Você também pode atualizar o número da conta da transportadora na Etapa 2 do assistente, caso o trabalho esteja nas fases Criando, Enviando ou Transferindo. Você não poderá atualizar o número da conta da transportadora para o trabalho em questão quando ele estiver na fase de Empacotamento.

7. Você pode acompanhar o andamento do trabalho no painel do portal. Veja o que significa cada estado do trabalho na seção anterior em [Exibindo o status do trabalho](#viewing-your-job-status).

## Como posso criar um Trabalho de Exportação?

Crie um trabalho de exportação para notificar o serviço de Importação/Exportação que você enviará uma ou mais unidades vazias para o data center para que os dados possam ser exportados de sua conta de armazenamento para as unidades e as unidades, então, sejam enviadas para você.

### Preparar suas unidades

As pré-verificações a seguir são recomendadas para preparar suas unidades para um trabalho de exportação:

1. Verifique o número de discos necessários usando o comando PreviewExport da ferramenta Importação/Exportação do Azure. Para obter mais informações, consulte [Visualizando o Uso da Unidade para um Trabalho de Exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). A ferramenta ajuda você a visualizar o uso da unidade para os blobs que você selecionou, com base no tamanho das unidades que você pretende usar.

2. Verifique se você pode ler/gravar no disco rígido que será enviado para o trabalho de exportação.

### Criar o trabalho de Exportação

1. 	Para criar um trabalho de exportação, navegue até sua conta de armazenamento no [portal Clássico](https://manage.windowsazure.com) e exiba o Painel. Em **Visão Rápida**, clique em **Criar um Trabalho de Exportação** e continue com o assistente.

2. 	Na Etapa 2, forneça as informações de contato da pessoa responsável por esse trabalho de exportação. Se desejar salvar dados de log detalhados para o trabalho de exportação, marque a opção para **Salvar o log detalhado no meu contêiner de blob 'waimportexport'**.

3.	Na Etapa 3, especifique quais dados de blob deseja exportar da sua conta de armazenamento para a(s) unidade(s) em branco. Você pode optar por exportar todos os dados de blob na conta de armazenamento ou especificar quais blobs ou conjuntos de blobs serão exportados.

	Para especificar um blob para exportação, use o seletor **Igual a** e especifique o caminho relativo do blob, começando pelo nome do contêiner. Use *$root* para especificar o contêiner raiz.

	Para especificar todos os blobs que começam com um prefixo, use o seletor **Começa com** e especifique o prefixo, começando com uma barra '/'. O prefixo pode ser do nome do contêiner, o nome do contêiner completo ou o nome do contêiner completo seguido do prefixo do nome do blob.

	A tabela a seguir mostra exemplos de caminhos de blob válidos:

	Seletor|Caminho do Blob|Descrição
	---|---|---
	Começa com|/|Exporta todos os blobs na conta de armazenamento
	Começa com|/$root/|Exporta todos os blobs no contêiner raiz
	Começa com|/book|Exporta todos os blobs em qualquer contêiner que comece com o prefixo **book**
	Começa com|/music/|Exporta todos os blobs no contêiner **music**
	Começa com|/music/love|Exporta todos os blobs no contêiner **music** que comecem com o prefixo **love**
	Igual a|$root/logo.bmp|Exporta o blob **logo.bmp** no contêiner raiz
	Igual a|videos/story.mp4|Exporta o blob **story.mp4** no contêiner **vídeos**

	Você deve fornecer os caminhos de blob nos formatos válidos para evitar erros durante o processamento, como mostrado nesta captura de tela.

	![Criar o trabalho de exportação - Etapa 3](./media/storage-import-export-service/export-job-03.png)


4.	Na Etapa 4, digite um nome descritivo para o trabalho de exportação. O nome fornecido pode conter somente letras minúsculas, números, hifens e sublinhados, deve começar por letra e não pode conter espaços.

	A região do data center indicará o data center para o qual você deverá enviar seu pacote. Consulte as Perguntas Frequentes abaixo para obter mais informações.

5. 	Na Etapa 5, selecione sua operadora de retorno na lista e insira o número da conta da operadora. A Microsoft irá usar esta conta para enviar suas unidades de volta para você após a conclusão do seu trabalho de exportação.

	Se você tiver um número de controle, selecione a transportadora de entrega na lista e insira seu número de controle.

	Se ainda não tiver um número de controle, escolha **Fornecerei minhas informações de remessa para este trabalho de exportação após enviar meu pacote** e, em seguida, conclua o processo de exportação.

6. Para inserir o número de controle após enviar o pacote, volte à página **Importação/Exportação** da sua conta de armazenamento no portal Clássico, selecione seu trabalho na lista e escolha **Informações do Envio**. Navegue pelo assistente e insira o número de acompanhamento na Etapa 2.

	Se o número de acompanhamento não está atualizado em 2 semanas após a criação do trabalho, este irá expirar.

	Você também pode atualizar o número da conta da transportadora na Etapa 2 do assistente, caso o trabalho esteja nas fases Criando, Enviando ou Transferindo. Você não poderá atualizar o número da conta da transportadora para o trabalho em questão quando ele estiver na fase de Empacotamento.

	> [AZURE.NOTE] Se o blob a ser exportado estiver em uso no momento da cópia do disco rígido, o serviço de Importação/Exportação do Azure tira um instantâneo do blob e copia o instantâneo.

7.	Você pode acompanhar o andamento do trabalho no painel do portal Clássico. Veja o que significa cada estado do trabalho na seção anterior em “Exibindo o status do trabalho”.

8.	Depois de receber as unidades com os dados exportados, você poderá exibir e copiar as chaves do BitLocker geradas pelo serviço para a unidade. Navegue até sua conta de armazenamento no portal Clássico e clique na guia Importação/Exportação. Selecione o trabalho de exportação na lista e clique no botão Exibir Chaves. As chaves do BitLocker aparecem como mostrado abaixo:

	![Exibir chaves do BitLocker para um trabalho de exportação](.\media\storage-import-export-service\export-job-bitlocker-keys.png)

Leia a seção de perguntas frequentes abaixo, que abrange as perguntas mais comuns que os clientes encontram ao usar esse serviço.

## Perguntas frequentes ##

**Quanto tempo levará para copiar meus dados após a unidade atingir o data center?**

O tempo para copiar varia de acordo com diversos fatores, como tipo de trabalho, tipo e tamanho dos dados sendo copiados, tamanho dos discos fornecidos e carga de trabalho existente. Ele pode variar de alguns dias para duas semanas, dependendo desses fatores. Portanto, é difícil fornecer uma estimativa geral. O serviço tenta otimizar seu trabalho copiando várias unidades em paralelo quando possível. Se tiver um trabalho de importação/exportação de tempo crítico, entre em contato conosco para obter uma estimativa.

**Posso usar o serviço de Importação/Exportação do Azure com uma conta de armazenamento do Gerenciador de Recursos?**

Não, não é possível copiar os dados para ou a partir de uma conta de armazenamento do Gerenciador de Recursos usando diretamente o serviço de Importação/Exportação do Azure. O serviço só dá suporte às contas de armazenamento clássicas. O suporte para as contas de armazenamento do Gerenciador de Recursos será fornecido em breve. Como alternativa, você pode importar os dados para uma conta de armazenamento clássica e migrá-los para sua conta de armazenamento do Gerenciador de Recursos usando o [AzCopy](storage-use-azcopy.md) ou o CopyBlob.

**Posso copiar os Arquivos do Azure usando o serviço de Importação/Exportação do Azure?**

Não, o serviço de Importação/Exportação do Azure só dá suporte a Blobs de Bloco e Blobs de Página. Todos os outros tipos de armazenamento incluindo os Arquivos do Azure, Tabelas e Filas não têm suporte.

**O serviço de Importação/Exportação do Azure está disponível para as assinaturas CSP?**

Não, o serviço de Importação/Exportação do Azure não dá suporte a assinaturas CSP. O suporte será adicionado no futuro.

**Posso ignorar a etapa de preparação da unidade para um trabalho de importação ou posso me preparar uma unidade sem copiar?**

Qualquer unidade que você deseja enviar para importar os dados deve ser preparada usando a ferramenta do cliente de Importação/Exportação do Azure. Você deve usar a ferramenta de cliente para copiar dados para a unidade.

**É necessário executar alguma preparação de disco ao criar um trabalho de exportação?**

Não, mas algumas pré-verificações são recomendadas. Verifique o número de discos necessários usando o comando PreviewExport da ferramenta Importação/Exportação do Azure. Para obter mais informações, consulte [Visualizando o Uso da Unidade para um Trabalho de Exportação](https://msdn.microsoft.com/library/azure/dn722414.aspx). A ferramenta ajuda você a visualizar o uso da unidade para os blobs que você selecionou, com base no tamanho das unidades que você pretende usar. Verifique também se você pode ler e gravar no disco rígido que será enviado para o trabalho de exportação.

**O que acontecerá se eu enviar por engano uma unidade de disco rígido que não esteja em conformidade com os requisitos com suporte?**

O data center do Azure devolverá a unidade que não estiver em conformidade com os requisitos com suporte. Se apenas algumas unidades no pacote atenderem aos requisitos de suporte, elas serão processadas e as unidades que não atenderem aos requisitos serão retornadas para você.

**Posso cancelar meu trabalho?**

Você pode cancelar um trabalho quando seu status for Criando ou Enviando.

**Por quanto tempo posso exibir o status dos trabalhos concluídos no portal Clássico?**

Você pode exibir o status dos trabalhos concluídos por até 90 dias. Os trabalhos concluídos serão excluídos após 90 dias.

**Se eu quiser importar ou exportar mais de 10 unidades, o que devo fazer?**

Um trabalho de importação ou de exportação pode fazer referência a apenas 10 unidades em um único trabalho para o serviço de Importação/Exportação. Se quiser enviar mais de 10 unidades, você poderá criar vários trabalhos. Unidades que estão associadas com o mesmo trabalho devem ser enviadas juntas no mesmo pacote.

**Posso usar um adaptador USB-SATA que não esteja na lista recomendada?**

Se tiver um conversor que não está relacionado acima, você pode tentar executar a Ferramenta de Importação/Exportação do Azure usando seu conversor para preparar a unidade e ver se funciona, antes de adquirir um conversor com suporte.

**O serviço formata as unidades antes de retorná-las?**

Não. Todas as unidades são criptografadas com o BitLocker.

**Pode adquirir unidades para os trabalhos de importação/exportação da Microsoft?**

Não. Você precisará enviar suas próprias unidades para os trabalhos de importação e exportação.

**Depois do trabalho de importação ser concluído, como ficarão meus dados na conta de armazenamento? A hierarquia de diretório será preservada?**

Ao preparar um disco rígido para um trabalho de importação, o destino será especificado pelo parâmetro /dstdir:. É o contêiner de destino na conta de armazenamento para o qual os dados do disco rígido serão copiados. Dentro desse contêiner de destino, diretórios virtuais são criados para as pastas do disco rígido e blobs são criados para os arquivos.

**Se a unidade tiver arquivos que já existem na minha conta de armazenamento, o serviço substituirá os blobs existentes em minha conta de armazenamento?**

Ao preparar a unidade, você pode especificar se os arquivos de destino devem ser substituídos ou ignorados usando o parâmetro denominado /Disposition:<rename|no-overwrite|overwrite>. Por padrão, o serviço irá renomear os novos arquivos em vez de substituir os blobs existentes.

**A ferramenta do cliente de Importação/Exportação do Azure é compatível com os sistemas operacionais de 32 bits?** Não. A ferramenta do cliente só é compatível com os sistemas operacionais Windows de 64 bits. Consulte a seção Sistemas Operacionais em [pré-requisitos](#pre-requisites) para obter uma lista completa de versões do SO com suporte.

**Devo incluir algo diferente de unidade de disco rígido no meu pacote?**

Envie somente seus discos rígidos. Não inclua itens como cabos de alimentação ou cabos USB.

**É necessário enviar minhas unidades usando FedEx ou DHL?**

Você pode enviar as unidades para o data center usando qualquer transportadora conhecida, como FedEx, DHL, UPS ou Serviço Postal. No entanto, para enviar de volta as unidades para você a partir do data center, você deverá fornecer um número de conta FedEx nos EUA e UE ou um número de conta DHL nas regiões da Ásia e Austrália.

**Há restrições quanto ao envio de minha unidade internacionalmente?**

Observe que a mídia física que está enviando talvez precise cruzar fronteiras internacionais. Você é responsável por garantir que seus dados e mídia física sejam importados e/ou exportados de acordo com as leis aplicáveis. Antes de enviar a mídia física, verifique com seus consultores se a mídia e os dados podem ser enviados legalmente ao data center identificado. Isso ajudará a garantir que eles cheguem à Microsoft pontualmente.

**Ao criar um trabalho, o endereço de envio será um local diferente do meu local da conta de armazenamento. O que devo fazer?**

Alguns locais de armazenamento de conta são mapeados para os locais de entrega alternativos. Locais de envio disponíveis anteriormente também podem ser temporariamente mapeados para locais alternativos. Sempre verifique o endereço de envio fornecido durante a criação do trabalho antes de enviar suas unidades.

**Por que o status do meu trabalho no portal Clássico informa “Enviando” quando o site da transportadora mostra que meu pacote foi entregue?**

O status no portal Clássico muda de Enviando para Transferindo quando o processamento da unidade inicia. Se a unidade tiver alcançado a instalação, porém, ainda não tiver iniciado o processamento, o status do trabalho será exibido como Enviando.

**Ao enviar a unidade, a transportadora solicitará o nome de contato do data center e o número de telefone. O que devo fornecer?**

O número de telefone é fornecido a você durante a criação do trabalho. Se você precisar de um nome de contato, entre em contato conosco em waimportexport@microsoft.com e forneceremos essas informações.

**Posso usar o serviço de Importação/Exportação do Azure para copiar os dados do SharePoint e as caixas de correio PST para O365?**

Consulte [Importar arquivos PST ou dados do SharePoint para o Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Posso usar o serviço de Importação/Exportação do Azure para copiar meus backups offline para o Serviço de Backup do Azure?**

Consulte o [Fluxo de trabalho do backup offline no Backup do Azure](../backup/backup-azure-backup-import-export.md).

## Consulte também:

- [Configurando a ferramenta do cliente de Importação/Exportação do Azure](https://msdn.microsoft.com/library/dn529112.aspx)

- [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0629_2016-->