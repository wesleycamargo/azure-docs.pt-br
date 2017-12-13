---
title: "Gerenciar servidores registrados com a Sincronização de arquivos do Azure (versão prévia) | Microsoft Docs"
description: "Saiba como registrar e cancelar o registro de um Windows Server com um Serviço de Sincronização de Armazenamento de Sincronização de Arquivo do Azure."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 8e707c193c5a8e294710973e128e1cf96d4f6461
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2017
---
# <a name="manage-registered-servers-with-azure-file-sync-preview"></a>Gerenciar servidores registrados com a Sincronização de arquivos do Azure (versão prévia)
A Sincronização de Arquivos do Azure (versão prévia) permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. Ele faz isso transformando Windows Servers em um cache rápido do seu compartilhamento de Arquivos do Azure. Você pode usar qualquer protocolo disponível no Windows Server para acessar seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter todos os caches de que precisar ao redor do mundo.

O artigo a seguir ilustra como registrar e gerenciar um servidor com um Serviço de Sincronização de Armazenamento. Consulte [Como implantar a Sincronização de Arquivo do Azure (versão prévia)](storage-sync-files-deployment-guide.md) para obter informações sobre como implantar a Sincronização de Arquivo do Azure de ponta a ponta.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registrar/cancelar o registro de um servidor no Serviço de Sincronização de Armazenamento
Registrar um servidor na Sincronização de arquivos do Azure estabelece uma relação de confiança entre o Windows Server e o Azure. Essa relação pode ser usada para criar *pontos de extremidade do servidor* no servidor, que representam as pastas específicas que devem ser sincronizadas com um compartilhamento de arquivos do Azure (também conhecidas como *pontos de extremidade de nuvem*). 

### <a name="prerequisites"></a>Pré-requisitos
Para registrar um servidor em um Serviço de Sincronização de Armazenamento, primeiro você deve preparar o servidor com os pré-requisitos necessários:

* O servidor deve estar executando uma versão do Windows Server compatível. Para obter mais informações, consulte [Versões com suporte do Windows Server](storage-sync-files-planning.md#supported-versions-of-windows-server).
* Certifique-se de que um Serviço de Sincronização de Armazenamento foi implantado. Para obter mais informações sobre como implantar um Serviço de Sincronização de Armazenamento, consulte [Como implantar a Sincronização de Arquivo do Azure (versão prévia)](storage-sync-files-deployment-guide.md).
* Certifique-se de que o servidor está conectado à Internet e que o Azure está acessível.
* Desabilite a Configuração de Segurança Reforçada do IE para administradores com a interface do usuário do Gerenciador do Servidor.
    
    ![Interface do usuário do Gerenciador do Servidor com a Configuração de Segurança Reforçada do IE realçada](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Certifique-se de que o módulo AzureRM PowerShell está instalado no servidor. Se o servidor for um membro de um Cluster de Failover, todos os nós no cluster precisarão do módulo AzureRM. É possível encontrar mais detalhes sobre como instalar o módulo do AzureRM em [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (Instalar e configurar o Azure PowerShell).

    > [!Note]  
    > É recomendável usar a versão mais recente do módulo AzureRM PowerShell para registrar/cancelar o registro de um servidor. Se o pacote do AzureRM tiver sido instalado anteriormente nesse servidor (e a versão do PowerShell nesse servidor for 5.* ou maior), você poderá usar o cmdlet `Update-Module` para atualizar esse pacote. 

### <a name="register-a-server-with-storage-sync-service"></a>Registrar um servidor com o Serviço de Sincronização de Armazenamento
Para que um servidor possa ser usado como um *ponto de extremidade do servidor* em um *grupo de sincronização* da Sincronização de arquivos do Azure, ele deve ser registrado em um *Serviço de Sincronização de Armazenamento*. Um servidor apenas pode ser registrado em um único Serviço de Sincronização de Armazenamento de cada vez.

#### <a name="install-the-azure-file-sync-agent"></a>Instalar o agente de Sincronização de Arquivo do Azure
1. [Baixe o agente de Sincronização de Arquivo do Azure](https://go.microsoft.com/fwlink/?linkid=858257).
2. Inicie o instalador do agente de Sincronização de Arquivo do Azure.
    
    ![O primeiro painel do instalador do agente de Sincronização de Arquivo do Azure](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Certifique-se de habilitar as atualizações para o agente de Sincronização de Arquivo do Azure usando o Microsoft Update. É importante porque correções de segurança críticas e aprimoramentos de recursos para o pacote do servidor são enviadas por meio do Microsoft Update.

    ![Certifique-se de que o Microsoft Update está habilitado no painel do Microsoft Update do instalador do agente de Sincronização de Arquivo do Azure](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Se o servidor não tiver sido registrado anteriormente, a interface do usuário de registro do servidor será aberta imediatamente após a conclusão da instalação.

> [!Important]  
> Se o servidor for um membro de um Cluster de Failover, o agente de Sincronização de Arquivo do Azure precisará ser instalado em cada nó no cluster.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registrar o servidor usando a interface do usuário de registro do servidor
> [!Important]  
> As assinaturas CSP (Provedor de Soluções na Nuvem) não podem usar a interface do usuário de registro de servidor. Em vez disso, use o PowerShell (abaixo desta seção).

1. Se a interface do usuário de registro do servidor não for iniciada imediatamente após a conclusão da instalação do agente de Sincronização de Arquivo do Azure, ela poderá ser iniciada manualmente executando `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Clique em *Entrar* para acessar sua assinatura do Azure. 

    ![Abrindo a caixa de diálogo da interface do usuário de registro de servidor](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Selecione a assinatura, o grupo de recursos e o Serviço de Sincronização de Armazenamento na caixa de diálogo.

    ![Informações do Serviço de Sincronização de Armazenamento](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Na versão prévia, é necessário entrar mais uma vez para concluir o processo. 

    ![Caixa de diálogo Entrar](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Se o servidor for um membro de um Cluster de Failover, cada servidor precisará executar o Registro do Servidor. Quando você exibe os servidores registrados no Portal do Azure, a Sincronização de arquivos do Azure reconhece automaticamente cada nó como um membro do mesmo Cluster de Failover e agrupa-os corretamente.

#### <a name="register-the-server-with-powershell"></a>Registrar o servidor com o PowerShell
Você também pode executar o registro do servidor por meio do PowerShell. Essa é a única maneira permitida para o registro do servidor em assinaturas de CSP (Provedor de Soluções na Nuvem):

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Cancelar o registro de um servidor com o Serviço de Sincronização de Armazenamento
Há várias etapas que são necessárias para cancelar o registro de um servidor com um Serviço de Sincronização de Armazenamento. Vamos analisar como cancelar o registro de um servidor corretamente.

#### <a name="optional-recall-all-tiered-data"></a>(Opcional) Realizar o recall de todos os dados em camadas
Quando habilitada para um ponto de extremidade do servidor, a definição de camadas de nuvem *colocará os arquivos em camadas* nos compartilhamentos de arquivos do Azure. Isso permite que os compartilhamentos de arquivos locais atuem como um cache, em vez de uma cópia completa do conjunto de dados, para utilizar o espaço no servidor de arquivos de forma eficiente. No entanto, se um ponto de extremidade do servidor for removido e ainda houver arquivos em camadas localmente no servidor, esses arquivos ficarão inacessíveis. Portanto, se o acesso contínuo ao arquivo for desejado, será necessário realizar o recall de todos os arquivos em camadas dos Arquivos do Azure antes de continuar com o cancelamento de registro. 

Isso pode ser feito com o cmdlet do PowerShell como mostrado abaixo:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Se o volume local que hospeda o ponto de extremidade do servidor não tiver espaço livre suficiente para recuperar todos os dados em camadas, o cmdlet `Invoke-StorageSyncFileRecall` falhará.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Remover o servidor de todos os grupos de sincronização
Antes de cancelar o registro do servidor no Serviço de Sincronização de Armazenamento, todos os pontos de extremidade do servidor nesse servidor devem ser removidos. Isso pode ser feito no portal do Azure:

1. Navegue até o Serviço de Sincronização do Armazenamento no qual o servidor está registrado.
2. Remova todos os pontos de extremidade do servidor deste servidor em cada grupo de sincronização no Serviço de Sincronização de Armazenamento. Isso pode ser feito clicando com o botão direito do mouse no ponto de extremidade do servidor relevante no painel Grupo de sincronização.

    ![Removendo um ponto de extremidade do servidor de um grupo de sincronização](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Isso também pode ser feito com um script simples do PowerShell:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Login-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

#### <a name="unregister-the-server"></a>Cancelar o registro do servidor
Agora que todos os dados foram recuperados e o servidor foi removido de todos os grupos de sincronização, é possível cancelar o registro do servidor. 

1. No portal do Azure, navegue até a seção *Servidores registrados* do Serviço de Sincronização de Armazenamento.
2. Clique com botão direito do mouse no servidor do qual deseja cancelar o registro e clique em “Cancelar Registro do Servidor”.

    ![Cancelar o registro do servidor](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Garantindo que a Sincronização de arquivos do Azure seja uma boa vizinha no seu data center 
Como a Sincronização de arquivos do Azure raramente será o único serviço em execução em seu data center, é interessante limitar o uso de rede e de armazenamento da Sincronização de arquivos do Azure.

> [!Important]  
> Definir limites muito baixos afetará o desempenho de sincronização e de recuperação da Sincronização de arquivos do Azure.

### <a name="set-azure-file-sync-network-limits"></a>Definir limites de rede da Sincronização de arquivos do Azure
Você pode restringir a utilização da rede para a Sincronização de arquivos do Azure usando os cmdlets 'StorageSyncNetworkLimit'. 

Por exemplo, você pode criar um novo limite de rede para garantir que a Sincronização de arquivos do Azure não usa mais de 10 Mbps entre 9h e 17h de segunda a sexta-feira: 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Você pode ver o seu limite usando o seguinte cmdlet:

```PowerShell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Para remover os limites de rede, use `Remove-StorageSyncNetworkLimit`. Por exemplo, o comando a seguir remove todos os limites de rede:

```PowerShell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Usar a QoS de armazenamento do Windows Server 
Quando a Sincronização de arquivos do Azure estiver hospedada em uma máquina virtual em execução em um host de virtualização do Windows Server, você poderá usar a QoS de armazenamento (qualidade de serviço de armazenamento) para regular o consumo de E/S do armazenamento. A política de QoS de armazenamento pode ser definida como um nível máximo (ou um limite, da mesma forma em que o limite de StorageSyncNetwork é imposto acima) ou como um nível mínimo (ou uma reserva). Definir um nível mínimo em vez de um máximo permite que a Sincronização de arquivos do Azure seja disparada para usar a largura de banda de armazenamento disponível se outras cargas de trabalho não a estiverem usando. Para obter mais informações, consulte [Qualidade de serviço do armazenamento](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Consulte também
- [Planejando uma implantação da Sincronização de arquivos do Azure (versão prévia)](storage-sync-files-planning.md)
- [Implantar a Sincronização de arquivos do Azure (versão prévia)](storage-sync-files-deployment-guide.md) 
- [Solucionar problemas da Sincronização de Arquivos do Azure (versão prévia)](storage-sync-files-troubleshoot.md)