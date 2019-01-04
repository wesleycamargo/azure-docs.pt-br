---
title: Migrar para a API do Azure Batch AI atualizada | Microsoft Docs
description: Como atualizar código IA do Lote do Azure e scripts para usar o workspace e testar recursos
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 75a9a5e9bafd62b320397c00ef6574b7536d9e09
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407773"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>Migre para a API de AI atualizado do lote

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Na API REST de IA do Lote versão 2018-05-01 e nos SDKs e ferramentas de IA do Lote relacionados, foram introduzidos novos recursos e alterações significativas.

Se você usou uma versão anterior da API do AI em lotes, este artigo explica como modificar seu código e scripts para trabalhar com a nova API. 

## <a name="whats-changing"></a>O que está sendo alterado?

Em resposta aos comentários dos clientes, podemos remover limites no número de trabalhos e tornando mais fácil de gerenciar recursos de lote AI. A nova API apresenta dois novos recursos, *workspace* e *experimentar*. Colete trabalhos de treinamento relacionados em uma experiência e organize todos os recursos da IA do Lote relacionados (clusters, servidores de arquivos, experimentos, tarefas) em um workspace.  

* **Workspace** - uma coleção de nível superior de todos os tipos de recursos da IA do lote. Clusters e servidores de arquivos estão contidos em um workspace. Os workspaces geralmente separam o trabalho pertencente a diferentes grupos ou projetos. Por exemplo, você pode ter um dev e um workspace de teste. Você provavelmente precisará apenas de um número limitado de workspaces por assinatura. 

* **Experiência** - Uma coleção de tarefas relacionadas que podem ser consultadas e gerenciadas juntas. Por exemplo, use uma experiência para agrupar todos os trabalhos que são executados como parte de uma varredura de ajuste de hiper-parâmetros. 

A imagem a seguir mostra um exemplo de hierarquia de recursos. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>Monitore e gerencie os recursos existentes
Cada grupo de recursos onde você já criou clusters de IA do Lote, trabalhos ou servidores de arquivos, o serviço de lote AI criará um workspace chamado `migrated-<region>` (por exemplo, `migrated-eastus`) e uma experiência denominado `migrated`. Para acessar os trabalhos criados anteriormente, clusters ou servidores de arquivos, você precisa usar o workspace migrado e experiências. 

### <a name="portal"></a>Portal 
Para acessar jobs, clusters ou servidores de arquivos criados anteriormente usando o portal, primeiro selecione o`migrated-<region>` workspace. Depois de selecionar o workspace, execute operações como redimensionar ou excluir um cluster e exibir o status e as saídas do trabalho. 

### <a name="sdks"></a>SDKs 
Para acessar jobs, clusters ou servidores de arquivos criados anteriormente por meio dos SDKs do IA do Lote, forneça os parâmetros do nome do workspace e do nome do experimento. 

Se você usar o SDK de Python, as alterações relevantes são mostradas nos exemplos a seguir. As alterações são semelhantes a outros SDKs AI do lote. 


#### <a name="get-old-cluster"></a>Obter o cluster antigo 

```python
cluster = client.clusters.get(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="delete-old-cluster"></a>Deletar o cluster antigo 

```python
client.clusters.delete(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="get-old-file-server"></a>Obter servidor de arquivos antigo 

```python
cluster = client.fileservers.get(resource_group_name, 'migrated-<region>', fileserver_name)
```

#### <a name="delete-old-file-server"></a>Deletar o servidor de arquivos antigo  

```python
client.fileservers.delete(resource_group_name, 'migrated-<region>', fileserver_name)
``` 


#### <a name="get-old-job"></a>Obter trabalho antigo 

```python
cluster = client.jobs.get(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```

#### <a name="delete-old-job"></a>Deletar trabalho antigo

```python
client.jobs.delete(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```
 
### <a name="azure-cli"></a>CLI do Azure 
 
Ao usar a CLI do Azure para trabalhos de acesso criado anteriormente, clusters ou servidores de arquivos, use o `-w` e `-e` parâmetros para fornecer workspace e experimente nomes. 


#### <a name="get-old-cluster"></a>Obter o cluster antigo

```azurecli
az batchai cluster show -g resource-group-name -w migrated-<region> -n cluster-name
```


#### <a name="delete-old-cluster"></a>Deletar o cluster antigo 

```azurecli
az batchai cluster delete -g resource-group-name -w migrated-<region> -n cluster-name
```

#### <a name="get-old-file-server"></a>Obter servidor de arquivos antigo

```azurecli
az batchai file-server show -g resource-group-name -w migrated-<region> -n fileserver-name
```


#### <a name="delete-old-file-server"></a>Deletar o servidor de arquivos antigo 

```azurecli
az batchai file-server delete -g resource-group-name -w migrated-<region> -n fileserver-name
``` 


#### <a name="get-old-job"></a>Obter trabalho antigo

```azurecli
az batchai job show -g resource-group-name -w migrated-<region> -e migrated -n job-name
```


#### <a name="delete-old-job"></a>Deletar trabalho antigo 

```azurecli
az batchai job delete -g resource-group-name -w migrated-<region> -e migrated -n job-name
``` 

## <a name="create-batch-ai-resources"></a>Criar recursos da IA em lote 
 
Se você estiver usando um dos SDKs de AI lote existente, você pode continuar a usá-lo para criar recursos de lote AI (trabalhos, clusters ou servidores de arquivos) sem fazer alterações de código. No entanto, após a atualização para o novo SDK, você precisa fazer as seguintes alterações.
 
### <a name="create-workspace"></a>Criar workspace 
Dependendo do cenário, você pode criar um workspace única manualmente por meio do portal ou o CLI. Se você estiver usando a CLI, crie um workspace usando o seguinte comando: 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>Criar um experimento 


Dependendo do cenário, você pode criar uma experiência única manualmente por meio do portal ou o CLI. Se você estiver usando a CLI, crie uma experiência usando o seguinte comando: 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>Criar trabalhos, servidores de arquivos e clusters
 
Se você usar o portal para criar trabalhos, clusters ou servidores de arquivos, o portal orientará você durante a experiência de criação para fornecer o nome do workspace e testar os parâmetros de nome.

Para criar trabalhos, clusters ou servidores de arquivos através do SDK atualizado, forneça o parâmetro de nome de workspace. Se você usar o SDK de Python, as alterações relevantes são mostradas nos exemplos a seguir. Substituir *workspace_name* e *experiment_name* com o workspace e a experiência que você criou anteriormente. As alterações são semelhantes a outros SDKs AI do lote. 


#### <a name="create-cluster"></a>Criar cluster 

```python
_ = client.clusters.create(resource_group_name, workspace_name, cluster_name, cluster_create_parameters).result()
```

#### <a name="create-file-server"></a>Criar servidor de arquivos 

```python
_ = client.fileservers.create(resource_group_name, workspace_name, fileserver_name, fileserver_create_parameters).result()
```

#### <a name="create-job"></a>Criar trabalho 

```python
_ = client.jobs.create(resource_group_name, workspace_name, experiment_name, job_name job_create_parameters).result()
```


## <a name="next-steps"></a>Próximas etapas

* Consulte a referência da API de IA do Lote: [CLI](/cli/azure/batchai), [.NET](/dotnet/api/overview/azure/batchai), [Java](/java/api/overview/azure/batchai), [Node.js](/javascript/api/overview/azure/batchai), [Python](/python/api/overview/azure/batchai) e [REST](/rest/api/batchai)