---
title: Exporte ou exclua seus dados em Visão Personalizada, Serviços Cognitivos do Azure | Microsoft Docs
description: Saiba como exportar ou excluir seus dados em Visão Personalizada.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 49fc49b3181f56c23167cfbae18911e7db441f8c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364684"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Exportar ou excluir dados de usuário em Visão Personalizada

O Content Moderator coleta dados de usuário para operar o serviço, mas os clientes precisam ter controle total sobre a exibição, a exportação e a exclusão dos dados usando a [API de Treinamento](https://go.microsoft.com/fwlink/?linkid=865446) do Serviço de Visão Personalizada.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Para obter mais informações sobre como exportar e excluir dados do usuário em Visão Personalizada, veja a tabela a seguir.

| Dados | Operação de exportar | Operação de Exclusão |
| ---- | ---------------- | ---------------- |
| Informações da Conta (Chaves de Assinatura) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Exclua usando o portal do Azure (Assinaturas do Azure). Ou usando o botão "Excluir sua Conta" na página de configurações de CustomVision.ai (Assinaturas de Conta Microsoft) |
| Detalhes da iteração | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Detalhes de desempenho da iteração | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Lista de iterações | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projetos e detalhes do projeto | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) e [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) |
| Marcas de imagem | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) e [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) |
| Imagens | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornece o uri para download da imagem) e [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fornece o uri para download da imagem) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) |
| Modelos exportados | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Excluído mediante a exclusão da conta |
