---
title: Criar uma imagem do Azure RemoteApp baseada em uma VM do Azure | Microsoft Docs
description: "Saiba como criar uma imagem para o RemoteApp do Azure começando com uma máquina virtual do Azure."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: d41583ef-6cd8-4115-8dcb-b2cd5b3d301a
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: c7ecd590503fcd2bc3ba06919a2f1a5c0fd0943e
ms.lasthandoff: 03/31/2017


---
# <a name="create-a-azure-remoteapp-image-based-on-an-azure-virtual-machine"></a>Criar uma imagem de RemoteApp do Azure com base em uma máquina virtual do Azure
> [!IMPORTANT]
> O Azure RemoteApp será descontinuado até 31 de agosto de 2017. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Você pode criar imagens do RemoteApp do Azure (que contêm os aplicativos que você compartilha em sua coleção) de uma máquina virtual do Azure. Você também pode optar por usar uma máquina virtual que adicionamos à galeria de imagens de VM do Azure que atende a todos os requisitos de imagem de RemoteApp do Azure - você pode usar essa imagem de VM como ponto de partida para a sua própria VM, se desejar. Basta procurar a imagem "Host da Sessão da Área de Trabalho Remota do Windows Server" na biblioteca.

Há duas etapas para criar sua própria imagem com base em uma VM do Azure: criar a imagem e, em seguida, carregá-la da biblioteca de VM do Azure para o RemoteApp do Azure.

## <a name="create-a-custom-image-based-on-an-azure-vm"></a>Criar uma imagem personalizada com base em uma VM do Azure
Use estas etapas para criar uma imagem com base em uma VM do Azure.

1. Crie uma máquina virtual do Azure. Você pode usar a imagem do “Host da Sessão da Área de Trabalho Remota do Windows Server” ou “Host da Sessão da Área de Trabalho Remota do Windows Server com o Microsoft Office 365 ProPlus” da galeria de imagens de máquinas virtuais do Azure. Essa imagem atende a todos os requisitos de imagem de modelo do Azure RemoteApp.
   
    Para obter detalhes, consulte [Criar uma VM que executa o Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Conecte-se à VM e instale e configure os aplicativos que deseja compartilhar por meio do RemoteApp. Certifique-se de executar quaisquer configurações adicionais do Windows necessárias para seus aplicativos.
   
    Para mais detalhes, consulte [Como fazer logon em uma máquina virtual executando o Windows Server](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
3. Se estiver usando uma das imagens do Host da Sessão da Área de Trabalho Remota do Windows Server, haverá um script de validação incluído que garantirá que a sua VM atenda aos pré-requisitos do RemoteApp. Para executar o script, clique duas vezes em **ValidateRemoteAppImage** na área de trabalho. Certifique-se de que todos os erros relatados pelo script sejam corrigidos antes de prosseguir para a próxima etapa.
4. O SYSPREP generaliza e captura a imagem. Consulte [Como capturar uma máquina virtual do Windows para usar como modelo](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) para obter instruções.

## <a name="import-the-image-into-the-azure-remoteapp-image-library"></a>Importar a imagem para a biblioteca de imagens do RemoteApp do Azure
Use estas etapas para importar a nova imagem para o RemoteApp do Azure:

1. Na guia **Imagens de modelo** :
   
   * Se você não tiver nenhuma imagem existente, clique em **Carregar ou Importar uma Imagem de Modelo**.
   * Se você já tiver pelo menos uma imagem, clique em **+** para adicionar uma nova imagem.
2. Selecione a biblioteca **Importar uma imagem de suas Máquinas Virtuais** e clique em **Avançar**.
3. Na próxima página, selecione a imagem personalizada na lista e verifique se você seguiu as etapas listadas ao criar a imagem. Clique em **Avançar**.
4. Insira um nome para a nova imagem do RemoteApp, selecione o local e clique na marca de seleção para iniciar o processo de importação.

> [!NOTE]
> Você pode importar imagens de qualquer local do Azure com suporte nas Máquinas Virtuais do Azure para qualquer local do Azure que tenha suporte no Azure RemoteApp. Dependendo dos locais, a importação pode levar até 25 minutos.
> 
> 

Agora você está pronto para criar a nova coleção, uma coleção na [nuvem](remoteapp-create-cloud-deployment.md) ou [híbrida](remoteapp-create-hybrid-deployment.md), dependendo de suas necessidades.


