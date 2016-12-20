---
title: "Gerenciar fórmulas no Azure DevTest Labs para criar VMs | Microsoft Docs"
description: "Aprenda a criar, atualizar e remover fórmulas do Azure DevTest Labs e a usá-las para criar novas VMs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f7ea1762e3a3ffe8d3c0c6af307b846dc1f3c696


---
# <a name="manage-devtest-labs-formulas-to-create-vms"></a>Gerenciar fórmulas de Laboratórios de Desenvolvimento/Teste para criar VMs
Uma fórmula no Azure DevTest Labs é uma lista de valores de propriedade padrão usados para criar uma VM (máquina virtual). Ao se criar uma VM com base em uma fórmula, os valores padrão podem ser usados como estão ou ser modificados. Assim como as [imagens personalizadas](devtest-lab-create-template.md) e as [imagens do Marketplace](devtest-lab-configure-marketplace-images.md), as fórmulas fornecem um mecanismo para o provisionamento rápido de VMs.  

Neste artigo, você aprenderá a realizar as seguintes tarefas:

* [Criar uma fórmula](#create-a-formula)
* [Usar uma fórmula para provisionar uma VM](#use-a-formula-to-provision-a-vm)
* [Modificar uma Fórmula](#modify-a-formula)
* [Excluir uma fórmula](#delete-a-formula)

> [!NOTE]
> As fórmulas – assim como as [imagens personalizadas](devtest-lab-create-template.md) – permitem que você crie uma imagem base de um arquivo VHD. A imagem base pode, então, ser usada para provisionar uma nova VM. Para ajudar a decidir o que é melhor para seu ambiente específico, confira o artigo [Comparar imagens e fórmulas personalizadas em DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).
> 
> 

## <a name="create-a-formula"></a>Criar uma fórmula
Qualquer pessoa com permissões de *Usuários* no DevTest Labs pode criar VMs usando uma fórmula como base. Existem duas maneiras de criar fórmulas: 

* De uma base – use quando quiser definir todas as características da fórmula.
* Com base em uma VM de laboratório existente: use quando desejar criar uma fórmula com base nas configurações de uma VM existente.

### <a name="create-a-formula-from-a-base"></a>Criar uma fórmula de uma base
As etapas a seguir o orientarão no processo de criação de uma fórmula usando uma imagem personalizada, uma imagem do Marketplace ou outra fórmula.

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na folha do laboratório, selecione **Fórmulas (bases reutilizáveis)**.
   
    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na folha **Fórmulas do laboratório**, selecione **+ Adicionar**.
   
    ![Adicionar uma fórmula](./media/devtest-lab-manage-formulas/add-formula.png)
6. Na folha **Escolher uma base** , selecione a base (imagem personalizada, imagem do Marketplace ou fórmula) por meio da qual você deseja criar a fórmula.
   
    ![Lista de base](./media/devtest-lab-manage-formulas/base-list.png)
7. Na folha **Criar fórmula** , especifique os seguintes valores:
   
   * **Nome da fórmula** – digite um nome para a fórmula. Esse valor será exibido na lista de imagens de base quando você criar uma máquina virtual. O nome é validado durante a digitação e, se não servir, uma mensagem indicará os requisitos para um nome válido.
   * **Descrição** – digite uma descrição relevante para a fórmula. Esse valor está disponível no menu de contexto da fórmula quando você cria uma VM.
   * **Nome de usuário** – digite um nome de usuário que receberá privilégios de administrador.
   * **Senha** – digite – ou selecione na lista suspensa – um valor associado ao segredo (senha) que você deseja usar para o usuário especificado.  
   * **Imagem** – este campo exibe o nome da imagem de base que você selecionou na folha anterior. 
   * **Tamanho da máquina virtual** – selecione um dos itens predefinidos que especificam os núcleos de processador, o tamanho da RAM e o tamanho do disco rígido da VM a ser criada.
   * **Rede virtual** – especifique a rede virtual desejada.
   * **Sub-rede** – especifique a sub-rede desejada.
   * **Endereço IP público** – se a política de laboratório for definida para permitir endereços IP públicos para a sub-rede selecionada, especifique se você deseja que o endereço IP seja público selecionando **Sim** ou **Não**. Caso contrário, essa opção será desabilitada e selecionada como **Não**.
   * **Artefatos** – selecione e configure os artefatos que você deseja adicionar à imagem base. Valores de cadeias de caracteres seguras não são salvos com a fórmula. Portanto, os parâmetros de artefato que são cadeias de caracteres seguras não são exibidos. 
     
       ![Criar fórmula](./media/devtest-lab-manage-formulas/create-formula.png)
8. Selecione **Criar** para criar a fórmula.

### <a name="create-a-formula-from-a-vm"></a>Criar uma fórmula de uma VM
As etapas a seguir o orientarão durante o processo de criação de uma fórmula com base em uma VM existente. 

> [!NOTE]
> Para criar uma fórmula de uma VM, a VM deve ter sido criada após 30 de março de 2016. 
> 
> 

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na folha **Visão geral** do laboratório, selecione a VM com base na qual você deseja criar a fórmula.
   
    ![VMs do Labs](./media/devtest-lab-manage-formulas/my-vms.png)
5. Na folha da VM, selecione **Criar fórmula (base reutilizável)**.
   
    ![Criar fórmula](./media/devtest-lab-manage-formulas/create-formula-menu.png)
6. Na folha **Criar fórmula**, digite um **Nome** e uma **Descrição** para sua nova fórmula.
   
    ![Folha Criar fórmula](./media/devtest-lab-manage-formulas/create-formula-blade.png)
7. Selecione **OK** para criar a fórmula.

## <a name="use-a-formula-to-provision-a-vm"></a>Usar uma fórmula para provisionar uma VM
Após ter criado uma fórmula, você pode criar uma VM com base na fórmula. A seção [Adicionar uma VM com artefatos](devtest-lab-add-vm-with-artifacts.md#add-a-vm-with-artifacts) orienta você nesse processo.

## <a name="modify-a-formula"></a>Modificar uma Fórmula
Para modificar uma fórmula, siga estas etapas:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na folha do laboratório, selecione **Fórmulas (bases reutilizáveis)**.
   
    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na folha **Fórmulas do laboratório** , selecione a fórmula que você deseja modificar.
6. Na folha **Atualizar fórmula**, faça as edições desejadas e selecione **Atualizar**.

## <a name="delete-a-formula"></a>Excluir uma fórmula
Para excluir uma fórmula, siga estas etapas:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na folha **Configurações**, selecione **Fórmulas**.
   
    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na folha **Fórmulas do laboratório** , clique nas reticências à direita da fórmula que você deseja excluir.
   
    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. No menu de contexto da fórmula, selecione **Excluir**.
   
    ![Menu de contexto da Fórmula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Selecione **Sim** na caixa de diálogo de confirmação de exclusão.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Próximas etapas
Depois de criar uma fórmula para uso durante a criação de uma VM, a próxima etapa será [adicionar uma VM ao seu laboratório](devtest-lab-add-vm-with-artifacts.md).




<!--HONumber=Nov16_HO3-->


