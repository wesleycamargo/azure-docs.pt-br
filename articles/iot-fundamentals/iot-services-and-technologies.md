---
title: Soluções e tecnologias de Internet das Coisas (IoT) do Azure
description: Apresenta a coleção de tecnologias de SaaS/PaaS e soluções disponíveis para a criação de uma solução de IoT do Azure.
services: iot-hub
documentationcenter: ''
author: BryanLa
manager: timlt
editor: bryanla; v-clay
ms.service: iot
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: bryanla; v-clay
ms.custom: ''
ms.openlocfilehash: 04b4079038ab0ef3f87cfceea521a1424e93cce1
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2018
---
# <a name="azure-iot-technologies-and-solutions-paas-and-saas"></a>Soluções e tecnologias de IoT do Azure: PaaS e SaaS

A Microsoft criou um portfólio que atende às necessidades de todos os clientes, permitindo que qualquer pessoa tenha acesso aos benefícios de transformação digital. O portfólio de produtos de IoT do Azure é uma visão geral das tecnologias e soluções disponíveis de SaaS/PaaS. Apresenta os dois caminhos que estão disponíveis para criar sua própria solução:
- Plataforma como Serviço (PaaS): crie seu aplicativo usando o [Azure IoT Suite](https://www.azureiotsuite.com/), uma coleção de soluções pré-configuradas de nível empresarial que permitem que você acelere o desenvolvimento de soluções personalizadas de IoT.
- Software como Serviço (SaaS): inicie rápido com o [Azure IoT Central](https://azure.microsoft.com/services/iot-central/), a nova solução de SaaS para desenvolver aplicativos de IoT sem ser exposto à complexidade da solução de IoT. Se a sua organização não tiver os recursos para criar sua própria solução de IoT, o Azure IoT Central será uma solução de IoT sem código que pode criar modelos de dispositivos, painéis e regras em minutos.

![O portfólio mais abrangente do setor de tecnologias e soluções][img-paas-saas-technologies-solutions]

## <a name="solutions"></a>Soluções

Inicie rapidamente com as ofertas Azure IoT Suite e SaaS. Escolha uma das soluções pré-configuradas que permitem cenários de IoT comuns, como monitoramento remoto, manutenção preditiva e alocador conectado, para criar uma solução totalmente personalizável. Ou use o Azure IoT Central, uma solução de ponta a ponta totalmente gerenciada que habilita cenários de IoT avançados sem exigir experiência em soluções de nuvem.

### <a name="azure-iot-suite-paas"></a>Azure IoT Suite (PaaS)

Azure IoT Suite é uma coleção de nível empresarial de soluções PaaS personalizáveis que fornecem um alto nível de controle sobre sua solução de IoT. Se a sua empresa estiver implementando IoT para operações conectadas ou tiver requisitos específicos de personalização para produtos conectados, o Azure IoT Suite fornecerá o controle necessário. 

As organizações com um grande número de dispositivos ou modelos de dispositivos e os fabricantes que procuram soluções de alocador conectadas são exemplos de empresas podem se beneficiar do Azure IoT Suite. Criando soluções altamente personalizáveis adaptadas às necessidades complexas, o IoT Suite fornece:

- Soluções pré-configuradas
    - Monitoramento remoto
    - Fábrica conectada
    - Manutenção preditiva
    - Simulação de dispositivo
- Capacidade de implantar em minutos
- Tempo de retorno acelerado
- Soluções que fornecem controle total 
 
### <a name="azure-iot-central-saas"></a>Azure IoT Central (SaaS)

Azure IoT Central é uma solução de SaaS totalmente gerenciada, que permite que você se familiarize rapidamente com experiência mínima em IoT. Se a sua empresa estiver buscando velocidade em vez de personalização, modelos de SaaS poderão ser a escolha ideal para suas necessidades de implementação de IoT. 

Organizações com menos modelos de dispositivos, os mais cenários previsíveis e recursos limitados de IoT/TI agora podem aproveitar os benefícios de IoT por meio de uma abordagem de SaaS. As empresas que anteriormente não tinham o tempo, o dinheiro e a experiência para desenvolver produtos conectados agora podem se familiarizar rapidamente com o Azure IoT Central. A Microsoft é líder de setor no fornecimento de uma solução de SaaS madura que trata os requisitos comuns de implementação de IoT. 

- SaaS de IoT totalmente gerenciado
- Nenhuma experiência em desenvolvimento de solução de nuvem necessária
- Configurável de acordo com suas necessidades
- Ideal para as necessidades de IoT simples

### <a name="compare-azure-iot-suite-and-azure-iot-central"></a>Comparar Azure IoT Suite e Azure IoT Central

Para implementar uma típica [arquitetura de solução de IoT](/azure/iot-fundamentals/iot-introduction#iot-solution-architecture), o Azure IoT oferece várias opções, [Azure IoT Suite](/azure/iot-suite) e [Azure IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions), cada um apropriado para diferentes conjuntos de requisitos do cliente.

O [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) é a principal PaaS do Azure usada pelo Azure IoT Central e pelo Azure IoT Suite. O Hub IoT permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e uma solução de nuvem. O Hub IoT ajuda a solucionar os desafios de implementação de IoT como:

* Conectividade e gerenciamento de dispositivo de alto volume.
* Ingestão de telemetria de alto volume.
* Comando e controle de dispositivos.
* Imposição de segurança de dispositivo.

Escolher seu produto do Azure IoT é uma parte essencial do planejamento da sua solução de IoT. O Hub IoT é um serviço individual do Azure que, por si só, não fornece uma solução de IoT de ponta a ponta. O Hub IoT pode ser usado como um ponto de partida para qualquer solução de IoT, e você não precisa do Azure IoT Suite ou do Azure IoT Central para usá-lo. O Azure IoT Suite e o Azure IoT Central usam o Hub IoT junto com outros serviços do Azure. A tabela a seguir resume as principais diferenças entre o Azure IoT Suite e o Azure IoT Central para ajudá-lo a escolher a opção correta para seus requisitos:

|                        | Azure IoT Suite | Azure IoT Central |
| ---------------------- | --------- | ----------- |
| Uso principal | Para acelerar o desenvolvimento de uma solução de IoT personalizada que precisa obter a máxima flexibilidade. | Para acelerar o tempo para colocação no mercado de soluções de IoT simples que não exigem personalização profunda de serviço. |
| Acesso a serviços PaaS subjacentes          | Você tem acesso aos serviços do Azure subjacentes para gerenciá-los ou substituí-los conforme necessário. | SaaS. Solução totalmente gerenciada, os serviços subjacentes não são expostos. |
| Flexibilidade            | Alta. O código para os microsserviços é de código aberto e pode ser modificado da maneira que desejar. Além disso, é possível personalizar a infraestrutura de implantação.| Média. Você pode usar a experiência do usuário baseada no navegador interno para personalizar o modelo da solução e os aspectos da interface do usuário. A infraestrutura não é personalizável porque os diferentes componentes não ficam expostos.|
| Nível de habilidade                 | Médio-alto. Você precisa habilidades no Java ou .NET para personalizar a solução de back-end. Você precisa de habilidades no JavaScript para personalizar a visualização. | Baixa. Você precisa de habilidades de modelagem para personalizar a solução. Nenhuma habilidade de codificação é necessária. |
| Experiência para começar | Soluções pré-configuradas implementam cenários comuns de IoT. Podem ser implantados em minutos. | Modelos de aplicativo e de dispositivo fornecem modelos predefinidos. Podem ser implantados em minutos. |
| Preços                | Você pode ajustar os serviços para controlar o custo. | Estrutura de preços simples e previsíveis. |

A decisão de qual produto usar para criar sua solução de IoT acaba sendo determinada por:

* Seus requisitos de negócios.
* O tipo de solução que você deseja criar
* O conjunto de habilidades de sua organização para criar e manter a solução no longo prazo.

## <a name="technologies-paas"></a>Tecnologias (PaaS)

Com o portfólio de IoT mais abrangente de serviços de plataforma, as tecnologias de Plataforma como Serviço (PaaS) que abrangem a plataforma Azure permitem facilmente criar, personalizar e controlar todos os aspectos de sua solução de IoT. Estabeleça uma comunicação bidirecional com bilhões de dispositivos IoT e gerencie seus dispositivos de IoT em escala. Em seguida, integre seus dados de dispositivo de IoT aos outros serviços de plataforma, como Azure Cosmos DB e Azure Time Series Insights, para aprimorar os insights na sua solução. 

### <a name="device-support"></a>Suporte a dispositivos

Comece a trabalhar em seu projeto de IoT com confiança aproveitando os [Kits de início do Azure IoT](https://catalog.azureiotsuite.com/kits) ou escolhendo um entre as centenas de dispositivos Microsoft Azure Certified para IoT no [catálogo de dispositivos](http://catalog.azureiotsuite.com/). Todos os dispositivos são independentes de plataforma e testados para permitir uma conexão perfeita ao Hub IoT.
Conecte todos os seus dispositivos ao Azure IoT usando os [SDKs de dispositivo](/azure/iot-hub/iot-hub-devguide-sdks) de código aberto. Os SDKs dão suporte a vários sistemas operacionais, como Linux, Windows e sistemas operacionais em tempo real, bem como a várias linguagens de programação, como [C](https://github.com/Azure/azure-iot-sdk-c), [Node.js](https://github.com/Azure/azure-iot-sdk-node), [Java](https://github.com/Azure/azure-iot-sdk-java), [.NET](https://github.com/Azure/azure-iot-sdk-csharp) e [Python](https://github.com/Azure/azure-iot-sdk-python).

### <a name="iot"></a>IoT 
O [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end da solução. O Serviço de Provisionamento de Dispositivos no Hub IoT do Azure é um serviço auxiliar para o Hub IoT que permite o provisionamento de toque zero, Just-In-Time no Hub IoT correto sem necessidade de intervenção humana, permitindo que os clientes provisionem milhões de dispositivos de modo seguro e escalonável.

### <a name="edge"></a>Microsoft Edge
O [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) é um serviço de Internet das Coisas (IoT). Este serviço destina-se a clientes que desejam analisar dados em dispositivos, conhecido como "na borda". Movendo partes de sua carga de trabalho para a borda, você experimentará latência reduzida e terá a opção de cenários offline.


### <a name="data-and-analytics"></a>Dados e análises
Aproveite uma matriz de dados do Azure e as ofertas de PaaS de análise em sua solução de IoT, desde introduzir a inteligência de nuvem na borda com o Azure Machine Learning até armazenar dados de dispositivo de IoT de maneira econômica com o Azure Data Lake e até a visualização de grande quantidade de dados de dispositivos de IoT com [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/).

### <a name="visualization-and-integration"></a>Visualização e integração
O Microsoft Azure oferece uma solução completa de nuvem, que combina uma coleção em constante crescimento dos serviços de nuvem integrados com um compromisso líder do setor para a proteção e a privacidade dos seus dados. Saiba mais sobre o [Microsoft Azure](https://azure.microsoft.com/).





## <a name="next-steps"></a>Próximas etapas

Confira a seção [Introdução](/azure/iot-hub/iot-hub-get-started) para experimentar os recursos de IoT rápida e facilmente. Ou para uma experiência prática mais detalhada, experimente um dos [Tutoriais](/azure/iot-edge/tutorial-simulate-device-windows).

[img-paas-saas-technologies-solutions]: media/iot-comparison/paas-saas-technologies-solutions.png

