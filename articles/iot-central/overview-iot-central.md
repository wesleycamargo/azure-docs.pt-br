---
title: O que é Azure IoT Central | Microsoft Docs
description: O Azure IoT Central é uma solução de SaaS de ponta a ponta que pode ser usada para compilar e gerenciar sua solução de IoT personalizada. Este artigo fornece uma visão geral dos recursos do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 9fc565996797c90a6d2ac9b3851ac3408f1842c7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183264"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>O que é Azure IoT Central?

O Azure IoT Central é uma solução de Software como um serviço de IoT totalmente gerenciado que torna mais fácil criar produtos que conectam os mundos físicos e digitais. É possível dar vida à sua visão de produto conectado:

- Derivando novos insights de dispositivos conectados para possibilitar produtos e experiências melhores para seus clientes.
- Criando novas oportunidades de negócios para sua organização.

O Azure IoT Central, em comparação com um projeto típico de IoT, elimina a complicação de gerenciar uma solução de IoT:

- Reduzindo a sobrecarga de gerenciamento.
- Reduzindo as sobrecargas e os custos operacionais.
- Tornando mais fácil personalizar o seu aplicativo, aproveitando:
  - Tecnologias líderes do setor, como [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) e [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/).
  - Recursos de segurança de nível empresarial, como a criptografia de ponta a ponta.

O vídeo a seguir apresenta uma visão geral do Azure IoT Central:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Microsoft-IoT-Central-intro-walkthrough/Player]

O restante deste artigo descreve o Azure IoT Central:

- As personas típicas associadas a um projeto.
- Como criar seu aplicativo.
- Como conectar seus dispositivos ao seu aplicativo
- Como gerenciar seu aplicativo.

## <a name="personas"></a>Personas

A documentação do Azure IoT Central refere-se a quatro personas típicas que interagem com um aplicativo do Azure IoT Central:

- Um _construtor_ é responsável pela definição dos tipos de dispositivos que se conectam ao aplicativo e pela personalização do aplicativo para o operador.
- Um _operador_ gerencia os dispositivos conectados ao aplicativo.
- Um _administrador_ é responsável por tarefas administrativas, como gerenciar usuários e funções dentro do aplicativo.
- Um _desenvolvedor de dispositivo_ cria o código que é executado em um dispositivo conectado ao seu aplicativo.

## <a name="create-your-azure-iot-central-application"></a>Criar seu aplicativo do Azure IoT Central

Como um construtor, você usa o Azure IoT Central para criar uma solução de IoT personalizada e hospedada na nuvem para sua organização. Uma solução de IoT personalizada normalmente consiste em:

- Um aplicativo baseado em nuvem que recebe telemetria de seus dispositivos e permite o gerenciamento dos dispositivos.
- Vários dispositivos executando o código personalizado conectado ao seu aplicativo baseado em nuvem.

É possível implantar rapidamente um novo aplicativo do Azure IoT Central e personalizá-lo conforme seus requisitos específicos diretamente no navegador. Como um construtor do Azure IoT Central, você pode usar as ferramentas baseadas na web para criar um _modelo de dispositivo_ para os dispositivos que se conectam ao seu aplicativo. Um modelo de dispositivo é o blueprint de um modelo do dispositivo. Todos os dispositivos criados desde o mesmo modelo de dispositivo compartilham o modelo. Um modelo de dispositivo define as características e o comportamento de um tipo de dispositivo, como:

- A telemetria que ele envia.
- As propriedades de negócios que um operador pode modificar.
- As propriedades do dispositivo que são definidas por um dispositivo e são somente leitura no aplicativo.
- Os limites aos quais o aplicativo responde.
- Configurações que determinam o comportamento do dispositivo.

É possível testar imediatamente seus modelos de dispositivo e o aplicativo com dados simulados que o Azure IoT Central gera para você.

Como um construtor, você também pode personalizar o aplicativo do Azure IoT Central da interface do usuário para os operadores responsáveis pelo uso diário do aplicativo. As personalizações que um construtor pode fazer incluem:

- A definição do layout de propriedades e configurações em um modelo do dispositivo.
- A configuração de painéis personalizados para ajudar os operadores a descobrirem insights e resolverem problemas mais rapidamente.
- A configuração da análise personalizada para explorar os dados de série temporal de seus dispositivos conectados.

## <a name="connect-your-devices"></a>Conecte seus dispositivos

Depois que o construtor define os tipos de dispositivos que podem se conectar ao aplicativo, um desenvolvedor de dispositivo cria o código para executar os dispositivos. Como um desenvolvedor de dispositivo, você usa os [SDKs do Azure IoT](https://github.com/Azure/azure-iot-sdks) de código aberto da Microsoft para criar seu código de dispositivo. Esses SDKs têm amplo suporte a linguagem, plataforma e protocolo para atender às suas necessidades de conexão de dispositivos ao seu aplicativo do Azure IoT Central. Os SDKs ajudam você a executar as seguintes tarefas no dispositivo conectado ao Azure IoT Central:

- Criar uma conexão segura.
- Enviar telemetria.
- Relatar o status.
- Receber atualizações periódicas.

Para obter mais informações, consulte a postagem de blog [Os benefícios de usar os SDKs do Azure IoT e as armadilhas a serem evitadas caso não os use](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Gerenciar seu aplicativo

Aplicativos do Azure IoT Central são totalmente hospedados pela Microsoft, o que reduz a sobrecarga de administração do gerenciamento de seus aplicativos.

Como um operador, você pode usar o aplicativo do Azure IoT Central para gerenciar os dispositivos em sua solução do Azure IoT Central. Os operadores podem executar tarefas como:

- Monitorar os dispositivos conectados ao aplicativo.
- Solucionar e corrigir problemas com dispositivos.
- Provisionar novos dispositivos.

Um desenvolvedor pode definir regras personalizadas e ações que operem em um fluxo de dados no nível do modelo do dispositivo. Um operador pode habilitar ou desabilitar essas regras no nível do dispositivo para controlar e automatizar tarefas dentro do aplicativo.

Os administradores podem gerenciar o acesso ao seu aplicativo [com funções de usuário e permissões](howto-administer.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma visão geral do Azure IoT Central, aqui estão as próximas etapas sugeridas:

- Entender as diferenças entre [Azure IoT Central e aceleradores de solução do Azure IoT](overview-iot-options.md).
- Familiarizar-se com a [interface do usuário do Azure IoT Central](overview-iot-central-tour.md).
- Comece com a [criação de um aplicativo do Azure IoT Central](quick-deploy-iot-central.md).
- Siga uma sequência de tutoriais que mostram:
  - [Como um construtor, como criar um modelo de dispositivo](tutorial-define-device-type.md)
  - [Como um construtor, como adicionar regras para automatizar a sua solução](tutorial-configure-rules.md)
  - [Como um construtor, como personalizar o aplicativo para seus operadores](tutorial-customize-operator.md)
  - [Como um operador, como monitorar seus dispositivos](tutorial-monitor-devices.md)
  - [Como um operador, como adicionar um dispositivo real à sua solução](tutorial-add-device.md)
  - [Como um desenvolvedor do dispositivo, como criar código para seus dispositivos](tutorial-add-device.md#prepare-the-client-code)
