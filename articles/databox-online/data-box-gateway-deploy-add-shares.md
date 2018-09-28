---
title: Transferir dados com o Gateway do Azure Data Box | Microsoft Docs
description: Saiba como adicionar e conectar-se aos compartilhamentos no dispositivo do Gateway do Data Box.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 44fa19191f08f0e35c06f0b7ea5a3e5e41611de3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979110"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway-preview"></a>Tutorial: Transferir dados com o Gateway do Azure Data Box (Versão prévia)


## <a name="introduction"></a>Introdução

Este artigo descreve como adicionar e conectar-se aos compartilhamentos no Gateway do Data Box. Depois que os compartilhamentos são adicionados, o dispositivo Gateway do Data Box pode transferir dados para o Azure.

Esse procedimento leva cerca de 10 minutos para ser concluído. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um compartilhamento
> * Conectar-se para compartilhar

> [!IMPORTANT]
> - O Gateway do Data Box está em versão prévia. Examine os [termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de solicitar e implantar essa solução. 
 
## <a name="prerequisites"></a>Pré-requisitos

Antes de adicionar compartilhamentos ao Gateway do Data Box, verifique se:

* Você provisionou um dispositivo virtual e se conectou a ele conforme detalhado em [Provisionar um Gateway do Data Box no Hyper-V](data-box-gateway-deploy-provision-hyperv.md) ou [Provisionar um Gateway do Data Box no VMware](data-box-gateway-deploy-provision-vmware.md). 

    O dispositivo virtual foi ativado conforme detalhado em [Conectar e ativar o Gateway do Azure Data Box](data-box-gateway-deploy-connect-setup-activate.md) e está pronto para criar compartilhamentos e transferir dados.


## <a name="add-a-share"></a>Adicionar um compartilhamento

Execute as etapas a seguir no [portal do Azure](https://portal.azure.com/) para criar um compartilhamento.

1. Volte para o portal do Azure. Vá para **Todos os recursos** e procure o recurso Gateway do Data Box.
    
2. Na lista filtrada de recursos, escolha o recurso Gateway do Data Box e navegue até **Visão Geral**. Clique em **+ Adicionar compartilhamento** na barra de comandos do dispositivo.
   
   ![Adicionar um compartilhamento](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. Em **Adicionar compartilhamento**, especifique as configurações de compartilhamento. Forneça um nome exclusivo para seu compartilhamento. 

   Os nomes de compartilhamentos só podem conter números, letras minúsculas e hifens. O nome do compartilhamento deve ter entre 3 e 63 caracteres e começar com uma letra ou um número. Cada hífen deve ser precedido e seguido por um caractere que não seja um hífen.
    
5. Escolha um **Tipo** para o compartilhamento. O tipo pode ser SMB ou NFS, em que SMB é o padrão. SMB é o padrão para clientes do Windows e NFS é usado para clientes Linux. Dependendo da escolha entre compartilhamentos SMB ou NFS, as opções apresentadas serão ligeiramente diferentes. 

6. Você deve fornecer uma conta de armazenamento onde o compartilhamento residirá. Um contêiner será criado na conta de armazenamento com o nome do compartilhamento se o contêiner já não existir. Se o contêiner já existir, ele será usado. 
    
7. Escolha o **Serviço de armazenamento** entre blob de blocos, blobs de página ou arquivos. O tipo do serviço escolhido depende do formato escolhido para os dados que residirão no Azure. Por exemplo, nesta instância, queremos que os dados residam como blocos de blob no Azure e, portanto, vamos escolher o Blob de Blocos. Se escolher o Blob de Páginas, você deverá fazer com que seus dados sejam alinhados com 512 bytes. Observe que o VHDX é sempre de 512 bytes alinhados.
   
8. Esta etapa depende do tipo de compartilhamento criado, SMB ou NFS. 
     
    - **Se a criação for de um compartilhamento SMB**: no campo Usuário local com todos os privilégios, escolha **Criar novo** ou **Usar existente**. Se a criação for de um novo usuário local, forneça o **nome de usuário**, a **senha**e **confirme a senha**. Isso atribui as permissões ao usuário local. Depois de atribuir as permissões aqui, você pode usar o Gerenciador de Arquivos para modificar essas permissões.
    
        ![Adicionar compartilhamento SMB](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Se você marcar **Permitir somente operações de leitura** para esses dados de compartilhamento, terá a opção de especificar usuários somente leitura.
        
    - **Se a criação for de um compartilhamento NFS**: você precisa fornecer os endereços IP dos clientes permitidos que podem acessar o compartilhamento.

        ![Adicionar compartilhamento NFS](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Clique em **Criar** para criar o compartilhamento. 
    
    Você será notificado de que a criação do compartilhamento está em andamento. Depois que o compartilhamento for criado com as configurações especificadas, a folha **Compartilhamentos** será atualizada para refletir o novo compartilhamento. 
    
    ![Lista atualizada de compartilhamentos](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Conectar-se ao compartilhamento

Execute estas etapas no cliente do Windows Server conectado ao Gateway do Data Box para se conectar aos compartilhamentos.


1. Abra uma janela de comando. No prompt de comando, digite:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Quando solicitado, digite a senha do compartilhamento. O exemplo de saída desse comando é apresentado aqui.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2817 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60' • 
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Pressione Windows + R. Na janela **Executar**, especifique o `\\<device IP address>`. Clique em **OK**. O File Explorer será aberto. Agora você deverá ser capaz de ver os compartilhamentos que criou como pastas. Selecione e clique duas vezes em um compartilhamento (pasta) para exibir o conteúdo.
 
    ![Conectar-se ao compartilhamento SMB](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Os dados são gravados nesses compartilhamentos da forma em que são gerados e o dispositivo envia os dados para a nuvem.

### <a name="connect-to-an-nfs-share"></a>Conectar-se a um compartilhamento NFS

Execute estas etapas no cliente Linux conectado ao Edge do Data Box.

1. Verifique se o cliente tem o cliente NFSv4 instalado. Para instalar o cliente NFS, use o seguinte comando:

   `sudo apt-get install nfs-common`

    Para obter mais informações, vá para [Instalar cliente NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Depois que o cliente NFS for instalado, use o seguinte comando para montar o compartilhamento NFS criado em seu dispositivo do Gateway do Data Box:

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    Antes de configurar as montagens, verifique se os diretórios que atuarão como pontos de montagem em seu computador local já foram criados e, também, se eles não contêm arquivos ou subpastas.

    O exemplo a seguir mostra como se conectar via NFS a um compartilhamento no dispositivo do Gateway. O IP do dispositivo virtual é `10.10.10.60`, o compartilhamento `mylinuxshare2` é montado no ubuntuVM e `/home/databoxubuntuhost/gateway` é o ponto de montagem.

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> As seguintes condições são aplicáveis à versão prévia:
> - Depois que um arquivo é criado nos compartilhamentos, não há suporte para a renomeação do arquivo. 
> - A exclusão de um arquivo de um compartilhamento não exclui a entrada na conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu tópicos do Gateway do Data Box, como:

> [!div class="checklist"]
> * Adicionar um compartilhamento
> * Conectar-se para compartilhar


Avance para o próximo tutorial para aprender a administrar seu Gateway do Data Box.

> [!div class="nextstepaction"]
> [Usar a interface do usuário Web local para administrar o Gateway do Data Box](http://aka.ms/dbg-docs)


