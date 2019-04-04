---
title: Mover um cofre dos Serviços de Recuperação entre assinaturas do Azure ou para outro grupo de recursos
description: Instruções para mover o cofre dos serviços de recuperação entre grupos de recursos e assinaturas do Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: sogup
ms.openlocfilehash: 7745f986c6e9ba22258f51f9329444b8232762e1
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905748"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups-limited-public-preview"></a>Mover um cofre dos Serviços de Recuperação entre Assinaturas do Azure e Grupos de Recursos (Versão Prévia Pública Limitada)

Este artigo explica como mover um cofre dos Serviços de Recuperação configurado para Backup do Azure entre assinaturas do Azure ou para outro grupo de recursos na mesma assinatura. Você pode usar o portal do Azure ou o PowerShell para mover um cofre dos Serviços de Recuperação.

> [!NOTE]
> Para mover um cofre dos serviços de recuperação e seus recursos associados ao grupo de recursos diferente, primeiro você deve [registrar a assinatura de código-fonte](#register-the-source-subscription-to-move-your-recovery-services-vault).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-moving-a-vault"></a>Pré-requisitos para mover um cofre

- Ao mover entre grupos de recursos, o grupo de recursos de origem e o grupo de recursos de destino são ambos bloqueados durante a operação. Até a movimentação ser concluída, as operações de gravação e exclusão serão bloqueadas nos grupos de recursos.
- Somente o administrador de assinatura tem as permissões para mover um cofre.
- Ao mover um cofre entre assinaturas, a assinatura de destino deve existir em um estado habilitado e estar no mesmo locatário que a assinatura de origem.
- Você deve ter permissão para executar operações de gravação no grupo de recursos de destino.
- Você não pode alterar o local do cofre dos Serviços de Recuperação. Mover cofre altera apenas o grupo de recursos. Novo grupo de recursos pode estar em um local diferente, mas isso não altera o local do cofre.
- No momento, você pode mover um cofre dos Serviços de Recuperação por região por vez.
- Se uma VM não for movida com o cofre dos Serviços de Recuperação entre assinaturas, ou para um novo grupo de recursos, os pontos de recuperação da VM atuais permanecerão intactos no cofre até expirarem.
- Seja a VM movida com o cofre ou não, você sempre pode restaurar a VM do histórico de backup retido no cofre.
-   O Azure Disk Encryption exige que seu cofre de chaves e as VMs residam na mesma região e assinatura do Azure.
-   Para mover uma máquina virtual com discos gerenciados, veja este [artigo](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
-   As opções de movimentação dos recursos implantados por meio do modelo Clássico apresentam diferenças dependendo de se você está movendo os recursos em uma assinatura ou para uma nova assinatura. Para obter mais informações, veja este [artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#classic-deployment-limitations).
-   Políticas de backup definidas para o cofre são mantidas após o cofre ser movido entre assinaturas ou para um novo grupo de recursos.
-   Atualmente, não é possível mover cofres que contenham arquivos do Azure, Sincronização de Arquivos do Azure ou SQL em VMs IaaS entre assinaturas e grupos de recursos. O suporte para esses cenários será adicionado em futuras versões.
-   Se você mover um cofre contendo dados de backup da VM entre assinaturas, deverá mover as VMs para a mesma assinatura e usar o mesmo grupo de recursos de destino para continuar com os backups.<br>

> [!NOTE]
>
> Cofres dos Serviços de Recuperação configurados para uso com o **Azure Site Recovery** ainda não podem ser movidos. Se você tiver configurado todas as VMs (IaaS do Azure, Hyper-V, VMware) ou computadores físicos para recuperação de desastre usando o **Azure Site Recovery**, a operação de movimentação será bloqueada. O recurso de movimentação de recursos para o serviço do Site Recovery ainda não está disponível.

## <a name="register-the-source-subscription-to-move-your-recovery-services-vault"></a>Registrar a assinatura de origem para mover o cofre dos Serviços de Recuperação

Para registrar a assinatura de origem para **Mover** o cofre dos Serviços de Recuperação, execute os seguintes cmdlets no terminal do PowerShell:

1. Entre na sua conta do Azure

   ```
   Connect-AzAccount
   ```

2. Selecione a assinatura que você deseja registrar

   ```
   Get-AzSubscription –SubscriptionName "Subscription Name" | Select-AzSubscription
   ```
3. Registrar esta assinatura

   ```
   Register-AzProviderFeature -ProviderNamespace Microsoft.RecoveryServices -FeatureName RecoveryServicesResourceMove
   ```

4. Executar o comando

   ```
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

Aguarde 30 minutos para que a assinatura ser colocada na lista de permissões antes de começar a operação de movimentação usando o portal do Azure ou o PowerShell.

## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-different-resource-group"></a>Use o portal do Azure para mover um cofre dos Serviços de Recuperação para outro grupo de recursos

Para mover um cofre dos serviços de recuperação e seus recursos associados para um grupo de recursos diferentes

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Abra a lista de **Cofres dos Serviços de Recuperação** e selecione o cofre que você deseja mover. Quando o painel do cofre se abrir, ele aparecerá como mostrado na imagem a seguir.

   ![Abrir o Cofre do Serviço de Recuperação](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Se você não visualizar as informações do **Essentials** para seu cofre, clique no ícone de menu suspenso. Agora você deve ver as informações do Essentials para seu cofre.

   ![Guia de Informações do Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu de visão geral do cofre, clique em **alterar** ao lado de **Grupo de recursos** para abrir a folha **Mover recursos**.

   ![Alterar Grupo de Recursos](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Na folha **Mover recursos**, para o cofre selecionado, é recomendado mover os recursos relacionados opcionais marcando a caixa de seleção conforme mostrado na imagem a seguir.

   ![Mover assinatura](./media/backup-azure-move-recovery-services/move-resource.png)

5. Para adicionar o grupo de recursos de destino, na lista suspensa **Grupo de recursos**, selecione um grupo de recursos existente ou clique na opção **criar um novo grupo**.

   ![Criar recurso](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Depois de adicionar o grupo de recursos, confirme a opção **Eu entendo que ferramentas e scripts associados aos recursos movidos não funcionarão até que eu os atualize para usar as novas IDs de recurso** e, em seguida, clique em **OK** para concluir a movimentação do cofre.

   ![Mensagem de confirmação](./media/backup-azure-move-recovery-services/confirmation-message.png)


## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-a-different-subscription"></a>Use o portal do Azure para mover um cofre dos Serviços de Recuperação para uma assinatura diferente

Você pode mover um cofre dos Serviços de Recuperação e seus recursos associados para uma assinatura diferente

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Abra a lista de cofres dos Serviços de Recuperação e selecione o cofre que você deseja mover. Quando o painel do cofre se abrir, ele aparecerá como mostrado na imagem a seguir.

    ![Abrir o Cofre do Serviço de Recuperação](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Se você não visualizar as informações do **Essentials** para seu cofre, clique no ícone de menu suspenso. Agora você deve ver as informações do Essentials para seu cofre.

    ![Guia de Informações do Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu de visão geral do cofre, clique em **alterar** lado de **Assinatura** para abrir a folha **Mover recursos**.

   ![Alterar assinatura](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selecione os recursos a serem movidos. Aqui, recomendamos usar a opção **Selecionar Tudo** para selecionar todos os recursos opcionais listados.

   ![mover recurso](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Selecione a assinatura de destino na lista suspensa **Assinaturas** para a qual você deseja que o cofre seja movido.
6. Para adicionar o grupo de recursos de destino, na lista suspensa **Grupo de recursos**, selecione um grupo de recursos existente ou clique na opção **criar um novo grupo**.

   ![Adicionar assinatura](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Clique na opção **Eu entendo que ferramentas e scripts associados aos recursos movidos não funcionarão até que eu os atualize para usar as novas IDs de recurso** para confirmar e, em seguida, clique em **OK**.

> [!NOTE]
> Backup entre assinaturas (cofre RS e VMs protegidas estão em assinaturas diferentes) não é um cenário com suporte. Além disso, a opção de redundância de armazenamento de LRS (armazenamento com redundância local) para GRS (armazenamento com redundância global) e vice-versa não pode ser modificada durante a operação de movimentação de cofre.
>
>

## <a name="use-powershell-to-move-a-vault"></a>Usar o PowerShell para mover um cofre

Para mover um cofre dos Serviços de Recuperação para outro grupo de recursos, use o cmdlet `Move-AzResource`. `Move-AzResource` requer o nome do recurso e o tipo de recurso. Você pode obter ambos do cmdlet `Get-AzRecoveryServicesVault`.

```
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Para mover os recursos para uma assinatura diferente, inclua o parâmetro `-DestinationSubscriptionId`.

```
Move-AzResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Depois de executar os cmdlets acima, você será solicitado a confirmar se deseja mover os recursos especificados. Digite **Y** para confirmar. Após uma validação bem-sucedida, o recurso será movido.

## <a name="use-cli-to-move-a-vault"></a>Use a CLI para mover um cofre

Para mover um cofre dos Serviços de Recuperação para outro grupo de recursos, use o seguinte cmdlet:

```
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Para mover para uma nova assinatura, forneça o parâmetro `--destination-subscription-id`.

## <a name="post-migration"></a>Após a migração

1. Você precisa definir/verificar os controles de acesso para os grupos de recursos.  
2. O recurso de monitoramento e relatórios de Backup precisa ser configurado novamente para o cofre após a movimentação ser concluída. A configuração anterior será perdida durante a operação de movimentação.



## <a name="next-steps"></a>Próximas etapas

Você pode mover vários tipos diferentes de recursos entre grupos de recursos e assinaturas.

Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
