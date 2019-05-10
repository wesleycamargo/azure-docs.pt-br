---
title: Anexar ou desanexar uma galeria de imagens compartilhadas nos serviços de laboratório do Azure | Microsoft Docs
description: Saiba como anexar uma galeria de imagens compartilhadas a um laboratório no Azure Lab Services.
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
ms.openlocfilehash: de4e9fb4b15f4c346926fe46f23255c668204c2e
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413882"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Anexar ou desanexar uma galeria de imagens compartilhadas no Azure Lab Services
Administrador de professores/laboratório pode salvar uma imagem de VM do modelo no Azure [Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md) para que ele seja reutilizado por outros. Como uma primeira etapa, o administrador de laboratório anexa uma galeria de imagem compartilhada existente para a conta de laboratório. Depois que a Galeria de imagens compartilhadas é anexada, laboratórios criados na conta de laboratório podem salvar imagens de galeria de imagens compartilhadas. Outros professores podem selecionar essa imagem da Galeria de imagens compartilhadas para criar um modelo para suas classes. 

Este artigo mostra como anexar ou desanexar uma galeria de imagens compartilhadas para uma conta de laboratório. 

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurar no momento da criação da conta de laboratório
Quando você estiver criando uma conta de laboratório, você pode anexar uma galeria de imagens compartilhadas para a conta de laboratório. Você pode selecionar uma galeria de imagem compartilhada existente na lista suspensa ou criar um novo. Para criar e anexar uma galeria de imagens compartilhadas para a conta de laboratório, selecione **criar novo**, insira um nome para a Galeria e insira **Okey**. 

![Configure a Galeria de imagem compartilhada no momento da criação da conta de laboratório](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configurar depois de criar a conta de laboratório
Depois que a conta de laboratório é criada, você pode fazer as seguintes tarefas:

- Criar e anexar uma galeria de imagem compartilhada
- Anexar uma galeria de imagens compartilhadas para a conta de laboratório
- Desanexar uma galeria de imagens compartilhadas da conta de laboratório

## <a name="create-and-attach-a-shared-image-gallery"></a>Criar e anexar uma galeria de imagem compartilhada
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo. Selecione **Serviços de Laboratório** na seção **DEVOPS**. Se você selecionar a estrela (`*`) ao lado de **Serviços de Laboratório**, ela será adicionada à seção **FAVORITOS** no menu esquerdo. Na próxima vez em diante, selecione **Serviços de Laboratório** em **FAVORITOS**.

    ![Todos os Serviços -> Serviços de Laboratório](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecione sua conta de laboratório para ver os **conta de laboratório** página. 
4. Selecione **Galeria de imagens compartilhadas** no menu à esquerda e selecione **+ criar** na barra de ferramentas.  

    ![Criar um botão na Galeria de imagem compartilhada](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. No **criar a Galeria de imagem compartilhada** janela, insira um **nome** para a Galeria e insira **Okey**. 

    ![Criar janela de galeria de imagem compartilhada](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    O Azure Lab Services cria a Galeria de imagens compartilhadas e anexá-lo para a conta de laboratório. Todos os laboratórios criados nessa conta de laboratório tem acesso à Galeria de imagens compartilhadas anexado. 

    ![Galeria da imagem anexada](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    No painel inferior, você vê imagens na Galeria de imagens compartilhadas. Nesta nova galeria, não há nenhuma imagem. Quando você carregar as imagens na galeria, você pode vê-los nesta página.     

    Todas as imagens na Galeria de imagens compartilhadas anexados são habilitadas por padrão. Você pode habilitar ou desabilitar as imagens selecionadas selecionando-os na lista e usando o **habilitar imagens selecionadas** ou **desabilitar imagens selecionadas** botão.

## <a name="attach-an-existing-shared-image-gallery"></a>Anexar uma galeria de imagem compartilhada existente
O procedimento a seguir mostra como anexar uma galeria de imagem compartilhada existente a uma conta de laboratório. 

1. Sobre o **conta de laboratório** página, selecione **Galeria de imagens compartilhadas** no menu à esquerda e selecione **anexar** na barra de ferramentas. 

    ![Compartilhado da Galeria de imagem - adicionar botão](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Sobre o **anexar uma galeria de imagens existente compartilhado** página, selecione sua galeria de imagens compartilhadas e selecione **Okey**.

    ![Selecione uma galeria existente](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Você verá a tela a seguir: 

    ![Minha galeria na lista](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Neste exemplo, ainda não há nenhuma imagem na Galeria de imagens compartilhadas.

    Identidade do Azure Lab Services é adicionada como um colaborador para a Galeria de imagem compartilhada que é anexada ao laboratório. Ele permite aos professores / administrador de TI para salvar a máquina virtual de imagens na Galeria de imagem compartilhada. Todos os laboratórios criados nessa conta de laboratório tem acesso à Galeria de imagens compartilhadas anexado. 

    Todas as imagens na Galeria de imagens compartilhadas anexados são habilitadas por padrão. Você pode habilitar ou desabilitar as imagens selecionadas selecionando-os na lista e usando o **habilitar imagens selecionadas** ou **desabilitar imagens selecionadas** botão. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvar uma imagem na Galeria de imagem compartilhada
Depois que estiver anexada a uma galeria de imagem compartilhada, um administrador de conta de laboratório ou um professor pode salvar ou carregar uma imagem à Galeria de imagens compartilhadas para que ele possa ser reutilizado por outros professores. Para obter instruções para carregar uma imagem à Galeria de imagens compartilhadas, consulte [visão geral da Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Atualmente, a interface do usuário de laboratórios de sala de aula (UI) não dá suporte a salvamento de uma imagem de laboratório para a Galeria de imagens compartilhadas. 

## <a name="detach-a-shared-image-gallery"></a>Desanexar uma galeria de imagem compartilhada
Apenas uma galeria de imagens compartilhadas pode ser anexada a um laboratório. Se você deseja anexar outro Galeria de imagens compartilhadas, Desanexe o ano atual antes de anexar um novo. Para desanexar uma galeria de imagem compartilhada do seu laboratório, selecione **desanexar** na barra de ferramentas e confirme a operação desanexar. 

![Desanexar a Galeria de imagens compartilhadas da conta de laboratório](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como salvar uma imagem de laboratório para a Galeria de imagens compartilhadas ou usar uma imagem da Galeria de imagens compartilhadas para criar uma VM, consulte [como usar a Galeria de imagens compartilhadas](how-to-use-shared-image-gallery.md).

Para obter mais informações sobre compartilhados galerias de imagens em geral, consulte [Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md).
