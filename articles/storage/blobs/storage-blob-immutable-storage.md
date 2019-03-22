---
title: Armazenamento imutável para Azure Storage Blobs | Microsoft Docs
description: O Armazenamento do Microsoft Azure oferece suporte WORM (Gravar uma vez, reutilizar frequentemente) para o armazenamento de Blob (objeto) que possibilita que os usuários armazenem dados em um estado não apagável e não modificável para um intervalo especificado.
services: storage
author: xyh1
ms.service: storage
ms.topic: article
ms.date: 03/02/2019
ms.author: hux
ms.subservice: blobs
ms.openlocfilehash: 86e28c3561968b1411a3baa9ec0daecfab6ac73f
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202870"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>Armazenar dados comercialmente críticos no Armazenamento de Blobs do Azure

Armazenamento imutável para o armazenamento de BLOBs do Azure permite aos usuários armazenar objetos de dados críticos de negócios em um estado WORM (Write Once, Read Many). Esse estado torna os dados não apagáveis e não modificáveis para um intervalo especificado pelo usuário. Objetos de blob podem ser criados e lido, mas não modificados ou excluídos, para a duração do intervalo de retenção. Armazenamento imutável está habilitado para uso geral v2 e contas de armazenamento de BLOBs em todas as regiões do Azure.

## <a name="overview"></a>Visão geral

Armazenamento imutável ajuda a organização de saúde, instituições financeiras e setores relacionados – particularmente corretores que as organizações – armazenar dados com segurança. Ele também pode ser utilizado em qualquer cenário para proteger dados críticos contra modificação ou exclusão. 

Os aplicativos típicos incluem:

- **Conformidade normativa**: O armazenamento imutável para armazenamento de Blobs do Azure ajuda as organizações a atender às regulamentações SEC 17a-4 (f), CFTC 1.31 (d), FINRA e outras. Esses requisitos normativos podem ser baixados por meio de endereços de armazenamento como imutável de detalhes de um white paper técnico por Cohasset associa a [Portal de confiança do serviço de Microsoft](https://aka.ms/AzureWormStorage). O [Central de confiabilidade do Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) contém informações detalhadas sobre nossas certificações de conformidade.

- **Retenção segura de documentos**: Armazenamento imutável para o armazenamento de BLOBs do Azure garante que os dados não podem ser modificados ou excluídos por qualquer usuário, incluindo usuários com privilégios administrativos da conta.

- **Retenção legal**: Armazenamento imutável para o armazenamento de BLOBs do Azure permite aos usuários armazenar informações confidenciais que é essenciais para o uso de litígio ou business em um estado de prova de adulteração, a duração desejada até que o bloqueio seja removido. Este recurso não se limita apenas aos casos de uso legal, mas pode também ser considerado uma espera baseada em evento ou um bloqueio de enterprise, em que a necessidade de proteger os dados com base em política corporativa ou de gatilhos de evento é necessária.

Armazenamento imutável suporta o seguinte:

- **[Suporte à política de retenção baseada em tempo](#time-based-retention)**: Os usuários podem definir políticas para armazenar dados de um intervalo especificado. Quando uma política de retenção baseada em tempo é definido, blobs podem ser criados e lido, mas não modificados ou excluídos. Depois que o período de retenção expirar, blobs podem ser excluídos, mas não substituídos.

- **[Suporte à política de retenção legal](#legal-holds)**: Se o intervalo de retenção não for conhecido, os usuários podem definir Retenções legais para armazenar dados immutably até que a retenção legal é limpo.  Quando uma política de retenção legal for definida, blobs podem ser criados e lido, mas não modificados ou excluídos. Cada bloqueio legal é associado uma definida pelo usuário alfanumérica marca (por exemplo, uma ID de caso, o nome do evento, etc.) que é usada como uma cadeia de caracteres do identificador. 

- **Suporte para todas as camadas de blob**: As políticas do WORM são independentes da camada de armazenamento do Azure Blob e se aplicam a todas as camadas: frequente, esporádica e de arquivo. Os usuários podem fazer a transição dos dados de suas cargas de trabalho para a camada de custo mais otimizado, mantendo a imutabilidade dos dados.

- **Configuração de nível do contêiner**: Os usuários podem configurar políticas de retenção com base no tempo e marcas de retenção legal no nível do contêiner. Usando configurações de nível de contêiner simples, os usuários podem criar e políticas de retenção baseada em tempo de bloqueio, estender os intervalos de retenção, conjunto e retenções legais clara e muito mais. Essas políticas se aplicam a todos os blobs no contêiner, existentes e novos.

- **Suporte de registro em log de auditoria**: Cada contêiner inclui um registro de auditoria. Ele mostra até cinco comandos de retenção baseados em tempo para políticas de retenção com base em tempo bloqueado, com um máximo de três logs para extensões de intervalo de retenção. Para retenção baseada em tempo, o log contém o ID do usuário, o tipo de comando, os registros de data e hora e o intervalo de retenção. Para retenções legais, o log contém as identificações de ID do usuário, tipo de comando, carimbos de tempo e retenção legal. Este registro é retido durante a vida útil do contêiner, de acordo com as diretrizes regulamentares da SEC 17a-4 (f). O [Log de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md) mostra um log mais abrangente de todas as atividades de plano de controle; permitindo [Logs de diagnóstico do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md) retém e mostra as operações do plano de dados. É responsabilidade do usuário armazenar esses logs de forma persistente, conforme o necessário para regulamentações ou outros fins.

## <a name="how-it-works"></a>Como ele funciona

O armazenamento imutável para armazenamento de Blobs do Azure oferece suporte a dois tipos de políticas WORM ou imutáveis: retenção baseada em tempo e retenções legais. Quando uma política de retenção baseada em tempo ou retenção legal for aplicada em um contêiner, todos os blobs existentes movem para um estado imutável do WORM em menos de 30 segundos. Todos os novos blobs que são carregados para esse contêiner também serão movidos para o estado imutável. Depois que todos os blobs foram movidos para o estado imutável, a política imutável é confirmada e todos os substituir ou excluam não são permitidas operações para objetos novos e existentes no contêiner imutável.

### <a name="time-based-retention"></a>Retenção baseada em tempo

> [!IMPORTANT]
> Uma política de retenção baseada em tempo deve ser *bloqueada* para que o blob esteja em um estado imutável (gravar e excluir protegido) para a SEC 17a-4 (f) e outras normas de conformidade. Recomendamos que você bloqueie a política em um período de tempo razoável, geralmente dentro de 24 horas. Não recomendamos usar o *desbloqueada* estado para qualquer finalidade que não seja de avaliações de recurso de curto prazo.

Quando uma política de retenção baseada em tempo é aplicada em um contêiner, todos os blobs no contêiner ficará no estado imutável durante o *efetivo* período de retenção. O período de retenção em vigor para os blobs existentes é igual à diferença entre a hora de modificação do blob e o intervalo de retenção especificado pelo usuário.

Para novos blobs, o período efetivo de retenção é igual ao intervalo de retenção especificado pelo usuário. Como os usuários podem estender o intervalo de retenção, o armazenamento imutável usa o valor mais recente do intervalo de retenção especificado pelo usuário para calcular o período de retenção efetivo.

> [!TIP]
> **Exemplo:** Um usuário cria uma política de retenção baseada em tempo com um intervalo de retenção de cinco anos.
>
> O blob existente nesse contêiner _testblob1_, foi criado um ano atrás. O período de retenção efetiva _testblob1_ é de quatro anos.
>
> Um novo blob, _testblob2_, agora é carregado no contêiner. O período de retenção em vigor para esse novo blob é de cinco anos.

### <a name="legal-holds"></a>Retenções legais

Quando você define uma retenção legal, todos os blobs novos e existentes permanecem em um estado imutável até que a retenção legal é limpo. Para obter mais informações sobre como definir e retenções legais clara, consulte a seção [guia de Introdução](#getting-started).

Um contêiner pode ter uma retenção legal e uma política de retenção baseada no tempo ao mesmo tempo. Todos os blobs nesse contêiner permanecem no estado imutável até que todas as retenções legais sejam apagadas, mesmo que o período de retenção efetivo tenha expirado. Por outro lado, um blob permanece em um estado imutável até que o período de retenção efetivo expire, mesmo que todas as retenções legais tenham sido compensadas.

A tabela a seguir mostra os tipos de operações de blob desativadas para os diferentes cenários imutáveis. Para obter mais informações, consulte a documentação da [API do Serviço de Blob do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

|Cenário  |Estado do blob  |Operações de blob não permitidas  |
|---------|---------|---------|
|O intervalo efetivo de retenção no blob ainda não expirou e/ou a retenção legal está definida     |Imutável: protegido contra exclusão e gravação         | Colocar Blob<sup>1</sup>, coloque o bloco<sup>1</sup>, colocar lista de blocos<sup>1</sup>, excluir metadados de Blob do contêiner, Blob Delete, conjunto, colocar página, definir propriedades de Blob, Blob de instantâneo, Blob de cópia Incremental Acrescentar bloco         |
|O intervalo efetivo de retenção no blob expirou     |Protegido apenas contra gravação  (operações de exclusão são permitidas)         |Inserir blob<sup>1</sup>, inserir bloco<sup>1</sup>, inserir lista de blocos<sup>1</sup>, definir metadados de blob, inserir página, definir propriedades de blob, obter instantâneo de blob, fazer cópia incremental de blob, acrescentar bloco         |
|Todas as retenções legais são liberadas e nenhuma política de retenção baseada em tempo é definida no contêiner     |Mutável         |Nenhum         |
|Nenhuma política WORM é criada (retenção baseada em tempo ou retenção legal)     |Mutável         |Nenhum         |

<sup>1</sup> o aplicativo permite que essas operações criar um novo blob de uma vez. Todos os próximos substituir operações em um caminho de blob existente em um contêiner de imutável não são permitidas.

## <a name="pricing"></a>Preços

Não há nenhum custo adicional para usar esse recurso. Dados imutáveis são cobrados da mesma forma como dados regulares, mutáveis. Para obter detalhes de preço do Armazenamento de Blobs do Azure, confira a [Página de preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="getting-started"></a>Introdução

As versões mais recentes do [portal do Azure](https://portal.azure.com), [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), e [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) oferece suporte a armazenamento imutável para o armazenamento de BLOBs do Azure. [Suporte de biblioteca de cliente](#client-libraries) também é fornecido.

> [!NOTE]
>
> Armazenamento imutável está disponível somente para uso geral v2 e contas de armazenamento de Blob. Essas contas devem ser gerenciados por meio [do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Para obter informações sobre como atualizar uma conta de armazenamento v1 de uso geral existente, consulte [atualizar uma conta de armazenamento](../common/storage-account-upgrade.md).

### <a name="azure-portal"></a>Portal do Azure

1. Crie um novo contêiner ou selecione um existente para armazenar os blobs que precisam ser mantidos no estado imutável.
 O contêiner precisa estar em uma conta de Armazenamento de Blobs ou GPv2.
2. Selecione **Política de acesso** no menu de contexto. Em seguida, selecione **+ Adicionar política** em **Armazenamento de blobs imutável**.

    ![Configurações de contêiner no portal](media/storage-blob-immutable-storage/portal-image-1.png)

3. Para habilitar a retenção baseada em tempo, selecione **baseada em tempo de retenção** no menu suspenso.

    !["Com base no tempo de retenção" selecionada em "Tipo de política"](media/storage-blob-immutable-storage/portal-image-2.png)

4. Digite o intervalo de retenção em dias (os valores aceitáveis de 1 para 146000 dias).

    ![Caixa "Período de retenção de atualização para"](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    O estado inicial da política é desbloqueado permitindo que você teste o recurso e fazer alterações à política antes de você bloqueá-lo. A política de bloqueio é essencial para conformidade com as normas, como SEC 17a-4.

5. A política de bloqueio. Clique no botão de reticências (**...** ), e o seguinte menu será exibido com ações adicionais:

    !["Política de bloqueio" no menu](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    Selecione **política de bloqueio**. A política agora está bloqueada e não pode ser excluída, somente as extensões do intervalo de retenção serão permitidas.

6. Para ativar retenções legais, selecione **+ Adicionar Política**. Selecione **Retenção legal** no menu suspenso.

    !["Legal hold" no menu em "Tipo de política"](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. Crie uma retenção legal com uma ou mais tags.

    ![Caixa "Nome da tag" no tipo de política](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

8. Para limpar uma retenção legal, simplesmente remova a marca de identificador legal do aplicado.

### <a name="azure-cli"></a>CLI do Azure

O recurso está incluído nos seguintes grupos de comandos: `az storage container immutability-policy`e`az storage container legal-hold`. Execute `-h` neles para ver os comandos.

### <a name="powershell"></a>PowerShell

O módulo de visualização Az.Storage dá suporte a armazenamento imutável.  Para habilitar o recurso, siga estas etapas:

1. Certifique-se de que você tenha a versão mais recente do PowerShellGet instalado: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Remova qualquer instalação anterior do PowerShell do Azure.
3. Instale o Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.
4. Instale a versão prévia do módulo de armazenamento do Azure PowerShell: `Install-Module Az.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`

O [código do PowerShell de exemplo](#sample-powershell-code) seção mais adiante neste artigo ilustra o uso de recurso.

## <a name="client-libraries"></a>Bibliotecas de cliente

As seguintes bibliotecas de cliente dão suporte a armazenamento imutável para o armazenamento de BLOBs do Azure:

- [Biblioteca de cliente .NET versão 7.2.0-preview e posterior](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Biblioteca do cliente Node.js versão 4.0.0 e posterior](https://www.npmjs.com/package/azure-arm-storage)
- [Biblioteca de cliente do Python versão 2.0.0 versão Release Candidate 2 e posterior](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Biblioteca de clientes Java](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="supported-values"></a>Valores com suporte

- O intervalo de retenção mínimo é um dia. O máximo é de dias 146,000 (400 anos).
- Para uma conta de armazenamento, o número máximo de contêineres com políticas imutáveis bloqueados é 1.000.
- Para uma conta de armazenamento, o número máximo de contêineres com uma configuração de retenção legal é 1.000.
- Para um contêiner, o número máximo de tags de retenção legal é 10.
- O comprimento máximo de uma tag de retenção legal é de 23 caracteres alfanuméricos. O comprimento mínimo é de três caracteres.
- Para um contêiner, o número máximo de extensões de intervalo de retenção permitido para políticas imutáveis bloqueadas é três.
- Para um contêiner com uma política imutável bloqueado, um máximo de cinco logs da política de retenção baseada em tempo e um máximo de 10 legal mantenha os logs são mantidos para a duração do contêiner de política.

## <a name="faq"></a>Perguntas frequentes

**Você pode fornecer a documentação de conformidade do WORM?**

Sim. A conformidade de documento Microsoft mantidos de uma empresa de avaliação independente especializado em controle de informações e gerenciamento de registros, Cohasset Associates, para avaliar o armazenamento de BLOBs imutável e sua conformidade com requisitos específicos no setor de serviços financeiros. Cohasset validado que imutável Blob de armazenamento do Azure, quando usada para manter os Blobs com base no tempo em um estado WORM, cumpra os requisitos de armazenamento relevantes de regra CFTC 1.31(c)-(d), FINRA regra 4511 e norma SEC 17a-4. Microsoft direcionados a esse conjunto de regras, pois eles representam as diretrizes prescritivas mais globalmente para retenção de registros para instituições financeiras. O relatório Cohasset está disponível na [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage).

**O recurso se aplica apenas a blobs de bloco ou a páginas e acréscimos de blobs também?**

O armazenamento imutável pode ser usado com qualquer tipo de blob, mas é mais recomendado usá-lo para blobs de blocos. Ao contrário dos blobs de bloco, os blobs de página e os blobs de acréscimo precisam ser criados fora de um contêiner WORM e depois copiados. Depois de copiar esses blobs em um contêiner WORM, não mais *acrescenta* para um acréscimo são permitidas alterações em um blob de páginas ou blob.

**Sempre preciso criar uma nova conta de armazenamento para esse recurso?**

Você pode usar o armazenamento imutável com v2 de uso geral existente ou recém-criado ou contas de armazenamento de Blob. Esse recurso destina-se a uso com blobs de blocos em contas GPv2 e armazenamento de BLOBs.

**Posso aplicar uma retenção legal e a política de retenção baseada em tempo?**

Um contêiner pode ter uma retenção legal e uma política de retenção baseada no tempo ao mesmo tempo. Todos os blobs nesse contêiner permanecem no estado imutável até que todas as retenções legais sejam apagadas, mesmo que o período de retenção efetivo tenha expirado. Por outro lado, um blob permanece em um estado imutável até que o período de retenção efetivo expire, mesmo que todas as retenções legais tenham sido compensadas.

**São as políticas de retenção legal apenas para procedimentos legais ou há outros cenários de uso?**

Não, o controle Legal é simplesmente o termo genérico usado para uma política de retenção não baseados em tempo. Ele não precisa ser usado apenas para litígios relacionados judiciais. Políticas de retenção legais são úteis para desabilitar a substituição e exclusões para proteger a empresa importante dados WORM, em que o período de retenção é desconhecido. Você pode usá-lo como uma diretiva corporativa para proteger suas cargas de trabalho WORM críticas missão ou usá-lo como uma política de preparo antes de um gatilho de evento personalizado requer o uso de uma política de retenção baseada em tempo. 

**O que acontece se eu tentar excluir um contêiner com uma política de retenção baseada em tempo ou retenção legal *bloqueada*?**

A operação Excluir contêiner falhará se houver pelo menos um blob com uma política de retenção baseada em tempo ou uma retenção legal bloqueada. A operação Excluir contêiner será bem-sucedida somente se não houver nenhum blob com um intervalo de retenção ativo e não houver nenhuma retenção legal. Você deve excluir os blobs antes de excluir o contêiner.

**O que acontece se eu tentar excluir uma conta de armazenamento com um contêiner WORM que tem uma política de retenção baseada em tempo ou retenção legal *bloqueada*?**

A exclusão da conta de armazenamento falhará se houver pelo menos um contêiner WORM com retenção legal ou um blob com um intervalo de retenção ativa.  Você deve excluir todos os contêineres do WORM antes de excluir a conta de armazenamento. Para obter informações sobre exclusão de contêiner, consulte a pergunta anterior.

**Posso mover os dados entre níveis diferentes de blob (frequente, esporádico, passivo) quando o blob estiver no estado imutável?**

Sim, você pode usar o comando Definir nível de Blob para mover dados entre as camadas de blob, mantendo os dados em um estado imutável em conformidade. O armazenamento imutável tem suporte nas camadas de blob frequente, esporádica e de arquivos.

**O que acontece se eu não conseguir pagar e meu intervalo de retenção não expirou?**

No caso de não pagamento, as políticas normais de retenção de dados serão aplicadas conforme estipulado nos termos e condições do seu contrato com a Microsoft.

**Há oferta de um período de avaliação ou de cortesia para apenas experimentar o recurso?**

Sim. Quando uma política de retenção baseada em tempo é criada pela primeira vez, ela está em um estado *desbloqueado*. Nesse estado, você pode fazer qualquer alteração desejada no intervalo de retenção, como aumentar ou diminuir e até excluir a política. Depois que a política é bloqueada, ela permanece bloqueada até que o intervalo de retenção expire. Essa política bloqueada impede que a exclusão e modificação para o intervalo de retenção. É altamente recomendável que você use o *desbloqueada* apenas para fins de avaliação de estado e a política de bloqueio dentro de um período de 24 horas. Essas práticas ajudarão-lo a cumprir 17a-4(f) s e outros regulamentos.

**Posso usar a exclusão reversível junto com as políticas de BLOBs imutável?**

Sim. [Exclusão reversível para o armazenamento de BLOBs do Azure](storage-blob-soft-delete.md) se aplica a todos os contêineres dentro de uma conta de armazenamento, independentemente de uma retenção legal ou política de retenção com base no tempo. É recomendável habilitar exclusão reversível para proteção adicional antes de qualquer imutável WORM as diretivas são aplicadas e confirmadas. 

**O recurso está disponível nas nuvens nacionais e governamentais?**

O armazenamento imutável está disponível nas regiões do Azure Público, China e Governo. Se o armazenamento imutável não está disponível em sua região, entre em contato com o suporte e o email azurestoragefeedback@microsoft.com.

## <a name="sample-powershell-code"></a>Exemplos de código do PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O exemplo de script do PowerShell a seguir é para referência. Este script cria uma nova conta e contêiner de armazenamento. Em seguida, ele mostra como definir e limpar detenções legais, criar e bloquear uma política de retenção baseada em tempo (também conhecida como política de imutabilidade) e estender o intervalo de retenção.

Configurar e testar a conta de Armazenamento do Azure:

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

Definir e limpar retenções legais:

```powershell
# Set a legal hold
Add-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Criar ou atualizar políticas de imutabilidade:
```powershell
# with an account name or container name
Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Recuperar políticas de imutabilidade:
```powershell
# Get an immutability policy
Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzStorageContainerImmutabilityPolicy -Container $containerObject
```

Bloquear políticas de imutabilidade (adicione -Force para ignorar o prompt):
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Estender as políticas de imutabilidade:
```powershell

# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Remover uma política de imutabilidade (adicione -Force para ignorar o prompt):
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
