---
title: Implantar uma VM de seus VHDs para o Azure Marketplace | Microsoft Docs
description: Explica como registrar uma VM de um VHD do Azure implantado.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2771549af29b3e717d117afb42de6db03fbee226
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638811"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Implantar uma VM por meio dos seus VHDs

Este artigo explica como registrar uma máquina virtual (VM) de um disco rígido virtual (VHD) implantado no Azure.  Ele lista as ferramentas necessárias e como usá-las para criar uma imagem de VM do usuário e implantá-la no Azure usando o [portal do Microsoft Azure](https://ms.portal.azure.com/) ou scripts do PowerShell. 

Depois de carregar seus discos rígidos virtuais (VHDs) - o VHD do sistema operacional generalizado e zero ou mais VHDs de disco de dados - para uma conta de armazenamento do Azure, você pode registrá-los como uma imagem VM do usuário. Em seguida, você pode testar essa imagem. Como seu VHD do sistema operacional é generalizado, você não pode implantar a VM diretamente fornecendo a URL do VHD.

Para obter mais informações sobre imagens de VM consulte os posts de blog abaixo:

- [Image da VM](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Guia do PowerShell da Imagem da VM](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)


## <a name="set-up-the-necessary-tools"></a>Configurar as ferramentas necessárias

Se você ainda não fez isso, instale o Azure PowerShell e CLI do Azure, usando as instruções a seguir:

<!-- TD: Change the following URLs (in this entire topic) to relative paths.-->

- [Instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
- [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="create-a-user-vm-image"></a>Criar uma imagem VM de usuário

Em seguida, você criará uma imagem não gerenciada de seu VHD generalizado.

#### <a name="capture-the-vm-image"></a>Captura da imagem da VM

Use as instruções no artigo a seguir sobre como capturar a VM que corresponde à sua abordagem de acesso:

-  PowerShell: [Como criar uma imagem VM não gerenciada de uma VM do Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  CLI do Azure: [Como criar uma imagem de uma máquina virtual ou um VHD](../../../virtual-machines/linux/capture-image.md)
-  API: [Máquinas Virtuais – Capturar](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

### <a name="generalize-the-vm-image"></a>Generalizar a imagem VM

Porque você gerou a imagem de usuário de um VHD generalizado anteriormente, a imagem também deve ser generalizada.  Novamente, selecione o artigo a seguir que corresponde ao seu mecanismo de acesso.  (você pode ter já generalizado seu disco quando você o capturou)

-  PowerShell: [Generalizar a VM](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  CLI do Azure: [Etapa 2: criar imagem VM](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Máquinas Virtuais – Generalizar](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="deploy-a-vm-from-a-user-vm-image"></a>Implante uma VM usando uma imagem de VM de usuário

Em seguida, você implantará uma VM de uma imagem VM de usuário, usando o portal do Azure ou PowerShell.

<!-- TD: Recapture following hilited images and replace with red-box. -->

### <a name="deploy-a-vm-from-azure-portal"></a>Implante uma VM no portal do Azure atual

Use o processo a seguir para implantar sua VM de usuário do portal do Azure.

1.  Faça logon no [Portal do Azure](https://portal.azure.com).

2.  Clique em **Novo** e pesquise a **Implantação de modelo**, em seguida, selecione **Criar seu próprio modelo no Editor**.  <br/>
  ![Criar modelo de implantação de VHD no portal do Azure](./media/publishvm_021.png)

3. Copie e cole este [modelo JSON](./cpp-deploy-json-template.md) no editor e clique em **Salvar**. <br/>
  ![Salve o modelo de implantação do VHD no portal do Azure](./media/publishvm_022.png)

4. Forneça os valores de parâmetro para as páginas de propriedades **Implantação Personalizada**.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parâmetro**              |   **Descrição**                                                            |
   |  -------------              |   ---------------                                                            |
   | Nome de Conta de Armazenamento do Usuário   | Nome da conta de armazenamento onde se encontra o VHD generalizado                    |
   | Nome do Contêiner de Armazenamento de Usuário | Nome do contêiner onde se encontra o VHD generalizado                          |
   | Nome DNS para o IP público      | Nome DNS do IP público                                                           |
   | Nome de Usuário do Administrador             | Nome de usuário da conta do administrador para a nova VM                                  |
   | Senha do Administrador              | Senha da conta Administrador para nova VM                                  |
   | Tipo de sistema operacional                     | Sistema operacional da VM: `Windows` \| `Linux`                                    |
   | ID da assinatura             | Identificador para a assinatura selecionada                                      |
   | Local padrão                    | Localização geográfica da implantação                                        |
   | Tamanho da VM                     | [Tamanho da VM do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), por exemplo `Standard_A2` |
   | Nome do endereço IP público      | Nome do seu endereço IP público                                               |
   | Nome da VM                     | Nome da nova VM                                                           |
   | Nome da VNET        | Nome da rede virtual usada pela VM                                   |
   | Nome da NIC                    | Nome da placa de interface de rede em execução na rede virtual               |
   | URL do VHD                     | Conclua a URL do VHD de disco do sistema operacional                                                     |
   |  |  |
            
5. Depois de fornecer esses valores, clique em **Comprar**. 

O Azure começará a implantação: ele cria uma nova VM com o VHD não gerenciado especificado, no caminho de conta de armazenamento especificada.  Você pode acompanhar o progresso no portal do Azure clicando em **Máquinas Virtuais** no lado esquerdo do portal.  Quando a VM tiver sido criada, o status será alterado de `Starting` para `Running`. 


### <a name="deploy-a-vm-from-powershell"></a>Implantar uma VM a partir do PowerShell

Para implantar uma VM grande, a partir da imagem VM generalizada recém-criada, use os cmdlets a seguir.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

<!-- TD: The following is a marketplace-publishing article and may be out-of-date.  TD: update and move topic.
For help with issues, see [Troubleshooting common issues encountered during VHD creation](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting) for additional assistance.
-->

## <a name="next-steps"></a>Próximas etapas

Depois que a VM for implantada, você estará pronto para [Configurar a VM](./cpp-configure-vm.md).
