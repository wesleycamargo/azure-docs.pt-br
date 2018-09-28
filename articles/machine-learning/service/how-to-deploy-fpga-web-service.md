---
title: Implantar um modelo como um serviço Web em uma FPGA com o Azure Machine Learning
description: Saiba como implantar um serviço Web com um modelo em execução em uma FPGA com o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 09/24/2018
ms.openlocfilehash: ee67585a523ab96b1442d9eee3e9dfd55a758d32
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971477"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Implantar um modelo como um serviço Web em uma FPGA com o Azure Machine Learning

Você pode implantar um modelo como um serviço web em [campo matrizes de portões programáveis (FPGAs)](concept-accelerate-with-fpgas.md).  O uso de FPGAs fornece inferência de latência extremamente baixa, mesmo com um tamanho de lote único.   

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Um espaço de trabalho do Azure Machine Learning e o SDK de aprendizado de máquina do Azure para Python instalado. Aprenda como obter esses pré-requisitos usando o documento [Como configurar um ambiente de desenvolvimento](how-to-configure-environment.md).
 
  - Seu espaço de trabalho precisa estar na região *East US 2*.

  - Instale os extras contrib:

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>Criar e implantar seu modelo
Crie um pipeline para pré-processar a imagem de entrada, caracterize-a usando o ResNet 50 em um FPGA e, em seguida, execute os recursos por meio de um classificador treinado no conjunto de dados do ImageNet.

Siga as instruções para:

* Define o modelo pipeline
* Implantar o modelo
* Consumir o modelo implantado
* Excluir serviços implantados

> [!IMPORTANT]
> Para otimizar a latência e a taxa de transferência, seu cliente deve estar na mesma região do Azure que o ponto de extremidade.  Atualmente, as APIs são criadas na região do Azure Leste dos EUA.

### <a name="get-the-notebook"></a>Obter o bloco de anotações

Para sua conveniência, este tutorial está disponível como um notebook Jupyter. Use um desses métodos para executar o bloco `project-brainwave/project-brainwave-quickstart.ipynb`:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

### <a name="preprocess-image"></a>Imagem de pré-processamento
O primeiro estágio do pipeline é pré-processar as imagens.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```
### <a name="add-featurizer"></a>Adicionar recurso
Inicialize o modelo e baixe um ponto de verificação TensorFlow da versão quantizada do ResNet50 para ser usado como um featurizer.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Adicionar classificação
Este classificador foi treinado no conjunto de dados do ImageNet.

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>Criar definição de serviço
Agora que você definiu o pré-processamento, o recurso e o classificador de imagens que são executados no serviço, você pode criar uma definição de serviço. A definição de serviço é um conjunto de arquivos gerados a partir do modelo que é implantado no serviço FPGA. A definição de serviço consiste em um pipeline. O pipeline é uma série de estágios que são executados em ordem.  Estágios TensorFlow, estágios Keras e estágios BrainWave são suportados.  Os estágios são executados em ordem no serviço, com a saída de cada entrada de estágio no estágio subsequente.

Para criar um estágio do TensorFlow, especifique uma sessão contendo o gráfico (neste caso, o gráfico padrão é usado) e os tensores de entrada e saída para este estágio.  Essas informações são usadas para salvar o gráfico para que ele possa ser executado no serviço.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Implantar modelo
Crie um serviço da definição de serviço.  Seu espaço de trabalho precisa estar no local Leste dos EUA 2.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>Teste o serviço
Para enviar uma imagem para a API e testar a resposta, inclua um mapeamento do ID da classe de saída no nome da classe ImageNet.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Ligue para o seu serviço e substitua o nome do arquivo "sua-imagem.jpg" abaixo por uma imagem da sua máquina. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Limpeza de serviço
Exclua o serviço.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Proteger serviços web FPGA

Modelos de Machine Learning do Azure em execução em FPGAs fornecem suporte a SSL e autenticação baseada em chave. Isso permite que você restrinja o acesso ao serviço e proteja os dados enviados pelos clientes.

> [!IMPORTANT]
> A autenticação só está habilitada para serviços que forneceram um certificado SSL e uma chave. 
>
> Se você não habilitar o SSL, qualquer usuário na internet será capaz de fazer chamadas ao serviço.
>
> Se você habilitar o SSL, a chave de autenticação será exigida na hora de acessar o serviço.

O SSL criptografa os dados enviados entre o cliente e o serviço. Ele também é usado pelo cliente para verificar a identidade do servidor.

Você pode implantar um serviço com SSL habilitado ou atualizar um serviço já implantado para habilitá-lo. As etapas são as mesmas:

1. Adquira um nome de domínio.

2. Compre um certificado SSL.

3. Implante ou atualize o serviço com SSL habilitado.

4. Atualize o DNS para apontar para o serviço.

### <a name="acquire-a-domain-name"></a>Adquirir um nome de domínio

Se você ainda não tem um nome de domínio, pode comprá-lo de um __registrador de nome de domínio__. O processo é diferente entre registradores, assim como o custo. O registrador também fornece ferramentas para gerenciar o nome de domínio. Essas ferramentas são usadas para mapear um nome de domínio totalmente qualificado (como www.contoso.com) para o endereço IP em que o serviço está hospedado.

### <a name="acquire-an-ssl-certificate"></a>Compre um certificado SSL

Há muitas maneiras de obter um certificado SSL. A mais comum é comprar um de uma __CA__ (autoridade de certificação). Independentemente de onde você obtém o certificado, é necessário ter os seguintes arquivos:

* Um __certificado__. O certificado deve conter a cadeia de certificados completa e deve ser codificado em PEM.
* Uma __chave__. A chave deve ser codificada em PEM.

> [!TIP]
> Se a Autoridade de Certificação não puder fornecer o certificado e a chave como arquivos codificados em PEM, você poderá usar um utilitário como [OpenSSL](https://www.openssl.org/) para alterar o formato.

> [!IMPORTANT]
> Certificados autoassinados devem ser usados apenas para desenvolvimento. Eles não devem ser usados na produção.
>
> Se você usar um certificado autoassinado, confira a seção [Consumindo serviços com certificados autoassinados](#self-signed) para obter instruções específicas.

> [!WARNING]
> Ao solicitar um certificado, você deve fornecer o FQDN (nome de domínio totalmente qualificado) do endereço que planeja usar para o serviço. Por exemplo, www.contoso.com. O endereço identificado no certificado e o endereço usado pelos clientes são comparados na hora de validar a identidade do serviço.
>
> Se os endereços não coincidem, os clientes recebem um erro. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Implantar ou atualizar o serviço com SSL habilitado

Para implantar o serviço com SSL habilitado, defina o parâmetro `ssl_enabled` como `True`. Defina o parâmetro `ssl_certificate` como o valor do arquivo __certificate__ e a `ssl_key` como o valor do arquivo __key__. O exemplo abaixo demonstra como implantar um serviço com SSL habilitado:

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

A resposta da operação `create_service` contém o endereço IP do serviço. O endereço IP é usado na hora de mapear o nome DNS para o endereço IP do serviço.

A resposta também contém uma __chave primária__ e uma __chave secundária__ que são usadas para consumir o serviço.

### <a name="update-your-dns-to-point-to-the-service"></a>Atualizar o DNS para apontar para o serviço

Use as ferramentas fornecidas pelo registrador de nome de domínio para atualizar o registro DNS do nome de domínio. O registro deve apontar para o endereço IP do serviço.

> [!NOTE]
> Dependendo do registrador e da TTL (vida útil) configurados para o nome de domínio, pode levar vários minutos a várias horas até que os clientes possam resolver o nome de domínio.

### <a name="consume-authenticated-services"></a>Consumir serviços autenticados

Os exemplos abaixo demonstram como consumir um serviço autenticado usando Python e C#:

> [!NOTE]
> Substitua `authkey` pela chave primária ou secundária retornada na criação do serviço.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

Outros clientes gRPC podem autenticar solicitações definindo um cabeçalho de autorização. A abordagem geral é criar um objeto `ChannelCredentials` que combine `SslCredentials` com `CallCredentials`. Isso é adicionado ao cabeçalho de autorização da solicitação. Para saber mais sobre a implementação de suporte para cabeçalhos específicos, confira [ https://grpc.io/docs/guides/auth.html ](https://grpc.io/docs/guides/auth.html).

Os exemplos a seguir demonstram como definir o cabeçalho em C# e Go:

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>Consumindo serviços com certificados autoassinados

Há duas maneiras de habilitar o cliente para autenticação em um servidor protegido com um certificado autoassinado:

* No sistema do cliente, defina a variável de ambiente `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` no sistema do cliente a fim de apontar para o arquivo de certificado.

* Ao construir um objeto `SslCredentials`, passe o conteúdo do arquivo do certificado para o construtor.

O uso de um desses métodos faz com que gRPC use o certificado como o certificado raiz.

> [!IMPORTANT]
> gRPC não aceitar certificados não confiáveis. O uso de um certificado não confiável falhará com um código de status `Unavailable`. Os detalhes da falha contêm `Connection Failed`.
