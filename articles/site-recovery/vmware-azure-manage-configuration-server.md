---
title: Gerenciar o servidor de configuração para a recuperação após desastres do VMware com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como gerenciar um servidor de configuração existente de recuperação de desastre do VMware para o Azure com o Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: df5b2ecce2a5c9d7c263ee0acc3a49b859b93f7f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346113"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>Gerenciar o servidor de configuração para VMs VMware

Você configura um servidor de configuração local quando você usa o [Azure Site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos para o Azure. O servidor de configuração coordena a comunicação entre o ambiente de VMware local e o Azure e gerencia a replicação de dados. Este artigo resume as tarefas comuns para gerenciar o servidor de configuração depois de implantado.



## <a name="modify-vmware-settings"></a>Modificar as configurações de VMware

Você pode acessar o servidor de configuração da seguinte maneira:
    - Entre na VM na qual ela está implantada e inicie o Azure Site Recovery Configuration Manager no atalho da área de trabalho.
    - Como alternativa, você pode acessar o servidor de configuração remotamente a partir de **https://*ConfigurationServerName*/:44315 /**. Entre com credenciais de administrador.
   
### <a name="modify-vmware-server-settings"></a>Modifique as configurações do servidor VMware

1. Para associar outro servidor VMware ao servidor de configuração, após a entrada, selecione **Adicionar vCenter Server/servidor vSphere ESXi**.
2. Digite os detalhes e, em seguida, selecione **OK**.


### <a name="modify-credentials-for-automatic-discovery"></a>Modificar as credenciais para a descoberta automática

1. Para atualizar as credenciais usadas para conectar-se ao servidor VMware para descoberta automática de VMs do VMware, após o login, selecione **Editar**.
2. Digite as novas credenciais e selecione **OK**.

    ![Modificar VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>Modificar credenciais para instalação do Serviço de Mobilidade

Modifique as credenciais usadas para instalar automaticamente o Serviço de Mobilidade nas VMs do VMware que você habilitar para replicação.

1. Depois de entrar, selecione **gerenciar credenciais de máquina virtual**
2. Digite as novas credenciais e selecione **OK**.

    ![Modificar as credenciais de Serviço de Mobilidade](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Modificar as configurações de proxy

Modificar as configurações de proxy usadas pela máquina do servidor de configuração para acesso à internet para o Azure. Se você tiver uma máquina de servidor de processo, além do servidor de processo padrão em execução na máquina do servidor de configuração, modifique as definições em ambos os computadores.

1. Depois de entrar no servidor de configuração, selecione **gerenciam a conectividade**.
2. Atualize os valores de proxy. Selecione **Salvar** para atualizar as configurações.

## <a name="add-a-network-adapter"></a>Adicione um adaptador de rede

O modelo Open Virtualization Format (OVF) implanta a VM do servidor de configuração com um único adaptador de rede.

- Você pode [adicionar outro adaptador à VM](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), mas precisa adicioná-lo antes de registrar o servidor de configuração no cofre.
- Para adicionar um adaptador depois de registrar o servidor de configuração no cofre, adicione o adaptador nas propriedades da VM. Então você precisa registrar novamente o servidor no cofre.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Registrar um servidor de configuração no mesmo cofre

Você pode registrar novamente o servidor de configuração no mesmo cofre se necessário. Se você tiver uma máquina de servidor de processo adicional, além do servidor de processo padrão em execução na máquina do servidor de configuração, registre novamente em ambos os computadores.


  1. No cofre, abra **gerenciar** > **infra-estrutura do Azure Site Recovery** > **Servidores de Configuração**.
  2. Em **Servidores**, selecione **Baixar a chave de registro** para baixar o arquivo de credenciais do cofre.
  3. Conecte-se ao computador do servidor de configuração.
  4. Em **%ProgramData%\ASR\home\svsystems\bin**, abra **cspsconfigtool.exe**.
  5. Sobre o **registro do cofre** guia, selecione **procurar**e localize o arquivo de credenciais de cofre que você baixou.
  6. Se necessário, forneça os detalhes do servidor proxy. Em seguida, selecione **Registrar**.
  7. Abra uma janela de comando do PowerShell de administrador e execute o comando a seguir:
   ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

      >[!NOTE] 
      >Para obter os **certificados mais recentes** do servidor de configuração para o servidor de processo de expansão, execute o comando *“<Unidade de Instalação \ Recuperação do Site do Azure do Microsoft \ agent \ cdpcli.exe>" --registermt*

  8. Finalmente, reinicie o obengine executando o seguinte comando.
  ```
          net stop obengine
          net start obengine

## Upgrade the configuration server

You run update rollups to update the configuration server. Updates can be applied for up to N-4 versions. For example:

- If you run 9.7, 9.8, 9.9, or 9.10, you can upgrade directly to 9.11.
- If you run 9.6 or earlier and you want to upgrade to 9.11, you must first upgrade to version 9.7. before 9.11.

Links to update rollups for upgrading to all versions of the configuration server are available in the [wiki updates page](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade the server as follows:

1. In the vault, go to **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.
2. If an update is available, a link appears in the **Agent Version** > column.
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Download the update installer file to the configuration server.

    ![Update](./media/vmware-azure-manage-configuration-server/update1.png)

4. Double-click to run the installer.
5. The installer detects the current version running on the machine. Click **Yes** to start the upgrade.
6. When the upgrade completes the server configuration validates.

    ![Update](./media/vmware-azure-manage-configuration-server/update3.png)
    
7. Click **Finish** to close the installer.

## Delete or unregister a configuration server

1. [Disable protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) for all VMs under the configuration server.
2. [Disassociate](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) and [delete](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) all replication policies from the configuration server.
3. [Delete](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) all vCenter servers/vSphere hosts that are associated with the configuration server.
4. In the vault, open **Site Recovery Infrastructure** > **Configuration Servers**.
5. Select the configuration server that you want to remove. Then, on the **Details** page, select **Delete**.

    ![Delete configuration server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### Delete with PowerShell

You can optionally delete the configuration server by using PowerShell.

1. [Install](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) the Azure PowerShell module.
2. Sign in to your Azure account by using this command:
    
    `Connect-AzureRmAccount`
3. Select the vault subscription.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Set the vault context.
    
    ```
    $vault = get-AzureRmRecoveryServicesVault-nome <name of your vault> conjunto AzureRmSiteRecoveryVaultSettings - ARSVault $vault
    ```
4. Retrieve the configuration server.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Delete the configuration server.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> You can use the **-Force** option in Remove-AzureRmSiteRecoveryFabric for forced deletion of the configuration server.
 
## Generate configuration server Passphrase

1. Sign in to your configuration server, and then open a command prompt window as an administrator.
2. To change the directory to the bin folder, execute the command **cd %ProgramData%\ASR\home\svsystems\bin**
3. To generate the passphrase file, execute **genpassphrase.exe -v > MobSvc.passphrase**.
4. Your passphrase will be stored in the file located at **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## Renew SSL certificates

The configuration server has an inbuilt web server, which orchestrates activities of the Mobility Service, process servers, and master target servers connected to it. The web server uses an SSL certificate to authenticate clients. The certificate expires after three years and can be renewed at any time.

### Check expiry

For configuration server deployments before May 2016, certificate expiry was set to one year. If you have a certificate that is going to expire, the following occurs:

- When the expiry date is two months or less, the service starts sending notifications in the portal, and by email (if you subscribed to Site Recovery notifications).
- A notification banner appears on the vault resource page. For more information, select the banner.
- If you see an **Upgrade Now** button, it indicates that some components in your environment haven't been upgraded to 9.4.xxxx.x or higher versions. Upgrade the components before you renew the certificate. You can't renew on older versions.

### Renew the certificate

1. In the vault, open **Site Recovery Infrastructure** > **Configuration Server**. Select the required configuration server.
2. The expiry date appears under **Configuration Server health**.
3. Select **Renew Certificates**. 


## Next steps

Review the tutorials for setting up disaster recovery of [VMware VMs](vmware-azure-tutorial.md) to Azure.
