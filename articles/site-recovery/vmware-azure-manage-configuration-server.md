---
title: Gerenciar o servidor de configuração para a recuperação após desastres do VMware com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como gerenciar um servidor de configuração existente de recuperação de desastre do VMware para o Azure com o Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 753e123c660b1aacea1157157f0e580e15c47536
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287398"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>Gerenciar o servidor de configuração para VMs VMware

Você configura um servidor de configuração local quando você usa o [Azure Site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos para o Azure. O servidor de configuração coordena a comunicação entre o ambiente de VMware local e o Azure e gerencia a replicação de dados. Este artigo resume as tarefas comuns para gerenciar o servidor de configuração depois de implantado.



## <a name="modify-vmware-settings"></a>Modificar as configurações de VMware

Você pode acessar o servidor de configuração da seguinte maneira:
    - Entre na VM na qual ela está implantada e inicie o Azure Site Recovery Configuration Manager no atalho da área de trabalho.
    - Como alternativa, você pode acessar o servidor de configuração remotamente a partir de **https://*ConfigurationServerName*/:44315 /**. Entre com credenciais de administrador.
   
### <a name="modify-vmware-server-settings"></a>Modifique as configurações do servidor VMware

1. Para associar um servidor VMware diferente ao servidor de configuração, após o login, selecione **Adicionar servidor vCenter Server / vSphere ESXi**.
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

- Você pode [ adicionar um adaptador adicional à VM) ](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), mas deve adicioná-lo antes de registrar o servidor de configuração no vault.
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
      net stop obengine
      net start obengine
      ```

## <a name="upgrade-the-configuration-server"></a>Atualizar o servidor de configuração

Você executa pacotes cumulativos de atualização para atualizar o servidor de configuração. As atualizações podem ser aplicadas até versões N-4. Por exemplo: 

- Se executar 9.7, 9.8, 9.9 ou 9.10, você poderá atualizar diretamente para 9.11.
- Se executar 9.6 ou anterior e quiser atualizar para 9.11, você deverá primeiramente atualizar para a versão 9.7. antes de 9.11.

Os links para pacotes cumulativos de atualização para todas as versões do servidor de configuração estão disponíveis no [página de atualizações wiki](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Atualize o servidor da seguinte maneira:

1. No cofre, vá para **Gerenciar**  >  **Infraestrutura de Recuperação do Site**  >  **Servidores de Configuração**.
2. Se uma atualização estiver disponível, aparecerá um link no **versão do agente** > coluna.

    ![Atualizar](./media/vmware-azure-manage-configuration-server/update2.png)

1. Baixe o arquivo instalador da atualização no servidor de configuração.

    ![Atualizar](./media/vmware-azure-manage-configuration-server/update1.png)

4. Clique duas vezes para executar o instalador.
2. O instalador detecta a versão atual em execução no computador. Clique em **Sim** para iniciar a atualização. 
3. Quando a atualização for concluída valida a configuração do servidor.

    ![Atualizar](./media/vmware-azure-manage-configuration-server/update3.png)

4. Clique em **Finish** para fechar o instalador.


## <a name="delete-or-unregister-a-configuration-server"></a>Excluir ou cancelar o registro de um Servidor de Configuração

1. [ Desative a proteção ](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para todas as VMs no servidor de configuração.
2. [Desassociar](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) e [Excluir](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) todas as políticas de Replicação do Servidor de Configuração.
3. [Exclua](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) todos os servidores vCenter/hosts vSphere associados ao servidor de configuração.
4. No cofre, abra **Infraestrutura do Site Recovery** > **Servidores de Configuração**.
5. Clique no servidor de configuração que deseja remover. Em seguida, na página **Detalhes**, selecione **Excluir**.

    ![Excluir um servidor de configuração](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Excluir com o PowerShell

Opcionalmente, você pode excluir o servidor de configuração usando o PowerShell.

1. [Instale](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) o módulo Azure PowerShell.
2. Conecte à sua conta do Azure usando este comando:
    
    `Connect-AzureRmAccount`
3. Selecione a assinatura do cofre.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Definir o contexto do cofre.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Recupere o servidor de configuração.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Excluir o servidor de configuração.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Você pode usar a opção **-Force** em RRemove-AzureRmSiteRecoveryFabric para exclusão forçada do servidor de configuração.
 


## <a name="renew-ssl-certificates"></a>Renovar certificados SSL

O servidor de configuração tem uma servidor de Web embutido, que coordena as atividades do Serviço de Mobilidade, dos servidores de processo e do servidores de destino mestre conectados a ele. O servidor da web usa um certificado SSL para autenticar clientes. O certificado expira depois de três anos e pode ser renovado a qualquer momento.

### <a name="check-expiry"></a>Verificar expiração

Para implantações de servidor de configuração antes de maio de 2016, a expiração do certificado foi definida para um ano. Se você tiver um certificado prestes a expirar, ocorrerá o seguinte:

- Quando a data de expiração é de dois meses ou menos, o serviço começa a enviar notificações no portal e por email (se você tiver assinado as notificações do Site Recovery).
- Uma faixa de notificação será exibida na página de recursos do cofre. Para obter mais informações, selecione a faixa.
- Se você vir um botão **Atualizar agora**, isso indica que alguns componentes em seu ambiente não foram atualizados para a versão 9.4.xxxx.x ou superiores. Atualize os componentes antes de renovar o certificado. Não é possível renovar versões mais antigas.

### <a name="renew-the-certificate"></a>Renovar o certificado

1. No cofre, abra **Infraestrutura do Site Recovery** > **Servidor de Configuração**. Selecione o servidor de configuração necessário.
2. A data de expiração aparece sob **Integridade do servidor de configuração**.
3. Selecione **Renovar Certificados**. 


## <a name="next-steps"></a>Próximas etapas

Analise os tutoriais para configurar a recuperação de desastre de [VMs VMware](vmware-azure-tutorial.md) para o Azure.
