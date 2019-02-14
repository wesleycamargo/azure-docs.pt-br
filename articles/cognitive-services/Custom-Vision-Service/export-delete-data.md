---
title: Exportar ou excluir dados - Serviço de Visão Personalizada
titlesuffix: Azure Cognitive Services
description: Saiba como exportar ou excluir dados no Serviço de Visão Personalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: pafarley
ms.openlocfilehash: 7f27170a5e97d7860bcb059f16273b38757de747
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867028"
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
