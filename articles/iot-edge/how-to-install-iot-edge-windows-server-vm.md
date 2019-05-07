---
title: Executar o Azure IoT Edge em máquinas virtuais do Windows Server | Microsoft Docs
description: Instruções de instalação do Azure IoT Edge no Marketplace máquinas virtuais Windows Server
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: gregman
ms.openlocfilehash: be7479d3f042d6e64428a07e0509907b78595200
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159774"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Executar o Azure IoT Edge em máquinas virtuais do Windows Server
O tempo de execução do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O tempo de execução pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o tempo de execução do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele.

Para saber mais sobre como funciona o tempo de execução do IoT Edge e quais componentes estão incluídos, veja [Entender o tempo de execução do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para executar o tempo de execução do IoT Edge do Azure em uma máquina virtual do Windows Server 2019 usando o [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) oferta do Azure Marketplace. Siga as instruções em [instalar o tempo de execução do Azure IoT Edge](how-to-install-iot-edge-windows.md) no Windows para uso com outras versões.

> [!NOTE]
> O tempo de execução do IoT Edge no Windows Server está em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-from-the-azure-marketplace"></a>Implantar do Azure Marketplace
1.  Navegue até a [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) oferta do Azure Marketplace ou procure por "Windows Server" em [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Selecione **obter agora** 
3.  Na **plano de Software**, localize "2019 Datacenter Server Core com contêineres do Windows Server" e, em seguida, selecione **continuar** na próxima caixa de diálogo.
    * Você também pode usar estas instruções para outras versões do Windows Server com contêineres
4.  Quando estiver no portal do Azure, selecione **Criar** e siga o assistente para implantar a VM. 
    *   Se for a primeira vez em que a tentativa de uma VM, é mais fácil de usar uma senha e habilitar o RDP e SSH no menu de porta de entrada pública. 
    *   Se você tiver uma carga de trabalho com muitos recursos, atualize o tamanho da máquina virtual, adicionando mais CPUs e/ou memória.
5.  Depois que a máquina virtual for implantada, configure-a para conectar ao Hub IoT:
    1.  Copie a cadeia de conexão do dispositivo do dispositivo do IoT Edge criado no Hub IoT (você poderá seguir a guia de instruções [Registrar um novo dispositivo do Azure IoT Edge no portal do Azure](how-to-register-device-portal.md), se não estiver familiarizado com esse processo )
    1.  Selecionar o recurso de máquina virtual recém-criada no portal do Azure e abrir a opção **executar comando**
    1.  Selecione o **RunPowerShellScript** opção
    1.  Copie esse script na janela de comando com a cadeia de conexão do dispositivo: 
        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```
    1.  Execute o script para instalar o tempo de execução do Edge e definir sua cadeia de conexão selecionando **executar**
    1.  Depois de um ou dois minutos, você deve ver uma mensagem que o tempo de execução do Edge foi instalado e provisionado com êxito.


## <a name="deploy-from-the-azure-portal"></a>Implantar do portal do Azure
1. No portal do Azure, pesquise por "Windows Server" e selecione **Datacenter do Windows Server 2019** para iniciar o fluxo de trabalho de criação de VM. 
2. Partir **selecione um plano de software** escolha "2019 Datacenter Server Core com contêineres do Windows Server" e selecione **criar**
3. Conclua as instruções da etapa 5 em "Implantar do the Azure Marketplace" acima.

## <a name="deploy-from-azure-cli"></a>Implantar da CLI do Azure
1. Se você estiver usando a CLI do Azure na área de trabalho, comece fazendo logon:

   ```azurecli-interactive
   az login
   ```
    
1. Se você tiver várias assinaturas, selecione a assinatura que gostaria de usar:
   1. Liste suas assinaturas:
    
      ```azurecli-interactive
      az account list --output table
      ```
    
   1. Copie o campo SubscriptionID da assinatura que você quer usar
   1. Execute este comando com a ID que você copiou:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Crie um novo grupo de recursos (ou especifique um existente nas próximas etapas):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```
    
1. Crie uma nova máquina virtual:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```
   * Esse comando pedirá uma senha, mas você pode adicionar a opção `--admin-password` para defini-lo mais facilmente em um script
   * A imagem do Windows Server Core tem comando suporte somente com a área de trabalho remota, então, se você deseja ter a experiência de área de trabalho completa, especifique `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` como a imagem

1. Defina a cadeia de conexão do dispositivo (você poderá seguir o guia de instruções [Registrar um novo dispositivo do Azure IoT Edge com a CLI do Azure](how-to-register-device-cli.md), se você não estiver familiarizado com esse processo):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge provisionado com o tempo de execução instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com o tempo de execução do Edge instalado corretamente, confira a [solução de problemas](troubleshoot.md) página.

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o IoT Edge de segurança e o tempo de execução do IoT Edge](how-to-update-iot-edge.md).

Saiba mais sobre como usar máquinas virtuais Windows do [documentação das máquinas virtuais de Windows](https://docs.microsoft.com/azure/virtual-machines/windows/).

Se você quiser SSH para essa VM após a instalação, execute as [instalação do OpenSSH para Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) guia usando o powershell remoto ou desktop remoto.
