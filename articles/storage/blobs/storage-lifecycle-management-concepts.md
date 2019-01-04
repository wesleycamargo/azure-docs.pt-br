---
title: Gerenciar o ciclo de vida do Armazenamento do Azure
description: Aprenda a criar regras de política de ciclo de vida para fazer a transição dos dados antigos para os níveis Hot to Cool e Archive.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 11/04/2018
ms.author: yzheng
ms.component: common
ms.openlocfilehash: 4dff63a20f9ae3372e37cbd413dd3ec6187ea2cc
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310194"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Gerenciando o ciclo de vida do Armazenamento de Blobs do Azure (Versão Prévia)

Conjuntos de dados têm ciclos de vida exclusivos. No início do ciclo de vida, as pessoas geralmente acessam alguns dados. Mas a necessidade de acesso cai drasticamente à medida que os dados envelhecem. Alguns dados permanecem ociosos na nuvem e raramente são acessados depois de armazenados. Alguns dados expiram dias ou meses após a criação, enquanto outros conjuntos de dados são lidos e modificados ativamente durante seu tempo de vida. O gerenciamento do ciclo de vida do Armazenamento de Blobs do Azure (Versão Prévia) oferece uma política sofisticada baseada em regra para contas de Armazenamento de Blobs e GPv2. Use a política para fazer a transição de seus dados para as camadas de acesso apropriadas ou expirá-los ao final do ciclo de vida dos dados.

A política de gerenciamento do ciclo de vida permite:

- Fazer a transição dos blobs para uma camada de armazenamento mais esporádico (frequente para esporádico, frequente para arquivos ou esporádico para arquivos), a fim de otimizar o desempenho e os custos
- Excluir os blobs no final dos respectivos ciclos de vida
- Definir regras a serem executadas uma vez por dia no nível da conta de armazenamento
- Aplicar regras a contêineres ou a um subconjunto de blobs (usando prefixos como filtros)

Considere o cenário em que um conjunto de dados obtém acesso frequente durante os estágios iniciais do ciclo de vida, mas posteriormente apenas ocasionalmente após duas semanas. Após o primeiro mês, o conjunto de dados raramente é acessado. Nesse cenário, o armazenamento frequente é melhor durante os estágios iniciais. O armazenamento esporádico é mais apropriado para o acesso ocasional e o armazenamento de arquivos é a melhor opção de camada depois que os dados envelhecem mais de um mês. Ajustando as camadas de armazenamento em relação à idade dos dados, você pode criar as opções de armazenamento menos dispendiosas para suas necessidades. Para conseguir essa transição, as regras de política de gerenciamento do ciclo de vida estão disponíveis para mover os dados antigos para camadas mais frias.

## <a name="storage-account-support"></a>Suporte da conta de armazenamento

A política de gerenciamento do ciclo de vida está disponível nas contas GPv2 (Uso Geral v2) e do Armazenamento de Blobs. No portal do Azure, atualize uma conta GPv1 (Uso Geral) existente para uma conta GPv2 com um processo simples de um clique. Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Preços 

O recurso de gerenciamento do ciclo de vida é gratuito na versão prévia. O custo de operação normal para as chamadas à API [Listar Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) e [Definir Camada de Blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) é cobrado dos clientes. Para obter mais informações sobre preços, confira [Preços do Blob de Blocos](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="register-for-preview"></a>Registrar-se para a versão prévia 
Para se inscrever na versão prévia pública, você precisará enviar uma solicitação para registrar esse recurso em sua assinatura. Geralmente, as solicitações são aprovadas em até duas semanas. Após a aprovação, todas as contas de Armazenamento de Blobs ou GPv2 novas e existentes nas seguintes regiões incluirão o recurso: Oeste dos EUA 2, Centro-oeste dos EUA, Leste dos EUA 2 e Europa Ocidental. A versão prévia dá suporte apenas a blob de blocos. Assim como ocorre com a maioria das versões prévias, você não deverá usar esse recurso para cargas de trabalho de produção até que ele seja alterado para GA.

Para enviar uma solicitação, execute os seguintes comandos da CLI ou do PowerShell.

### <a name="powershell"></a>PowerShell

Para enviar uma solicitação:

```powershell
Register-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
É possível verificar o status de aprovação do registro com o seguinte comando:
```powershell
Get-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Com a aprovação e o devido registro, você receberá o estado *Registrado* ao enviar as solicitações anteriores.

### <a name="azure-cli"></a>CLI do Azure

Para enviar uma solicitação: 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
É possível verificar o status de aprovação do registro com o seguinte comando:
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
Com a aprovação e o devido registro, você receberá o estado *Registrado* ao enviar as solicitações anteriores.


## <a name="add-or-remove-a-policy"></a>Adicionar ou remover uma política 

Adicione, edite ou remova uma política usando o portal do Azure, o [PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.3-preview), a [CLI do Azure](https://docs.microsoft.com/cli/azure/ext/storage-preview/storage/account/management-policy?view=azure-cli-latest#ext-storage-preview-az-storage-account-management-policy-create), as [APIs REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies/createorupdate) ou as ferramentas de cliente nas seguintes linguagens: [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0) e [Ruby](   https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione **Todos os Recursos** e, em seguida, selecione sua conta de armazenamento.

3. Selecione **Gerenciamento do Ciclo de Vida (versão prévia)** agrupado em serviço Blob para exibir ou alterar a política.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

### <a name="azure-cli"></a>CLI do Azure

```
az account set --subscription "[subscriptionName]”
az extension add --name storage-preview
az storage account management-policy show --resource-group [resourceGroupName] --account-name [accountName]
```

> [!NOTE]
Se você habilitar as regras de firewall para sua conta de armazenamento, as solicitações de gerenciamento do ciclo de vida poderão ser bloqueadas. Desbloqueie essas solicitações fornecendo exceções. Para obter mais informações, confira a seção Exceções em [Configurar firewalls e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policy"></a>Política

Uma política de gerenciamento do ciclo de vida é uma coleção de regras em um documento JSON:

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "rule1",
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


Uma política exige dois parâmetros:

| Nome do parâmetro | Tipo de parâmetro | Observações |
|----------------|----------------|-------|
| version        | Uma cadeia de caracteres expressada como `x.x` | O número de versão da versão prévia é 0.5. |
| regras          | Uma matriz de objetos de regra | Pelo menos uma regra é necessária em cada política. Durante a versão prévia, você pode especificar até quatro regras por política. |

Cada regra na política exige três parâmetros:

| Nome do parâmetro | Tipo de parâmetro | Observações |
|----------------|----------------|-------|
| NOME           | Cadeia de caracteres | Um nome de regra pode incluir qualquer combinação de caracteres alfanuméricos. A regra de nome diferencia maiúsculas de minúsculas. Ela deve ser exclusiva em uma política. |
| Tipo           | Um valor de enumeração | O valor válido para a versão prévia é `Lifecycle`. |
| definição     | Um objeto que define a regra de ciclo de vida | Cada definição é composta por um conjunto de filtros e um conjunto de ações. |

## <a name="rules"></a>Regras

Cada definição de regra inclui um conjunto de filtros e um conjunto de ações. O [conjunto de filtros](#rule-filters) limita as ações de regras a determinado conjunto de objetos em um contêiner ou a nomes de objetos. O [conjunto de ações](#rule-actions) aplica-se a camada ou excluir ações para o conjunto filtrado de objetos.

### <a name="sample-rule"></a>Regra de exemplo
A regra de amostra a seguir filtra a conta para executar as ações somente em `container1/foo`. Execute as seguintes ações para todos os objetos existentes em `container1` **E** que comecem com `foo`: 

- Colocar o blob na camada esporádica 30 dias após a última modificação
- Colocar o blob na camada de arquivos 90 dias após a última modificação
- Excluir o blob 2.555 dias (sete anos) após a última modificação
- Excluir instantâneos de blob 90 dias após a criação do instantâneo

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "ruleFoo", 
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

Durante a versão prévia, os filtros válidos incluem:

| Nome do filtro | Tipo do filtro | Observações | Obrigatório |
|-------------|-------------|-------|-------------|
| blobTypes   | Uma matriz de valores de enumeração predefinidos. | A versão prévia só dá suporte a `blockBlob`. | SIM |
| prefixMatch | Uma matriz de cadeias de caracteres para prefixos a serem correspondidos. Uma cadeia de caracteres de prefixo deve começar com um nome de contêiner. Por exemplo, se você deseja encontrar uma correspondência para todos os blobs em "https://myaccount.blob.core.windows.net/container1/foo/..." em uma regra, o prefixMatch é `container1/foo`. | Se você não definir o prefixMatch, as regras serão aplicadas a todos os blobs da conta. | Não  |

### <a name="rule-actions"></a>Ações de regra

Ações são aplicadas aos blobs filtrados quando a condição de execução é atendida.

Na versão prévia, o gerenciamento do ciclo de vida dá suporte à colocação de blobs em camadas e à exclusão de blobs, bem como à exclusão de instantâneos de blob. Defina, pelo menos, uma ação para cada regra em blobs ou instantâneos de blob.

| Ação        | Blob base                                   | Instantâneo      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Dá suporte aos blobs atualmente presentes na camada frequente         | Sem suporte |
| tierToArchive | Dá suporte aos blobs atualmente presentes na camada frequente ou esporádica | Sem suporte |
| excluir        | Com suporte                                   | Com suporte     |

>[!NOTE] 
Se você definir mais de uma ação no mesmo blob, o gerenciamento do ciclo de vida aplicará a ação mais barata ao blob. Por exemplo, a ação `delete` é mais barata do que a ação `tierToArchive`. A ação `tierToArchive` é mais barata do que a ação `tierToCool`.

Na versão prévia, as condições de execução da ação são com base na idade. Os blobs base usam a hora da última modificação para acompanhar a idade, enquanto os instantâneos de blob usam a hora de criação do instantâneo para executar a mesma tarefa.

| Condição de execução da ação | Valor de condição | DESCRIÇÃO |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Valor inteiro que indica a idade em dias | Condição válida para ações de blob de base |
| daysAfterCreationGreaterThan     | Valor inteiro que indica a idade em dias | Condição válida para ações de instantâneo de blob | 

## <a name="examples"></a>Exemplos
Os exemplos a seguir demonstram como tratar cenários comuns com as regras de política de ciclo de vida.

### <a name="move-aging-data-to-a-cooler-tier"></a>Mover dados antigos para uma camada mais fria

Este exemplo mostra como fazer a transição de blobs de blocos prefixados com `container1/foo` ou `container2/bar`. A política faz a transição de blobs que não foram modificados há mais de 30 dias para o armazenamento esporádico e de blobs não modificados há mais de 90 dias para a camada de arquivos:

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "agingRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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

Alguns dados permanecem ociosos na nuvem e raramente ou nunca são acessados depois de armazenados. Arquive esses dados imediatamente depois de ingeridos. A política de ciclo de vida a seguir é configurada para arquivar dados no ato da ingestão. Este exemplo faz a transição de blocos de blocos na conta de armazenamento do contêiner `archivecontainer` imediatamente para uma camada de arquivamento. A transição imediata é realizada agindo sobre os blobs 0 dias após a hora da última modificação:

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "archiveRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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
  "version": "0.5",
  "rules": [ 
    {
      "name": "expirationRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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
  "version": "0.5",
  "rules": [ 
    {
      "name": "snapshotRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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

A plataforma executa a política de ciclo de vida uma vez por dia. Depois que você define uma nova política, podem ser necessárias até 24 horas para que algumas ações (como colocação em camadas e exclusão) sejam iniciadas e executadas.  

## <a name="next-steps"></a>Próximas etapas

Saiba como recuperar dados após uma exclusão acidental:

- [Exclusão reversível para blobs do Armazenamento do Azure ](../blobs/storage-blob-soft-delete.md)
