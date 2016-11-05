---
title: Capturar uma imagem de uma VM do Windows do Azure | Microsoft Docs
description: Capture uma imagem de uma máquina virtual do Windows do Azure criada com o modelo de implantação clássico.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn

---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model."></a>Capture uma imagem de uma máquina virtual do Windows do Azure criada com o modelo de implantação clássico.
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Para obter informações do modelo Gerenciador de Recursos, confira [Create a copy Windows VM running in Azure](virtual-machines-windows-vhd-copy.md) (Criar uma cópia da VM do Windows em execução no Azure).

Esse artigo mostra como capturar uma máquina virtual do Azure executando Windows para que você a use como uma imagem para criar outras máquinas virtuais. Essa imagem inclui o disco do sistema operacional e discos de dados anexados à máquina virtual. Ele não inclui as configurações da rede, então você precisará configurá-la quando criar as outras máquinas virtuais que usam a imagem.

O Azure armazena a imagem em **Minhas Imagens**. Esse é o mesmo local em que as imagens que você carregou são armazenadas. Para obter detalhes sobre imagens, consulte [Sobre as imagens de máquinas virtuais](virtual-machines-linux-classic-about-images.md).

## <a name="before-you-begin##"></a>Antes de começar
Essas etapas assumem que você já criou uma máquina virtual do Azure e já configurou o sistema operacional, incluindo os anexos de discos de dados. Se você ainda não fez isso, consulte essas instruções:

* [Criar uma máquina virtual de uma imagem](virtual-machines-windows-classic-createportal.md)
* [Como anexar um disco de dados à máquina virtual](virtual-machines-windows-classic-attach-disk.md)
* Verifique se que as funções de servidor são compatíveis com Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!WARNING]
> Esse processo exclui a máquina virtual original depois de ela ser capturada. 
> 
> 

Antes de capturar uma imagem de uma máquina virtual do Azure, é recomendável fazer o backup da máquina virtual de destino. O backup das máquinas virtuais do Azure pode ser feito usando o Backup do Azure. Para obter detalhes, veja [Fazer backup de máquinas virtuais do Azure](../backup/backup-azure-vms.md). Existem outras soluções de parceiros certificados. Para descobrir o que está disponível no momento, pesquise no Azure Marketplace.

## <a name="capture-the-virtual-machine"></a>Capturar a máquina virtual
1. No [portal clássico do Azure](http://manage.windowsazure.com), **conecte-se** à máquina virtual. Para obter instruções, confira [Como entrar em uma máquina virtual que executa o Windows Server][Como entrar em uma máquina virtual que executa o Windows Server].
2. Abra uma janela de Prompt de comando como administrador.
3. Altere o diretório para `%windir%\system32\sysprep`e execute sysprep.exe.
4. A caixa de diálogo **Ferramenta de Preparação do Sistema** é aberta. Faça o seguinte:
   
   * Em **Ação de Limpeza do Sistema**, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a opção **Generalizar** está marcada. Para obter mais informações sobre como usar o Sysprep, consulte [Como usar Sysprep: uma introdução][Como usar Sysprep: uma introdução].
   * Em **Opções de Desligamento**, selecione **Desligar**.
   * Clique em **OK**.
   
   ![Executar o Sysprep](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)
5. O Sysprep desliga a máquina virtual, alterando o status dela no portal clássico do Azure para **Parado**.
6. No portal clássico do Azure, clique em **Máquinas Virtuais** e selecione a máquina virtual que você deseja capturar.
7. Na barra de comandos, clique em **Capturar**.
   
   ![Capturar a máquina virtual](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)
   
   A caixa de diálogo **Capturar máquina virtual** é exibida.
8. Em **Nome da Imagem**, digite o nome da nova imagem.
9. Antes de adicionar uma imagem do Windows Server ao seu conjunto de imagens personalizadas, ela deve ser generalizada executando Sysprep conforme as instruções das etapas anteriores. Clique em **Executei o Sysprep na máquina virtual** para indicar que você fez isso.
10. Clique na marca de verificação para capturar a imagem. A nova imagem agora está disponível em **Imagens**.
    
    ![Captura de imagem bem-sucedida](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Próximas etapas
A imagem está pronta para ser usada para criar máquinas virtuais. Para fazer isso, crie uma máquina virtual usando o item de menu **Da Galeria** e selecionando a imagem que você acabou de criar. Para obter instruções, consulte [Criar uma máquina virtual de uma imagem](virtual-machines-windows-classic-createportal.md).

[Como entrar em uma máquina virtual que executa o Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Como usar Sysprep: uma introdução]: http://technet.microsoft.com/library/bb457073.aspx
[Executar o Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Inserir as opções de Sysprep.exe]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[A máquina virtual é parada]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capturar uma imagem da máquina virtual]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Digitar o nome da imagem]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Captura de imagem bem-sucedida]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Usar a imagem capturada]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png



<!--HONumber=Oct16_HO2-->


