---
title: Proteger serviços Web do Azure Machine Learning com SSL
description: Saiba como proteger um serviço Web implantado com o serviço do Azure Machine Learning. Você pode restringir o acesso a serviços Web e proteger os dados enviados pelos clientes usando SSL e autenticação baseada em chave.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.openlocfilehash: 885d867d0733ef923d327d8d6a36fc1588fd4961
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801005"
---
# <a name="secure-azure-machine-learning-web-services-with-ssl"></a>Proteger serviços Web do Azure Machine Learning com SSL

Neste artigo, você aprenderá como proteger um serviço Web implantado com o serviço do Azure Machine Learning. Você pode restringir o acesso a serviços Web e proteger os dados enviados pelos clientes usando SSL e autenticação baseada em chave.

> [!Warning]
> Se você não habilitar o SSL, qualquer usuário na Internet poderá fazer chamadas ao serviço Web.

O SSL criptografa os dados enviados entre o cliente e o serviço Web. Ele também é usado pelo cliente para verificar a identidade do servidor. A autenticação só está habilitada para serviços que forneceram um certificado SSL e uma chave.  Se você habilitar o SSL, uma chave de autenticação será necessária para acessar o serviço Web.

Se você implantar um serviço Web habilitado com SSL ou habilitar o SSL para um serviço Web implantado existente, as etapas serão as mesmas:

1. Obter um nome de domínio.

2. Obter um certificado SSL.

3. Implantar ou atualizar o serviço Web com a configuração de SSL habilitada.

4. Atualizar o DNS para apontar para o serviço Web.

Há pequenas diferenças ao proteger serviços Web entre os [destinos de implantação](how-to-deploy-and-where.md). 

## <a name="get-a-domain-name"></a>Obter um nome de domínio

Se você ainda não tem um nome de domínio, pode comprá-lo de um __registrador de nome de domínio__. O processo é diferente entre registradores, assim como o custo. O registrador também fornece ferramentas para gerenciar o nome de domínio. Essas ferramentas são usadas para mapear um nome de domínio totalmente qualificado (como www.contoso.com) para o endereço IP que hospeda o serviço Web.

## <a name="get-an-ssl-certificate"></a>Obter um certificado SSL

Há muitas maneiras de obter um certificado SSL. A mais comum é comprar um de uma __CA__ (autoridade de certificação). Independentemente de onde você obtém o certificado, é necessário ter os seguintes arquivos:

* Um __certificado__. O certificado deve conter a cadeia de certificados completa e deve ser codificado em PEM.
* Uma __chave__. A chave deve ser codificada em PEM.

Ao solicitar um certificado, você precisa fornecer o FQDN (nome de domínio totalmente qualificado) do endereço que planeja usar para o serviço Web. Por exemplo, www.contoso.com. O endereço identificado no certificado e o endereço usado pelos clientes são comparados na hora de validar a identidade do serviço Web. Se os endereços não coincidem, os clientes recebem um erro. 

> [!TIP]
> Se a Autoridade de Certificação não puder fornecer o certificado e a chave como arquivos codificados em PEM, você poderá usar um utilitário como [OpenSSL](https://www.openssl.org/) para alterar o formato.

> [!IMPORTANT]
> Certificados autoassinados devem ser usados apenas para desenvolvimento. Eles não devem ser usados na produção. Se você usa um certificado autoassinado, confira a seção [Consumindo serviços Web com certificados autoassinados](#self-signed) para obter instruções específicas.


## <a name="enable-ssl-and-deploy"></a>Habilitar SSL e implantar

Para implantar (ou reimplantar) o serviço com SSL habilitado, defina o parâmetro `ssl_enabled` para `True`, conforme o necessário. Defina o parâmetro `ssl_certificate` como o valor do arquivo __certificate__ e a `ssl_key` como o valor do arquivo __key__. 

+ **Implantar no AKS (Serviço de Kubernetes do Azure)**
  
  Ao provisionar o cluster do AKS, forneça valores para parâmetros relacionados a SSL, conforme é mostrado no snippet de código:

    ```python
    from azureml.core.compute import AksCompute
    
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Implantar nas ACI (Instâncias de Contêiner do Azure)**
 
  Durante a implantação nas ACI, forneça valores para os parâmetros relacionados a SSL, conforme é mostrado no snippet de código:

    ```python
    from azureml.core.webservice import AciWebservice
    
    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Implantar em FPGAs (Matrizes de Portas Programáveis em Campo)**

  A resposta da operação `create_service` contém o endereço IP do serviço. O endereço IP é usado na hora de mapear o nome DNS para o endereço IP do serviço. A resposta também contém uma __chave primária__ e uma __chave secundária__ que são usadas para consumir o serviço. Forneça valores para os parâmetros relacionados a SSL, conforme é mostrado no snippet de código

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

## <a name="update-your-dns"></a>Atualizar o DNS

Em seguida, você precisa atualizar o DNS para apontar para o serviço Web.

+ **Para ACI e FPGA**:  

  Use as ferramentas fornecidas pelo registrador de nome de domínio para atualizar o registro DNS do nome de domínio. O registro deve apontar para o endereço IP do serviço.  

  Dependendo do registrador e da TTL (vida útil) configurados para o nome de domínio, pode levar vários minutos a várias horas até que os clientes possam resolver o nome de domínio.

+ **Para AKS**: 

  Atualize o DNS na guia "Configuração" do "Endereço IP Público" do cluster do AKS, conforme é mostrado na imagem. Encontre o endereço IP público como um dos tipos de recursos criados no grupo de recursos que contém os nós de agente do AKS e outros recursos de rede.

  ![Serviço do Azure Machine Learning: protegendo serviços Web com SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="consume-authenticated-services"></a>Consumir serviços autenticados

### <a name="how-to-consume"></a>Como consumir 
+ **Para ACI e AKS**: 

  Para serviços Web das ACI e do AKS, veja como consumir serviços Web nestes artigos:
  + [Como implantar nas ACI](how-to-deploy-to-aci.md)

  + [Como implantar no AKS](how-to-deploy-to-aks.md)

+ **Para FPGA**:  

  Os exemplos a seguir demonstram como consumir um serviço da FPGA autenticado em Python e C#.
  Substitua `authkey` pela chave primária ou secundária que foi retornada quando o serviço foi implantado.

  Exemplo de Python:
    ```python
    from amlrealtimeai import PredictionClient
    client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
    image_file = R'C:\path_to_file\image.jpg'
    results = client.score_image(image_file)
    ```

  Exemplo de C#:
    ```csharp
    var client = new ScoringClient(host, 50051, useSSL, "authKey");
    float[,] result;
    using (var content = File.OpenRead(image))
        {
            IScoringRequest request = new ImageRequest(content);
            result = client.Score<float[,]>(request);
        }
    ```

### <a name="set-the-authorization-header"></a>Definir o cabeçalho de autorização
Outros clientes gRPC podem autenticar solicitações definindo um cabeçalho de autorização. A abordagem geral é criar um objeto `ChannelCredentials` que combine `SslCredentials` com `CallCredentials`. Isso é adicionado ao cabeçalho de autorização da solicitação. Para saber mais sobre a implementação de suporte para cabeçalhos específicos, confira [ https://grpc.io/docs/guides/auth.html ](https://grpc.io/docs/guides/auth.html).

Os exemplos a seguir demonstram como definir o cabeçalho em C# e Go:

+ Use C# para definir o cabeçalho:
    ```csharp
    creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                          async (context, metadata) =>
                          {
                              metadata.Add(new Metadata.Entry("authorization", "authKey"));
                              await Task.CompletedTask;
                          }));
    
    ```

+ Use Go para definir o cabeçalho:
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

<a id="self-signed"></a>

## <a name="consume-services-with-self-signed-certificates"></a>Consumir serviços com certificados autoassinados

Há duas maneiras de habilitar o cliente para autenticação em um servidor protegido com um certificado autoassinado:

* No sistema do cliente, defina a variável de ambiente `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` no sistema do cliente a fim de apontar para o arquivo de certificado.

* Ao construir um objeto `SslCredentials`, passe o conteúdo do arquivo do certificado para o construtor.

O uso de um desses métodos faz com que gRPC use o certificado como o certificado raiz.

> [!IMPORTANT]
> gRPC não aceitar certificados não confiáveis. O uso de um certificado não confiável falhará com um código de status `Unavailable`. Os detalhes da falha contêm `Connection Failed`.
