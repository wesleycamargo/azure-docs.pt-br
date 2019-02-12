---
title: Aplicar atualizações no Azure Stack | Microsoft Docs
description: Saiba como importar e instalar pacotes de atualização da Microsoft para um sistema integrado do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: 0c3f52c78bbfd3094324b74f3b66610fcebfa2f4
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099285"
---
# <a name="apply-updates-in-azure-stack"></a>Aplicar atualizações no Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Você pode usar o **atualizar** lado a lado no portal de administração para aplicar os pacotes de atualização da Microsoft ou OEM para o Azure Stack.

Se você estiver usando uma versão de sistemas integrados 1807 ou anterior, você deve baixar o pacote de atualização, importe os arquivos de pacote para o Azure Stack e, em seguida, instale o pacote de atualização. Para obter instruções, consulte [atualização do Azure Stack, baixando o pacote](#update-azure-stack-by-downloading-the-package)

Essas instruções de trabalho com sistemas integrados do Azure Stack de atualização. Se você estiver usando o sistema de desenvolvimento do Azure Stack, você deve baixar o pacote de instalação para a versão atual. Para obter instruções, consulte [instalar o Kit de desenvolvimento do Azure Stack](.\asdk\asdk-install.md)

## <a name="update-azure-stack"></a>Atualizar o Azure Stack

### <a name="select-and-apply-an-update-package"></a>Selecione e aplicar um pacote de atualização

1. Abra o portal de administração.

2. Selecione **Dashboard**. Selecione o **atualização** lado a lado.

    ![Atualização da pilha do Azure disponível](media/azure-stack-apply-updates/azure-stack-updates-1901-dashboard.png)

3. Anote a versão atual do Azure Stack. Você pode atualizar para a próxima versão completa. Por exemplo, se você está executando o Azure Stack 1811, a próxima versão lançada estiver 1901.

    ![Aplicar atualização da pilha do Azure](media/azure-stack-apply-updates/azure-stack-updates-1901-updateavailable.png)

4. Selecione a próxima versão disponível na lista de atualizações. Você pode selecionar **exibição** na versão de coluna de notas para abrir o tópico de notas de versão para a versão se gostaria de examinar as alterações de versão.

5. Selecione Atualizar agora. A atualização será iniciada.

### <a name="review-update-history"></a>Histórico de atualização de revisão

1. Abra o portal de administração.

2. Selecione **Dashboard**. Selecione o **atualização** lado a lado.

3. Selecione **histórico de atualizações**.

![Histórico de atualização de pilha do Azure](media/azure-stack-apply-updates/azure-stack-update-history.PNG)

## <a name="update-azure-stack-by-downloading-the-package"></a>Baixando o pacote de atualização do Azure Stack

Se você estiver usando uma versão de sistemas integrados 1807 ou anterior, você deve baixar o pacote de atualização, importe os arquivos de pacote para o Azure Stack e, em seguida, instale o pacote de atualização.

## <a name="download-the-update-package"></a>Baixe o pacote de atualização

Quando um pacote de atualização da Microsoft ou OEM para o Azure Stack está disponível, baixe o pacote para um local que seja acessível a partir do Azure Stack e examine o conteúdo do pacote. Um pacote de atualização normalmente consiste dos seguintes arquivos:

- Um autoextraível `<PackageName>.exe` arquivo. Esse arquivo contém a carga para a atualização, por exemplo a atualização cumulativa mais recente para o Windows Server.

- Correspondente `<PackageName>.bin` arquivos. Esses arquivos fornecem compactação para a carga que está associada com o *PackageName*.exe arquivo.

- Um `Metadata.xml` arquivo. Esse arquivo contém informações essenciais sobre a atualização, por exemplo o Editor, nome, pré-requisito, tamanho e URL do caminho de suporte.

> [!IMPORTANT]  
> Depois que o pacote de atualização do Azure Stack 1901 é aplicado, o formato de empacotamento para o Azure Stack update pacakges serão movidas do .exe, .bin(s) e o formato. XML para um .zip(s) e formato. XML. Operadores do Azure Stack que se conectaram carimbos não serão afetados. Operadores do Azure Stack que são desconectadas simplesmente importará os arquivos. XML e. zip usando o mesmo processo descrito abaixo.

## <a name="import-and-install-updates"></a>Importar e instalar atualizações

O procedimento a seguir mostra como importar e instalar pacotes de atualização no portal do administrador.

> [!IMPORTANT]  
> É altamente recomendável que você notifica os usuários de qualquer operação de manutenção e agendar as janelas de manutenção normal durante o horário comercial tanto quanto possível. Operações de manutenção podem afetar as cargas de trabalho do usuário e operações do portal.

1. No portal do administrador, selecione **todos os serviços**. Em seguida, sob o **dados + armazenamento** categoria, selecione **contas de armazenamento**. (Ou, na caixa de filtro, comece a digitar **contas de armazenamento**e selecioná-lo.)

    ![Mostra onde encontrar as contas de armazenamento no portal](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Na caixa de filtro, digite **atualize**e selecione o **updateadminaccount** conta de armazenamento.

3. No armazenamento de detalhes da conta, sob **Services**, selecione **Blobs**.
 
    ![Mostra como obter a Blobs da conta de armazenamento](media/azure-stack-apply-updates/ApplyUpdates3.png) 

4. Sob **serviço Blob**, selecione **+ contêiner** para criar um contêiner. Insira um nome (por exemplo *Update-1811*) e, em seguida, selecione **Okey**.
 
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

Você pode excluir manualmente as atualizações da conta de armazenamento após eles terem sido instalados no Azure Stack. O Azure Stack periodicamente verifica se há pacotes de atualização mais antigos e os remove do armazenamento. Azure Stack pode levar duas semanas para remover pacotes antigos.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md)
- [Política de manutenção de pilha do Azure](azure-stack-servicing-policy.md)
