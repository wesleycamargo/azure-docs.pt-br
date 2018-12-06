---
title: Como criar Assinaturas no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como criar ou Assinaturas no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 3f4e113125ec9644aac974e47996afe290e57cee
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621670"
---
# <a name="how-to-create-subscriptions-in-azure-api-management"></a>Como criar Assinaturas no Gerenciamento de API do Azure

Ao publicar APIs por meio do Gerenciamento de API do Azure (APIM), a maneira mais fácil e mais comum de proteger o acesso a essas APIs é usando chaves de assinatura. Em outras palavras, os aplicativos cliente que precisam consumir as APIs publicadas devem incluir uma chave de assinatura válida em solicitações HTTP ao fazer chamadas para essas APIs. Para obter uma chave de assinatura para acessar APIs, uma assinatura é necessária. Para obter mais informações sobre as assinaturas do Azure, confira [Subscrições no Gerenciamento de API do Azure](api-management-subscriptions.md)

Este artigo explica as etapas para criar assinaturas no portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para completar as etapas neste artigo, você precisa:

+ [Criar uma instância APIM](get-started-create-service-instance.md)
+ Entender as [Assinaturas em APIM](api-management-subscriptions.md)

## <a name="create-a-new-subscription"></a>Criar uma nova Assinatura

1. Clicar em **Assinaturas** no menu à esquerda
2. Clicar em **Adicionar assinatura**
3. Fornecer um nome da assinatura e selecione o escopo
4. Clique em **Salvar**

![Assinaturas flexíveis](./media/api-management-subscriptions/flexible-subscription.png)

Depois que a assinatura for criada, um par de chaves de API (primárias e secundárias) são provisionados para acessar as APIs.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte Gerenciamento de API:

+ Conheça outros [conceitos](api-management-terminology.md) no Gerenciamento de API
+ Siga nossos [tutoriais](import-and-publish.md) para saber mais sobre o Gerenciamento de API
+ Verifique nossa [página de perguntas Frequentes](api-management-faq.md) para perguntas comuns