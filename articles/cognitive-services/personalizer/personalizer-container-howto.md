---
title: Instalar e executar contêineres – Personalizador
titleSuffix: Azure Cognitive Services
description: Como baixar, instalar e executar contêineres para o Personalizador.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 2a62fd288f9118882e5cd9899ab572d4b247fc5a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156795"
---
# <a name="install-and-run-personalizer-containers"></a>Instalar e executar contêineres do Personalizador

O Serviço do Personalizador tem os seguintes contêineres: 

|Função|Recursos|
|-|-|
|personalizador|Determine a melhor ação do contexto atual do conteúdo e do usuário.|

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você deve atender aos seguintes pré-requisitos antes de usar contêineres de serviço do Personalizador:

|Obrigatório|Finalidade|
|--|--|
|Mecanismo Docker| É necessário ter o Mecanismo Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | É necessário ter uma compreensão básica de conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos do `docker`.| 
|Recurso de Serviço do Personalizador |Para usar esses contêineres, será necessário ter:<br><br>Um recurso do Azure de _Serviço do Personalizador_ para obter o URI de ponto de extremidade de cobrança e a chave de cobrança associada. Ambos os valores estão disponíveis nas páginas Visão Geral e Chaves do Serviço do Personalizador no portal do Azure e são necessários para iniciar o contêiner.<br><br>**{BILLING_KEY}**: chave do recurso<br><br>**{BILLING_ENDPOINT_URI}**: exemplo de URI de terminal é: `https://westus.api.cognitive.microsoft.com/`|

### <a name="the-host-computer"></a>O computador host

O **host** é o computador que executa o contêiner do Docker. Ele pode ser um computador local ou um serviço de hospedagem do Docker no Azure, incluindo:

* [Serviço de Kubernetes do Azure](https://docs.microsoft.com/aks/index.yml)
* [Instâncias de Contêiner do Azure](https://docs.microsoft.com/container-instances/index.yml)
* Cluster [Kubernetes](https://kubernetes.io/) implantado no [Azure Stack](https://docs.microsoft.com/azure-stack/index.yml). Para obter mais informações, consulte [Implantar Kubernetes no Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

A tabela a seguir descreve os núcleos de CPU e a memória mínimos e recomendados a serem alocados para cada contêiner do Serviço do Personalizador.

| Contêiner | Mínimo | Recomendadas |
|-----------|---------|-------------|
|personalizador | 1 núcleo, 4 GB de memória | 2 núcleos, 8 GB de memória |

Cada núcleo precisa ser de pelo menos 2,6 GHz (gigahertz) ou mais rápido.

Memória e núcleo correspondem às configurações `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

O contêiner deve ser capaz de conectar-se ao Hub de Eventos do Azure para retransmitir informações nas chamadas de Classificação e Recompensa para o servidor do Personalizador no Azure, além de conectar-se à API do Personalizador para carregar a configuração necessária e os modelos de machine learning mais recentes.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

Imagens de contêiner para o Serviço do Personalizador estão disponíveis. 

| Contêiner | Repositório |
|-----------|------------|
| personalizador | `mcr.microsoft.com/azure-cognitive-services/personalizer:latest` |

> [!TIP]
> Você pode usar o comando [imagens do estivador](https://docs.docker.com/engine/reference/commandline/images/) para listar as imagens do contêiner transferidas por download. Por exemplo, o comando a seguir lista o ID, o repositório e a tag de cada imagem do contêiner transferida por download, formatada como uma tabela:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/personalizer                 latest
>  ``` 

### <a name="docker-pull-for-the-personalizer-service-container"></a>Docker pull para o contêiner de Serviço do Personalizador

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/personalizer:latest
```

## <a name="how-the-container-works"></a>Como funciona o contêiner

O contêiner do Personalizador representa a parte do cliente do serviço do Personalizador. Quando o contêiner é executado, ele obtém os modelos e as definições de configuração do serviço do Personalizador na nuvem do Azure. O serviço de contêiner usa essas informações para responder a solicitações para **classificação** e **recompensa**. O contêiner também envia essas solicitações para o recurso de Personalizador na nuvem do Azure. Essas informações então são usadas para treinar seu modelo de loop do Personalizador com base na configuração atual para a frequência de atualização do modelo. O modelo atualizado é enviado de volta ao contêiner. 

![Arquitetura local para o cliente do contêiner do Personalizador](./media/personalizer-container-howto/personalizer-container-architecture.png)

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run) com as configurações de cobrança necessárias. Há outros [exemplos](personalizer-container-configuration.md#example-docker-run-commands) do comando `docker run` disponíveis. 
1. [Consulte o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Executar o contêiner com `docker run`

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para executar qualquer um dos três contêineres. O comando usa os seguintes parâmetros:

| Placeholder | Valor |
|-------------|-------|
|{BILLING_KEY} | Essa chave é usada para iniciar o contêiner e está disponível na página Chaves de Serviço do Personalizador do portal do Azure.  |
|{BILLING_ENDPOINT_URI} | O valor do URI do ponto de extremidade de cobrança está disponível na página Visão Geral do Serviço do Personalizador do portal do Azure.|

Substitua esses parâmetros por seus próprios valores no comando `docker run` de exemplo a seguir.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/personalizer\
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Esse comando:

* Executar um contêiner do Serviço do Personalizador usando a imagem de contêiner
* Aloca um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
* Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host. 

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para executar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

### <a name="run-multiple-containers-on-the-same-host"></a>Executar vários contêineres no mesmo host

Se você pretende executar vários contêineres com portas expostas, execute cada contêiner com uma porta diferente. Por exemplo, execute o primeiro contêiner na porta 5000 e o segundo contêiner na porta 5001.

Substitua `<container-registry>` e `<container-name>` pelos valores dos contêineres que você usa. Eles não precisam estar no mesmo contêiner. Você pode ter o contêiner do Personalizador e o contêiner do LUIS em execução no HOST juntos ou pode ter vários contêineres do Personalizador em execução. 

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

Cada contêiner seguinte deve estar em uma porta diferente. 

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta com base em REST. 

Use o host, https://localhost:5000, para APIs de contêiner.

## <a name="stop-the-container"></a>Parar o contêiner

Para desligar o contêiner, no ambiente de linha de comando em que o contêiner estiver em execução, pressione **Ctrl+C**.

## <a name="troubleshoot"></a>Solucionar problemas

Se você executar o contêiner com uma [montagem](personalizer-container-configuration.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem durante a inicialização ou execução do contêiner. 

## <a name="container-api-documentation"></a>Documentação da API do contêiner

O contêiner fornece um conjunto completo de documentação para os pontos de extremidade, bem como um recurso `Try it now`. Esse recurso permite que você insira suas configurações em um formulário HTML baseado na Web e faça a consulta sem precisar escrever nenhum código. Depois que a consulta é retornada, um exemplo de comando CURL é fornecido para demonstrar o formato do corpo e dos cabeçalhos HTTP exigidos. 

> [!TIP]
> Leia a [Especificação de OpenAPI](https://swagger.io/docs/specification/about/), que descreve as operações de API com suporte pelo contêiner, no URI relativo do `/swagger`. Por exemplo: 
>
>  ```http
>  http://localhost:5000/swagger
>  ```

## <a name="billing"></a>Cobrança

Os contêineres de Serviço do Personalizador enviam informações sobre cobrança para o Azure usando um recurso de _Serviço do Personalizador_ em sua conta do Azure. 

Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres dos Serviços Cognitivos não enviam dados do cliente para a Microsoft. 

O comando `docker run` usa os seguintes argumentos para fins de cobrança:

| Opção | DESCRIÇÃO |
|--------|-------------|
| `ApiKey` | A chave de API do recurso de _Serviço do Personalizador_ usada para rastrear as informações de cobrança. |
| `Billing` | O ponto de extremidade do recurso de _Serviço do Personalizador_ usado para rastrear as informações de cobrança.|
| `Eula` | Indica que você aceitou a licença para o contêiner.<br/>O valor dessa opção deve ser definido como `accept`. |

> [!IMPORTANT]
> Todas as três opções devem ser especificadas com valores válidos ou o contêiner não será iniciado.

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](personalizer-container-configuration.md).

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar os contêineres de Serviço do Personalizador. Em resumo:

* O Serviço do Personalizador fornece contêineres do Linux para Docker, encapsulando personalização.
* Imagens de contêiner são baixadas de um MCR (Registro de Contêiner da Microsoft) no Azure.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar operações em contêineres de Serviço do Personalizador especificando o URI do host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres do Personalizador também enviarão os dados da solicitação para o serviço correspondente no Azure para fins de treinamento online e obterão modelos atualizados periodicamente do Azure.

## <a name="next-steps"></a>Próximas etapas

[Como configurar o contêiner do Docker para o comando `Docker Run`](personalizer-container-configuration.md)