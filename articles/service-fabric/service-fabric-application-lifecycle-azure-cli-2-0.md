---
title: Gerenciar aplicativos do Service Fabric usando a CLI 2.0 do Azure
description: "Descreve o processo de implantação e remoção de aplicativos de um cluster do Service Fabric usando a CLI 2.0 do Azure"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: a99819f6a1c0ef31e14c95b6bd47138feb05053f
ms.contentlocale: pt-br
ms.lasthandoff: 07/01/2017

---
# <a name="manage-service-fabric-application-using-azure-cli-20"></a>Gerenciar um aplicativo do Service Fabric usando a CLI 2.0 do Azure

Acompanhe esta documentação para criar e excluir aplicativos em execução em um cluster do Service Fabric.

## <a name="prerequisites"></a>Pré-requisitos

Instale a CLI 2.0 do Azure e selecione o cluster do Service Fabric. Encontre mais informações na [documentação Introdução à CLI 2.0 do Azure](service-fabric-azure-cli-2-0.md).

Você também deve ter um pacote de aplicativos do Service Fabric pronto para ser implantado. Encontre mais informações sobre como criar e empacotar um aplicativo na [documentação do modelo de aplicativo](service-fabric-application-model.md).

## <a name="overview"></a>Visão geral

A implantação de um novo aplicativo consiste em quatro etapas:

1. Carregar um pacote de aplicativos no repositório de imagens do Service Fabric
1. Provisionar um tipo de aplicativo
1. Especificar e criar um aplicativo
1. Especificar e criar serviços

A remoção de um aplicativo existente exige três etapas:

1. Excluir aplicativo
1. Desprovisionar o tipo de aplicativo associado
1. Excluir o conteúdo do repositório de imagens

## <a name="deploy-a-new-application"></a>Implantar um novo aplicativo

Para implantar um novo aplicativo, siga estas etapas

### <a name="upload-a-new-application-package-to-the-image-store"></a>Carregar um novo pacote de aplicativos no repositório de imagens

Antes de criar um aplicativo, o pacote de aplicativos precisa ser carregado no repositório de imagens do Service Fabric.
Suponha que, em seguida, o pacote de aplicativos exista no diretório `app_package_dir`. Use os seguintes comandos para carregar o diretório:

```azurecli
az sf application upload --path ~/app_package_dir
```

Para pacotes de aplicativos grandes, especifique a opção `--show-progress` para exibir o progresso do upload.

### <a name="provision-application-type"></a>Provisionar um tipo de aplicativo

Depois que o upload é concluído, o aplicativo precisa ser provisionado. Para provisionar o aplicativo, use o seguinte comando

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

O `application-type-build-path` tem o mesmo nome do diretório que contém o pacote de aplicativos que foram carregados anteriormente

### <a name="create-application-from-application-type"></a>Criar um aplicativo com base no tipo de aplicativo

Depois que o aplicativo for provisionado, você poderá nomear e criar o aplicativo usando o seguinte comando:

```azurecli
az sf application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

Aqui, `app-name` é o nome que você deseja dar à instância do aplicativo. Os outros parâmetros podem ser encontrados no manifesto do aplicativo que foi configurado anteriormente.

O nome do aplicativo deve começar com o prefixo `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Criar serviços para o novo aplicativo

Depois que um aplicativo for criado, você poderá criar serviços por meio do aplicativo. Para este exemplo, criamos um novo serviço sem estado por meio de nosso aplicativo. Os serviços que podem ser criados por meio de um aplicativo são definidos em um manifesto do serviço dentro do pacote de aplicativos provisionados anteriormente.

```azurecli
az sf service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-creation-and-health"></a>Verificar a criação e a integridade do aplicativo

Para verificar se um aplicativo e serviço foram implantados com êxito, verifique se o aplicativo e serviço estão listados usando os seguintes comandos:

```azurecli
az sf application list
az sf service list --application-list TestApp
```

Para verificar se o serviço é íntegro, use comandos semelhantes para recuperar a integridade do serviço e do aplicativo

```azurecli
az sf application health --application-id TestApp
az sf service health --service-id TestApp/TestSvc
```

Os serviços e aplicativos íntegros devem ter um valor `HealthState` igual a `Ok`.

## <a name="remove-an-existing-application"></a>Remover um aplicativo existente

Para remover um aplicativo, siga estas etapas

### <a name="delete-the-application"></a>Excluir o aplicativo

Exclua o aplicativo executando o seguinte comando

```azurecli
az sf application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Desprovisionar o tipo de aplicativo

Depois que o aplicativo for excluído, o tipo de aplicativo poderá ser desprovisionado, caso não seja mais necessário. Use o seguinte comando para desprovisionar o tipo de aplicativo

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

Aqui, o nome e a versão do tipo devem corresponder ao nome e à versão no manifesto do aplicativo provisionado anteriormente

### <a name="delete-application-package"></a>Excluir um pacote de aplicativos

Depois que o tipo de aplicativo for desprovisionado, o pacote de aplicativos poderá ser excluído do repositório de imagens, caso não seja mais necessário. A exclusão de pacotes de aplicativos ajuda a recuperar o espaço em disco. Use o seguinte comando para excluir o pacote de aplicativos do repositório de imagens:

```azurecli
az sf application package-delete --content-path app_package_dir
```

Aqui, o `content-path` deve ter o mesmo nome do diretório que foi carregado inicialmente durante a criação do aplicativo

## <a name="related-articles"></a>Artigos relacionados

* [Introdução ao Service Fabric e a CLI do Azure 2.0](service-fabric-azure-cli-2-0.md)
* [Introdução à CLI XPlat do Service Fabric](service-fabric-azure-cli.md)

