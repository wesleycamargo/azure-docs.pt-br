---
title: "Implantar cluster do Serviço de Contêiner do Azure com CLI | Microsoft Docs"
description: "Implantar um cluster do Serviço de Contêiner do Azure usando a Visualização do Azure CLI 2.0"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 855f0fe77bd55f6ec0dacad4bc28603ac1c6979c
ms.openlocfilehash: c4a513686433e802f27f78de60e8b7fca21b4634


---
# <a name="using-the-azure-cli-20-preview-to-create-an-azure-container-service-cluster"></a>Como usar a visualização do Azure CLI 2.0 para criar um cluster do Serviço de Contêiner do Azure

Para criar um cluster do Serviço de Contêiner do Azure, é preciso:
* uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/))
* a [CLI do Azure v. 2.0 (visualização)](https://github.com/Azure/azure-cli#installation) instalada
* estar conectado à sua conta do Azure (veja abaixo)

## <a name="log-in-to-your-account"></a>Faça logon na sua conta
```azurecli
az login 
```
Você precisará acessar este [link](https://login.microsoftonline.com/common/oauth2/deviceauth) para autenticar com o código de dispositivo fornecido na CLI.

![comando de tipo](media/container-service-create-acs-cluster-cli/login.png)

![iPhone](media/container-service-create-acs-cluster-cli/login-browser.png)


## <a name="create-a-resource-group"></a>Criar um grupos de recursos
```azurecli
az resource group create -n acsrg1 -l "westus"
```

![Criação de grupo de recursos de imagens](media/container-service-create-acs-cluster-cli/rg-create.png)

## <a name="list-of-available-azure-container-service-cli-commands"></a>Lista de comandos da CLI do Serviço de Contêiner do Azure disponíveis

```azurecli
az acs -h
```

![Uso do comando ACS](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

## <a name="create-an-azure-container-service-cluster"></a>Criar um cluster do Serviço de Contêiner do Azure

*Uso da criação ACS na CLI*

```azurecli
az acs create -h
```
O nome do serviço do contêiner, o grupo de recursos criado na etapa anterior e um nome DNS exclusivo são obrigatórios. Outras entradas são definidas com valores padrão (consulte a tela a seguir com o instantâneo de ajuda abaixo), a menos que substituídas usando seus respectivos comutadores.
![Ajuda da criação ACS de imagem](media/container-service-create-acs-cluster-cli/acs-command-usage-help.png)

*Criação ACS rápida usando padrões. Se você não tiver uma chave SSH, use o segundo comando. Esse segundo comando de criação com a opção --generate-ssh-keys criará um para você*

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

*Verifique se o prefixo de dns (switch -d) é exclusivo. Se você receber um erro, tente novamente com uma cadeia de caracteres exclusiva.*

Depois de digitar o comando anterior, aguarde cerca de 10 minutos para o cluster ser criado.

![Criação ACS de imagem](media/container-service-create-acs-cluster-cli/cluster-create.png)

## <a name="list-acs-clusters"></a>Listar clusters ACS 

### <a name="under-a-subscription"></a>Em uma assinatura

```azurecli
az acs list --output table
```

### <a name="in-a-specific-resource-group"></a>Em um grupo de recursos específico

```azurecli
az acs list -g acsrg1 --output table
```

![Lista do ACS de imagem](media/container-service-create-acs-cluster-cli/acs-list.png)


## <a name="display-details-of-a-container-service-cluster"></a>Exibir detalhes de um cluster do serviço de contêiner

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![Lista do ACS de imagem](media/container-service-create-acs-cluster-cli/acs-show.png)


## <a name="scale-the-acs-cluster"></a>Dimensionar o cluster do ACS
*É permitido reduzir e escalar horizontalmente. O parâmetro new-agent-count é o número novo dos agentes no cluster do ACS.*

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![Escala do ACS de imagem](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>Exclui um cluster do serviço de contêiner
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
*Observe que esse comando de exclusão não exclui todos os recursos criados (rede e armazenamento) ao criar o contêiner de serviço. Para excluir todos os recursos, é recomendável que um único cluster ACS seja criado por grupo de recursos e, em seguida, o grupo de recursos seja excluído quando o cluster ACS não for mais necessário para garantir que todos os recursos relacionados sejam excluídos e que você não seja cobrado por eles.*



<!--HONumber=Nov16_HO3-->


