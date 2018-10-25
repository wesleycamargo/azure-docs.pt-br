---
title: Perguntas frequentes do Marketplace do módulo do IoT Edge | Microsoft Docs
description: Perguntas frequentes do Marketplace do módulo do IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2c2e7729eb70a5dd37dc4df10605eec9429e1043
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805029"
---
# <a name="iot-edge-module-marketplace-frequently-asked-questions"></a>Perguntas frequentes sobre o Marketplace de módulo do IoT Edge


## <a name="what-is-the-edge-module-marketplace"></a>O que é o Marketplace do módulo do Edge?


O Marketplace de módulo do IoT Edge é uma lista dos módulos do Edge pré-criados e *certificados* que podem ser *descobertos* por meio do Azure Marketplace.

![Módulos do IoT Edge](./media/cloud-partner-portal-iot-edge-module-faq/iot-edge-modules.png)

## <a name="where-will-edge-modules-be-visible"></a>Onde os módulos do Edge ficarão visíveis? 


No [Marketplace no portal do Azure](https://ms.portal.azure.com/) (experiência autenticados), na categoria Internet das Coisas, marcada como "Módulo do IoT Edge".

E no [Site do Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1) (experiência anônima), na categoria Internet das Coisas, marcada como "Módulo do IoT Edge".

## <a name="is-it-open-to-partners"></a>Ele está aberto para parceiros?


Ainda não. Atualmente, somente os módulos criados pela Microsoft são publicados na seção do IoT Edge do Marketplace. 

## <a name="why-should-partners-publish-their-iot-edge-modules"></a>Por que os parceiros devem publicar seus módulos do IoT Edge?


-   Para aumentar a visibilidade dos produtos adicionando esse canal no mercado e demonstrando suas soluções.

-   Para obter mais clientes potenciais. Como parte do Azure Marketplace, eles podem obter detalhes de quem está interessado em sua solução.

-   Estar entre os primeiros a aproveitar mais funcionalidades de monetização.

## <a name="what-is-the-onboarding-process"></a>O que é o processo de integração?


Embora ainda não esteja aberto, o processo de integração será feito pelo Azure Marketplace. Há diretrizes detalhadas no [Guia de publicação do Azure Marketplace e do AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide). Confira o tipo de listagem de transação para contêineres. 

Os parceiros primeiro precisam se tornar editores do Azure Marketplace. E, em seguida, eles poderão integrar seus módulos do Edge por meio do [Portal do Cloud Partner](./cloud-partner-portal-getting-started-with-the-cloud-partner-portal.md).

## <a name="are-there-any-monetization-capabilities"></a>Existe alguma funcionalidade de monetização?


Não imediatamente neste momento. Nossa primeira meta é abrir um marketplace com módulos do Edge *gratuitos* ou *Traga sua própria licença*. Incluiremos mais funcionalidades de monetização, como um modelo de cobrança por consumo.

## <a name="what-is-bring-your-own-license-byol"></a>O que é BYOL (traga sua própria licença)?


A definição oficial nas [políticas de participação do Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) é:

- *Os clientes obtém fora do Azure Marketplace o direito de acesso ou de uso da oferta e não são cobrados pelos valores do Azure Marketplace pelo uso da oferta no Azure Marketplace.*

Cabe aos parceiros licenciar seu software e coletar a receita relacionada a ele.

## <a name="can-partners-publish-a-freemium-module"></a>Os parceiros podem publicar um módulo "freemium"?


Sim, os módulos freemium, que são módulos disponíveis gratuitamente, mas com algumas restrições, serão considerados como qualquer outra publicação.

## <a name="is-intellectual-property-protected"></a>A propriedade intelectual está protegida?


Um módulo do Edge é um contêiner compatível com o Docker. Cabe aos parceiros proteger sua IP (propriedade intelectual) empacotada nos contêineres distribuídos.

## <a name="where-will-the-modules-be-hosted"></a>Onde os módulos serão hospedados?


Os módulos do IoT Edge serão hospedados em um registro de contêiner de propriedade da Microsoft que será apto para consultas de forma anônima, como o Hub do Docker.

## <a name="what-are-the-integration-plans-between-the-azure-marketplace-and-the-azure-iot-tools"></a>Quais são os planos de integração entre o Azure Marketplace e as ferramentas de IoT do Azure?

Vamos criar uma integração maior entre o Azure Marketplace e as ferramentas do IoT do Azure. Exemplos do que temos em mente são habilitar algumas procuras no marketplace de módulo do IoT Edge diretamente do portal do Hub IoT ou diretamente do Visual Studio Code.

## <a name="which-operating-system-or-architecture-should-my-container-support"></a>A qual sistema operacional ou arquitetura meu contêiner deve dar suporte?

Os módulos do IoT Edge precisam dar suporte à mesma matriz de sistema operacional e arquitetura que o IoT Edge em produção. Essa lista é mantida no [Suporte do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support). Por exemplo, no momento, o módulo precisa dar suporte ao Linux x64 e ao Linux ARM32.

## <a name="are-there-any-other-certification-constraints-to-be-aware-of"></a>Há outras restrições de certificação que devem ser conhecidas?

Ainda estamos trabalhando no conjunto exato de restrições de certificação. Nossos princípios básicos são:

-   Promover a qualidade sobre quantidade.

-   Contêineres específicos de IoT Edge (não aleatórios).

-   Grau de produção.

-   Implantação de um clique (pelo menos com um conjunto padrão de valores de configuração fornecido).

## <a name="any-other-questions"></a>Tem mais perguntas?


Entre em contato com a [Integração do Azure IoT Edge](mailto:azureiotedgeonboarding@service.microsoft.com).
