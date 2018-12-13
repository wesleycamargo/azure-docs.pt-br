---
title: Implantar serviços Cognitivos do Azure ao Azure Stack | Microsoft Docs
description: Saiba como implantar serviços Cognitivos do Azure para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: mabrigg
ms.reviewer: guanghu
ms.openlocfilehash: 5af508714b5eae5cdd23c940af0ae21300c0c5b8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53194653"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>Implantar serviços Cognitivos do Azure ao Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!Note]  
> Serviços Cognitivos do Azure no Azure Stack está em visualização.

Você pode usar os serviços Cognitivos do Azure com suporte de contêiner no Azure Stack. Suporte a contêiner nos serviços Cognitivos do Azure permite que você use as mesmas APIs avançadas que estão disponíveis no Azure. O uso de contêineres permite flexibilidade em onde implantar e hospedar os serviços fornecidos em [contêineres do Docker](https://www.docker.com/what-container). Suporte de contêiner está disponível atualmente em visualização para um subconjunto dos serviços Cognitivos do Azure, incluindo partes de [computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview), e [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), e [Vocal](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS).

Uso de contêineres é uma abordagem de distribuição de software em que um aplicativo ou serviço, incluindo suas dependências e a configuração, são empacotadas como uma imagem de contêiner. Com pouca ou nenhuma modificação, você pode implantar uma imagem em um host do contêiner. Cada contêiner é isolado de outros contêineres e do sistema operacional subjacente. O sistema, em si, tem apenas os componentes necessários para executar sua imagem. Um host do contêiner tem uma superfície menor do que uma máquina virtual. Além disso, você pode criar contêineres de imagens para tarefas de curto prazo e removida quando não for mais necessário.

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>Usar contêineres com os serviços Cognitivos no Azure Stack

- **O controle sobre dados**  
  Permitir que os usuários de seu aplicativo tem controle sobre seus dados ao usar os serviços Cognitivos. Você pode fornecer os serviços Cognitivos para os usuários do aplicativo que não é possível enviar dados para o Azure global ou a nuvem pública.

- **O controle sobre atualizações de modelo**  
  Fornece aos usuários do aplicativo para a versão e atualização dos modelos implantados em sua solução.

- **Arquitetura portátil**  
  Habilite a criação de uma arquitetura de aplicativo portátil para que você possa implantar sua solução para a nuvem pública, para uma nuvem privada local ou na borda. Você pode implantar seu contêiner no serviço de Kubernetes do Azure, instâncias de contêiner do Azure, ou em um cluster Kubernetes no Azure Stack. Para obter mais informações, consulte [Implantar Kubernetes no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

- **Alta taxa de transferência e baixa latência**  
   Fornece aos usuários de aplicativo a capacidade de dimensionar com picos de tráfego para alta taxa de transferência e baixa latência. Habilite os serviços Cognitivos executar no serviço Kubernetes do Azure fisicamente perto de seus dados e a lógica do aplicativo.

Com o Azure Stack, implante os contêineres de serviços Cognitivos em um cluster Kubernetes, juntamente com seus contêineres de aplicativo para alta disponibilidade e dimensionamento Elástico. Você pode desenvolver seu aplicativo, combinando os serviços Cognitivos com componentes criados em serviços de aplicativos, funções, Blob ou armazenamento, SQL ou mySQL bancos de dados. 

Para obter mais detalhes sobre os contêineres de serviços Cognitivos, acesse [suporte de contêiner em serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Implantar o mostrador da API do Azure

Este artigo descreve como implantar a API de detecção facial do Azure no cluster do Kubernetes no Azure Stack. Você pode usar a mesma abordagem para implantar outros contêineres de serviços cognitivos no cluster Kubernetes do Azure Stack.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará:

1.  Solicite acesso ao registro de contêiner para efetuar pull de imagens de contêiner de Face do registro de contêiner de serviços Cognitivos do Azure. Para obter detalhes, vá para a seção de [solicitar o acesso ao registro de contêiner privado](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Prepare um cluster Kubernetes no Azure Stack. Você pode seguir o artigo [implantar Kubernetes para o Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

## <a name="create-azure-resources"></a>Criar recursos do Azure

Crie um recurso de serviço cognitivo no Azure para visualizar os contêineres de Face, LUIS ou reconhecer texto, respectivamente. Você precisará usar a URL de ponto de extremidade e a chave de assinatura do recurso para instanciar os contêineres de serviço cognitivo.

1.  Crie um recurso do Azure no portal do Azure. Se você quiser visualizar os contêineres de Face, crie primeiro um recurso de Face correspondente no portal do Azure. Para obter mais informações, consulte [guia de início rápido: Criar uma conta de serviços Cognitivos no portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

    >  [!Note]  
    >  O recurso de Face ou visão do computador deve usar o tipo de preço F0.

2.  Obtenha o URL do ponto de extremidade e a chave de assinatura do recurso do Azure. Depois de criar o recurso do Azure, você deve usar a URL de ponto de extremidade e a chave de assinatura desse recurso para instanciar o contêiner de Face, LUIS ou reconhecer texto correspondente para a versão prévia.

## <a name="create-a-kubernetes-secret"></a>Criar um segredo do Kubernetes 

Faça uso o Kubectl criar comando secreto para acessar o registro de contêiner privado. Substitua **&lt;nome de usuário&gt;** com o nome de usuário e **&lt;senha&gt;** com a senha fornecida na credencial que você recebeu do Azure Equipe de serviço cognitivo.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>Preparar um YAML configurar arquivo

Você pretende usar o YAML a configurar o arquivo para simplificar a implantação do serviço cognitivo no cluster Kubernetes.

Aqui está um exemplo YAML configurar arquivo para implantar o serviço de detecção facial com o Azure Stack:

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

Esse YAML configura o arquivo, use o segredo que você usou para obter as imagens de contêiner de serviço cognitivo de registro de contêiner do Azure. Você e use o arquivo secreto para implantar uma réplica específica do contêiner. Você também pode criar um balanceador de carga para garantir que os usuários podem acessar esse serviço externamente.

Detalhes sobre os campos de chave:

| Campo | Observações |
| --- | --- |
| replicaNumber | Define as réplicas inicias de instâncias a serem criadas. Certamente, você pode dimensioná-lo mais tarde, após a implantação. |
| ImageLocation | Indica o local da imagem do contêiner de serviço cognitivo específico no ACR. Por exemplo, o serviço de detecção facial: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |A URL de ponto de extremidade anotado na etapa de [criar recurso do Azure](#create-azure-resources) |
| ApiKey | A chave de assinatura é anotada na etapa de [criar recurso do Azure](#create-azure-resources) |
| SecretName | O nome do segredo anotado na etapa de [criar secretas para acessar o registro de contêiner privado](#create-secrete-to-access-the-private-container-registry) |

## <a name="deploy-the-cognitive-service"></a>Implantar o serviço cognitivo

Uso do comando a seguir para implantar os contêineres de serviço cognitivo

```bash  
    Kubectl apply -f <yamlFineName>
```
Uso do comando a seguir para monitorar como ela implanta: 
```bash  
    Kubectl get pod – watch
```

## <a name="test-the-cognitive-service"></a>Testar o serviço cognitivo

Acesso a [especificação de OpenAPI](https://swagger.io/docs/specification/about/) (anteriormente conhecido como a especificação do Swagger), que descreve as operações com suporte por um contêiner de instâncias da **/swagger** URI relativo para o contêiner. Por exemplo, o seguinte URI fornece acesso à especificação OpenAPI para o contêiner de Análise de Sentimento instanciado no exemplo anterior:

```HTTP  
http:<External IP>:5000/swagger
```

Você pode obter o endereço IP externo do seguinte comando: 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Experimentar os serviços com o Python

Você pode tentar validar os serviços Cognitivos no Azure Stack executando alguns scripts de Python simples. Há exemplos oficiais de início rápido do Python para [computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview), e [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), e [vocal](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) ( LUIS) para sua referência.

Há duas coisas é preciso ter em mente para fazer com que o aplicativo Python executado em relação aos serviços em execução em contêineres: 
1. Serviços cognitivos no contêiner não é necessário sub chaves para autenticação, mas ainda precisamos qualquer cadeia de caracteres de entrada como um espaço reservado para satisfazer o SDK. 
2. Substitua o base_URL com o endereço IP do ponto de extremidade de serviço real 

Aqui está um exemplo Python script usado Face services SDK do Python para detectar e rostos em uma imagem de quadro. 

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>Próximas etapas

[Como instalar e executar os contêineres de API de visão do computador.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[Como instalar e executar os contêineres de API de detecção facial](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#create-a-face-resource-on-azure)

[Como instalar e executar os contêineres de API de análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[Como instalar e executar os contêineres de compreensão de idioma (LIUS)](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)