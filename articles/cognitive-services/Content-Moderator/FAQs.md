---
title: Perguntas frequentes – Content Moderator
titlesuffix: Azure Cognitive Services
description: Obtenha respostas para perguntas frequentes sobre como o Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 11/21/2016
ms.author: sajagtap
ms.openlocfilehash: 99b95a26a91d14edbef264ac3b107f8462e0ef5e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762170"
---
# <a name="frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ)

#### <a name="what-does-my-content-moderator-subscription-include"></a>O que minha assinatura do Content Moderator inclui?
Sua assinatura do Content Moderator inclui acesso para a ferramenta de análise e as APIs. Você pode decidir se deseja usar uma ou outra ou ambas, dependendo de suas necessidades de negócios.

#### <a name="what-are-the-limitsrestrictions-of-the-content-that-can-be-moderated-by-using-the-api"></a>Quais são os limites/restrições de conteúdo que podem ser moderados ao usar a API?
Ao usar a API, as imagens precisam ter, no mínimo, 128 pixels e o tamanho do arquivo máximo de 4 MB. Os textos podem ter no máximo 1024 de caracteres. Não há nenhum limite na duração da vídeo.

#### <a name="what-happens-if-the-content-passed-to-the-text-api-or-the-image-api-exceeds-the-size-limits"></a>O que acontecerá se o conteúdo passado para a API de texto ou para a API de imagem exceder os limites de tamanho?
A API de texto retorna um código de erro que informa que o texto é mais longo que o permitido. A API de imagem também retorna um código de erro que informa que a imagem não atende aos requisitos de tamanho.

#### <a name="do-you-keep-the-images-text-or-videos-that-are-submitted-for-moderation"></a>Vocês mantêm as imagens, texto ou vídeos que são enviados para moderação?
O conteúdo é seu e talvez não sejam retido pela Microsoft sem o seu consentimento. A Microsoft usa medidas de segurança do setor para ajudar a proteger seu conteúdo.

#### <a name="can-i-use-content-moderator-to-screen-for-illegal-child-exploitation-images"></a>Posso usar o Content Moderator para examinar imagens de exploração infantil ilegal?
 Não. No entanto, as organizações qualificadas podem usar o [Serviço de Nuvem PhotoDNA](https://www.microsoft.com/photodna "Serviço de Nuvem do Microsoft PhotoDNA") para examinar este tipo de conteúdo.

#### <a name="up-to-how-many-review-teams-can-a-user-join-can-the-user-switch-between-teams"></a>Até quantas equipes de revisão um usuário pode participar? O usuário pode alternar entre equipes?
Um usuário pode se integrar a uma equipe por vez.

#### <a name="what-kind-of-team-member-roles-are-supported-by-the-review-tool-how-are-they-different"></a>Que tipo de funções de membro da equipe são compatíveis com a ferramenta de análise? Como eles são diferentes?
Atualmente, o Studio permite atribuir funções de administrador e revisor. Os administradores podem convidar outros usuários e têm acesso para as definições de configuração enquanto revisores só podem revisar marcas e os resultados de moderação ou desmarcar o conteúdo.

#### <a name="what-is-a-tag-does-the-review-tool-support-custom-tags"></a>O que é uma marca? A ferramenta de análise oferece suporte a marcas personalizadas?
Uma marca é um código curto de uma ou duas letras que denota um sinalizador de moderação, como 'a' para adulto, 'r' para obsceno e assim por diante. Os administradores podem definir novas marcas para seus negócios, conforme necessário.

#### <a name="how-many-team-members-can-i-have-in-my-review-team"></a>Quantos membros da equipe posso ter na minha equipe de análise?
Você pode ter cinco membros de equipe, no máximo, incluindo o administrador em uma equipe.
