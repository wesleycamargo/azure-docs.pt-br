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
ms.openlocfilehash: add4c685c07c63944ae89f48a47ac78df28c1623
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363398"
---
# <a name="about-tags"></a>Sobre as marcas #

Além das duas marcas padrão (a – isadult e r – isracy), você pode criar marcas personalizadas para varredura mais direcionada. Essas marcas personalizadas estarão disponíveis para revisores humanos serem atribuídas a imagens ou texto.

## <a name="create-tags"></a>Criar marcas ##

1.  Selecione marcas na guia Configurações.

  ![Marcas de moderação de conteúdo](images/tags-1.png)

2.  Insira um código curto de duas letras para a marca.
3.  Insira um Nome para a marca. Mantenha o nome curto e descritivo. Por exemplo, “isNudity”.
4.  Insira uma Descrição.
5.  Clique em Adicionar.
6.  Quando você terminar de criar marcas, clique em Salvar.

![Definir marcas de moderação de conteúdo](images/tags-2-define.png)

## <a name="using-custom-tags"></a>Usar marcas personalizadas ##

As marcas personalizadas são usadas durante a análise humana. Elas são exibidas na visualização, e o revisor seleciona clicando nelas.

![Usar marcas de moderação de conteúdo](images/tags-3-use.png)

Você pode desativar marcas diferentes para diferentes revisões, marcando ou desmarcando É visível.
 
![Desabilitar as marcas de moderação de conteúdo](images/tags-4-disable.png)

Embora você não possa excluir as duas marcas padrão, isadult e isracy, você pode excluir qualquer marcas personalizadas que você definiu. Clique na lixeira próximo à marca que você deseja excluir.

![Excluir marcas de moderação de conteúdo](images/tags-5-delete.png)

## <a name="next-steps"></a>Próximas etapas ##

Para saber como usar marcas de moderação de imagem, consulte [Como analisar imagens moderadas](Review-Moderated-Images.md).
