---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 5/14/2018
ms.author: markgal
ms.custom: include file
ms.openlocfilehash: 5590da80a1c217e7902e8e010688e40f5624898c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34664895"
---
## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação
Um cofre dos Serviços de Recuperação é uma entidade que armazena os backups e os pontos de recuperação criados ao longo do tempo. O cofre de Serviços de Recuperação também contém as políticas de backup associadas às máquinas virtuais protegidas.

Para criar um cofre de Serviços de Recuperação:

1. Entre na sua assinatura no [portal do Azure](https://portal.azure.com/).
2. No menu esquerdo, selecione **Todos os Serviços**.

    ![Escolha a opção Todos os Serviços no menu principal](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. Na caixa de diálogo Todos os Serviços, digite *Serviços de Recuperação*. Conforme você começa a digitar, sua entrada filtra a lista de recursos. Ao ver a opção, selecione **Cofres dos Serviços de Recuperação**.

    ![Digite Serviços de Recuperação na caixa de diálogo Todos os serviços](./media/backup-create-rs-vault/all-services.png) <br/>

    A lista de cofres de Serviços de Recuperação na assinatura aparecerá.
4. No menu **Cofres de Serviços de Recuperação**, selecione **Adicionar**.

    ![Criar Cofre de Serviços de Recuperação - etapa 2](./media/backup-create-rs-vault/add-button-create-vault.png)

    O menu **Cofres de Serviços de Recuperação** é aberto. Ele solicita que você forneça informações para **Nome**, **Assinatura**, **Grupo de recursos** e **Localização**.

    ![Painel "Cofres de Serviços de Recuperação"](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. Em **Nome**, insira um nome amigável para identificar o cofre. O nome deve ser exclusivo para a assinatura do Azure. Digite um nome que contenha pelo menos dois, mas não mais de 50 caracteres. O nome deve começar com uma letra e pode conter apenas letras, números e hifens.
6. Para **Assinatura**, escolha a assinatura que deseja usar. Se você for um membro de apenas uma assinatura, esse nome será exibido. Se você não tiver certeza sobre qual assinatura usar, use a assinatura padrão (ou a sugerida). Só haverá múltiplas opções se sua conta corporativa ou de estudante estiver associada a várias assinaturas do Azure.
7. Para o **Grupo de recursos** é possível usar um grupo de recursos existente ou criar um novo. Para consultar a lista disponível de grupos de recurso na sua assinatura, selecione **Usar existente** e clique no menu suspenso. Para criar um novo grupo de recursos, selecione **Criar novo** e digite o nome. Para obter informações completas sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md).
8. Em **Local**, selecione a região geográfica para o cofre. Se você estiver criando um cofre para proteger as máquinas virtuais, o cofre *deve* estar na mesma região que as máquinas virtuais.

   > [!IMPORTANT]
   > Se você não souber ao certo a localização em que a VM se encontra, feche a caixa de diálogo de criação do cofre e vá para a lista de máquinas virtuais no portal. Se você tiver máquinas virtuais em várias regiões, crie um cofre de Serviços de Recuperação em cada região. Crie o cofre no primeiro local antes de ir para o próximo local. Não é necessário especificar contas de armazenamento para armazenar os dados de backup. O cofre de Serviços de Recuperação e o serviço de Backup do Azure lidam com isso automaticamente.
   >
   >

9. Quando você estiver pronto para criar o Cofre de Serviços de Recuperação, clique em **Criar**.

    ![Lista de cofres de backup](./media/backup-create-rs-vault/click-create-button.png)

    Talvez demore um pouco para o cofre de Serviços de Recuperação ser criado. Monitore as notificações de status na seção Notificações (área superior direita do portal). Depois que o cofre é criado, ele aparece na lista de cofres de Serviços de Recuperação. Se você não encontrar seu cofre, clique em **Atualizar**.

     ![Lista de cofres de backup](./media/backup-create-rs-vault/refresh-button.png)
