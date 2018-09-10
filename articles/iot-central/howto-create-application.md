---
title: Criar um aplicativo Azure IoT Central | Microsoft Docs
description: Como um administrador, saiba como criar um aplicativo Azure IoT Central.
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/09/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 1fbe3ea142e1dd738cd341f57d2b8f48b539ac75
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003759"
---
# <a name="create-your-azure-iot-central-application"></a>Criar seu aplicativo do Azure IoT Central

Você cria o aplicativo Microsoft Azure IoT Central a partir da página [Criar Aplicativo](https://apps.microsoftiotcentral.com/create). Para criar um aplicativo Azure IoT Central, será necessário preencher todos os campos desta página e, em seguida, escolher **Criar**. Você encontrará mais informações sobre cada um desses campos abaixo.

![Criar Página de Aplicativo](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>Plano de Pagamento

É possível criar um aplicativo pago ou de avaliação. Saiba mais sobre aplicativos pagos e de avaliação na [página de preço do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Nome do Aplicativo

O nome do aplicativo é exibido na página **Gerenciador de Aplicativos** e em cada aplicativo Azure IoT Central. É possível escolher qualquer nome para o aplicativo Azure IoT Central. Escolha um nome que faça sentido a você e às outras pessoas na organização.

## <a name="application-url"></a>URL do Aplicativo

A URL do aplicativo é o link para o aplicativo. Você pode salvar um indicador no navegador ou compartilhá-lo com outras pessoas.

Ao inserir o nome do aplicativo, a URL do aplicativo será gerada automaticamente. Se preferir, você pode escolher uma URL diferente para o aplicativo. Cada URL do Azure IoT Central deverá ser exclusiva dentro do Azure IoT Central. Se a URL escolhida já tiver sido utilizada, uma mensagem de erro será exibida.

## <a name="directory"></a>Diretório

Somente em aplicativos pagos.

Escolha um locatário do Azure Active Directory para criar um aplicativo Azure IoT Central. Um locatário do Azure Active Directory contém identidades de usuário, credenciais e outras informações organizacionais. Várias assinaturas do Azure podem ser associadas a um único locatário do Azure Active Directory.

Se você não tiver um locatário do Azure Active Directory, ele será criado quando você criar uma assinatura do Azure.

Para saber mais, consulte [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Assinatura do Azure

Uma assinatura do Azure permite que você crie instâncias de serviços do Azure. O Azure IoT Central localiza automaticamente todas as Assinaturas do Azure às quais você tem acesso e as exibe em uma lista suspensa na página **Criar Aplicativo**. Escolha uma assinatura do Azure para criar um novo aplicativo Azure IoT Central.

Se você não tiver uma assinatura do Azure, poderá criar uma na [página de entrada do Azure](https://aka.ms/createazuresubscription). Após criar a assinatura do Azure, navegue de volta para a página **Criar Aplicativo**. A nova assinatura aparece na lista suspensa **Assinatura do Azure**.

Para saber mais, consulte [Assinaturas do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Região

Somente em aplicativos pagos.

Escolha a região onde você gostaria de criar o Aplicativo Azure IoT Central. Normalmente, você deve escolher a região mais próxima fisicamente de seus dispositivos para obter um desempenho ideal.

Para saber mais, consulte [Regiões do Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#azure-regions).

É possível visualizar as regiões nas quais o Azure IoT Central está disponível, na página [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

> [!Note]
> Após escolher uma região, não será mais possível mover posteriormente o aplicativo para uma região diferente.

## <a name="application-template"></a>Modelo de aplicativo

Você pode escolher um dos modelos de aplicativo disponíveis para o novo aplicativo Azure IoT Central. Um modelo de aplicativo pode conter itens predefinidos, como modelos de dispositivo e painéis, para ajudá-lo a começar.

| Modelo de aplicativo | DESCRIÇÃO |
| -------------------- | ----------- |
| Aplicativo personalizado   | Cria um aplicativo vazio para você preencher com seus próprios dispositivos e modelos de dispositivos. |
| Exemplo Contoso       | Cria um aplicativo que inclui um modelo de dispositivo para um dispositivo conectado simples. Use esse modelo para começar a explorar o Azure IoT Central. |
| Exemplo Devkits       | Cria um aplicativo com modelos de dispositivos prontos para você conectar um dispositivo MXChip ou Raspberry Pi. Use esse modelo se você for um desenvolvedor de dispositivos que está experimentando o código em um desses dispositivos. |

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar um aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Administrar o aplicativo](howto-administer.md)