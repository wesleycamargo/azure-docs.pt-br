---
title: Use uma galeria de imagem compartilhada nos serviços de laboratório do Azure | Microsoft Docs
description: Saiba como configurar uma conta de laboratório para usar uma galeria de imagens compartilhadas para que um usuário pode compartilhar uma imagem com os outros e outro usuário pode usar a imagem para criar um modelo de VM no laboratório.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 8d8b6fffe197d4180b091518dcd1615d0e0b9d19
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412848"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Use uma galeria de imagem compartilhada no Azure Lab Services
Este artigo mostra como o administrador de professores/laboratório pode economizar uma imagem de máquina virtual de modelo para que ele seja reutilizado por outros. Essas imagens são salvas no Azure [Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md). Como uma primeira etapa, o administrador de laboratório anexa uma galeria de imagem compartilhada existente para a conta de laboratório. Depois que a Galeria de imagens compartilhadas é anexada, laboratórios criados na conta de laboratório podem salvar imagens de galeria de imagens compartilhadas. Outros professores podem selecionar essa imagem da Galeria de imagens compartilhadas para criar um modelo para suas classes. 

## <a name="prerequisites"></a>Pré-requisitos
- Criar uma galeria de imagem compartilhada usando um [Azure PowerShell](../../virtual-machines/windows/shared-images.md) ou [CLI do Azure](../../virtual-machines/linux/shared-images.md).
- Você atribuiu a Galeria de imagens compartilhadas para a conta de laboratório. Para obter instruções passo a passo, consulte [Galeria de imagens de compartilhado como anexar ou desanexar](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvar uma imagem na Galeria de imagem compartilhada
Depois que estiver anexada a uma galeria de imagem compartilhada, um professor pode salvar ou carregar uma imagem à Galeria de imagens compartilhadas para que ele possa ser reutilizado por outros professores. Para obter instruções para carregar uma imagem à Galeria de imagens compartilhadas, consulte [visão geral da Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Atualmente, a interface do usuário de laboratórios de sala de aula (UI) não dá suporte a salvamento de uma imagem de laboratório para a Galeria de imagens compartilhadas. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Usar uma imagem da Galeria de imagens compartilhadas
Professor/professor pode escolher uma imagem personalizada disponível na Galeria de imagens de compartilhado para o modelo durante a criação do novo laboratório.

![Use a imagem de máquina virtual da Galeria](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre galerias de imagens compartilhadas, consulte [Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md).
