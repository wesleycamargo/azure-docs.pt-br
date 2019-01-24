---
title: Problemas ao criar um aplicativo de Application Proxy| Microsoft Docs
description: Como solucionar problemas de criação de aplicativos de Application Proxy no portal de administração do Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 3e60ed3c291f3acd4372216fb34c7a089a78a414
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54460984"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problemas ao criar um aplicativo de Application Proxy 

Abaixo estão alguns dos problemas comuns que as pessoas enfrentam ao criar um novo aplicativo de Application Proxy.

## <a name="recommended-documents"></a>Documentos recomendados 

Para saber mais sobre como criar um aplicativo de Application Proxy por meio do Portal de administração, consulte [publicar aplicativos usando o Application Proxy do Azure AD](application-proxy-add-on-premises-application.md).

Se você estiver seguindo as etapas na documentação e estiver recebendo um erro ao criar o aplicativo, consulte os detalhes do erro para obter informações e sugestões para corrigir o aplicativo. A maioria das mensagens de erro incluem uma correção sugerida. 

## <a name="specific-things-to-check"></a>Itens específicos a serem verificados

Para evitar erros comuns, verifique se:

-   Você é um administrador com permissão para criar um aplicativo de Application Proxy

-   A URL interna é exclusiva

-   A URL externa é exclusiva

-   As URLs começam com http ou https e terminam com um "/"

-   A URL deve ser um nome de domínio, não um endereço IP

A mensagem de erro deve ser exibida no canto superior direito ao criar o aplicativo. Você também pode selecionar o ícone de notificação para ver as mensagens de erro.

   ![Prompt de notificação](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Próximas etapas
[Habilitar o Proxy de Aplicativo no portal do Azure](application-proxy-add-on-premises-application.md)
