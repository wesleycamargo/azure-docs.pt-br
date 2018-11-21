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
ms.openlocfilehash: ec7b956f080837b297bac56e6237ac0672601ce7
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344477"
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

> [!WARNING]
> Certificados autoassinados devem ser usados apenas para desenvolvimento. Eles não devem ser usados na produção. Certificados autoassinados podem causar problemas nos aplicativos clientes. Para obter mais informações, consulte a documentação das bibliotecas de rede usadas no aplicativo cliente.

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

  ![Serviço do Azure Machine Learning: protegendo serviços Web com SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)self-

## <a name="next-steps"></a>Próximas etapas

Saiba como [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md).