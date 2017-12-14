---
title: "Agrupe VMs com marcação do VMware vCenter | Microsoft Docs"
description: "Descreve como criar um grupo antes de executar uma avaliação com o serviço Migrações para Azure."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 967c2a1fc0e5144c6c7d5c8c4a81cec3d77ffdb5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2017
---
# <a name="group-vms-with-vcenter-tagging"></a>Agrupar VMs com marcação no vCenter

Este artigo descreve como criar um grupo de máquinas para avaliação das [Migrações para Azure](migrate-overview.md) usando marcação no VMware vCenter. Especifique a categoria de marca que você deseja usar ao criar um grupo. 

## <a name="set-up-tagging"></a>Configurar a marcação

Durante a implantação das Migrações para Azure, uma VM local das Migrações para Azure descobre máquinas em execução em hosts ESXi gerenciadas por um servidor do vCenter. Você precisa configurar a marcação do vCenter antes do processo de descoberta.

1. No Cliente da Web do VMware vSphere, navegue até o servidor do vCenter.
2. Clique em **Marcas**, para examinar todas as marcas atuais.
3. Para marcar uma VM, selecione **Objetos Relacionados** > **Máquinas Virtuais** e, em seguida, selecione a VM que você deseja marcar.
4. Em **Resumo** > **Marcas**, clique em **Atribuir**. 
5. Clique em **Nova Marca** e especifique um nome de marca e uma descrição.
6. Para criar uma categoria para a marca, selecione **Nova Categoria** na lista suspensa.
7. Especifique um nome e descrição de categoria, e a cardinalidade. Em seguida, clique em **OK**.

    ![Marcas de VM](./media/how-to-tag-v-center/vm-tag.png)

## <a name="use-tagging-to-create-groups"></a>Usar a marcação para criar grupos

1. Configure a descoberta de máquinas locais, conforme descrito no [Tutorial de avaliação do VMware](tutorial-assessment-vmware.md#run-the-collector-to-discover-vms).
2. Em **Categoria de marca para agrupamento**, selecione a categoria de marca do vCenter no qual o grupo de avaliação deve ter base. As Migrações para Azure criam automaticamente um grupo para a categoria selecionada.

    

## <a name="next-steps"></a>Próximas etapas

[Configurar a avaliação da VM do VMware](tutorial-assessment-vmware.md).
