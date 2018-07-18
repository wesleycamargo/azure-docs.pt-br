---
title: Como implantar um modelo como um serviço Web em uma FPGA com o Azure Machine Learning
description: Saiba como implantar um serviço Web com um modelo em execução em uma FPGA com o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: f3237980a1ad1969b5cf8d42d547ddf96608dd97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33784465"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Implantar um modelo como um serviço Web em uma FPGA com o Azure Machine Learning

Neste documento, você aprenderá a configurar o ambiente de estação de trabalho e a implantar um modelo como um serviço Web em [FPGA (matrizes de portas programáveis em campo)](concept-accelerate-with-fpgas.md). O serviço Web usa o Project Brainwave para executar o modelo em FPGA.

O uso de FPGAs fornece inferência de latência extremamente baixa, mesmo com um tamanho de lote único.

## <a name="create-an-azure-machine-learning-model-management-account"></a>Criar uma conta do Gerenciamento de Modelos do Azure Machine Learning

1. Vá para a página de criação da Conta do Gerenciamento de Modelos no [Portal do Azure](https://aka.ms/aml-create-mma).

2. No portal, crie uma Conta do Gerenciamento de Modelos na região **Leste dos EUA 2**.

   ![Imagem da tela Criar Conta do Gerenciamento de Modelos](media/how-to-deploy-fpga-web-service/azure-portal-create-mma.PNG)

3. Dê um nome à Conta do Gerenciamento de Modelos, escolha uma assinatura e escolha um grupo de recursos.

   >[!IMPORTANT]
   >Em Local, você deve escolher **Leste dos EUA 2** como a região.  Não há outras regiões disponíveis no momento.

4. Escolha um tipo de preço (S1 é suficiente, mas S2 e S3 também funcionam).  Não há suporte para a camada DevTest.  

5. Clique em **Selecionar** para confirmar o tipo de preço.

6. Clique em **Criar** no Gerenciamento de Modelos do ML à esquerda.

## <a name="get-model-management-account-information"></a>Obter informações de conta do Gerenciamento de Modelos

Para obter informações sobre sua MMA (Conta do Gerenciamento de Modelos), clique na __Conta do Gerenciamento de Modelos__ no portal do Azure.

Copie os valores dos seguintes itens:

+ Nome da Conta do Gerenciamento de Modelos (no canto superior esquerdo)
+ Nome do grupo de recursos
+ ID da assinatura
+ Local (use "eastus2")

![Informações da conta do Gerenciamento de Modelos](media/how-to-deploy-fpga-web-service/azure-portal-mma-info.PNG)

## <a name="set-up-your-machine"></a>Configurar seu computador

Para configurar a estação de trabalho para implantação da FPGA, siga estas etapas:

1. Baixe e instale a versão mais recente do [Git](https://git-scm.com/downloads).

2. Instalar o [Anaconda (Python 3.6)](https://conda.io/miniconda.html).

3. Para baixar o ambiente Anaconda, use o seguinte comando em um prompt do Git:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```

4. Para criar o ambiente, abra um **prompt do Anaconda** (não em um prompt do Azure Machine Learning Workbench) e execute o seguinte comando:

    > [!IMPORTANT]
    > O arquivo `environment.yml` está no repositório git clonado na etapa anterior. Altere o caminho conforme o necessário para apontar para o arquivo na estação de trabalho.

    ```
    conda env create -f environment.yml
    ```

5. Para ativar o ambiente, use o seguinte comando:

    ```
    conda activate amlrealtimeai
    ```

6. Para iniciar o servidor do Jupyter Notebook, use o seguinte comando:

    ```
    jupyter notebook
    ```

    A saída desse comando é semelhante ao texto a seguir:

    ```text
    Copy/paste this URL into your browser when you connect for the first time, to login with a token:
        http://localhost:8888/?token=bb2ce89cc8ae931f5df50f96e3a6badfc826ff4100e78075
    ```

    > [!TIP]
    > Você receberá um token diferente cada vez que executar o comando.

    Se seu navegador não abrir automaticamente no Jupyter Notebook, use a URL HTTP retornada pelo comando anterior para abrir a página.

    ![Imagem da página do Jupyter Notebook](./media/how-to-deploy-fpga-web-service/jupyter-notebook.png)

## <a name="deploy-your-model"></a>Implantar o seu modelo

No Jupyter Notebook, abra o notebook `00_QuickStart.ipynb` do diretório `notebooks/resnet50`. Siga as instruções no bloco de notas para:

* Definir o serviço
* Implantar o modelo
* Consumir o modelo implantado
* Excluir serviços implantados

> [!IMPORTANT]
> Para otimizar a latência e a taxa de transferência, sua estação de trabalho deve estar na mesma região do Azure que o ponto de extremidade.  Atualmente, as APIs são criadas na região do Azure Leste dos EUA.

## <a name="ssltls-and-authentication"></a>SSL/TLS e autenticação

O Azure Machine Learning dá suporte a SSL e autenticação baseada em chave. Isso permite que você restrinja o acesso ao serviço e proteja os dados enviados pelos clientes.

> [!NOTE]
> As etapas nesta seção se aplicam somente a modelos Hardware Accelerated do Azure Machine Learning. Para serviços padrão do Azure Machine Learning, confira o documento [Como configurar SSL em computação do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/how-to-setup-ssl-on-mlc).

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

### <a name="consuming-authenticated-services"></a>Consumindo serviços autenticados

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
> O gRPC não aceitará certificados não confiáveis. O uso de um certificado não confiável falhará com um código de status `Unavailable`. Os detalhes da falha contêm `Connection Failed`.
