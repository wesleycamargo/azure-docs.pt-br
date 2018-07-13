---
title: Recurso de armazenamento imutável do armazenamento de Blobs do Azure (versão prévia) | Microsoft Docs
description: O Armazenamento do Azure agora oferece suporte a WORM para armazenamento de objeto de Blob que permite armazenar dados em um estado não apagável nem modificável por um intervalo de tempo especificado pelo usuário. Esse recurso habilita organizações em vários setores regulamentados, especialmente as organizações corretoras de valores para armazenar dados em conformidade com a SEC 17a-4(f) e outras normas.
services: storage
author: sangsinh
manager: twooley
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: sangsinh
ms.openlocfilehash: 195537b271c442b954d6d6e6fa8d1491c07822e8
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970237"
---
# <a name="immutable-storage-feature-of-azure-blob-storage-preview"></a>Recurso de armazenamento imutável do armazenamento de Blobs do Azure (versão prévia)

O recurso de armazenamento imutável para o recurso de Blobs do Azure permite que os usuários armazenem dados comercialmente críticos de negócios no armazenamento de Blobs do Azure em um estado WORM (Write Once Read Many). Esse estado o torna não apagável e não modificável para de um intervalo de tempo especificado pelo usuário. Blobs podem ser criados e lidos, mas não modificados ou excluídos durante o intervalo de retenção.

## <a name="overview"></a>Visão geral

Esse recurso de armazenamento imutável habilita organizações em vários setores regulamentados, especialmente as organizações corretoras de valores para armazenar dados em conformidade com a SEC 17a-4(f) e outras normas.

Os aplicativos típicos incluem:

- **Conformidade normativa**: o armazenamento imutável do recurso de Blobs do Azure foi projetado para ajudar as instituições financeiras e os setores relacionados a abordarem a SEC 17a-4(f), CFTC 1.31©-(d), FINRA, etc.

- **Proteger a retenção de documentos**: os usuários recebem máxima proteção de dados, uma vez que o serviço de armazenamento de Blob garante que os dados não podem ser modificados ou excluídos por nenhum usuário, incluindo aqueles com privilégios administrativos da conta.

- **Retenção legal**: o armazenamento imutável de blobs do Azure permite que os usuários armazenem informações confidenciais cruciais para um litígio judicial ou investigação criminal, etc., em um estado à prova de adulteração pela duração desejada.

O recurso de armazenamento imutável permite:

- **Suporte à política de retenção com base em tempo:** os usuários definem políticas para armazenar dados para um intervalo de tempo especificado.

- **Suporte da política de retenção legal:** quando o intervalo de retenção não for conhecido, os usuários podem definir retenções legais para armazenar dados imutáveis até que a retenção legal seja limpa.  Quando uma retenção legal é definida, blobs podem ser criados e lidos, mas não modificados ou excluídos. Cada restrição legal é associada uma marca alfanumérica definida pelo usuário que é usada como uma cadeia de caracteres do identificador (como uma ID de caso).

- **Suporte para todas as camadas de blob:** políticas WORM são independentes da camada de armazenamento de Blobs do Azure e se aplicam a todas as camadas, frequentes, esporádicas e arquivamento. Isso permite que os clientes armazenem os dados na camada com o custo mais otimizado para a maioria das suas cargas de trabalho, mantendo a imutabilidade de dados

- **Configuração de nível do contêiner:** o recurso de armazenamento imutável permite aos usuários configurar políticas de retenção baseadas em tempo e marcas de retenção legal no nível do contêiner.  Os usuários podem criar e bloquear políticas de retenção baseadas em tempo, estender intervalos de retenção, definir e controlar retenções legais, etc., por meio das configurações simples de nível de contêiner.  Essas políticas serão aplicadas a todos os blobs no contêiner, novos e existentes.

- **Suporte ao log de auditoria:** cada contêiner contém um log de auditoria mostrando até cinco comandos de retenção baseados em tempo para políticas de retenção baseadas em tempo bloqueadas com um máximo de três logs para extensões de intervalo de retenção.  Para a retenção baseada em tempo, o log contém a ID de usuário, o tipo de comando, os carimbos de data/hora e o intervalo de retenção. Para a retenções legais, o log contém a ID de usuário, o tipo de comando, os carimbos de data/hora e as marcas da retenção legal. Esse log é retido pelo tempo de vida do contêiner pela norma regulatória SEC 17a-4(f). Um log mais abrangente de todas as atividades do plano de controle pode ser encontrado no [Log de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). É responsabilidade do usuário armazenar esses logs de modo persistente, uma vez que eles podem ser necessários para normas ou outros fins.

 O recurso está habilitado em todas as regiões públicas do Azure.

## <a name="how-it-works"></a>Como ele funciona

O armazenamento imutável para Blobs do Azure oferece suporte a dois tipos de WORM ou políticas imutáveis: retenção e retenções legais baseadas em tempo. Consulte a seção [Introdução](#Getting-started) para obter detalhes sobre como criar essas políticas imutáveis.
Quando uma política de retenção baseada em tempo ou uma retenção legal é aplicada em um contêiner, todos os blobs existentes serão movidos para o estado imutável (proteção contra gravação e exclusão). Todos os novos blobs carregados no contêiner também serão movidos para o estado imutável.

> [!IMPORTANT]
> Uma política de retenção baseada em tempo deve ser *bloqueada* para o blob ficar em um estado imutável (protegido contra gravação e exclusão) estado para SEC 17a-4(f) e outros conformidades regulatórias. É recomendável que a política seja bloqueada em um período razoável de tempo, geralmente dentro de 24 horas. Não é recomendável o uso do estado *desbloqueado* para qualquer finalidade diferente avaliações de recurso de curto prazo.

 Quando uma política de retenção baseada em tempo é aplicada em um contêiner, todos os blobs no contêiner permanecerão no estado imutável durante o período *efetivo* de retenção. O período efetivo de retenção de blobs existentes é igual à diferença entre a hora de criação do blob e o intervalo de retenção especificado pelo usuário. Para novos blobs, o período efetivo de retenção é igual ao intervalo de retenção especificado pelo usuário. Como os usuários podem alterar o intervalo de retenção, o valor mais recente do intervalo de retenção especificado pelo usuário será usado para calcular o período efetivo de retenção.

> [!TIP]
> Exemplo: o usuário cria uma política de retenção baseada em tempo com um intervalo de retenção de cinco anos.
> Há um blob existente, testblob1, no contêiner que foi criado um ano atrás. O período efetivo de retenção para testblob1 será de quatro anos.
> Um novo blob, testblob2, agora é carregado no contêiner. O período efetivo de retenção para esse novo blob será de cinco anos.

### <a name="legal-holds"></a>Retenções legais

No caso de retenções legais, todos os blobs existentes e novos permanecerão no estado imutável até que a retenção legal seja limpa.
Para obter mais informações sobre como definir e limpar retenções legais, consulte a seção [Introdução](#Getting-started) para obter detalhes.

Um contêiner pode ter uma retenção legal e uma política de retenção baseada em tempo simultaneamente. Todos os blobs nesse contêiner permanecerão no estado imutável até que todos os controles legais sejam limpos, mesmo se seu período efetivo de retenção tenha expirado. Por outro lado, um blob permanecerá em um estado imutável até que o período efetivo de retenção expire, embora todas as restrições legais tenham sido limpas.
A tabela a seguir mostra os tipos de operações de blob que serão desabilitadas para os diferentes cenários imutáveis.
Consulte a documentação da [API do serviço de Blob do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) para obter os detalhes de API de REST do Blob.

|Cenário  |Estado do trabalho  |Operações de blob não permitidas  |
|---------|---------|---------|
|O intervalo efetivo de retenção no blob ainda não expirou e/ou a retenção legal está definida     |Imutável: protegido contra exclusão e gravação         |Excluir contêiner, excluir Blob, colocar Blob1, colocar bloco, colocar lista de blocos, definir metadados de Blob, colocar página, definir propriedades de Blob, Blob de instantâneo, Blob de cópia incremental, acrescentar bloco         |
|O intervalo efetivo de retenção no blob expirou     |Protegido apenas contra gravação (é permitido excluir operações)         |Colocar Blob, colocar bloco, colocar lista de blocos, definir metadados do Blob, colocar página, definir propriedades do Blob, Blob de instantâneo, cópia incremental do Blob, acrescentar bloco         |
|Todas as restrições legais limpas e nenhuma política de retenção baseada em tempo no contêiner     |Mutável         |Nenhum         |
|Nenhuma política WORM criada (retenção baseada em tempo ou retenção legal)     |Mutável         |Nenhum         |

> [!NOTE]
> O primeiro Colocar Blob e as operações Colocar lista de blocos e Colocar bloco necessárias para criar um blob são permitidos nos dois primeiros cenários da tabela acima; todas as operações subsequentes não são permitidas.
> O recurso de armazenamento imutável só está disponível nas contas de armazenamento de blob e GPv2 e deve ser criado por meio do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="pricing"></a>Preços

Não há nenhum custo adicional para usar esse recurso, e os dados imutáveis são precificados da mesma maneira como os dados normais e mutáveis. Consulte a [página Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) para obter os detalhes relacionados a preços.

### <a name="restrictions"></a>Restrições

As seguintes restrições se aplicam durante a versão prévia:

- **Não armazene dados críticos de produção ou de negócios**
- Todas as restrições de versão prévia/NDA se aplicam

## <a name="getting-started"></a>Introdução

Há suporte para o armazenamento imutável do Azure para Blobs do Azure nas versões mais recentes do [portal do Azure](http://portal.azure.com), o [CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e Azure [PowerShell](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May2018)

### <a name="azure-portal"></a>Portal do Azure

1. Crie um novo contêiner ou selecione um existente para armazenar os blobs que precisam ser mantidos no estado imutável.
 O contêiner deve estar em uma conta de armazenamento GPv2.
2. Clique na Política de Acesso nas configurações do contêiner e depois clique em **+ Adicionar política** na política de **Armazenamento de Blob imutável**, conforme ilustrado abaixo.

    ![Portal](media/storage-blob-immutable-storage/portal-image-1.png)

3. Para habilitar a retenção baseada em tempo, escolha Retenção baseada em tempo no menu suspenso.

    ![Retenção](media/storage-blob-immutable-storage/portal-image-2.png)

4. Insira o intervalo de retenção desejado em dias (o mínimo é um dia)

    ![Intervalo de retenção](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Como você pode ver acima, o estado inicial da política está desbloqueado. Isso permite testar o recurso com um intervalo de retenção menor e fazer alterações à política antes de bloqueá-lo. O bloqueio é essencial para conformidade com a norma SEC 17a-4 e outras.

5. Bloqueie a política clicando com botão direito em ..., e o menu a seguir será exibido:

    ![Bloquear política](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    Clique em Bloquear política e o estado da política agora será mostrado como bloqueado. Uma vez bloqueada, a política não mais pode ser excluída, e somente as extensões do intervalo de retenção serão permitidas.

6. Para habilitar as restrições legais, clique em + Adicionar política e escolha Retenção legal no menu suspenso

    ![Retenção legal](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. Criar uma retenção legal com uma ou mais marcas

    ![Definir marcas de retenção legal](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

### <a name="cli-20"></a>CLI 2.0

Instalar a [Extensão da CLI](http://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) com `az extension add -n storage-preview`

Caso já tenha a extensão instalada, use o seguinte comando para habilitar o recurso de armazenamento imutável: `az extension update -n storage-preview`

O recurso está incluído nos seguintes grupos de comando (executar “-h” nelas para ver os comandos): `az storage container immutability-policy` e `az storage container legal-hold`.

### <a name="powershell"></a>PowerShell

O recurso de armazenamento imutável tem suporte no [PowerShell versão 4.4.0-versão prévia](https://github.com/Azure/azure-powershell/releases/tag/Azure.Storage.v4.4.0-preview-May20180).
Para habilitar o recurso, execute as seguintes etapas:

1. Verifique se você tem a versão mais recente do PowerShellGet instalado usando `Install-Module PowerShellGet –Repository PSGallery –Force`
2. Remova as instalações anteriores do Azure PowerShell
3. Instale o AzureRM (o Azure pode ser instalado de modo semelhante neste repositório) `Install-Module AzureRM –Repository PSGallery –AllowClobber`
4. Instale a versão prévia da visualização dos cmdlets do plano de gerenciamento de armazenamento`Install-Module -Name AzureRM.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`

Um código do PowerShell de exemplo que ilustra o uso de recurso é fornecido abaixo.

## <a name="client-libraries"></a>Bibliotecas de cliente

Há suporte para o armazenamento imutável do recurso de Blobs do Azure nas seguintes versões de biblioteca de cliente

- [Biblioteca de cliente .net (versão 7.2.0-versão prévia e superior](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Biblioteca de cliente node.js (versão 4.0.0 e superior)](https://www.npmjs.com/package/azure-arm-storage)
- [Biblioteca de cliente do Python (versão 2.0.0 do Release Candidate 2 e superior)](https://pypi.org/project/azure-mgmt-storage/2.0.0rc1/)

## <a name="supported-values"></a>Valores com suporte

- O intervalo de retenção mínimo é de um dia, o máximo é de 400 anos
- Para uma determinada conta de armazenamento, o número máximo de contêineres por conta de armazenamento com políticas imutáveis bloqueadas é de 1.000
- Para uma determinada conta de armazenamento, o número máximo de contêineres com uma configuração de retenção legal é 1000
- Para um contêiner específico, o número máximo de marcas de retenção legal é 10
- O comprimento máximo de uma marca de retenção legal é 23 caracteres alfanuméricos; o comprimento mínimo é três caracteres
- Para um contêiner específico, o número máximo de extensões de intervalo de retenção permitido para políticas imutáveis bloqueadas é três
- Para um contêiner específico com uma política imutável bloqueada, há um máximo de cinco logs de política de retenção baseada em tempo e um máximo de 10 logs de política de retenção legal que são mantidos pela duração do contêiner.

## <a name="faq"></a>Perguntas frequentes

**O recurso se aplica apenas a blobs de blocos ou também a blobs de páginas e de acréscimo?**

O recurso de armazenamento imutável para blobs pode ser usado com qualquer tipo de blob.  No entanto, é recomendado que o recurso seja usado principalmente para blobs de blocos. Ao contrário dos blobs de blocos, blobs de páginas e blobs de acréscimo precisam ser criados fora de um contêiner WORM e depois copiados nele.  Uma vez copiados em um contêiner WORM, não são mais permitidos *acréscimos* ou alterações a um blob de acréscimo em um blob de páginas.

**Sempre preciso criar uma nova conta de armazenamento para esse recurso?**

Você pode usar o recurso de armazenamento imutável com contas GPv2 existentes ou em novas contas de armazenamento se o tipo de conta for GPv2. Esse recurso só está disponível com o armazenamento de blob.

**O que acontece se eu tentar excluir um contêiner com uma política de retenção baseada em tempo ou retenção legal *bloqueada*?**

A operação Excluir contêiner falhará se for pelo menos um blob com uma política de retenção baseada em tempo ou retenção legal bloqueada. A operação Excluir contêiner será bem-sucedida se não houver nenhum blob com um intervalo de retenção ativo e não haja nenhuma retenção legal. Você deve excluir os blobs antes de excluir o contêiner.

**O que acontece se eu tentar excluir uma conta de armazenamento com um contêiner WORM que tem uma política de retenção baseada em tempo ou retenção legal *bloqueada*?**

A exclusão da conta de armazenamento falhará se houver pelo menos um contêiner WORM com retenção legal ou um blob com um intervalo de retenção ativa.  Todos os contêineres WORM devem ser excluídos antes que a conta de armazenamento possa ser excluída.  Consulte a Pergunta #2 para obter informações sobre a exclusão do contêiner.

**Posso mover os dados entre níveis diferentes de blob (frequente, esporádico, passivo) quando o blob estiver no estado imutável?**

Sim, você pode usar o comando Definir nível de Blob para mover dados entre as camadas de blob, mantendo os dados no estado imutável. O recurso de armazenamento imutável é suportado nas camadas de Blobs frequentes, esporádicos e passivos.

**O que acontece se eu não conseguir pagar e meu intervalo de retenção não expirou?**

No caso de falta de pagamento, as políticas normais de retenção de dados serão aplicadas como cortesia estipulada especificada nos termos e condições do seu contrato com a Microsoft.

**Há oferta de um período de avaliação ou de cortesia para apenas experimentar o recurso?**

Sim, quando uma política de retenção baseada em tempo é criada, ela ficará em um estado *desbloqueado*. Nesse estado, você pode fazer qualquer alteração desejada para o intervalo de retenção, como aumentar ou diminuir e até mesmo excluir a política. Uma vez que a política está bloqueada, ela permanecerá bloqueada indefinidamente, evitando a exclusão. Além disso, o intervalo de retenção não pode ser diminuído quando a política estiver bloqueada. É altamente recomendável que você use o estado *desbloqueado* apenas para fins de avaliação e bloqueie a política dentro de um período de 24 horas para não correr o risco de não conformidade com a SEC 17a-4(f) e outras normas.

**O recurso está disponível nas nuvens nacionais e governamentais?**

Atualmente, o recurso de armazenamento imutável só está disponível em regiões públicas do Azure. Envie email para azurestoragefeedback@microsoft.com caso tenha interesse em uma nuvem nacional específica.

## <a name="sample-code"></a>Exemplo de código

Um exemplo de script do PowerShell é fornecido abaixo para referência.
Esse script cria uma nova conta de armazenamento e contêiner; depois mostra como definir e limpar retenções legais, criar e bloquear uma política de retenção baseada em tempo (também conhecido como ImmutabilityPolicy), estender o intervalo de retenção, etc.

```powershell
\$ResourceGroup = "\<Enter your resource group\>”

\$StorageAccount = "\<Enter your storage account name\>"

\$container = "\<Enter your container name\>"

\$container2 = "\<Enter another container name\>”

\$location = "\<Enter the storage account location\>"

\# Login to the Azure Resource Manager Account

Login-AzureRMAccount

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage"

\# Create your Azure Resource Group

New-AzureRmResourceGroup -Name \$ResourceGroup -Location \$location

\# Create your Azure storage account

New-AzureRmStorageAccount -ResourceGroupName \$ResourceGroup -StorageAccountName
\$StorageAccount -SkuName Standard_LRS -Location \$location -Kind Storage

\# Create a new container

New-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container

\# Create Container 2 with Storage Account object

\$accountObject = Get-AzureRmStorageAccount -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount

New-AzureRmStorageContainer -StorageAccount \$accountObject -Name \$container2

\# Get container

Get-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container

\# Get Container with Account object

\$containerObject = Get-AzureRmStorageContainer -StorageAccount \$accountObject
-Name \$container

\#list container

Get-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount

\#remove container (Add -Force to dismiss prompt)

Remove-AzureRmStorageContainer -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container2

\#with Account object

Remove-AzureRmStorageContainer -StorageAccount \$accountObject -Name
\$container2

\#with Container object

\$containerObject2 = Get-AzureRmStorageContainer -StorageAccount \$accountObject
-Name \$container2

Remove-AzureRmStorageContainer -InputObject \$containerObject2

\#Set LegalHold

Add-AzureRmStorageContainerLegalHold -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container -Tag tag1,tag2

\#with Account object

Add-AzureRmStorageContainerLegalHold -StorageAccount \$accountObject -Name
\$container -Tag tag3

\#with Container object

Add-AzureRmStorageContainerLegalHold -Container \$containerObject -Tag tag4,tag5

\#Clear LegalHold

Remove-AzureRmStorageContainerLegalHold -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -Name \$container -Tag tag2

\#with Account object

Remove-AzureRmStorageContainerLegalHold -StorageAccount \$accountObject -Name
\$container -Tag tag3,tag5

\#with Container object

Remove-AzureRmStorageContainerLegalHold -Container \$containerObject -Tag tag4

\# create/update ImmutabilityPolicy

\#\# with account/container name

Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -ContainerName \$container
-ImmutabilityPeriod 10

\#with Account object

Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container -ImmutabilityPeriod 1 -Etag \$policy.Etag

\#with Container object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -ImmutabilityPeriod 7

\#\# with ImmutabilityPolicy object

Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy \$policy
-ImmutabilityPeriod 5

\#get ImmutabilityPolicy

Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName \$ResourceGroup
-StorageAccountName \$StorageAccount -ContainerName \$container

\#with Account object

Get-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container

\#with Container object

Get-AzureRmStorageContainerImmutabilityPolicy -Container \$containerObject

\#Lock ImmutabilityPolicy (Add -Force to dismiss prompt)

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy
\$policy -force

\#\# with account/container name

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-Etag \$policy.Etag

\#with Account object

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -StorageAccount
\$accountObject -ContainerName \$container -Etag \$policy.Etag

\#with Container object

\$policy = Lock-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -Etag \$policy.Etag -force

\#Extend ImmutabilityPolicy

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy
\$policy -ImmutabilityPeriod 11 -ExtendPolicy

\#\# with account/container name

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-ImmutabilityPeriod 11 -Etag \$policy.Etag -ExtendPolicy

\#with Account object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -StorageAccount
\$accountObject -ContainerName \$container -ImmutabilityPeriod 12 -Etag
\$policy.Etag -ExtendPolicy

\#with Container object

\$policy = Set-AzureRmStorageContainerImmutabilityPolicy -Container
\$containerObject -ImmutabilityPeriod 13 -Etag \$policy.Etag -ExtendPolicy

\#Remove ImmutabilityPolicy (Add -Force to dismiss prompt)

\#\# with ImmutabilityPolicy object

\$policy = Get-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container

Remove-AzureRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy \$policy

\#\# with account/container name

Remove-AzureRmStorageContainerImmutabilityPolicy -ResourceGroupName
\$ResourceGroup -StorageAccountName \$StorageAccount -ContainerName \$container
-Etag \$policy.Etag

\#with Account object

Remove-AzureRmStorageContainerImmutabilityPolicy -StorageAccount \$accountObject
-ContainerName \$container -Etag \$policy.Etag

\#with Container object

Remove-AzureRmStorageContainerImmutabilityPolicy -Container \$containerObject
-Etag \$policy.Etag
```