---
title: Agrupar máquinas para avaliação com as Migrações para Azure | Microsoft Docs
description: Descreve como agrupar máquinas antes de executar uma avaliação com o serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: ccab88c0195a7ca459c8579b7870d121dfd0fe1d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231399"
---
# <a name="group-machines-for-assessment"></a>Agrupar as máquinas para avaliação

Este artigo descreve como criar um grupo de máquinas para avaliação usando as [Migrações para Azure](migrate-overview.md). As Migrações para Azure avaliam máquinas no grupo para verificar se são adequadas para a migração para o Azure, e fornecem estimativas de dimensionamento e custo para execução da máquina no Azure.


## <a name="create-a-group"></a>Criar um grupo

1. Na **Visão geral** do projeto Migrações para Azure, em Gerenciar, clique em **Grupos** > **+ Grupo** e especifique um nome de grupo.
2. Adicione uma ou mais máquinas ao grupo e, em seguida, clique em  **Criar**. 
3. Opcionalmente, você pode selecionar a execução de uma nova avaliação para o grupo. 

    ![Criar um grupo](./media/how-to-create-a-group/create-group.png)

Após a criação do grupo, você pode modificá-lo selecionando o grupo na página **Grupos** e adicionando ou removendo máquinas.

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar o [mapeamento de dependência de máquina](how-to-create-group-machine-dependencies.md) para criar grupos com mais confiança.
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
