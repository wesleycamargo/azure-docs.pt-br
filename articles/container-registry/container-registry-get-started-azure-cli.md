---
title: "Criar um registro de contêiner do Azure - CLI | Microsoft Docs"
description: "Introdução à criação e gerenciamento de registros de contêiner do Azure com a CLI do Azure 2.0"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: 2a381431acb6436ddd8e13c69b05423a33cd4fa6
ms.openlocfilehash: 1d5e16952cbc56a381ead23843515cf6ed1d74a9
ms.lasthandoff: 02/22/2017

---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Criar um registro de contêiner usando a CLI do Azure
Usar comandos na [CLI do Azure 2.0](https://github.com/Azure/azure-cli) para criar um registro de contêiner e gerenciar as configurações em um computador Linux, Mac ou Windows. Você também pode criar e gerenciar registros de contêiner usando o [portal do Azure](container-registry-get-started-portal.md) ou por meio de programação com a [API REST](https://go.microsoft.com/fwlink/p/?linkid=834376) do Registro de Contêiner.


* Para obter informações de base e conceitos, confira [O que é o Registro de Contêiner do Azure?](container-registry-intro.md)
* Para obter ajuda sobre comandos da CLI do contêiner do registro (`az acr` comandos), passe o parâmetro `-h` para qualquer comando.

> [!NOTE]
> O Registro de Contêiner está atualmente em visualização.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* **CLI do Azure 2.0** – Para instalar e começar a usar a CLI 2.0, confira as [instruções de instalação](https://github.com/Azure/azure-cli/blob/master/README.rst). Faça logon em sua assinatura do Azure executando `az login`.
* **Grupo de recursos** - crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) antes de criar um registro de contêiner ou usar um grupo de recursos existente. Verifique se o grupo de recursos está em um local em que o serviço de Registro de Contêiner está [disponível](https://azure.microsoft.com/regions/services/). Para criar um grupo de recursos usando a CLI 2.0, confira [os exemplos de CLI 2.0](https://github.com/Azure/azure-cli-samples/tree/master/arm). 
* **Conta de armazenamento** (opcional) - cria uma [conta de armazenamento](../storage/storage-introduction.md) padrão do Azure para dar suporte ao registro de contêiner no mesmo local. Se você não especificar uma conta de armazenamento ao criar um registro com `az acr create`, o comando criará um para você. Para criar uma conta de armazenamento usando a CLI 2.0, confira [os exemplos de CLI 2.0](https://github.com/Azure/azure-cli-samples/tree/master/storage).
* **Entidade de serviço** (opcional) - quando você cria um registro com a CLI, por padrão, ele não é configurado para acesso. Dependendo das suas necessidades, você pode atribuir uma entidade de serviço do Azure Active Directory existente a um registro (ou criar e atribuir uma nova) ou habilitar a conta de usuário do administrador do registro. Confira as seções mais adiante neste artigo. Para obter mais informações sobre o acesso ao registro, confira [Autenticar no registro de contêiner](container-registry-authentication.md). 

## <a name="create-a-container-registry"></a>Criar um registro de contêiner
Execute o comando `az acr create` para criar um registro de contêiner. 

> [!TIP]
> Ao criar um registro, especifique um nome de domínio de nível superior exclusivo que contenha apenas letras e números. O nome do registro nos exemplos é `myRegistry`, mas substitua-o por um nome exclusivo. 
> 
> 

O seguinte comando usa os parâmetros mínimos para criar o registro de contêiner `myRegistry` no grupo de recursos `myResourceGroup` no local Centro-Sul dos EUA:

```azurecli
az acr create -n myRegistry -g myResourceGroup -l southcentralus
```

* `--storage-account-name` ou `-s` são opcionais. Se não for especificada, uma conta de armazenamento será criada com um nome aleatório no grupo de recursos especificado.

A saída deverá ser semelhante a esta:

![criar saída az acr](./media/container-registry-get-started-azure-cli/acr_create.png)


Observe particularmente:

* `id` - identificador do registro em sua assinatura, que é necessário se você deseja atribuir uma entidade de serviço. 
* `loginServer` - o nome totalmente qualificado que você especifica para [fazer logon no registro](container-registry-authentication.md). Neste exemplo, o nome é `myregistry-contoso.exp.azurecr.io` (todo em minúsculas).

## <a name="assign-a-service-principal"></a>Atribuir uma entidade de serviço
Use comandos da CLI 2.0 para atribuir uma entidade de serviço do Azure Active Directory a um registro. A entidade de serviço nesses exemplos é atribuída à função de Proprietário, mas você poderá atribuir [outras funções](../active-directory/role-based-access-control-configure.md), se desejar.

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>Criar uma entidade de serviço e atribuir acesso ao registro
No comando a seguir, uma nova entidade de serviço recebe acesso de função de proprietário para o identificador do registro passado com o parâmetro `--scopes`. Especifique uma senha forte com o parâmetro `--password`.

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>Atribuir uma entidade de serviço existente
Se já tiver uma entidade de serviço e desejar atribuir a ela acesso de função de Proprietário para o registro, execute um comando semelhante ao exemplo a seguir. Você passa a ID de aplicativo principal de serviço usando o parâmetro `--assignee`:

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>Gerenciar credenciais de administrador
Uma conta de administrador é automaticamente criada para cada registro de contêiner e é desabilitada por padrão. Os exemplos a seguir mostram comandos CLI `az acr` para gerenciar as credenciais de administrador para o registro de contêiner.

### <a name="obtain-admin-user-credentials"></a>Obter credenciais de usuário administrador
```azurecli
az acr credential show -n myRegistry
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Habilitar o usuário administrador para um registro existente
```azurecli
az acr update -n myRegistry --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Desabilitar o usuário administrador para um registro existente
```azurecli
az acr update -n myRegistry --admin-enabled false
```

## <a name="list-images-and-tags"></a>Listar imagens e marcas
Use os comandos de CLI `az acr` para consultar as imagens e marcas em um repositório. 

> [!NOTE]
> Atualmente, o Registro de Contêiner não dá suporte ao comando `docker search` para consulta para imagens e marcas.


### <a name="list-repositories"></a>Listar repositórios
O seguinte exemplo lista os repositórios em um registro, no formato JSON (JavaScript Object Notation):

```azurecli
az acr repository list -n myRegistry -o json
```

### <a name="list-tags"></a>Listar marcas
O seguinte exemplo lista as marcas no repositório **samples/nginx**, no formato JSON:

```azurecli
az acr repository show-tags -n myRegistry --repository samples/nginx -o json
```

## <a name="next-steps"></a>Próximas etapas
* [Enviar por push sua primeira imagem usando a CLI do Docker](container-registry-get-started-docker-cli.md)


