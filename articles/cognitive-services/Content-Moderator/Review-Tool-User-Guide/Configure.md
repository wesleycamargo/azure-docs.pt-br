---
title: Definir configurações da Ferramenta de Análise do Content Moderator – Content Moderator
titlesuffix: Azure Cognitive Services
description: Configurar ou obter sua equipe, marcas, conectores, fluxos de trabalho e credenciais.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 8717eb58538889e99938164f97f10c010f87d6a5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262941"
---
# <a name="configure-review-tool-settings"></a>Definir configurações da Ferramenta de Análise

Usando a guia Configurações no painel da ferramenta Revisar, é fácil definir e alterar muitos componentes.

![Configurações de análise do Content Moderator](images/settings-1.png)

## <a name="team-and-subteams"></a>Equipe e subequipes

Gerencie sua equipe e subequipes nessa guia. Você só pode ter uma equipe, mas você pode [criar várias subequipes](subteams.md) e enviar convites para membros futuros. Após enviar convites, você pode monitorá-los, alterar permissões para membros da equipe e convidar usuários adicionais. Depois que os membros da equipe tiverem aceitado o convite, você pode atribuir os membros para subequipes diferentes. Você pode definir funções de membros da equipe como administradores ou revisores: os administradores podem convidar outros usuários, enquanto os revisores não podem.

![Configurações de equipe do Content Moderator](images/settings-2-team.png)

## <a name="tags"></a>Marcas

Isso é onde você pode [definir marcas personalizadas](tags.md) digitando o código curto, o nome e a descrição de suas marcas. Depois de criá-lo, ele está disponível durante as revisões. Você pode usar marcas diferentes para diferentes revisões, ativando ou desativando a visibilidade.

![Configurações de marcas do Content Moderator](images/settings-3-tags.png)

## <a name="connectors"></a>Conectores

Os fluxos de trabalho adicionam funcionalidade usando conectores para se comunicar com a ferramenta Revisar. A ferramenta de revisão chama as APIs do Content Moderator com o fluxo de trabalho padrão para moderar o conteúdo. Quando você se inscreve na ferramenta "Revisar", provisiona automaticamente as credenciais da API do Google Moderator para você. Ele também dá suporte à integração de APIs de outros conectores, desde que um conector esteja disponível. Disponibilizamos alguns conectores fora da caixa.

A [guia Conectores](connectors.md) é onde você gerencia os conectores. Você pode adicionar ou excluir os conectores e encontrar sua chave de assinatura para um conector específico. Clique em Conectar para adicioná-los aos seus fluxos de trabalho personalizados. 

![Configurações de conectores do Content Moderator](images/settings-4-connectors.png)

## <a name="workflows"></a>Fluxos de trabalho

Gerencie fluxos de trabalho a partir da guia de fluxos de trabalho. Você pode testar os fluxos de trabalho, carregando um arquivo de exemplo. Você também pode [definir fluxos de trabalho personalizados](workflows.md) para imagem e texto, usando os conectores de API disponíveis (encontrados na guia Conectores). 

![Configurações de fluxo de trabalho do Content Moderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Credenciais

Essa guia fornece acesso rápido a sua chave de assinatura do Content Moderator, você precisará usar as APIs incluídas com o Content Moderator (APIs de moderação de imagem, moderação de texto, lista de gerenciamento, fluxo de trabalho e revisão).
 
![Credenciais do Content Moderator](images/settings-6-credentials.png)
