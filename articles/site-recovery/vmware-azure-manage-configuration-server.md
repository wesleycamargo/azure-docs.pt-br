---
title: "Gerenciar o servidor de configuração para a recuperação após desastres do VMware com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como gerenciar um servidor de configuração existente de recuperação de desastre do VMware para o Azure com o Azure Site Recovery."
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: ddb1c9426874634fea54f1b67509d4ac2af70eba
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>Gerenciar o servidor de configuração para VMs VMware

Você configura um servidor de configuração local quando você usa o [Azure Site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos para o Azure. O servidor de configuração coordena a comunicação entre o ambiente de VMware local e o Azure e gerencia a replicação de dados. Este artigo resume as tarefas comuns para gerenciar o servidor de configuração depois de implantado.


## <a name="modify-vmware-settings"></a>Modificar as configurações de VMware

Modificar as configurações do servidor de VMware que conecta o servidor de configuração.

1. Conecte-se ao computador que executa o servidor de configuração.
2. Inicie o Gerenciador de Configurações do Azure Site Recovery a partir de um atalho da área de trabalho. Ou abra [este link](https://configuration-server-name/IP:44315).
3. Selecione **Gerenciar Servidor vCenter/servidor vSPhere ESXi** e proceda da seguinte forma:

    * Para associar um servidor diferente do VMwareao servidor de configuração, selecione **Adicionar Servidor vCenter/servidor vSPhere ESXi**. Insira os detalhes do servidor.

    * Para atualizar as credenciais usadas para se conectar ao servidor do VMware para a descoberta automática de VMs do VMware, selecione em **Editar**. Digite as novas credenciais e selecione **OK**.

    ![Modificar VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

## <a name="modify-credentials-for-mobility-service-installation"></a>Modificar credenciais para instalação do Serviço de Mobilidade

Modifique as credenciais usadas para instalar automaticamente o Serviço de Mobilidade nas VMs do VMware que você habilitar para replicação.

1. Conecte-se ao computador que executa o servidor de configuração.
2. Inicie o Gerenciador de Configurações do Site Recovery a partir de um atalho da área de trabalho. Ou abra [este link](https://configuration-server-name/IP:44315).
3. Selecione em **Gerenciar credenciais de máquina virtual**e digite as novas credenciais. A seguir, selecione **OK** para atualizar as configurações.

    ![Modificar as credenciais de Serviço de Mobilidade](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Modificar as configurações de proxy

Modificar as configurações de proxy usadas pela máquina do servidor de configuração para acesso à internet para o Azure. Se você tiver uma máquina de servidor de processo, além do servidor de processo padrão em execução na máquina do servidor de configuração, modifique as definições em ambos os computadores.

1. Conecte-se ao computador que executa o servidor de configuração.
2. Inicie o Gerenciador de Configurações do Site Recovery a partir de um atalho da área de trabalho. Ou abra [este link](https://configuration-server-name/IP:44315).
3. Selecione **Gerenciar conectividade** e atualize os valores de proxy. Selecione **Salvar** para atualizar as configurações.

## <a name="add-a-network-adapter"></a>Adicione um adaptador de rede

O modelo Open Virtualization Format (OVF) implanta a VM do servidor de configuração com um único adaptador de rede. Você pode [adicionar mais um adaptador à máquina virtual)](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), mas é preciso adicioná-lo antes de registrar o servidor de configuração no cofre.

Para adicionar um adaptador depois de registrar o servidor de configuração no cofre, adicione o adaptador nas propriedades da VM. Em seguida, registre o servidor no cofre.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Registrar um servidor de configuração no mesmo cofre

Você pode registrar novamente o servidor de configuração no mesmo cofre se necessário. Se você tiver uma máquina de servidor de processo adicional, além do servidor de processo padrão em execução na máquina do servidor de configuração, registre novamente em ambos os computadores.


  1. No cofre, abra **gerenciar** > **infra-estrutura do Azure Site Recovery** > **Servidores de Configuração**.
  2. Em **Servidores**, selecione **Baixar a chave de registro** para baixar o arquivo de credenciais do cofre.
  3. Conecte-se ao computador do servidor de configuração.
  4. Em **%ProgramData%\ASR\home\svagent\bin**, abra **cspsconfigtool.exe**.
  5. Na guia **Registro do Cofre**, selecione **Procurar** e localize o arquivo de credenciais do cofre que você baixou.
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

1. Baixe o arquivo instalador da atualização no servidor de configuração.
2. Clique duas vezes para executar o instalador.
3. O instalador detecta a versão atual em execução no computador.
4. Selecione **OK** para confirmar e executar o upgrade. 


## <a name="delete-or-unregister-a-configuration-server"></a>Excluir ou cancelar o registro de um Servidor de Configuração

1. Desabilite [Desabilitar a proteção](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para todas as VMs no servidor de configuração.
2. [Desassociar](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) e [Excluir](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) todas as políticas de Replicação do Servidor de Configuração.
3. [Exclua](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) todos os servidores vCenter/hosts vSphere associados ao servidor de configuração.
4. No cofre, abra **Infraestrutura do Site Recovery** > **Servidores de Configuração**.
5. Clique no servidor de configuração que deseja remover. Em seguida, na página **Detalhes**, selecione **Excluir**.

    ![Excluir um servidor de configuração](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Excluir com o PowerShell

Opcionalmente, você pode excluir o servidor de configuração usando o PowerShell.

1. [Instale](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) o módulo Azure PowerShell.
2. Conecte à sua conta do Azure usando este comando:
    
    `Login-AzureRmAccount`
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
