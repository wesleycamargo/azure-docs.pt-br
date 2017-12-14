---
title: "Refinar um grupo de avaliação com o mapeamento do grupo de dependência nas Migrações para Azure | Microsoft Docs"
description: "Descreve como refinar uma avaliação usando o mapeamento de dependência de grupo no serviço Migrações para Azure."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: c30d6546e7c2d471d4b262a8af1ce593b2c1c3fb
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2017
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Refinar um grupo usando o mapeamento de dependências de grupo

Este artigo descreve como configurar o mapeamento de dependência de grupo para avaliação das [Migrações para Azure](migrate-overview.md). Normalmente, você usa esse método quando quer refinar as configurações de um grupo existente, verificando as dependências de grupo, antes de executar uma avaliação. Os grupos para os quais você deseja executar o mapeamento de dependência de grupo não devem conter mais de 10 máquinas.  

## <a name="modify-a-group"></a>Modificar um grupo

1. No projeto das Migrações para Azure, em **Gerenciar**, clique em  **Grupos** e selecione o grupo.
2. Na página do grupo, clique em  **Exibir Dependências**, para abrir o mapa de dependências de grupo. 

     ![Exibir grupo](./media/how-to-create-group-dependencies/create-group.png)

3. Para exibir dependências mais granulares, clique no intervalo de tempo para modificá-las. Por padrão, o intervalo é de uma hora. Você pode modificar o intervalo, ou especificar as datas de início e de término e a duração.
4. Use Ctrl + clique para selecionar as máquinas no mapa. Adicionar ou remover máquinas do mapa.
    - Você só pode adicionar máquinas que foram descobertas.
    - Adicionar e remover máquinas de um grupo invalida as últimas avaliações dele.
    - Opcionalmente, você pode criar uma nova avaliação ao modificar o grupo.
5. Clique em **OK** para salvar o grupo.

    ![Adicionar e remover](./media/how-to-create-group-dependencies/add-remove.png)

Se você quiser verificar as dependências de uma máquina específica que é exibida no mapa de dependência de grupo, [configure o mapeamento de dependência da máquina](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Próximas etapas

[Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
