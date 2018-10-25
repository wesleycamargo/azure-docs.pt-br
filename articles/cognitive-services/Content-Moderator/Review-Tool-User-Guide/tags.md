---
title: Usar marcas no Azure Content Moderator | Microsoft Docs
description: O Content Moderator inclui marcas padrão e você pode criar marcas personalizadas para moderar conteúdo específico para o seu negócio.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: c462ff2937453f942db7fdd5b751f3356b6fe715
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310072"
---
# <a name="about-tags"></a>Sobre as marcas #

Além de duas marcas padrão,**isadult** (**um**) e **isracy** (**r**), você pode criar marcas personalizadas para exames mais específicos. Essas marcas personalizadas estarão disponíveis para revisores humanos serem atribuídas a imagens ou texto.

## <a name="create-tags"></a>Criar marcas ##

1.  Selecione marcas na guia Configurações.

  ![Marcas de moderação de conteúdo](images/tags-1.png)

2.  Insira um código curto de duas letras para a marca.
3.  Insira um Nome para a marca. Mantenha o nome curto e descritivo. Por exemplo, **isbullying**.
4.  Insira uma Descrição.
5.  Clique em Adicionar.
6.  Quando você terminar de criar marcas, clique em Salvar.

![Definir marcas de moderação de conteúdo](images/tags-2-define.png)

## <a name="using-custom-tags"></a>Usar marcas personalizadas ##

As marcas personalizadas são usadas durante a análise humana. Elas são exibidas na visualização, e o revisor seleciona clicando nelas.

![Usar marcas de moderação de conteúdo](images/tags-3-use.png)

Você pode desativar marcas diferentes para diferentes revisões, marcando ou desmarcando É visível.
 
![Desabilitar as marcas de moderação de conteúdo](images/tags-4-disable.png)

Embora você não possa excluir as duas marcas padrão, **isadult** e **isracy**, você pode excluir qualquer marcas personalizadas que você definiu. Clique na Lixeira ao lado da marca que você deseja excluir.

![Excluir marcas de moderação de conteúdo](images/tags-5-delete.png)

## <a name="next-steps"></a>Próximas etapas ##

Para saber como usar marcas para moderação de imagem, consulte [Imagens de revisão moderadas](Review-Moderated-Images.md).
