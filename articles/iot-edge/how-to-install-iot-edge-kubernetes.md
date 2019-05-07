---
title: Como instalar o IoT Edge no Kubernetes | Microsoft Docs
description: Saiba mais sobre como instalar o IoT Edge no Kubernetes usando um ambiente de cluster de desenvolvimento local
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 66aca7be9a2df93d846d7e78bc64c93279afc2d1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160689"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Como instalar o IoT Edge em Kubernetes (versão prévia)

IoT Edge pode integrar com o Kubernetes para usá-lo como uma camada de infraestrutura flexível e altamente disponível. Ele registra um IoT Edge *definição de recurso personalizado* (CRD) com o servidor de API do Kubernetes. Além disso, ele fornece um *operador* (agente do IoT Edge) que reconcilia gerenciados na nuvem de estado desejado com o estado do cluster local. 

Tempo de vida de módulo é gerenciado pelo Agendador Kubernetes, que mantém a disponibilidade de módulo e escolhe o posicionamento. IoT Edge gerencia a plataforma de aplicativo do edge em execução na parte superior, reconciliando continuamente o estado desejado especificado no IoT Hub com o estado no cluster de borda. O modelo de aplicativo de borda ainda é o modelo familiar, com base em rotas e módulos do IoT Edge. O operador do agente do IoT Edge executa *automática* construções de tradução para os nativos do Kubernetes como pods, implantações, serviços, etc.

Aqui está um diagrama de arquitetura de alto nível:

![arch kubernetes](./media/how-to-install-iot-edge-kubernetes/k8s-arch.png)

Todos os componentes da implantação de borda está no escopo para um namespace de Kubernetes específico para o dispositivo, possibilitando que compartilham os mesmos recursos de cluster entre vários dispositivos de borda e suas implantações.

>[!NOTE]
>O IoT Edge em Kubernetes está em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="install-locally-for-a-quick-test-environment"></a>Instalar localmente para um ambiente de teste rápido

### <a name="prerequisites"></a>Pré-requisitos

* Kubernetes 1,10 ou mais recente. Se você não tiver uma configuração de cluster existente, você pode usar [Minikube](https://kubernetes.io/docs/setup/minikube/) para um ambiente de cluster local. 

* [Helm](https://helm.sh/docs/using_helm/#quickstart-guide), o Gerenciador de pacotes do Kubernetes.

* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para exibir e interagir com o cluster.

### <a name="setup-steps"></a>Etapas de instalação

1. Iniciar **Minikube**

    ``` shell
    minikube start
    ```

1. Inicializar o **Helm** componente do servidor (*tiller*) em seu cluster

    ``` shell
    helm init
    ```

1. Adicione o repositório do IoT Edge e atualize a instalação do helm

    ``` shell
    helm repo add edgek8s https://edgek8s.blob.core.windows.net/helm/
    helm repo update
    ```

1. [Criar um IoT Hub](../iot-hub/iot-hub-create-through-portal.md), [registrar um dispositivo IoT Edge](how-to-register-device-portal.md)e anote sua cadeia de caracteres de conexão.

1. Instalar iotedged e o agente do IoT Edge em seu cluster

    ```shell
    helm install \
    --name k8s-edge1 \
    --set "deviceConnectionString=replace-with-device-connection-string" \
    edgek8s/edge-kubernetes
    ```
1. Abra o painel do Kubernetes no navegador

    ```shell
    minikube dashboard
    ```

    Sob os namespaces de cluster, você verá uma para o dispositivo do IoT Edge seguindo a convenção *msiot -\<iothub-name >-\<edgedevice-name >*. Os pods de agente e iotedged do IoT Edge devem estar em execução nesse namespace.

1. Adicionar um módulo de sensor de temperatura simulados usando as etapas a [implantar um módulo](quickstart-linux.md#deploy-a-module) seção do guia de início rápido. Gerenciamento de módulo do IoT Edge é feito no portal do IoT Hub, assim como qualquer outro dispositivo do IoT Edge. Alterações locais para a configuração do módulo por meio de ferramentas do Kubernetes, não é recomendado porque eles podem ser substituídos.

1. Em poucos segundos, atualizando o **Pods** página sob o namespace de dispositivo de borda no painel listará o hub do IoT Edge e pods de sensor simulado como em execução com o hub do IoT Edge pod ingerir dados no IoT Hub.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos criados pela implantação de borda, use o comando a seguir com o nome usado na etapa 5 da seção anterior.

``` shell
helm delete --purge k8s-edge1
```

## <a name="next-steps"></a>Próximas etapas

### <a name="deploy-as-a-highly-available-edge-gateway"></a>Implantar como um gateway de borda altamente disponível 

O dispositivo de borda em um cluster Kubernetes pode ser usado como um gateway IoT para dispositivos downstream. Ele pode ser configurado para ser resiliente a falhas de nó, oferecendo alta disponibilidade para implantações de borda. Consulte este [passo a passo detalhado](https://github.com/Azure-Samples/iotedge-gateway-on-kubernetes) usar IoT Edge nesse cenário.