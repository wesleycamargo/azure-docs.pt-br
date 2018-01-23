---
title: Montar armazenamento de arquivos do Azure com base em uma VM do Windows Azure | Microsoft Docs
description: "Armazene o arquivo na nuvem com o armazenamento de arquivos do Azure e monte o compartilhamento de arquivos de nuvem de uma VM (máquina virtual) do Azure."
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: tysonn
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 8d537bdc882487784baef9f693e4677c76d3bd8d
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="use-azure-file-shares-with-windows-vms"></a>Usar compartilhamentos de arquivos do Azure com VMs do Windows 

Você pode usar compartilhamentos de arquivos do Azure como uma maneira de armazenar e acessar arquivos na VM. Por exemplo, você pode armazenar um script ou um arquivo de configuração de aplicativo que deseja que todas as VMs compartilhem. Neste artigo, mostramos como criar e montar um compartilhamento de arquivos do Azure e como carregar e baixar arquivos.

## <a name="connect-to-a-file-share-from-a-vm"></a>Conectar a um compartilhamento de arquivo por meio de uma VM

Esta seção pressupõe que você já tenha um compartilhamento de arquivos ao qual deseja se conectar. Se precisar criar um, confira [Criar um compartilhamento de arquivos](#create-a-file-share) mais adiante neste artigo.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **Contas de armazenamento**.
3. Escolha sua conta de armazenamento.
4. Na página **Visão geral**, em **Serviços**, selecione **Arquivos**.
5. Selecione um compartilhamento de arquivos ou clique em **+ Compartilhamento de arquivos** para criar um novo compartilhamento de arquivos a ser usado.
6. Clique em **Conectar** para abrir uma página que mostra a sintaxe de linha de comando para montar o compartilhamento de arquivos do Windows ou Linux.
7. Em **Letra da unidade**, selecione a letra que deseja usar para identificar a unidade.
8. Escolha qual sintaxe usar e selecione o botão Copiar à direita para copiá-la para a área de transferência. Cole-a em algum lugar onde você possa acessá-la facilmente. 
8. Conecte-se à VM e abra um prompt de comando.
9. Cole a sintaxe de conexão editado e pressione **Enter**.
10. Quando a conexão tiver sido criada, você receberá a mensagem **O comando foi concluído com êxito.**
11. Verifique a conexão digitando a letra da unidade para alternar para a unidade e digite **dir** para ver o conteúdo do compartilhamento de arquivo.



## <a name="create-a-file-share"></a>Criar um compartilhamento de arquivos 
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **Contas de armazenamento**.
3. Escolha sua conta de armazenamento.
4. Na página **Visão geral**, em **Serviços**, selecione **Arquivos**.
5. Na página Serviço de arquivo, clique em **+ Compartilhamento de arquivos**.
6. Preencha o nome do compartilhamento de arquivo. Nomes de compartilhamento de arquivos podem usar letras minúsculas, números e hifens únicos. O nome não pode começar com um hífen e não é possível usar várias hifens consecutivos. 
7. Preencha o limite de tamanho do arquivo, até 5120 GB.
8. Clique em **OK** para criar o compartilhamento de arquivos.
   
## <a name="upload-files"></a>Carregar arquivos
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **Contas de armazenamento**.
3. Escolha sua conta de armazenamento.
4. Na página **Visão geral**, em **Serviços**, selecione **Arquivos**.
5. Selecione um compartilhamento de arquivos.
6. Clique em **Carregar** para abrir a página **Carregar arquivos**.
7. Clique no ícone de pasta para procurar no sistema de arquivos local um arquivo para carregar.   
8. Clique em **Carregar** para carregar o arquivo para o compartilhamento de arquivos.

## <a name="download-files"></a>Baixar arquivos
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, clique em **Contas de armazenamento**.
3. Escolha sua conta de armazenamento.
4. Na página **Visão geral**, em **Serviços**, selecione **Arquivos**.
5. Selecione um compartilhamento de arquivos.
6. Clique com o botão direito do mouse no arquivo e escolha **Baixar** para baixá-lo no computador local.
   

## <a name="next-steps"></a>Próximas etapas

Você também pode criar e gerenciar compartilhamentos de arquivos usando o PowerShell. Para obter mais informações, confira [Introdução ao Armazenamento de Arquivos do Azure no Windows](../../storage/files/storage-dotnet-how-to-use-files.md).
