---
title: Como instalar e executar contêineres - Pesquisa Visual Computacional
titlesuffix: Azure Cognitive Services
description: Como baixar, instalar e executar contêineres para Pesquisa Visual Computacional neste tutorial passo a passo.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6a6468cd71cf83c627f6dd72e5a1fc5564361d50
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579848"
---
# <a name="install-and-run-recognize-text-containers"></a>Instalar e executar os contêineres de Reconhecimento de Texto

O uso de Contêineres é uma abordagem para distribuição de software na qual um aplicativo ou serviço é empacotado como uma imagem de contêiner. A configuração e as dependências do aplicativo ou serviço estão incluídas na imagem do contêiner. A imagem do contêiner pode então ser implantada em um host de contêiner com pouca ou nenhuma modificação. Os contêineres são isolados uns dos outros e do sistema operacional subjacente, com um espaço menor do que uma máquina virtual. Contêineres podem ser criados com base em imagens de contêiner para tarefas de curto prazo e removidos quando não são mais necessários.

A parte Reconhecimento de Texto da Pesquisa Visual Computacional também está disponível como um contêiner do Docker. Ela permite a você detectar e extrair texto impresso das imagens de vários objetos com diferentes superfícies e fundos, como recibos, cartazes e cartões de visita.  
> [!IMPORTANT]
> O contêiner de Reconhecimento de Texto atualmente funciona somente em inglês.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="preparation"></a>Preparação

Você deve cumprir os seguintes pré-requisitos antes de usar o contêiner de Reconhecimento de Texto:

**Mecanismo do Docker**: é necessário ter o Mecanismo do Docker instalado localmente. O Docker fornece pacotes que configuram o ambiente do Docker em [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) e [Windows](https://docs.docker.com/docker-for-windows/). No Windows, o Docker deve ser configurado para suportar contêineres do Linux. Os contêineres do Docker também podem ser implantados diretamente no [Serviço do Azure Kubernetes](../../aks/index.yml), [Instâncias do Contêiner do Azure](../../container-instances/index.yml) ou em um cluster do [Kubernetes](https://kubernetes.io/) implantado no [Azure Stack](../../azure-stack/index.yml). Para obter mais informações sobre como implantar o Kubernetes na Pilha do Azure, consulte [Implantar o Kubernetes na Pilha do Azure](../../azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).

O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure.

**Familiaridade com o Registro de Contêiner da Microsoft e com o Docker**: é necessário ter uma compreensão básica tanto de conceitos do Docker quanto do Registro de Contêiner da Microsoft, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos `docker`.  

Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

O contêiner de Reconhecimento de Texto requer um mínimo de 1 núcleo de CPU, pelo menos 2,6 GHz (gigahertz) ou mais rápido e 8 GB (gigabytes) de memória alocada, mas a configuração recomendável é pelo menos 2 núcleos de CPU e 8 GB de memória alocada.

## <a name="request-access-to-the-private-container-registry"></a>Solicitar acesso ao registro de contêiner privado

Você deve primeiro concluir e enviar o [formulário de Solicitação de Contêineres de Pesquisa Visual dos Serviços Cognitivos](https://aka.ms/VisionContainersPreview) para solicitar acesso ao contêiner de Reconhecimento de Texto. O formulário solicita informações sobre você, sua empresa e o cenário do usuário para o qual você usará o contêiner. Depois de submetida, a equipe de Serviços Cognitivos do Azure revisa o formulário para garantir que você atenda aos critérios de acesso ao registro do contêiner particular.

> [!IMPORTANT]
> Você deve usar um endereço de e-mail associado a uma Conta da Microsoft (MSA) ou uma conta do Microsoft Azure Active Directory (Azure AD) no formulário.

Se a solicitação for aprovada, você receberá então um email com instruções sobre como obter suas credenciais e acessar o registro de contêiner privado.

## <a name="create-a-computer-vision-resource-on-azure"></a>Criar um recurso de Pesquisa Visual Computacional no Azure

Se quiser usar o contêiner de Reconhecimento de Texto, você precisará criar um recurso de Pesquisa Visual Computacional no Azure. Depois de criar o recurso, você usa a chave de assinatura e a URL de ponto de extremidade do recurso para criar a instância do contêiner. Para obter mais informações sobre como criar uma instância de um contêiner, confira [Criar uma instância de um contêiner com base em uma imagem de contêiner baixada](#instantiate-a-container-from-a-downloaded-container-image).

Execute as etapas a seguir para criar e recuperar informações de um recurso do Azure:

1. Crie um recurso do Azure no portal do Azure.  
   Se quiser usar o contêiner de Reconhecimento de Texto, crie primeiro um recurso de Pesquisa Visual Computacional correspondente no portal do Azure. Para saber mais, consulte [Início Rápido: criar uma conta de Serviços Cognitivos no portal do Azure](../cognitive-services-apis-create-account.md).

1. Obtenha o URL do ponto de extremidade e a chave de assinatura do recurso do Azure.  
   Depois de criar o recurso do Azure, você deve usar a chave de assinatura e a URL de ponto de extremidade desse recurso para instanciar o contêiner de Reconhecimento de Texto correspondente. Você pode copiar a URL de ponto de extremidade e a chave de assinatura de, respectivamente, as páginas Início Rápido e de Chaves do recurso de Pesquisa Visual Computacional no portal do Azure.

## <a name="log-in-to-the-private-container-registry"></a>Fazer logon no registro de contêiner privado

Há várias maneiras de autenticar com o registro de contêiner particular para Contêineres de Serviços Cognitivos, mas o método recomendado a partir da linha de comandos é usando a [CLI do Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Use o comando [login do docker](https://docs.docker.com/engine/reference/commandline/login/), conforme mostrado no exemplo a seguir, para efetuar login no `containerpreview.azurecr.io`, o registro do contêiner particular para Contêineres de Serviços Cognitivos. Substitua *\<nome de usuário\>* pelo nome de usuário e *\<senha\>* com a senha fornecida nas credenciais recebidas da equipe do Azure Cognitive Services.

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se você tiver protegido suas credenciais em um arquivo de texto, poderá concatenar o conteúdo desse arquivo de texto, usando o `cat` comando, para o comando `docker login`, conforme mostrado no exemplo a seguir. Substitua *\<passwordFile\>* pelo caminho e nome do arquivo de texto que contém a senha e o *\<nome de usuário\>* com o nome de usuário fornecido em suas credenciais.

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

## <a name="download-container-images-from-the-private-container-registry"></a>Baixar imagens de contêiner do registro de contêiner privado

A imagem de contêiner para o contêiner de Reconhecimento de Texto está disponível de um registro de contêiner do Docker privado, chamado `containerpreview.azurecr.io`, no Registro de Contêiner do Azure. A imagem de contêiner para o contêiner de Reconhecimento de Texto precisa ser baixada do repositório para executar o contêiner localmente.

Use o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do repositório. Por exemplo, para baixar a imagem de contêiner de Reconhecimento de Texto mais recente do repositório, use o seguinte comando:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

Para obter uma descrição completa de marcas disponíveis para o contêiner de Reconhecimento de Texto, confira [Reconhecimento de Texto](https://go.microsoft.com/fwlink/?linkid=2018655) no Hub do Docker.

> [!TIP]
> Você pode usar o comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) comando para listar as imagens de contêiner baixadas. Por exemplo, o comando a seguir lista o ID, o repositório e a tag de cada imagem do contêiner transferida por download, formatada como uma tabela:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Instanciar um contêiner de uma imagem de contêiner baixada

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para instanciar um contêiner a partir de uma imagem de contêiner baixada. Por exemplo, o seguinte comando:

* Cria uma instância de um contêiner com base em uma imagem de contêiner de Reconhecimento de Texto
* Aloca dois núcleos de CPU e 8 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um PSEUDO-TTY para o contêiner
* Remove automaticamente o contêiner após ele sair

```docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westus.api.cognitive.microsoft.com/vision/v2.0 ApiKey=0123456789
```

Quando instanciado, você pode chamar operações do contêiner usando o URI de host do contêiner. Por exemplo, o seguinte URI de host representa o contêiner de Reconhecimento de Texto instanciado no exemplo anterior:

```http
http://localhost:5000/
```

> [!IMPORTANT]
> Você pode acessar a [Especificação de OpenAPI](https://swagger.io/docs/specification/about/) (anteriormente conhecida como a especificação de Swagger), que descreve as operações compatíveis com um contêiner instanciado, por meio do URI relativo `/swagger` para esse contêiner. Por exemplo, o seguinte URI fornece acesso à especificação OpenAPI para o contêiner de Reconhecimento de Texto instanciado no exemplo anterior:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Você pode [chamar as operações da API REST](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) disponíveis no seu contêiner para reconhecimento síncrono ou assíncrono de texto ou então usar a biblioteca de clientes do [SDK de Pesquisa Visual Computacional dos Serviços Cognitivos do Azure](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) para chamar essas operações.  
> [!IMPORTANT]
> Você precisará ter o SDK de Pesquisa Visual Computacional dos Serviços Cognitivos do Azure versão 3.2.0 ou posterior se você quiser usar a biblioteca de clientes com o seu contêiner.

### <a name="asynchronous-text-recognition"></a>Reconhecimento de texto assíncrono

Você pode usar as operações `POST /vision/v2.0/recognizeText` e `GET /vision/v2.0/textOperations/*{id}*` em conjunto para reconhecer assincronamente texto impresso em uma imagem, semelhante a como o serviço de Pesquisa Visual Computacional usa essas operações REST correspondentes. O contêiner de Reconhecimento de Texto reconhece apenas texto impresso, texto não manuscrito, neste momento, portanto, o parâmetro `mode` normalmente especificado para a operação de serviço de Pesquisa Visual Computacional é ignorado pelo contêiner de Reconhecimento de Texto.

### <a name="synchronous-text-recognition"></a>Reconhecimento de texto síncrono

Você pode usar a operação `POST /vision/v2.0/recognizeTextDirect` para, de forma síncrona, reconhecer texto impresso em uma imagem. Como essa operação é síncrona, o corpo da solicitação para esta operação é o mesmo que aquele para a operação `POST /vision/v2.0/recognizeText`, mas o corpo da resposta para esta operação é o mesmo que aquele retornado pela operação `GET /vision/v2.0/textOperations/*{id}*`.

### <a name="billing"></a>Cobrança

Os contêineres de Reconhecimento de Texto enviam informações de cobrança do Azure, usando um recurso de Pesquisa Visual Computacional correspondente em sua conta do Azure. As opções a seguir são usadas pelo contêiner de Reconhecimento de Texto para fins de cobrança:

| Opção | DESCRIÇÃO |
|--------|-------------|
| `ApiKey` | A chave de API do recurso de Pesquisa Visual Computacional usado para rastrear informações de cobrança.<br/>O valor dessa opção deve ser definido como uma chave de API para o recurso de Pesquisa Visual Computacional do Azure provisionado especificado em `Billing`. |
| `Billing` | O ponto de extremidade do recurso de Pesquisa Visual Computacional usado para rastrear informações de cobrança.<br/>O valor dessa opção precisa ser definido para o URI de ponto de extremidade de um recurso de Pesquisa Visual Computacional do Azure provisionado.|
| `Eula` | Indica que você aceitou a licença para o contêiner.<br/>O valor dessa opção deve ser definido como `accept`. |

> [!IMPORTANT]
> Todas as três opções devem ser especificadas com valores válidos ou o contêiner não será iniciado.

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](computer-vision-resource-container-config.md).

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar os contêineres de Pesquisa Visual Computacional. Em resumo:

* A Pesquisa Visual Computacional fornece um contêiner do Linux para o Docker, para detectar e extrair texto impresso.
* Imagens de contêiner são baixadas de um registro de contêiner privado no Azure.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar as operações em contêineres de Pesquisa Visual Computacional especificando o URI do host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](computer-vision-resource-container-config.md) para configurações
* Examinar [Visão geral da Pesquisa Visual Computacional](Home.md) para saber mais sobre como reconhecer texto impresso e manuscrito  
* Veja a [API da Pesquisa Visual Computacional](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) para obter detalhes sobre os métodos compatíveis com o contêiner.
* Veja as [Perguntas frequentes](FAQ.md) para resolver problemas relacionados à funcionalidade de Pesquisa Visual Computacional.
