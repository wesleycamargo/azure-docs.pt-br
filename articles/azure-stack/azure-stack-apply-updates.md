---
title: "Aplicar as atualizações na pilha do Azure | Microsoft Docs"
description: "Saiba como importar e instalar pacotes de atualização da Microsoft para um sistema de pilha do Azure integradas."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: twooley
ms.openlocfilehash: b00bd606faaffaad30ff6cea3bcf47dc85282f69
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2017
---
# <a name="apply-updates-in-azure-stack"></a>Aplicar as atualizações na pilha do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

Como um operador de pilha do Azure, você pode aplicar os pacotes para a pilha do Azure usando a atualização lado a lado no portal do administrador do Microsoft update. Você deve baixar o pacote de atualização do Microsoft, importar os arquivos de pacote para a pilha do Azure e, em seguida, instale o pacote de atualização. 

## <a name="download-the-update-package"></a>Baixe o pacote de atualização

Quando um pacote de atualização do Microsoft Azure pilha está disponível, baixe o pacote para um local que possa ser acessado pela pilha do Azure e examine o conteúdo do pacote. Um pacote de atualização normalmente consiste dos seguintes arquivos:

- Um arquivo de extração automática *PackageName*.exe arquivo. Este arquivo contém a carga para a atualização, como a atualização cumulativa mais recente para o Windows Server.   
- Correspondente *PackageName*arquivos. bin. Esses arquivos fornecem compactação para a carga que está associada com o *PackageName*.exe arquivo. 
- Um arquivo Metadata.xml. Esse arquivo contém informações essenciais sobre a atualização, por exemplo o Editor, nome, pré-requisito, tamanho e caminho URL de suporte.

## <a name="import-and-install-updates"></a>Importar e instalar atualizações

O procedimento a seguir mostra como importar e instalar pacotes de atualização no portal do administrador.

> [!IMPORTANT]
> É altamente recomendável que você notificar os usuários de qualquer operação de manutenção e agendar as janelas de manutenção normal durante o horário comercial tanto quanto possível. As operações de manutenção podem afetar as operações de portal e cargas de trabalho do usuário.

1. No portal do administrador, selecione **mais serviços**. Em seguida, sob o **dados + armazenamento** categoria, selecione **contas de armazenamento**. (Ou, na caixa de filtro, comece a digitar **contas de armazenamento**e selecione-o.)

    ![Mostra onde encontrar contas de armazenamento no portal](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Na caixa de filtro, digite **atualizar**e selecione o **updateadminaccount** conta de armazenamento.

    ![Mostra como pesquisar updateadminaccount](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. No armazenamento de conta em detalhes, **serviços**, selecione **Blobs**.
 
    ![Mostra como obter a conta de armazenamento para Blobs](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. Em **do serviço Blob**, selecione **+ contêiner** para criar um contêiner. Insira um nome (por exemplo *atualização 1709*) e, em seguida, selecione **Okey**.
 
     ![Mostra como adicionar um contêiner na conta de armazenamento](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. Depois que o contêiner é criado, clique no nome do contêiner e, em seguida, clique em **carregar** para carregar os arquivos de pacote para o contêiner.
 
    ![Mostra como carregar os arquivos de pacote](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. Em **carregar blob**, clique no ícone de pasta, navegue até arquivo de .exe do pacote de atualização e, em seguida, clique em **abrir** na janela do Gerenciador de arquivos.
  
7. Em **carregar blob**, clique em **carregar**. 
 
    ![Mostra onde carregar cada arquivo de pacote](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Repita as etapas 6 e 7 para o *PackageName*. bin e Metadata.xml arquivos. 
9. Quando terminar, você pode revisar as notificações (ícone de sino no canto superior direito do portal). As notificações devem indicar que o carregamento foi concluído. 
10. Navegue de volta para o bloco de atualização no painel. O bloco deve indicar que uma atualização está disponível. Clique no bloco para examinar o pacote de atualização recém-adicionada.
11. Para instalar a atualização, selecione o pacote que está marcado como **pronto** e com o botão direito do pacote e selecione **atualizar agora**, ou clique no **atualizar agora** ação na parte superior .
12. Quando você clica em um pacote de atualização de instalação, você pode exibir o status no **detalhes da execução de atualização** área. A partir daqui, você também pode clicar em **baixar logs completos** para baixar os arquivos de log.
13. Quando a atualização for concluída, o bloco de atualização mostra a versão atualizada da pilha do Azure.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar atualizações na visão geral de pilha do Azure](azure-stack-updates.md)
- [Política de manutenção de pilha do Azure](azure-stack-servicing-policy.md)
