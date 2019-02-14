---
title: Transferir dados com o Azure Data Box Edge | Microsoft Docs
description: Saiba como adicionar e conectar-se a compartilhamentos no dispositivo do Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 6810818e48329d883961c840fa83857d84b98fd4
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112862"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge-preview"></a>Tutorial: Transferir dados com o Azure Data Box Edge (versão prévia)

Este tutorial descreve como adicionar e conectar-se a compartilhamentos no dispositivo Azure Data Box Edge. Depois de adicionar os compartilhamentos, o Data Box Edge pode transferir dados para o Azure.

Esse procedimento leva cerca de 10 minutos para ser concluído. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um compartilhamento
> * Conectar-se ao compartilhamento

> [!IMPORTANT]
> O Data Box Edge está em versão prévia. Antes de pedir e implantar essa solução, examine os [Termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
 
## <a name="prerequisites"></a>Pré-requisitos

Antes de adicionar compartilhamentos ao Data Box Edge, verifique se:
* Você instalou o seu dispositivo físico conforme descrito em [Instalar o Azure Data Box Edge](data-box-edge-deploy-install.md). 

* Você já ativou o dispositivo físico conforme descrito em [Conectar, configurar e ativar o Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md). 

* O dispositivo está pronto para você criar compartilhamentos e transferir dados.


## <a name="add-a-share"></a>Adicionar um compartilhamento

Para criar um compartilhamento, siga o procedimento a seguir:

1. No [portal do Azure](https://portal.azure.com/), acesse **Todos os recursos** e pesquise seu recurso do Data Box Edge.
    
1. Na lista filtrada de recursos, selecione o recurso Data Box Edge.

1. No painel esquerdo, selecione **Visão geral** e, em seguida, selecione **Adicionar compartilhamento**.
   
   ![Adicionar um compartilhamento](./media/data-box-edge-deploy-add-shares/click-add-share.png)

1. No painel **Adicionar compartilhamento**, siga o procedimento a seguir:

     a. Na caixa **Nome**, forneça um nome exclusivo para seu compartilhamento.  
    O nome do compartilhamento pode ter apenas letras minúsculas, números e hifens. Ele deve ter de 3 a 63 caracteres e começar com uma letra ou um número. Hifens devem ser precedidos e seguidos por uma letra ou um número.
    
    b. Escolha um **Tipo** para o compartilhamento.  
    O tipo pode ser **SMB** ou **NFS**, sendo SMB o padrão. SMB é o padrão para clientes do Windows e NFS é usado para clientes Linux.  
    O restante das opções varia levemente dependendo se você escolher compartilhamentos SMB ou NFS. 

    c. Forneça uma conta de armazenamento em que o compartilhamento será armazenado.  
    Se um contêiner ainda não existir, ele será criado na conta de armazenamento com o nome do compartilhamento criado recentemente. Se o contêiner já existir, esse contêiner será usado. 
    
    d. Na lista suspensa **Serviço de armazenamento**, selecione **Blob de Blocos**, **Blob de Páginas** ou **Arquivos**.  
    O tipo de serviço selecionado depende de qual formato você deseja que os dados usem no Azure. Neste exemplo, porque queremos armazenar os dados como blocos de blobs no Azure, selecionamos **Blob de Blocos**. Se você selecionar o Blob de Páginas, deverá garantir que seus dados sejam alinhados com 512 bytes. Por exemplo, um VHDX sempre é alinhado com 512 bytes.
   
    e. Dependendo de se você criou um compartilhamento SMB ou um compartilhamento NFS, execute uma das seguintes etapas: 
     
    - **Compartilhamento SMB**: Em **Todos os usuários locais com privilégios**, selecione **Criar novo** ou **Usar existente**. Se você criar um novo usuário local, digite um nome de usuário e a senha e, em seguida, confirme a senha. Essa ação atribui permissões para o usuário local. Depois de atribuir as permissões aqui, você pode usar o Explorador de Arquivos para modificá-las.

        Se você marcar a caixa de seleção **Permitir operações somente leitura** para esses dados de compartilhamento, poderá especificar usuários somente leitura.

        ![Adicionar compartilhamento SMB](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **Compartilhamento NFS**: Insira os endereços IP dos clientes permitidos que podem acessar o compartilhamento.

        ![Adicionar compartilhamento NFS](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
1. Selecione **Criar** para criar o compartilhamento. 
    
    Você será notificado de que a criação do compartilhamento está em andamento. Depois que o compartilhamento for criado com as configurações especificadas, a seção **Compartilhamentos** será atualizada com as novas informações de compartilhamento. 
    
    ![Lista atualizada de compartilhamentos](./media/data-box-edge-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Conectar-se ao compartilhamento

Agora, você pode se conectar a um ou mais compartilhamentos que criou na etapa anterior. As etapas podem ser diferentes dependendo de seu compartilhamento ser SMB ou NFS. 

### <a name="connect-to-an-smb-share"></a>Conectar-se a um compartilhamento SMB

No seu cliente do Windows Server conectado ao seu dispositivo do Data Box Edge, conecte-se a um compartilhamento SMB inserindo os comandos:


1. Em uma janela Comando, digite:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

1. Quando você for solicitado a fazer isso, insira a senha para o compartilhamento.  
   O exemplo de saída desse comando é apresentado aqui.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192) 
    (c) 2017 Microsoft Corporation. All rights reserved. 
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60': 
    The command completed successfully. 
    
    C: \Users\DataBoxEdgeUser>
    ```   


1. No teclado, selecione Windows + R. 

1. Na janela **Executar**, especifique `\\<device IP address>` e, em seguida, selecione **OK**.  
   O Explorador de Arquivos é aberto. Agora você deverá ser capaz de exibir os compartilhamentos que criou como pastas. No Explorador de Arquivos, clique duas vezes em um compartilhamento (pasta) para exibir o conteúdo.
 
    ![Conectar-se ao compartilhamento SMB](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Os dados são gravados nesses compartilhamentos da forma em que são gerados e o dispositivo envia os dados para a nuvem.

### <a name="connect-to-an-nfs-share"></a>Conectar-se a um compartilhamento NFS

No cliente Linux conectado ao seu dispositivo do Data Box Edge, siga o procedimento a seguir:

1. Verifique se o cliente tem o cliente NFSv4 instalado. Para instalar o cliente NFS, use o seguinte comando:

   `sudo apt-get install nfs-common`

    Para obter mais informações, vá para [Instalar cliente NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

1. Após o cliente NFS ser instalado, monte o compartilhamento NFS criado em seu dispositivo do Data Box Edge usando o comando a seguir:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > Antes de montar os compartilhamentos, verifique se os diretórios que atuarão como pontos de montagem no computador local já foram criados. Esses diretórios não devem conter arquivos nem subpastas.

    O exemplo a seguir mostra como se conectar via NFS a um compartilhamento em seu dispositivo do Data Box Edge. O IP do dispositivo é `10.10.10.60`. O compartilhamento `mylinuxshare2` é montado no ubuntuVM. O ponto de montagem do compartilhamento é `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> As seguintes condições são aplicáveis à versão prévia:
> - Depois que um arquivo é criado no compartilhamento, não há suporte para a renomeação do arquivo. 
> - Excluir um arquivo de um compartilhamento não exclui a entrada na conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre os tópicos do Data Box Edge a seguir:

> [!div class="checklist"]
> * Adicionar um compartilhamento
> * Conectar-se para compartilhar

Para saber como transformar seus dados usando o Data Box Edge, prossiga para o próximo tutorial:

> [!div class="nextstepaction"]
> [Transformar dados com o Data Box Edge](./data-box-edge-deploy-configure-compute.md)


