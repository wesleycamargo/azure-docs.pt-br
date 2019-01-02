---
title: Instalar, executar contêineres
titlesuffix: Face - Azure Cognitive Services
description: Como fazer o download, instalar e executar contêineres para o Face neste tutorial passo a passo.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 556cf755890f49e540afe64de6e485d9ebde2147
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086429"
---
# <a name="install-and-run-containers"></a>Instalar e executar contêineres

O uso de Contêineres é uma abordagem para distribuição de software na qual um aplicativo ou serviço é empacotado como uma imagem de contêiner. A configuração e as dependências do aplicativo ou serviço estão incluídas na imagem do contêiner. A imagem do contêiner pode então ser implantada em um host de contêiner com pouca ou nenhuma modificação. Os contêineres são isolados uns dos outros e do sistema operacional subjacente, com um espaço menor do que uma máquina virtual. Os contêineres podem ser instanciados a partir de imagens de contêiner para tarefas de curto prazo e removidos quando não são mais necessários.

O Face fornece um contêiner Linux padronizado para o Docker, chamado Face, que detecta rostos humanos em imagens e identifica atributos, incluindo referências faciais (como narizes e olhos), sexo, idade e outros recursos faciais preditos pela máquina. Além da detecção, o Face pode verificar se duas faces na mesma imagem ou imagens diferentes são iguais usando uma pontuação de confiança ou comparar faces em relação a um banco de dados para ver se uma face semelhante ou idêntica já existe. Além disso, também pode organizar rostos semelhantes em grupos, usando traços visuais compartilhados.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="preparation"></a>Preparação

Você deve atender aos seguintes pré-requisitos antes de usar o contêiner Face:

**Mecanismo do Docker**: é necessário ter o Mecanismo do Docker instalado localmente. O Docker fornece pacotes que configuram o ambiente do Docker em [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) e [Windows](https://docs.docker.com/docker-for-windows/). No Windows, o Docker deve ser configurado para suportar contêineres do Linux. Os contêineres do Docker também podem ser implantados diretamente no [Serviço do Azure Kubernetes](/azure/aks/), [Instâncias do Contêiner do Azure](/azure/container-instances/) ou em um cluster do [Kubernetes](https://kubernetes.io/) implantado no [Azure Stack](/azure/azure-stack/). Para obter mais informações sobre como implantar o Kubernetes na Pilha do Azure, consulte [Implantar o Kubernetes na Pilha do Azure](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure.

**Familiaridade com o Registro de Contêiner da Microsoft e com o Docker**: é necessário ter uma compreensão básica tanto de conceitos do Docker quanto do Registro de Contêiner da Microsoft, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos `docker`.  

Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos do contêiner

O container Face requer no mínimo 1 núcleo de CPU, pelo menos 2,6 gigahertz (GHz) ou mais rápido, e 4 gigabytes (GB) de memória alocada, mas recomendamos pelo menos 2 núcleos de CPU e 6 GB de memória alocada.

## <a name="request-access-to-the-private-container-registry"></a>Solicitar acesso ao registro de contêiner privado

Você deve primeiro preencher e enviar o [formulário Solicitação de Contêineres de Visão dos Serviços Cognitivos](https://aka.ms/VisionContainersPreview) para solicitar acesso ao contêiner Face. O formulário solicita informações sobre você, sua empresa e o cenário do usuário para o qual você usará o contêiner. Depois de submetida, a equipe de Serviços Cognitivos do Azure revisa o formulário para garantir que você atenda aos critérios de acesso ao registro do contêiner particular.

> [!IMPORTANT]
> Você deve usar um endereço de e-mail associado a uma Conta da Microsoft (MSA) ou uma conta do Microsoft Azure Active Directory (Azure AD) no formulário.

Se sua solicitação for aprovada, você receberá um e-mail com instruções descrevendo como obter suas credenciais e acessar o registro de contêiner privado.

## <a name="create-a-face-resource-on-azure"></a>Criar um recurso de face no Azure

Você deve criar um recurso Face no Azure se quiser usar o contêiner Face. Depois de criar o recurso, use a chave de assinatura e o URL do terminal do recurso para instanciar o contêiner. Para obter mais informações sobre como instanciar um contêiner, consulte [Instanciar um contêiner de uma imagem de contêiner transferida por download](#instantiate-a-container-from-a-downloaded-container-image).

Execute as etapas a seguir para criar e recuperar informações de um recurso Face:

1. Crie um recurso de rosto no portal do Azure.  
   Se você quiser usar o container Face, crie primeiro um recurso Face correspondente no portal do Azure. Para saber mais, consulte [Início Rápido: criar uma conta de Serviços Cognitivos no portal do Azure](../cognitive-services-apis-create-account.md).

1. Obtenha o URL do ponto de extremidade e a chave de assinatura do recurso do Azure.  
   Depois que o recurso do Azure for criado, você deverá usar o URL do ponto de extremidade e a chave de assinatura desse recurso para instanciar o contêiner Face correspondente. Você pode copiar o URL do ponto de extremidade e a chave de assinatura de, respectivamente, as páginas Início rápido e Chaves do recurso Face no portal do Azure.

## <a name="log-in-to-the-private-container-registry"></a>Efetue login no registro do contêiner particular

Há várias maneiras de autenticar com o registro de contêiner particular para Contêineres de Serviços Cognitivos, mas o método recomendado a partir da linha de comandos é usando a [CLI do Docker](https://docs.docker.com/engine/reference/commandline/cli/).

Use o comando [login do docker](https://docs.docker.com/engine/reference/commandline/login/), conforme mostrado no exemplo a seguir, para efetuar login no `containerpreview.azurecr.io`, o registro do contêiner particular para Contêineres de Serviços Cognitivos. Substitua *\<nome de usuário\>* pelo nome de usuário e *\<senha\>* com a senha fornecida nas credenciais recebidas da equipe do Azure Cognitive Services.

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Se você tiver protegido suas credenciais em um arquivo de texto, poderá concatenar o conteúdo desse arquivo de texto, usando o `cat` comando, para o comando `docker login`, conforme mostrado no exemplo a seguir. Substitua *\<passwordFile\>* pelo caminho e nome do arquivo de texto que contém a senha e o *\<nome de usuário\>* com o nome de usuário fornecido em suas credenciais.

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

## <a name="download-container-images-from-the-private-container-registry"></a>Faça o download de imagens de contêiner do registro do contêiner particular

A imagem do contêiner do contêiner Face está disponível em um registro de contêiner do Docker privado, denominado `containerpreview.azurecr.io`, no Registro de Contêiner do Azure. A imagem do contêiner do contêiner Face deve ser baixada do repositório para executar o contêiner localmente.

Use o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para fazer download de uma imagem de contêiner do repositório. Por exemplo, para baixar a última imagem do contêiner Face do repositório, use o seguinte comando:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

Para obter uma descrição completa das tags disponíveis para o contêiner Face, consulte [Reconhecer texto](https://go.microsoft.com/fwlink/?linkid=2018655) no Docker Hub.

> [!TIP]
> Você pode usar o comando [imagens do estivador](https://docs.docker.com/engine/reference/commandline/images/) para listar as imagens do contêiner transferidas por download. Por exemplo, o comando a seguir lista o ID, o repositório e a tag de cada imagem do contêiner transferida por download, formatada como uma tabela:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Instanciar um contêiner de uma imagem de contêiner baixada

Use o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) para instanciar um contêiner a partir de uma imagem de contêiner baixada. Por exemplo, o seguinte comando:

* Instancia um contêiner da imagem do container Face
* Aloca dois núcleos de CPU e 6 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
* Remove automaticamente o contêiner depois que ele sai

```Docker
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
```

> [!IMPORTANT]
> As opções `Eula`, `Billing` e `ApiKey` devem ser especificadas para instanciar o contêiner; caso contrário, o contêiner não será iniciado.  Para mais informações, consulte [Faturamento](#billing).

Depois de instanciado, você pode chamar as operações do contêiner usando o URI do host do contêiner. Por exemplo, a seguinte URI do host representa o contêiner Face que foi instanciado no exemplo anterior:

```http
http://localhost:5000/
```

> [!TIP]
> É possível acessar a [especificação OpenAPI](https://swagger.io/docs/specification/about/) (anteriormente, a especificação Swagger), descrevendo as operações suportadas por um contêiner instanciado, a partir do URI relativo `/swagger` para esse contêiner. Por exemplo, o seguinte URI fornece acesso à especificação OpenAPI para o contêiner Face que foi instanciado no exemplo anterior:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Você pode [chamar as operações da API REST](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/howtodetectfacesinimage) disponíveis em seu contêiner ou usar a biblioteca cliente [Biblioteca cliente do Azure Cognitive Face Client](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/) para chamar essas operações.  
> [!IMPORTANT]
> Você deve ter a Biblioteca de Cliente Facial dos Serviços Cognitivos do Azure versão 2.0 ou posterior, se quiser usar a biblioteca cliente com seu contêiner.

A única diferença entre chamar uma determinada operação de seu contêiner e chamar a mesma operação de um serviço correspondente no Azure é que você usará o URI do host do seu contêiner, em vez do URI do host de uma região do Azure, para chamar a operação. Por exemplo, se você quiser usar uma instância Face em execução na região do West US Azure para detectar faces, chame a seguinte operação da API REST:

```http
POST https://westus.api.cognitive.microsoft.com/face/v1.0/detect
```

Se você quisesse usar um contêiner Face em execução em sua máquina local em sua configuração padrão para detectar faces, chamaria a seguinte operação da API REST:

```http
POST http://localhost:5000/face/v1.0/detect
```

### <a name="billing"></a>Cobrança

O container Face envia informações de cobrança para o Azure, usando um recurso Face correspondente em sua conta do Azure. As seguintes opções de linha de comando são usadas pelo contêiner Face para fins de faturamento:

| Opção | DESCRIÇÃO |
|--------|-------------|
| `ApiKey` | A chave de API do recurso Face usada para rastrear informações de faturamento.<br/>O valor dessa opção deve ser definido como uma chave de API para o recurso provisionado do Face Azure especificado em `Billing`. |
| `Billing` | O endpoint do recurso Face usado para rastrear informações de faturamento.<br/>O valor dessa opção deve ser definido como o URI do terminal de um recurso Face Azure provisionado.|
| `Eula` | Indica que você aceitou a licença para o contêiner.<br/>O valor dessa opção deve ser definido como `accept`. |

> [!IMPORTANT]
> Todas as três opções devem ser especificadas com valores válidos ou o contêiner não será iniciado.

Para obter mais informações sobre essas opções, consulte [Configurar contêineres](face-resource-container-config.md).

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu conceitos e fluxo de trabalho para baixar, instalar e executar os contêineres de Análise de Texto. Em resumo:

* O Face fornece um contêiner do Linux para o Docker, chamado Face, para detectar rostos ou identificar rostos usando um banco de dados de pessoas.
* Imagens de contêiner são baixadas de um registro de contêiner particular no Azure.
* Imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar operações em contêineres Face, especificando o URI do host do contêiner.
* Você deve especificar informações de faturamento ao instanciar um contêiner.

> [!IMPORTANT]
> Os contêineres dos Serviços Cognitivos não estão licenciados para execução sem estarem conectados ao Azure para medição. Os clientes precisam ativar os contêineres para comunicar informações de cobrança com o serviço de medição em todos os momentos. Os contêineres de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Próximas etapas

* Revise [Configurar contêineres](face-resource-container-config.md) para configurações
* Analise [Visão geral da face](Overview.md) para saber mais sobre como detectar e identificar rostos  
* Consulte a [API Face](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para obter detalhes sobre os métodos suportados pelo contêiner.
* Consulte [Perguntas frequentes (FAQ)](FAQ.md) para resolver problemas relacionados à funcionalidade Face.
