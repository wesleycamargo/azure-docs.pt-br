---
title: Criar registro privado do Docker - portal do Azure | Microsoft Docs
description: "Introdução à criação e ao gerenciamento de contêiner privado do Docker com o portal do Azure"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 560aee42b0c5a61c37c594d7937f833ab7183d49
ms.contentlocale: pt-br
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-portal"></a>Criar um registro de contêiner gerenciado usando o portal do Azure

O Registro de Contêiner do Azure é um serviço de registro de contêiner Docker gerenciado usado para armazenar imagens de contêiner de Docker particulares. Este guia detalha a criação de uma instância gerenciada do Registro de Contêiner do Azure usando o portal do Azure.

Os registros de contêiner gerenciados do Azure estão em versão prévia e não estão disponíveis em todas as regiões.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

2. Pesquise no marketplace o **registro de contêiner do Azure** e selecione-o.

3. Clique em **Criar**, que abrirá a folha de criação do ACR.

    ![Configurações de registro de contêiner](./media/container-registry-get-started-portal/managed-container-registry-settings.png)

4. Na folha **Registro de Contêiner do Azure**, insira as informações a seguir. Clique em **Criar** quando terminar.

    a. **Nome do registro**: um nome de domínio de nível superior exclusivo para o registro específico. Neste exemplo, o nome do registro é *myAzureContainerRegistry1*, mas substitua-o por um nome exclusivo. O nome pode conter apenas letras e números.

    b. **Grupo de recursos**: selecione um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) existente ou digite o nome de um novo.

    c. **Local**: selecione um local de datacenter do Azure em que o serviço está [disponível](https://azure.microsoft.com/regions/services/), como **Centro-Sul dos EUA**.

    d. **Usuário administrador**: se desejar, habilite um usuário administrador para acessar o registro. Você pode alterar essa configuração após a criação do registro.

    e. **Registro gerenciado Use**: selecione Sim para que o ACR gerencie automaticamente o armazenamento de registro, use webhooks e use a autenticação do AAD.

    f. **Tipo de Preço**: selecione um tipo de preço, veja aqui os preços do ACR para saber mais.

## <a name="log-in-to-acr-instance"></a>Fazer logon na instância ACR

Antes de enviar por push e pull imagens de contêiner, você deverá fazer logon na instância ACR. 

Para fazer isso, use a CLI do Azure 2.0. Primeiro, se necessário, faça logon no Azure usando o comando [az login](/cli/azure/#login). 

```azurecli
az login
```

Em seguida, use o comando [az acr login](/cli/azure/acr#login) para fazer logon no Registro de Contêiner do Azure.

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

## <a name="use-azure-container-registry"></a>Usar o Registro de Contêiner do Azure

### <a name="list-container-images"></a>Listar imagens de contêiner

Use os comandos de CLI `az acr` para consultar as imagens e marcas em um repositório.

> [!NOTE]
> Atualmente, o Registro de Contêiner não dá suporte ao comando `docker search` para consulta para imagens e marcas.

### <a name="list-repositories"></a>Listar repositórios

O seguinte exemplo lista os repositórios em um registro, no formato JSON (JavaScript Object Notation):

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>Listar marcas

O seguinte exemplo lista as marcas no repositório **samples/nginx**, no formato JSON:

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou uma instância gerenciada do Registro de Contêiner do Azure usando o portal do Azure.

> [!div class="nextstepaction"]
> [Enviar por push sua primeira imagem usando a CLI do Docker](container-registry-get-started-docker-cli.md)
