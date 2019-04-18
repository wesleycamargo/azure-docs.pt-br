---
title: Gerenciar o ciclo de vida do Armazenamento do Azure
description: Aprenda a criar regras de política de ciclo de vida para fazer a transição dos dados antigos para os níveis Hot to Cool e Archive.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: conceptual
ms.date: 3/20/2019
ms.author: yzheng
ms.subservice: common
ms.openlocfilehash: 2de194e501c05ba0bdb9971ca6045e67a42b0fd9
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681719"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Gerenciar o ciclo de vida do armazenamento de BLOBs do Azure

Conjuntos de dados têm ciclos de vida exclusivos. No início do ciclo de vida, as pessoas geralmente acessam alguns dados. Mas a necessidade de acesso cai drasticamente à medida que os dados envelhecem. Alguns dados permanecem ociosos na nuvem e raramente são acessados depois de armazenados. Alguns dados expiram dias ou meses após a criação, enquanto outros conjuntos de dados são lidos e modificados ativamente durante seu tempo de vida. Gerenciamento de ciclo de vida de armazenamento de BLOBs do Azure oferece uma política avançada, baseada em regra, para contas de armazenamento de GPv2 e BLOBs. Use a política para fazer a transição de seus dados para as camadas de acesso apropriadas ou expirá-los ao final do ciclo de vida dos dados.

A política de gerenciamento do ciclo de vida permite:

- Fazer a transição dos blobs para uma camada de armazenamento mais esporádico (frequente para esporádico, frequente para arquivos ou esporádico para arquivos), a fim de otimizar o desempenho e os custos
- Excluir os blobs no final dos respectivos ciclos de vida
- Definir regras a serem executadas uma vez por dia no nível da conta de armazenamento
- Aplicar regras a contêineres ou a um subconjunto de blobs (usando prefixos como filtros)

Considere o cenário em que um conjunto de dados obtém acesso frequente durante os estágios iniciais do ciclo de vida, mas posteriormente apenas ocasionalmente após duas semanas. Após o primeiro mês, o conjunto de dados raramente é acessado. Nesse cenário, o armazenamento frequente é melhor durante os estágios iniciais. O armazenamento esporádico é mais apropriado para o acesso ocasional e o armazenamento de arquivos é a melhor opção de camada depois que os dados envelhecem mais de um mês. Ajustando as camadas de armazenamento em relação à idade dos dados, você pode criar as opções de armazenamento menos dispendiosas para suas necessidades. Para conseguir essa transição, as regras de política de gerenciamento do ciclo de vida estão disponíveis para mover os dados antigos para camadas mais frias.

## <a name="storage-account-support"></a>Suporte da conta de armazenamento

A política de gerenciamento do ciclo de vida está disponível nas contas GPv2 (Uso Geral v2) e do Armazenamento de Blobs. No portal do Azure, atualize uma conta GPv1 (Uso Geral) existente para uma conta GPv2 com um processo simples de um clique. Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Preços 

O recurso de gerenciamento do ciclo de vida é gratuito. O custo de operação normal para as chamadas à API [Listar Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) e [Definir Camada de Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) é cobrado dos clientes. Operação de exclusão é gratuita. Para obter mais informações sobre preços, confira [Preços do Blob de Blocos](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Disponibilidade regional 
O recurso de gerenciamento do ciclo de vida está disponível em todas as regiões públicas do Azure. 


## <a name="add-or-remove-a-policy"></a>Adicionar ou remover uma política 

Você pode adicionar, editar ou remover uma política por meio do portal do Azure, [Azure PowerShell](https://github.com/Azure/azure-powershell/releases), a CLI do Azure, [APIs REST](https://docs.microsoft.com/en-us/rest/api/storagerp/managementpolicies), ou uma ferramenta de cliente. Este artigo mostra como gerenciar a política usando o portal e métodos do PowerShell.  

> [!NOTE]
> Se você habilitar as regras de firewall para sua conta de armazenamento, as solicitações de gerenciamento do ciclo de vida poderão ser bloqueadas. Desbloqueie essas solicitações fornecendo exceções. O bypass necessário são: `Logging,  Metrics,  AzureServices`. Para obter mais informações, confira a seção Exceções em [Configurar firewalls e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

### <a name="azure-portal"></a>Portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione **todos os recursos** e, em seguida, selecione sua conta de armazenamento.

3. Sob **serviço Blob**, selecione **gerenciamento de ciclo de vida** para exibir ou alterar sua política.

### <a name="powershell"></a>PowerShell

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery 

#Create a new action object

$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd 

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy 
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1

```


## <a name="policy"></a>Política

Uma política de gerenciamento do ciclo de vida é uma coleção de regras em um documento JSON:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```


Uma política é uma coleção de regras:

| Nome do parâmetro | Tipo de parâmetro | Observações |
|----------------|----------------|-------|
| regras          | Uma matriz de objetos de regra | Pelo menos uma regra é necessária em uma política. Você pode definir até 100 regras em uma política.|

Cada regra na política tem vários parâmetros:

| Nome do parâmetro | Tipo de parâmetro | Observações | Obrigatório |
|----------------|----------------|-------|----------|
| Nome           | Cadeia de caracteres |Um nome de regra pode incluir até 256 caracteres alfanuméricos. A regra de nome diferencia maiúsculas de minúsculas.  Ela deve ser exclusiva em uma política. | True |
| Habilitado | BOOLEAN | Um booliano opcional para permitir que uma regra para ser temporário desabilitado. Valor padrão é true se não for definido. | Falso | 
| tipo           | Um valor de enumeração | O tipo atual de válido é `Lifecycle`. | True |
| definição     | Um objeto que define a regra de ciclo de vida | Cada definição é composta por um conjunto de filtros e um conjunto de ações. | True |

## <a name="rules"></a>Regras

Cada definição de regra inclui um conjunto de filtros e um conjunto de ações. O [conjunto de filtros](#rule-filters) limita as ações de regras a determinado conjunto de objetos em um contêiner ou a nomes de objetos. O [conjunto de ações](#rule-actions) aplica-se a camada ou excluir ações para o conjunto filtrado de objetos.

### <a name="sample-rule"></a>Regra de exemplo
A regra de exemplo a seguir filtra a conta para executar as ações em objetos que existem dentro `container1` **AND** começar com `foo`.  

- Colocar o blob na camada esporádica 30 dias após a última modificação
- Colocar o blob na camada de arquivos 90 dias após a última modificação
- Excluir o blob 2.555 dias (sete anos) após a última modificação
- Excluir instantâneos de blob 90 dias após a criação do instantâneo

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}

```

### <a name="rule-filters"></a>Filtros de regra

Os filtros limitam as ações de regra a um subconjunto de blobs na conta de armazenamento. Se mais de um filtro for definido, um `AND` lógico será executado em todos os filtros.

Filtros válidos incluem:

| Nome do filtro | Tipo do filtro | Observações | Obrigatório |
|-------------|-------------|-------|-------------|
| blobTypes   | Uma matriz de valores de enumeração predefinidos. | A versão atual dá suporte a `blockBlob`. | Sim |
| prefixMatch | Uma matriz de cadeias de caracteres para prefixos a serem correspondidos. Cada regra pode definir até 10 prefixos. Uma cadeia de caracteres de prefixo deve começar com um nome de contêiner. Por exemplo, se você deseja encontrar uma correspondência para todos os blobs em "https://myaccount.blob.core.windows.net/container1/foo/..." em uma regra, o prefixMatch é `container1/foo`. | Se você não definir prefixMatch, a regra se aplica a todos os blobs na conta de armazenamento.  | Não  |

### <a name="rule-actions"></a>Ações de regra

Ações são aplicadas aos blobs filtrados quando a execução condição for atendida.

Gerenciamento de ciclo de vida dá suporte a disposição em camadas e exclusão de blobs e a exclusão de instantâneos de blob. Defina, pelo menos, uma ação para cada regra em blobs ou instantâneos de blob.

| Ação        | Blob base                                   | Instantâneo      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Dá suporte aos blobs atualmente presentes na camada frequente         | Sem suporte |
| tierToArchive | Dá suporte aos blobs atualmente presentes na camada frequente ou esporádica | Sem suporte |
| excluir        | Suportado                                   | Suportado     |

>[!NOTE] 
>Se você definir mais de uma ação no mesmo blob, o gerenciamento do ciclo de vida aplicará a ação mais barata ao blob. Por exemplo, a ação `delete` é mais barata do que a ação `tierToArchive`. A ação `tierToArchive` é mais barata do que a ação `tierToCool`.

As condições de execução são com base na idade. Os blobs base usam a hora da última modificação para acompanhar a idade, enquanto os instantâneos de blob usam a hora de criação do instantâneo para executar a mesma tarefa.

| Executar a condição de ação | Valor de condição | DESCRIÇÃO |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Valor inteiro que indica a idade em dias | Condição válida para ações de blob de base |
| daysAfterCreationGreaterThan     | Valor inteiro que indica a idade em dias | Condição válida para ações de instantâneo de blob | 

## <a name="examples"></a>Exemplos
Os exemplos a seguir demonstram como tratar cenários comuns com as regras de política de ciclo de vida.

### <a name="move-aging-data-to-a-cooler-tier"></a>Mover dados antigos para uma camada mais fria

Este exemplo mostra como fazer a transição de blobs de blocos prefixados com `container1/foo` ou `container2/bar`. A política faz a transição de blobs que não foram modificados há mais de 30 dias para o armazenamento esporádico e de blobs não modificados há mais de 90 dias para a camada de arquivos:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="archive-data-at-ingest"></a>Arquivar dados em ingestão 

Alguns dados permanecem ociosos na nuvem e raramente ou nunca são acessados depois de armazenados. A seguinte política de ciclo de vida está configurada para arquivar os dados depois que eles são consumidos. Este exemplo faz a transição bloquear blobs na conta de armazenamento dentro do contêiner `archivecontainer` em uma camada de arquivo morto. A transição é conseguida atuando em blobs 0 dias após a hora da última modificação:

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Expirar os dados com base na idade

Espera-se que alguns dados expirem dias ou meses após a criação para reduzir custos ou atender aos requisitos governamentais. Configure uma política de gerenciamento do ciclo de vida para expirar os dados por exclusão com base na idade deles. O exemplo a seguir mostra uma política que exclui todos os blobs de bloco (sem nenhum prefixo especificado) mais antigos do que 365 dias.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>Excluir instantâneos antigos

Para dados que são modificados e acessados regularmente durante seu ciclo de vida, instantâneos geralmente são usados para controlar versões mais antigas dos dados. Você pode criar uma política que exclui os instantâneos antigos com base na idade do instantâneo. A idade de instantâneo é determinada avaliando-se a hora de criação do instantâneo. Esta regra de política exclui instantâneos blobs de blocos no contêiner `activedata` com 90 dias ou mais após a criação do instantâneo.

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",      
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```
## <a name="faq---i-created-a-new-policy-why-are-the-actions-not-run-immediately"></a>Perguntas frequentes – Criei uma política. Por que as ações não são executadas imediatamente? 

A plataforma executa a política de ciclo de vida uma vez por dia. Depois de configurar uma política, ele pode levar até 24 horas para algumas ações (como disposição em camadas e exclusão) para ser executado pela primeira vez.  

## <a name="next-steps"></a>Próximas etapas

Saiba como recuperar dados após uma exclusão acidental:

- [Exclusão reversível para blobs de armazenamento do Azure](../blobs/storage-blob-soft-delete.md)
