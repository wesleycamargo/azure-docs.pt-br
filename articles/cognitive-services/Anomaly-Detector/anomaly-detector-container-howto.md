---
title: Instalar e executar contêineres - detector de anomalias
titleSuffix: Azure Cognitive Services
description: Use algoritmos avançados da API de Detector de anomalias para identificar anomalias em seus dados de série temporal.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/07/2019
ms.author: aahi
ms.openlocfilehash: 5dcec0d5f313b1c746c0674d0f9bf4d30ed19e5c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026307"
---
# <a name="install-and-run-anomaly-detector-containers"></a>Instalar e executar contêineres do Detector de anomalias

O Detector de anomalias tem o seguinte contêiner: 

|Função|Recursos|
|-|-|
|Detector de anomalias| <li> Detecta anomalias conforme elas ocorrem em tempo real. <li> Detecta anomalias em todo o conjunto de dados como um lote. <li> Infere o intervalo normal esperado de seus dados. <li> Dá suporte à detecção de anomalias sensibilidade ajuste para melhor se ajustar a seus dados. |

Para obter informações detalhadas sobre as APIs, consulte:
* [Saiba mais sobre o serviço de API do Detector de anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você deve cumprir os seguintes pré-requisitos antes de usar contêineres do Detector de anomalias:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.| 
|Recursos do Detector de anomalias |Para usar esses contêineres, você deve ter:<br><br>Uma _Detector de anomalias_ recursos do Azure para obter a chave de cobrança associada e o URI de ponto de extremidade cobrança. Ambos os valores estão disponíveis nas páginas de visão geral do Detector de anomalias e chaves do portal do Azure e são necessários para iniciar o contêiner.<br><br>**{BILLING_KEY}**: chave do recurso<br><br>**{BILLING_ENDPOINT_URI}**: exemplo de URI de terminal é: `https://westus2.api.cognitive.microsoft.com`|

## <a name="request-access-to-the-container-registry"></a>Solicitação de acesso para o registro de contêiner

Você deve primeiro concluir e enviar o [formulário de solicitação de contêiner do Detector de anomalias](https://aka.ms/adcontainer) para solicitar acesso ao contêiner.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>O computador host

O **host** é o computador que executa o contêiner do Docker. Ele pode ser um computador local ou um serviço de hospedagem do Docker no Azure, incluindo:

* [Serviço de Kubernetes do Azure](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)
* [Instâncias de Contêiner do Azure](https://docs.microsoft.com/container-instances/index.yml)
* Cluster [Kubernetes](https://kubernetes.io/) implantado no [Azure Stack](https://docs.microsoft.com/azure-stack/index.yml). Para obter mais informações, consulte [Implantar Kubernetes no Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).
<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve os núcleos de CPU e memória para alocar para o contêiner do Detector de anomalias mínimos e recomendados.

| QPS (consultas por segundo) | Mínimo | Recomendadas |
|-----------|---------|-------------|
| 10 QPS | 4 núcleos, 1GB de memória | 2GB de memória de 8 núcleos |
| 20 QPS | 8 núcleos, 2GB de memória | 4GB de memória de 16 núcleos |

Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

Use o comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner.

| Contêiner | Repositório |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-anomaly-detector-container"></a>Pull do docker para o contêiner do Detector de anomalias

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias. Há outros [exemplos](anomaly-detector-container-configuration.md#example-docker-run-commands) do comando `docker run` disponíveis. 
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar qualquer um dos três contêineres. O comando usa os seguintes parâmetros:

| Placeholder | Value |
|-------------|-------|
|{BILLING_KEY} | Essa chave é usada para iniciar o contêiner e está disponível na página de chaves do Detector de anomalias do portal do Azure.  |
|{BILLING_ENDPOINT_URI} | O valor do URI de ponto de extremidade cobrança está disponível na página de visão geral de Detector de anomalias do portal do Azure.|

Substitua esses parâmetros por seus próprios valores no comando `docker run` de exemplo a seguir.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Esse comando:

* Executa um contêiner do Detector de anomalias de imagem de contêiner
* Aloca um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host. 

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Execução de vários contêineres no mesmo host

Se você pretende executar vários contêineres com portas expostas, certifique-se de executar cada contêiner com uma porta diferente. Por exemplo, execute o primeiro contêiner na porta 5000 e o segundo contêiner na porta 5001.

Substitua os `<container-registry>` e `<container-name>` com os valores dos contêineres que você usar. Eles não precisam estar no mesmo contêiner. Você pode ter o contêiner do Detector de anomalias e o contêiner de LUIS em execução no HOST juntos ou você pode ter vários contêineres de Detector de anomalias em execução. 

Execute o primeiro contêiner na porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Execute o segundo contêiner na porta 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Cada contêiner subsequente deve estar em uma porta diferente. 

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta com base em REST. 

Use o host, https://localhost:5000, para APIs de contêiner.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>solução de problemas

Se você executar o contêiner com uma [montagem](anomaly-detector-container-configuration.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem durante a inicialização ou execução do contêiner. 

## <a name="billing"></a>Cobrança

O envio de contêineres do Detector de anomalias cobrança informações do Azure, usando um _Detector de anomalias_ recurso em sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](anomaly-detector-container-configuration.md).

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar contêineres do Detector de anomalias. Em resumo:

* Detector de anomalias fornece um contêiner do Linux para o Docker, encapsulando a detecção de anomalias com o vs do lote de streaming, inferência de tipos de intervalo esperado e sensibilidade de ajuste.
* Imagens de contêiner são baixadas de um registro de contêiner privado do Azure dedicada para visualização de contêineres.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar as operações em contêineres do Detector de anomalias, especificando o URI do contêiner de host.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Contêineres de serviços cognitivos não enviar dados do cliente (por exemplo, os dados de série temporal que está sendo analisados) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](anomaly-detector-container-configuration.md) para configurações
* [Saiba mais sobre o serviço de API do Detector de anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
