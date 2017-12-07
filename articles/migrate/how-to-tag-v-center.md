---
title: "Agrupe VMs com marcação do VMware vCenter | Microsoft Docs"
description: "Descreve como criar um grupo antes de executar uma avaliação com o serviço Migrações para Azure."
services: migration-planner
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: db33e31cdef143aa70809442457e447446a1681a
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2017
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
