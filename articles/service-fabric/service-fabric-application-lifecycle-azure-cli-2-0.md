---
title: Gerenciar aplicativos do Azure Service Fabric usando a CLI do Azure 2.0
description: Saiba como implantar e remover aplicativos de um cluster do Azure Service Fabric usando a CLI do Azure 2.0.
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 5728339236e3819b301e428f9d7a8add08f02b3e
ms.contentlocale: pt-br
ms.lasthandoff: 07/14/2017

---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-cli-20"></a>Gerenciar um aplicativo do Azure Service Fabric usando a CLI do Azure 2.0

Saiba como criar e excluir os aplicativos que estão em execução em um cluster do Azure Service Fabric.

## <a name="prerequisites"></a>Pré-requisitos

* Instale a CLI do Azure 2.0. Em seguida, selecione o cluster do Service Fabric. Para obter mais informações, consulte [Introdução à CLI do Azure 2.0](service-fabric-azure-cli-2-0.md).

* Tenha um pacote de aplicativos do Service Fabric pronto para ser implantado. Para obter mais informações sobre como criar e empacotar um aplicativo, leia sobre o [modelo de aplicativo do Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Visão geral

Para implantar um novo aplicativo, execute estas etapas:

1. Faça upload de um pacote de aplicativos no repositório de imagens do Service Fabric.
2. Provisione um tipo de aplicativo.
3. Especifique e crie um aplicativo.
4. Especifique e crie serviços.

Para remover um aplicativo existente, execute estas etapas:

1. Exclua o aplicativo.
2. Desprovisione o tipo de aplicativo associado.
3. Exclua o conteúdo do repositório de imagens.

## <a name="deploy-a-new-application"></a>Implantar um novo aplicativo

Para implantar um novo aplicativo, execute as seguintes tarefas.

### <a name="upload-a-new-application-package-to-the-image-store"></a>Carregar um novo pacote de aplicativos no repositório de imagens

Antes de criar um aplicativo, carregue o pacote de aplicativos no repositório de imagens do Service Fabric. 

Por exemplo, se o seu pacote de aplicativos está no diretório `app_package_dir`, use os seguintes comandos para carregar o diretório:

```azurecli
az sf application upload --path ~/app_package_dir
```

Para pacotes de aplicativos grandes, especifique a opção `--show-progress` para exibir o progresso do upload.

### <a name="provision-the-application-type"></a>Provisionar o tipo de aplicativo

Quando o upload for concluído, provisione o aplicativo. Para provisionar o aplicativo, use o seguinte comando:

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

O valor de `application-type-build-path` é o nome do diretório em que você carregou o pacote de aplicativos.

### <a name="create-an-application-from-an-application-type"></a>Criar um aplicativo com base em um tipo de aplicativo

Depois de provisionar o aplicativo, use o seguinte comando para nomear e criar seu aplicativo:

```azurecli
az sf application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` é o nome que você deseja usar para a instância do aplicativo. Você pode obter parâmetros adicionais no manifesto do aplicativo provisionado anteriormente.

O nome do aplicativo deve começar com o prefixo `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Criar serviços para o novo aplicativo

Depois de criar um aplicativo, crie serviços com base no aplicativo. No exemplo a seguir, criamos um novo serviço sem estado com base em nosso aplicativo. Os serviços que podem ser criados por meio de um aplicativo estão definidos em um manifesto do serviço dentro do pacote de aplicativos provisionado anteriormente.

```azurecli
az sf service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Verificar a integridade e a implantação do aplicativo

Para verificar se um aplicativo e um serviço foram implantados com êxito, verifique se o aplicativo e o serviço estão listados:

```azurecli
az sf application list
az sf service list --application-list TestApp
```

Para verificar se o serviço está íntegro, use comandos semelhantes para recuperar a integridade do serviço e do aplicativo:

```azurecli
az sf application health --application-id TestApp
az sf service health --service-id TestApp/TestSvc
```

Os serviços e aplicativos íntegros têm um valor `HealthState` igual a `Ok`.

## <a name="remove-an-existing-application"></a>Remover um aplicativo existente

Para remover um aplicativo, execute as seguintes tarefas.

### <a name="delete-the-application"></a>Excluir o aplicativo

Para excluir o aplicativo, use o seguinte comando:

```azurecli
az sf application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Desprovisionar o tipo de aplicativo

Depois de excluir o aplicativo, você pode desprovisionar o tipo de aplicativo se ele não for mais necessário. Para desprovisionar o tipo de aplicativo, use o seguinte comando:

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

O nome e a versão do tipo devem corresponder ao nome e à versão no manifesto do aplicativo provisionado anteriormente.

### <a name="delete-the-application-package"></a>Excluir o pacote de aplicativos

Depois de haver desprovisionado o tipo de aplicativo, você pode excluir o pacote de aplicativos do repositório de imagens, caso ele não seja mais necessário. A exclusão de pacotes de aplicativos ajuda a recuperar o espaço em disco. 

Para excluir o pacote de aplicativos do repositório de imagens, use o seguinte comando:

```azurecli
az sf application package-delete --content-path app_package_dir
```

`content-path` deve ser o nome do diretório que você carregou ao criar o aplicativo.

## <a name="related-articles"></a>Artigos relacionados

* [Introdução ao Service Fabric e à CLI do Azure 2.0](service-fabric-azure-cli-2-0.md)
* [Introdução à CLI XPlat do Service Fabric](service-fabric-azure-cli.md)

