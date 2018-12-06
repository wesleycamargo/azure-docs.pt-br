---
title: Suporte de contêiner nos Serviços Cognitivos do Azure
titleSuffix: Azure Cognitive Services
description: Saiba como os contêineres do Docker podem tornar os Serviços Cognitivos mais próximos de seus dados.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 05a364cc66d18909c7833138d351dc02bace32c2
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2018
ms.locfileid: "51822102"
---
# <a name="container-support-in-azure-cognitive-services"></a>Suporte de contêiner nos Serviços Cognitivos do Azure

O suporte a contêiner nos Serviços Cognitivos do Azure permite que os desenvolvedores usem as mesmas APIs ricas disponíveis no Azure e permite a flexibilidade de onde implantar e hospedar os serviços que vêm com [contêineres do Docker](https://www.docker.com/what-container). O suporte de contêiner atualmente está disponível em versão prévia de um subconjunto de Serviços Cognitivos do Azure, incluindo partes de [Pesquisa Visual Computacional](Computer-vision/Home.md), [Detecção Facial](Face/Overview.md) e [Análise de Texto](text-analytics/overview.md).

O uso de contêineres é uma abordagem para distribuição de software em que um aplicativo ou serviço, incluindo as dependências e configurações, é empacotado como uma imagem de contêiner. Com pouca ou nenhuma modificação, uma imagem de contêiner pode ser implantada em um host de contêiner. Os contêineres são isolados uns dos outros e do sistema operacional subjacente, com um espaço menor do que uma máquina virtual. Os contêineres poderão ser instanciados a partir de imagens de contêiner para tarefas de curto prazo e removidos, quando não forem mais necessários.

Assista a esse [vídeo rápido](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services) para ver uma demonstração.

Os serviços de [Pesquisa Visual Computacional](Computer-vision/Home.md), [Detecção Facial](Face/Overview.md) e [Análise de Textos](text-analytics/overview.md) estão disponíveis no [Microsoft Azure](https://azure.microsoft.com). Entre no [portal do Azure](https://portal.azure.com/) para criar e explorar recursos do Azure desses serviços.

## <a name="features-and-benefits"></a>Características e benefícios

- **Controle sobre dados**: permita que os clientes usem os Serviços Cognitivos com controle total sobre os dados.  Isso é essencial para clientes que não podem enviar dados para a nuvem, mas precisam acessar a tecnologia de Serviços Cognitivos. Dar suporte para consistência em ambientes híbridos – entre dados, gerenciamento, identidade e segurança.
- **Controle sobre atualizações de modelos**: fornecer aos clientes flexibilidade na versão e atualização dos modelos implantados nas soluções.
- **Arquitetura portátil**: habilite a criação de uma arquitetura de aplicativo portátil que possa ser implantada na nuvem, no local e na borda. Os contêineres também podem ser implantados diretamente no [Serviço de Kubernetes do Azure](/azure/aks/), [Instâncias de Contêiner do Azure](/azure/container-instances/), ou em um cluster do [Kubernetes](https://kubernetes.io/) implantado no [Azure Stack](/azure/azure-stack/). Para obter mais informações, consulte [Implantar Kubernetes no Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Alta taxa de transferência/baixa latência**: forneça aos clientes a capacidade de escalar para requisitos de alta taxa de transferência e baixa latência, permitindo que os Serviços Cognitivos sejam executados no Serviço de Kubernetes do Azure fisicamente próximo à lógica e aos dados do aplicativo.


## <a name="containers-in-azure-cognitive-services"></a>Contêineres nos Serviços Cognitivos do Azure

Os contêineres dos Serviços Cognitivos do Azure fornecem o seguinte conjunto de contêineres do Docker, cada um contendo um subconjunto de funcionalidades dos serviços dos Serviços Cognitivos do Azure:

| Serviço | Contêiner| DESCRIÇÃO |
|---------|----------|-------------|
|[Pesquisa Visual Computacional](Computer-vision/computer-vision-how-to-install-containers.md) |**Reconhecimento de Texto** |Extrai texto impresso de imagens de vários objetos com diferentes superfícies e planos de fundo como recibos, pôsters e cartões de visita.<br/><br/>**Importante:** atualmente, o contêiner de Reconhecimento de Texto funciona apenas com o idioma inglês.<br>[Solicitar acesso](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Detecção Facial](Face/face-how-to-install-containers.md) |**Detecção Facial** |Detecta rostos humanos em imagens e identifica atributos, incluindo referências faciais (como narizes e olhos), sexo, idade e outras características faciais previstas pelo computador. Além de detectar, a Detecção Facial pode verificar se duas faces na mesma imagem ou imagens diferentes são iguais usando uma pontuação de confiança, ou comparar faces em relação a um banco de dados para ver se uma face semelhante ou idêntica já existe. Além disso, também pode organizar rostos semelhantes em grupos, usando traços visuais compartilhados.<br>[Solicitar acesso](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Análise de Texto](text-analytics/how-tos/text-analytics-how-to-install-containers.md) |**Extração de Frases-Chave** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018757)) |Extrai frases-chave para identificar os principais pontos. Por exemplo, para o texto de entrada "A comida estava deliciosa e a equipe era maravilhosa", a API retorna os principais pontos de discussão: "comida" e "equipe maravilhosa". |
|[Análise de Texto](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|**Detecção de Idioma** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018759)) |Para até 120 idiomas, detecta em qual idioma o texto de entrada está escrito e informa um código de idioma único para cada documento enviado na solicitação. O código de idioma é emparelhado com uma pontuação que indica a intensidade da pontuação. |
|[Análise de Texto](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|**Análise de Sentimento** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018654)) |Analisa o texto bruto em busca de pistas sobre sentimentos positivos ou negativos. Essa API retorna uma pontuação de sentimento entre 0 e 1 para cada documento, em que 1 é a mais positiva. Os modelos de análise são pré-treinados usando um amplo corpo de texto e tecnologias de idioma natural da Microsoft. Para [idiomas selecionados](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages.md), a API pode analisar e pontuar qualquer texto bruto que você forneça, retornando diretamente os resultados ao aplicativo responsável pela chamada. |

## <a name="container-availability-in-azure-cognitive-services"></a>Disponibilidade de contêiner nos Serviços Cognitivos do Azure

Os contêineres dos Serviços Cognitivos do Azure estão disponíveis publicamente por meio da assinatura do Azure e as imagens do contêiner do Docker podem ser obtidas do Registro de Contêiner a Microsoft ou Hub do Docker. É possível usar o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro apropriado.

> [!IMPORTANT]
> Atualmente, é necessário concluir um processo de entrada para acessar os contêineres de [Detecção Facial](Face/face-how-to-install-containers.md) e [Reconhecimento de Texto](Computer-vision/computer-vision-how-to-install-containers.md), em que você preenche e envia um questionário com perguntas sobre você, sua empresa e o caso de uso para o qual deseja implementar os contêineres. Quando você tiver acesso e as credenciais fornecidas, poderá extrair as imagens de contêiner dos contêineres de Detecção Facial e Reconhecimento de Texto de um registro de contêiner particular hospedado pelo Registro de Contêiner do Azure.

## <a name="prerequisites"></a>Pré-requisitos

É necessário atender aos seguintes pré-requisitos antes de usar contêineres dos Serviços Cognitivos do Azure:

**Mecanismo do Docker**: é necessário ter o Mecanismo do Docker instalado localmente. O Docker fornece pacotes que configuram o ambiente do Docker em [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) e [Windows](https://docs.docker.com/docker-for-windows/). No Windows, o Docker deve ser configurado para dar suporte a contêineres do Linux. Os contêineres do Docker também podem ser implantados diretamente no [Serviço de Kubernetes do Azure](/azure/aks/) ou [Instâncias de Contêiner do Azure](/azure/container-instances/).

O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure.

**Familiaridade com Docker e Registro de Contêiner da Microsoft**: é necessário ter uma compreensão básica de ambos os conceitos de Docker e Registro de Contêiner da Microsoft, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos `docker`.  

Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).

Contêineres individuais podem ter seus próprios requisitos, incluindo requisitos de alocação de memória e servidor.

## <a name="developer-samples"></a>Exemplos do desenvolvedor

Exemplos de desenvolvedor estão disponíveis em nosso [repositório do GitHub](https://github.com/Azure-Samples/cognitive-services-containers-samples). 

## <a name="next-steps"></a>Próximas etapas

Instale e explore a funcionalidade fornecida pelos contêineres nos Serviços Cognitivos do Azure:

* [Instalar e usar contêineres de Pesquisa Visual Computacional](Computer-vision/computer-vision-how-to-install-containers.md)
* [Instalar e usar contêineres de Detecção Facial](Face/face-how-to-install-containers.md)
* [Instalar e usar contêineres de Análise de Texto](text-analytics/how-tos/text-analytics-how-to-install-containers.md)
