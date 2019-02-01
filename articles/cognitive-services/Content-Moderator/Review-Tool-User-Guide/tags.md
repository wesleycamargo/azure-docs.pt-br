---
title: Usar marcas personalizadas para moderação de conteúdo – Content Moderator
titlesuffix: Azure Cognitive Services
description: O Content Moderator inclui marcas padrão e você pode criar marcas personalizadas para moderar conteúdo específico para o seu negócio.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 942b30ab9949a28c42949913d90e7448c22b658f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206265"
---
# <a name="create-and-use-moderation-tags"></a>Criar e usar marcas de moderação

Além de duas marcas padrão,**isadult** (**um**) e **isracy** (**r**), você pode criar marcas personalizadas para exames mais específicos. Essas marcas personalizadas estarão disponíveis para revisores humanos serem atribuídas a imagens ou texto.

## <a name="create-tags"></a>Criar marcas

1.  Selecione marcas na guia Configurações.

  ![Marcas de moderação de conteúdo](images/tags-1.png)

2.  Insira um código curto de duas letras para a marca.
3.  Insira um Nome para a marca. Mantenha o nome curto e descritivo. Por exemplo, **isbullying**.
4.  Insira uma Descrição.
5.  Clique em Adicionar.
6.  Quando você terminar de criar marcas, clique em Salvar.

![Definir marcas de moderação de conteúdo](images/tags-2-define.png)

## <a name="using-custom-tags"></a>Usar marcas personalizadas

As marcas personalizadas são usadas durante a análise humana. Elas são exibidas na visualização, e o revisor seleciona clicando nelas.

![Usar marcas de moderação de conteúdo](images/tags-3-use.png)

Você pode desativar marcas diferentes para diferentes revisões, marcando ou desmarcando É visível.
 
![Desabilitar as marcas de moderação de conteúdo](images/tags-4-disable.png)

Embora você não possa excluir as duas marcas padrão, **isadult** e **isracy**, você pode excluir qualquer marcas personalizadas que você definiu. Clique na Lixeira ao lado da marca que você deseja excluir.

![Excluir marcas de moderação de conteúdo](images/tags-5-delete.png)

## <a name="next-steps"></a>Próximas etapas

Para saber como usar marcas para moderação de imagem, consulte [Imagens de revisão moderadas](Review-Moderated-Images.md).
