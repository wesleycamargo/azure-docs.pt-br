---
title: "Agrupar máquinas para avaliação com as Migrações para Azure | Microsoft Docs"
description: "Descreve como agrupar máquinas antes de executar uma avaliação com o serviço Migrações para Azure."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 966e0aebf56ab049e898d1787bfdfbb2ef23635e
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-for-assessment"></a>Agrupar as máquinas para avaliação

Este artigo descreve como criar um grupo de máquinas para avaliação usando as [Migrações para Azure](migrate-overview.md). As Migrações para Azure avaliam máquinas no grupo para verificar se são adequadas para a migração para o Azure, e fornecem estimativas de dimensionamento e custo para execução da máquina no Azure.


## <a name="create-a-group"></a>Criar um grupo

1. No **Painel** do projeto migrar das Migrações para Azure, clique em **Grupos** > **+ Grupo** e especifique um nome de grupo.
2. Adicione uma ou mais máquinas ao grupo e, em seguida, clique em  **Criar**. 
3. Opcionalmente, você pode selecionar a execução de uma nova avaliação para o grupo. 

    ![Criar um grupo](./media/how-to-create-a-group/create-group.png)

Após a criação do grupo, você pode modificá-lo selecionando o grupo na página **Grupos** e adicionando ou removendo máquinas.

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar [mapeamento de dependência de máquina](how-to-create-group-machine-dependencies.md) para criar grupos mais detalhados.
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
