---
title: Aplicar atualizações no Azure Stack | Microsoft Docs
description: Saiba como importar e instalar pacotes de atualização da Microsoft para um sistema integrado do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.openlocfilehash: ca07c3aca106f6c92187fea5a3ebf7bf172cddcc
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2018
ms.locfileid: "44026195"
---
# <a name="apply-updates-in-azure-stack"></a>Aplicar atualizações no Azure Stack

*Aplica-se a: sistemas integrados do Azure Stack*

Como um operador do Azure Stack, você pode aplicar a Microsoft ou pacotes de atualização de OEM para o Azure Stack usando a atualização lado a lado no portal do administrador. Você deve baixar o pacote de atualização, importe os arquivos de pacote para o Azure Stack e, em seguida, instale o pacote de atualização. 

## <a name="download-the-update-package"></a>Baixe o pacote de atualização

Quando um pacote de atualização da Microsoft ou OEM para o Azure Stack está disponível, baixe o pacote para um local que seja acessível a partir do Azure Stack e examine o conteúdo do pacote. Um pacote de atualização normalmente consiste dos seguintes arquivos:

- Um autoextraível *PackageName*.exe arquivo. Esse arquivo contém a carga para a atualização, por exemplo a atualização cumulativa mais recente para o Windows Server.   
- Correspondente *PackageName*arquivos. bin. Esses arquivos fornecem compactação para a carga que está associada com o *PackageName*.exe arquivo. 
- Um arquivo Metadata XML. Esse arquivo contém informações essenciais sobre a atualização, por exemplo o Editor, nome, pré-requisito, tamanho e URL do caminho de suporte.

## <a name="import-and-install-updates"></a>Importar e instalar atualizações

O procedimento a seguir mostra como importar e instalar pacotes de atualização no portal do administrador.

> [!IMPORTANT]
> É altamente recomendável que você notifica os usuários de qualquer operação de manutenção e agendar as janelas de manutenção normal durante o horário comercial tanto quanto possível. Operações de manutenção podem afetar as cargas de trabalho do usuário e operações do portal.

1. No portal do administrador, selecione **todos os serviços**. Em seguida, sob o **dados + armazenamento** categoria, selecione **contas de armazenamento**. (Ou, na caixa de filtro, comece a digitar **contas de armazenamento**e selecioná-lo.)

    ![Mostra onde encontrar as contas de armazenamento no portal](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Na caixa de filtro, digite **atualize**e selecione o **updateadminaccount** conta de armazenamento.

    ![Mostra como pesquisar updateadminaccount](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. No armazenamento de detalhes da conta, sob **Services**, selecione **Blobs**.
 
    ![Mostra como obter a Blobs da conta de armazenamento](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. Sob **serviço Blob**, selecione **+ contêiner** para criar um contêiner. Insira um nome (por exemplo *atualização 1709*) e, em seguida, selecione **Okey**.
 
     ![Mostra como adicionar um contêiner na conta de armazenamento](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. Depois que o contêiner é criado, clique no nome do contêiner e, em seguida, clique em **carregar** para carregar os arquivos de pacote para o contêiner.
 
    ![Mostra como carregar os arquivos de pacote](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. Sob **carregar blob**, clique no ícone de pasta, navegue até arquivo de .exe do pacote de atualização e, em seguida, clique em **abrir** na janela do Gerenciador de arquivo.
  
7. Sob **carregar blob**, clique em **carregar**. 
 
    ![Mostra onde carregar cada arquivo de pacote](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Repita as etapas 6 e 7 para o *PackageName*. bin e arquivos de Metadata. XML. Não importe o arquivo NOTICE complementares se for incluído.
9. Quando terminar, você pode examinar as notificações (ícone de sino no canto superior direito do portal). As notificações devem indicar que o upload for concluído. 
10. Navegue de volta para o bloco de atualização no painel. O bloco deve indicar que uma atualização está disponível. Clique no bloco para examinar o pacote de atualização recém-adicionado.
11. Para instalar a atualização, selecione o pacote que está marcado como **pronto** e o pacote com o botão direito e selecione **atualizar agora**, ou clique os **atualizar agora** ação na parte superior .
12. Quando você clica em instalar o pacote de atualização, você pode exibir o status na **detalhes de execução de atualização** área. A partir daqui, você também pode clicar **baixar logs completos** para baixar os arquivos de log.
13. Quando a atualização for concluída, o bloco de atualização mostra a versão atualizada do Azure Stack.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md)
- [Política de manutenção de pilha do Azure](azure-stack-servicing-policy.md)
