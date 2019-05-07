---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
description: Saiba como os contêineres do Docker podem tornar os Serviços Cognitivos mais próximos de seus dados.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 241bda5c684197a43cc5564e950e924fed668b89
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147577"
---
# <a name="container-support-in-azure-cognitive-services"></a>Suporte de contêiner nos Serviços Cognitivos do Azure

O suporte a contêiner nos Serviços Cognitivos do Azure permite que os desenvolvedores usem as mesmas APIs ricas disponíveis no Azure e permite a flexibilidade de onde implantar e hospedar os serviços que vêm com [contêineres do Docker](https://www.docker.com/what-container). O suporte de contêiner está disponível atualmente em visualização para um subconjunto dos serviços Cognitivos do Azure, incluindo partes de:

* [Detector de anomalias](Anomaly-Detector/overview.md)
* [Pesquisa Visual Computacional](Computer-vision/Home.md)
* [Detecção Facial](Face/Overview.md)
* [Reconhecedor de formulário](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409)
* [Compreensão de idioma](LUIS/luis-container-howto.md) (LUIS)
* [Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
* [API do Serviço de Fala](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409)
* [Análise de Texto](text-analytics/overview.md)

O uso de contêineres é uma abordagem para distribuição de software em que um aplicativo ou serviço, incluindo as dependências e configurações, é empacotado como uma imagem de contêiner. Com pouca ou nenhuma modificação, uma imagem de contêiner pode ser implantada em um host de contêiner. Os contêineres são isolados uns dos outros e do sistema operacional subjacente, com um espaço menor do que uma máquina virtual. Os contêineres poderão ser instanciados a partir de imagens de contêiner para tarefas de curto prazo e removidos, quando não forem mais necessários.

O vídeo a seguir demonstra como usar esse contêiner dos Serviços Cognitivos.

[![Demonstração de contêiner para os Serviços Cognitivos](./media/index/containers-video-image.png)](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services)

Recursos dos serviços cognitivos estão disponíveis no [Microsoft Azure](https://azure.microsoft.com). Entre no [portal do Azure](https://portal.azure.com/) para criar e explorar recursos do Azure desses serviços.

## <a name="features-and-benefits"></a>Características e benefícios

- **Controle sobre os dados**: permitir que os clientes escolham onde esses Serviços Cognitivos processam seus dados. Isso é essencial para clientes que não podem enviar dados para a nuvem, mas precisam acessar a tecnologia de Serviços Cognitivos. Suporte para consistência em ambientes híbridos – entre dados, gerenciamento, identidade e segurança.
- **Controle sobre atualizações do modelo**: fornecer aos clientes flexibilidade na versão e atualização dos modelos implantados nas soluções.
- **Arquitetura portátil**: habilite a criação de uma arquitetura de aplicativo portátil que possa ser implantada no Azure, localmente e na borda. Os contêineres podem ser implantados diretamente no [Serviço de Kubernetes do Azure](../aks/index.yml), nas [Instâncias de Contêiner do Azure](../container-instances/index.yml) ou em um cluster do [Kubernetes](https://kubernetes.io/) implantado no [Azure Stack](/azure-stack/operator). Para obter mais informações, consulte [Implantar Kubernetes no Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Baixa latência/alta produtividade**: forneça aos clientes a capacidade de escalar para requisitos de alta taxa de transferência e baixa latência, permitindo que os Serviços Cognitivos sejam executados fisicamente próximo à lógica e aos dados do aplicativo. Os contêineres não limitam as transações por segundo (TPS) e poderão ser escalados vertical e horizontalmente para lidar com a demanda se você fornecer os recursos de hardware necessários. 


## <a name="containers-in-azure-cognitive-services"></a>Contêineres nos Serviços Cognitivos do Azure

Os contêineres dos Serviços Cognitivos do Azure fornecem o seguinte conjunto de contêineres do Docker, cada um contendo um subconjunto de funcionalidades dos serviços dos Serviços Cognitivos do Azure:

| Serviço | Suporte para o tipo de preço | Contêiner | DESCRIÇÃO |
|---------|----------|----------|-------------|
|[Detector de anomalias](https://go.microsoft.com/fwlink/?linkid=2083925&clcid=0x409) |F0, S0|**Detector de anomalias** |A API do Detector de anomalias permite monitorar e detectar anormalidades nos seus dados de série temporal com aprendizado de máquina.<br>[Solicitar acesso](https://aka.ms/adcontainer)|
|[Pesquisa Visual Computacional](Computer-vision/computer-vision-how-to-install-containers.md) |F0, S1|**Reconhecimento de Texto** |Extrai texto impresso de imagens de vários objetos com diferentes superfícies e planos de fundo como recibos, pôsters e cartões de visita.<br/><br/>**Importante:** O contêiner de Reconhecimento de Texto atualmente funciona somente em inglês.<br>[Solicitar acesso](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Detecção Facial](Face/face-how-to-install-containers.md) |F0, S0|**Detecção Facial** |Detecta rostos humanos em imagens e identifica atributos, incluindo referências faciais (como narizes e olhos), sexo, idade e outras características faciais previstas pelo computador. Além de detectar, a Detecção Facial pode verificar se duas faces na mesma imagem ou imagens diferentes são iguais usando uma pontuação de confiança, ou comparar faces em relação a um banco de dados para ver se uma face semelhante ou idêntica já existe. Além disso, também pode organizar rostos semelhantes em grupos, usando traços visuais compartilhados.<br>[Solicitar acesso](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Reconhecedor de formulário](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) |F0, S0|**Reconhecedor de formulário** |Noções básicas sobre o formulário se aplica a tecnologia de aprendizado de máquina para identificar e extrair pares chave-valor e tabelas de formulários.<br>[Solicitar acesso](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS](LUIS/luis-container-howto.md) |F0, S0|**LUIS** ([imagem](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Carrega um modelo de reconhecimento vocal treinado ou publicado, também conhecido como um aplicativo LUIS, para um contêiner do Docker e fornece acesso às previsões de consulta dos pontos de extremidade da API do contêiner. Você pode coletar logs de consulta do contêiner e carregá-los novamente no [portal do LUIS](https://www.luis.ai) para aumentar a precisão da previsão do aplicativo.|
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Personalizer do Azure é um serviço de API baseado em nuvem que permite que você escolha a melhor experiência para mostrar aos seus usuários, aprendendo com o seu comportamento em tempo real.|
|[API do Serviço de Fala](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409) |F0, S0|**Conversão de fala em texto** |Transcreve fala em tempo real contínua em texto.<br>[Solicitar acesso](https://aka.ms/speechcontainerspreview/)|
|[API do Serviço de Fala](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409) |F0, S0|**Conversão de texto em fala** |Converte o texto em fala que soa natural.<br>[Solicitar acesso](https://aka.ms/speechcontainerspreview/)|
|[Análise de Texto](text-analytics/how-tos/text-analytics-how-to-install-containers.md) |F0, S|**Extração de Frases-Chave** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Extrai frases-chave para identificar os principais pontos. Por exemplo, para o texto de entrada "A comida estava deliciosa e a equipe era maravilhosa", a API retorna os principais pontos de discussão: "comida" e "equipe maravilhosa". |
|[Análise de Texto](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|F0, S|**Detecção de Idioma** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Para até 120 idiomas, detecta em qual idioma o texto de entrada está escrito e informa um código de idioma único para cada documento enviado na solicitação. O código de idioma é emparelhado com uma pontuação que indica a intensidade da pontuação. |
|[Análise de Texto](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|F0, S|**Análise de Sentimento** ([imagem](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analisa o texto bruto em busca de pistas sobre sentimentos positivos ou negativos. Essa API retorna uma pontuação de sentimento entre 0 e 1 para cada documento, em que 1 é a mais positiva. Os modelos de análise são pré-treinados usando um amplo corpo de texto e tecnologias de idioma natural da Microsoft. Para [idiomas selecionados](./text-analytics/language-support.md), a API pode analisar e pontuar qualquer texto bruto que você forneça, retornando diretamente os resultados ao aplicativo responsável pela chamada. |

Além disso, alguns contêineres têm suporte nos serviços Cognitivos [ **All-In-One oferta** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) chaves de recurso. Você pode criar um recurso de tudo em um único dos serviços Cognitivos e usar a mesma chave de cobrança em todos os serviços com suporte para os seguintes serviços:

* Visual Computacional
* Face
* LUIS
* Análise de texto

## <a name="container-availability-in-azure-cognitive-services"></a>Disponibilidade de contêiner nos Serviços Cognitivos do Azure

Os contêineres dos Serviços Cognitivos do Azure estão disponíveis publicamente por meio da assinatura do Azure e as imagens do contêiner do Docker podem ser obtidas do Registro de Contêiner a Microsoft ou Hub do Docker. É possível usar o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para baixar uma imagem de contêiner do registro apropriado.

> [!IMPORTANT]
> Atualmente, é necessário concluir um processo de entrada para acessar os contêineres de [Detecção Facial](Face/face-how-to-install-containers.md) e [Reconhecimento de Texto](Computer-vision/computer-vision-how-to-install-containers.md), em que você preenche e envia um questionário com perguntas sobre você, sua empresa e o caso de uso para o qual deseja implementar os contêineres. Quando você tiver acesso e as credenciais fornecidas, poderá extrair as imagens de contêiner dos contêineres de Detecção Facial e Reconhecimento de Texto de um registro de contêiner particular hospedado pelo Registro de Contêiner do Azure.

## <a name="prerequisites"></a>Pré-requisitos

É necessário atender aos seguintes pré-requisitos antes de usar contêineres dos Serviços Cognitivos do Azure:

**Mecanismo do Docker**: é necessário ter o Mecanismo do Docker instalado localmente. O Docker fornece pacotes que configuram o ambiente do Docker em [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) e [Windows](https://docs.docker.com/docker-for-windows/). No Windows, o Docker deve ser configurado para dar suporte a contêineres do Linux. Os contêineres do Docker também podem ser implantados diretamente no [Serviço de Kubernetes do Azure](../aks/index.yml) ou [Instâncias de Contêiner do Azure](../container-instances/index.yml).

O Docker deve ser configurado para permitir que os contêineres conectem-se e enviem dados de cobrança para o Azure.

**Familiaridade com o Registro de Contêiner da Microsoft e com o Docker**: é necessário ter uma compreensão básica tanto de conceitos do Docker quanto do Registro de Contêiner da Microsoft, como registros, repositórios, contêineres e imagens de contêiner, bem como conhecimento dos comandos básicos `docker`.

Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).

Contêineres individuais podem ter seus próprios requisitos, incluindo requisitos de alocação de memória e servidor.

## <a name="developer-samples"></a>Exemplos do desenvolvedor

Exemplos de desenvolvedor estão disponíveis em nosso [repositório do GitHub](https://github.com/Azure-Samples/cognitive-services-containers-samples).

## <a name="next-steps"></a>Próximas etapas

Instale e explore a funcionalidade fornecida pelos contêineres nos Serviços Cognitivos do Azure:

* [Contêineres do Detector de anomalias](Anomaly-Detector/anomaly-detector-container-howto.md)
* [Contêineres de visão do computador](Computer-vision/computer-vision-how-to-install-containers.md)
* [Contêineres de detecção facial](Face/face-how-to-install-containers.md)
* [Contêineres do reconhecedor de formulário](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409)
* [Contêineres de Luis (reconhecimento vocal) de linguagem](LUIS/luis-container-howto.md)
* [Contêineres de personalizer](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
* [Contêineres de API do serviço de fala](https://go.microsoft.com/fwlink/?linkid=2083926&clcid=0x409)
* [Contêineres de análise de texto](text-analytics/how-tos/text-analytics-how-to-install-containers.md)