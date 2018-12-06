---
title: Equipes e subequipes na API de Content Moderator | Microsoft Docs
description: Saiba como usar equipes e subequipes na API de Content Moderator para Serviços Cognitivos.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.openlocfilehash: 6e1fc08af1062ae8962ba33c6df980182175264b
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852139"
---
# <a name="team-and-subteams"></a>Equipe e subequipes #

Você deve criar uma equipe antes de usar o Content Moderator. Quando você se inscreve e nomeia sua equipe, esta equipe se tornará sua equipe padrão. Você só pode ter uma equipe na ferramenta de análise. No entanto, você pode criar várias subequipes. Subequipes são úteis para a criação de equipes de escalonamento ou equipes dedicadas para revisar categorias específicas de conteúdo. Por exemplo, talvez você queira enviar conteúdo adulto para uma equipe diferente para mais revisão.

Este tópico explica como criar subequipes e atribuir rapidamente análises em tempo real. No entanto, você pode usar [Fluxos de trabalho](workflows.md) para atribuir revisões com base em critérios específicos.

## <a name="go-to-the-teams-setting"></a>Vá para Configuração de Equipes ##

Para iniciar a criação de um subequipe, selecione a opção **Equipes** nas configurações.

![Configurações da Equipe](images/0-teams-1.png)

## <a name="create-subteams"></a>Criar subequipes ##

A equipe padrão contém todos os revisores potenciais; subequipes são subconjuntos da equipe padrão. Você não pode atribuir uma pessoa para um subequipe se ela ainda não for membro da equipe padrão, portanto você precisa adicionar todos os revisores para a equipe padrão agora. Clique no botão de Convidar, na página de equipe.

![Convidar usuários](images/invite-users.png)

### <a name="1-create-a-subteam"></a>1. Criar uma subequipe.
Role para baixo até a página de Equipe à seção Subequipe. Clique no botão Adicionar subequipe. 

![Adicionar subequipe](images/1-teams-1.png)

### <a name="2-name-your-subteam"></a>2. Nomeie seu subequipe.
Digite seu nome subequipe na caixa de diálogo, clique em Salvar.

![Nome da subequipe](images/1-Teams-2.PNG)

### <a name="3-assign-members-from-your-default-team"></a>3. Atribua membros de sua equipe padrão.
Clique no botão Adicionar membro para atribuir membros da sua equipe padrão para uma ou mais subequipes. Você só pode adicionar usuários existentes para uma subequipe. Para adicionar novos usuários que não estão na ferramenta de análise, convidá-los usando o botão "Convidar" na página de Configurações de equipe.

![Atribuir membros da subequipe](images/1-Teams-3.PNG)

## <a name="assign-reviews-to-your-subteams"></a>Atribuir revisões para os subequipes ##

Após a criação das suas subequipes e atribuição dos membros da equipe, comece a atribuir as análises de imagem e texto a essas subequipes. Isso é feito na janela de revisão.
Se você deseja atribuir uma imagem individual a um subequipe, clique no botão de reticências no canto superior direito da imagem, selecione Mover e selecione um subequipe.

![Atribuir a revisão de imagem à subequipe](images/3-review-image-subteam-1.png)

## <a name="switch-between-subteams-to-review-assigned-content"></a>Alternar entre subequipes para revisar o conteúdo atribuído ##

Se você for membro de uma ou mais subequipes, poderá alternar entre essas subequipes no painel da ferramenta Revisar. Para ver todas as revisões pendentes atuais que pertencem a uma subequipe, selecione Escolher subequipe da guia Imagem.

![Alternar entre subequipes](images/3-review-image-subteam-2.png)
