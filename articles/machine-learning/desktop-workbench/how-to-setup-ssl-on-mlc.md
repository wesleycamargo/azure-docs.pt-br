---
title: Habilitar o SSL em um cluster MLC (Computação do Machine Learning) do Azure | Microsoft Docs
description: Obter instruções para configurar o SSL para chamadas de pontuação em um cluster MLC (Computação do Azure Machine Learning)
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 982a6807ccaf393c3aea42f39f7e60bb7e0d3ac3
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "35630838"
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>Habilitar o SSL em um cluster MLC (Computação do Machine Learning) do Azure 

Essas instruções permitem que você configure o SSL para chamadas de pontuação em um cluster MLC (Computação do Machine Learning). 

## <a name="prerequisites"></a>Pré-requisitos 

1. Escolha um CNAME (nome DNS) a ser usado ao fazer chamadas de pontuação em tempo real.

2. Crie um certificado *sem senha* com esse CNAME.

3. Se o certificado ainda não estiver no formato PEM, converta-o em PEM usando ferramentas como *openssl*.

Você terá dois arquivos depois de concluir os pré-requisitos:

* Um arquivo para o certificado, por exemplo, `cert.pem`. Verifique se o arquivo tem a cadeia de certificados completa.
* Um arquivo para a chave, por exemplo, `key.pem`



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>Configurar um certificado SSL em um novo cluster do ACS

Usando a CLI do Azure Machine Learning, execute o seguinte comando com a opção `-c` para criar um cluster ACS com um certificado SSL anexado:

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>Configurar um certificado SSL em um cluster existente do ACS

Se estiver direcionando um cluster que foi criado sem SSL, adicione um certificado usando cmdlets do Azure PowerShell.

Você precisa fornecer a chave e o certificado em formato PEM bruto. Eles podem ser lidos em variáveis do PowerShell:

```
$keyValueInPemFormat = [IO.File]::ReadAllText('<path to key.pem file>')
$certValueInPemFormat = [IO.File]::ReadAllText('<path to cert.pem file>')
```
Adicionar o certificado ao cluster: 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>Mapear o CNAME e o Endereço IP

Crie um mapeamento entre o CNAME selecionado nos pré-requisitos e o endereço IP do FE (front-end) em tempo real. Para descobrir o endereço IP do FE, execute o comando a abaixo. A saída exibe um campo chamado “publicIpAddress”, que contém o endereço IP do front-end do cluster em tempo real. Consulte as instruções de seu provedor DNS para configurar um registro de FQDN usado em CNAME para o endereço IP público.



## <a name="auto-detection-of-a-certificate"></a>Detecção automática de um certificado 

Quando você cria um serviço Web em tempo real usando o comando “`az ml service create realtime`”, o Azure Machine Learning detecta automaticamente a configuração do SSL no cluster e configura automaticamente a URL de pontuação com o certificado designado. 

Para ver o status do certificado, execute o comando a seguir. Observe o prefixo “https” da URI de pontuação e o CNAME no nome do host. 

``` 
az ml service show realtime -n <service name>
{
                "id" : "<your service id>",
                "name" : "<your service name >",
                "state" : "Succeeded",
                "createdAt" : "<datetime>”,
                "updatedAt" : "< datetime>",
                "scoringUri" : "https://<your CNAME>/api/v1/service/<service name>/score"
}
```
