---
title: Aplicativo móvel que chamadas às APIs da web (mover para a produção) – plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo móvel que chama as APIs da Web (mover para a produção)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37d2de561a6f5841bf17a47fef86ad7639750d5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074944"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>Aplicativo móvel que chama o web APIs - passar para a produção

Este artigo fornece detalhes sobre como melhorar a qualidade e a confiabilidade do seu aplicativo para movê-lo para produção.

## <a name="handling-errors-in-mobile-applications"></a>Tratamento de erros em aplicativos móveis

Nos fluxos de diferentes que destacamos até o momento, há uma variedade de condições de erro que pode ser causado. O cenário principal para tratar é falhas silenciosas e fallback para interação. Há condições adicionais, que você também deve considerar para a produção, incluindo situações sem rede, interrupções de serviço, consentimento do administrador necessária e outros casos específicos de cenário.

Cada biblioteca MSAL tem conteúdo de wiki e de código de exemplo que se aprofunda na lidar com essas condições.

- [Wiki do Android MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>Redução de e para investigar problemas

Coleta de dados ajudará a seu aplicativo a diagnosticar problemas. Para obter mais detalhes sobre o tipo de dados, você pode coletar, confira o wiki de plataformas cada MSAL.

- Os usuários podem pedir ajuda ao encontrar um problema. É uma prática recomendada capturar e armazenar os logs temporariamente e permitir que os usuários para carregá-los em algum lugar. A MSAL oferece extensões de registro em log para capturar informações detalhadas sobre a autenticação.
- Se estiver disponível, habilite a telemetria por meio da MSAL para reunir dados sobre como os usuários estão se conectando ao seu aplicativo.

## <a name="testing-your-app"></a>Testando seu aplicativo

Certifique-se de testar seu aplicativo com o [lista de verificação da integração](identity-platform-integration-checklist.md).

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
