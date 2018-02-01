---
title: " Gerenciar o servidor de configuração para a recuperação após desastres do VMware com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como gerenciar um servidor de configuração existente para recuperação após desastres do VMware no Azure, com o serviço do Azure Site Recovery."
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 01/15/2017
ms.author: anoopkv
ms.openlocfilehash: e9e4bfc86df2cae1facac62472c915d91fb8c84c
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2018
---
# <a name="manage-the-configuration-server"></a>Gerenciar o servidor de configuração

Você configura um servidor de configuração local quando você usa o serviço do [Azure Site Recovery](site-recovery-overview.md) para a recuperação após desastres de VMs do VMware e servidores físicos para o Azure. O servidor de configuração coordena a comunicação entre o ambiente de VMware local e o Azure e gerencia a replicação de dados. Este artigo resume as tarefas comuns para gerenciar o servidor de configuração depois de implantado.

## <a name="modify-vmware-settings"></a>Modificar as configurações de VMware

Modificar as configurações do servidor de VMware que conecta o servidor de configuração.

1. Realize o logon no computador que executa o servidor de configuração.
2. Inicie o System Center Configuration Manager do Azure Site Recovery a partir de um atalho da área de trabalho. Ou abra **https://configuration-server-name/IP:44315**.
3. Clique em **Gerenciar vCenter Server/vSPhere ESXi server**:
    - Para associar um servidor diferente do VMware com o servidor de configuração, clique em **Adicionar vCenter Server/vSphere ESXi server**e especifique os detalhes do servidor.
    - Para atualizar as credenciais usadas para se conectar ao servidor do VMware para a descoberta automática de VMs do VMware, clique em **Editar**. Especifique as novas credenciais e clique em **OK**.

        ![Modificar VMware](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-vmware-server.png)

## <a name="modify-credentials-for-mobility-service-installation"></a>Modificar credenciais para instalação do serviço de mobilidade

Modifique as credenciais usadas para instalar automaticamente o serviço de mobilidade nas VMs do VMware que você habilitar para replicação.

1. Realize o logon no computador que executa o servidor de configuração.
2. Inicie o System Center Configuration Manager do Azure Site Recovery a partir de um atalho da área de trabalho. Ou abra **https://configuration-server-name/IP:44315**.
3. Clique em **Gerenciar credenciais de máquina virtual**e especifique as novas credenciais. A seguir, clique em **OK** para atualizar as configurações.

    ![Modificar as credenciais de serviço de mobilidade](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Modificar as configurações de proxy

Modificar as configurações de proxy usadas pela máquina do servidor de configuração para acesso à internet para o Azure. Se você tiver uma máquina de servidor de processo adicional, além do servidor de processo padrão em execução na máquina do servidor de configuração, modifique as definições em ambos os computadores.

1. Realize o logon no computador que executa o servidor de configuração.
2. Inicie o System Center Configuration Manager do Azure Site Recovery a partir de um atalho da área de trabalho. Ou abra **https://configuration-server-name/IP:44315**.
3. Clique em **Gerenciar conectividade**e atualize os valores de proxy. A seguir, clique em **Salvar** para atualizar as configurações.

## <a name="add-a-network-adapter"></a>Adicione um adaptador de rede

O modelo OVF implanta a VM do servidor de configuração com um único adaptador de rede. Você pode [adicionar um adaptador adicional para a máquina virtual)](how-to-deploy-configuration-server.md#add-an-additional-adapter), mas você precisa fazer isso antes de registrar o servidor de configuração no cofre.

Se você precisar adicionar um adaptador após ter registrado o servidor de configuração no cofre, você precisa adicionar o adaptador nas propriedades da VM e, em seguida, registrar novamente o servidor no cofre.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Registrar um servidor de configuração no mesmo cofre

Você pode registrar novamente o servidor de configuração no mesmo cofre se necessário. Se você tiver uma máquina de servidor de processo adicional, além do servidor de processo padrão em execução na máquina do servidor de configuração, registre novamente em ambos os computadores.

  1. No cofre, abra **gerenciar** > **infra-estrutura do Azure Site Recovery** > **Servidores de Configuração**.
  2. Em **Servidores**, clique em **Fazer download de chave de registro**. Isso baixa o arquivo de credenciais do cofre.
  3. Faça logon na máquina do servidor de configuração.
  4. Em **%ProgramData%\ASR\home\svagent\bin**, abra **cspsconfigtool.exe**.
  5. Na guia **Registro do cofre**, clique em Procurar e localizar o arquivo de credenciais de cofre que você baixou.
  6. Se necessário, forneça os detalhes do servidor proxy. Em seguida, clique em **Registrar**.
  7. Abra uma janela de comando do PowerShell de administrador e execute o comando a seguir:

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

## <a name="delete-or-unregister-a-configuration-server"></a>Excluir ou cancelar o registro de um Servidor de Configuração

1. Desabilite [Desabilitar a proteção](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para todas as VMs no servidor de configuração.
2. [Desassociar](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) e [Excluir](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) todas as políticas de Replicação do Servidor de Configuração.
3. [Excluir](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery) todos os servidores vCenters/hosts vSphere associados ao Servidor de Configuração.
4. No cofre, abra **infra-estrutura do Site Recovery** > **Servidores de Configuração**
5. Clique no servidor de configuração que você deseja remover. A seguir, na página **Detalhes**, clique em **Excluir**.

    ![Excluir um servidor de configuração](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Excluir com o PowerShell

Opcionalmente, você pode excluir o servidor de configuração usando o PowerShell:

1. [Instalar](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) o módulo Azure PowerShell
2. Faça logon na sua conta do Azure usando o comando:
    
    `Login-AzureRmAccount`
3. Selecione a assinatura do cofre:

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Defina o contexto do cofre:
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Recupere o servidor de configuração:

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Exclua o servidor de configuração:

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Você pode usar a opção **-Force** em RRemove-AzureRmSiteRecoveryFabric para exclusão forçada do servidor de configuração.
 


## <a name="renew-ssl-certificates"></a>Renovar certificados SSL

O servidor de configuração tem uma servidor de Web embutido, que coordena as atividades dos serviços móveis, servidores de processo e servidores de destino conectados a ele. O servidor da web usa um certificado SSL para autenticar clientes. O certificado expira depois de três anos e pode ser renovado a qualquer momento.

### <a name="check-expiry"></a>Verificar expiração

Para implantações de servidor de configuração antes de maio de 2016, a expiração do certificado foi definida para um ano. Se você tiver um certificado prestes a expirar, ocorrerá o seguinte:

- Quando a data de expiração é de dois meses ou menos, o serviço começa a enviar notificações no portal e por e-mail (se você tiver assinado as notificações do Azure Site Recovery).
- Uma faixa de notificação será exibida na página de recursos do cofre. Clique na faixa para obter mais detalhes.
- Se você vir um botão **Atualizar agora**, isso indica que há alguns componentes em seu ambiente que ainda não foram atualizados para a versão 9.4.xxxx.x ou superior. Atualize os componentes antes de renovar o certificado. Não é possível renovar versões mais antigas.

### <a name="renew-the-certificate"></a>Renovar o certificado

1. No cofre, abra **Infra-estrutura do Site Recovery** > **Servidor de Configuração**e clique no servidor de configuração necessário.
2. A data de expiração aparece sob **Integridade do servidor de configuração**
3. Clique em **Renovar certificados**. 


## <a name="next-steps"></a>Próximas etapas

Analisar os tutoriais para configurar a recuperação após desastres de [VMs VMware](tutorial-vmware-to-azure.md) e servidores físicos (tutorial-physical-to-azure.md) no Azure.
