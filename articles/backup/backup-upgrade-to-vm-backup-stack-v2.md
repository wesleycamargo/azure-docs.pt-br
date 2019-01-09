---
title: Upgrade para a pilha de backup de VM V2 do Azure
description: Atualizar o processo e perguntas frequentes para pilha de backup de VM, modelo de implantação do Gerenciador de Recursos
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 10/3/2018
ms.author: trinadhk
ms.openlocfilehash: 605ce97f786b6b674a4adf8d6b1ee50957ef25fa
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53582728"
---
# <a name="upgrade-to-azure-vm-backup-stack-v2"></a>Upgrade para pilha de Backup de VM V2 do Azure

O modelo de implantação do Gerenciador de Recursos para a atualização para a pilha de backup de máquina virtual (VM) fornece os seguintes aprimoramentos:

* Capacidade de visualizar o instantâneo tirado como parte do trabalho de backup que está disponível para recuperação sem aguardar a conclusão da transferência de dados. Isso reduz o tempo de espera para instantâneos para copiar para o cofre antes de disparar a restauração. Além disso, isso eliminará o requisito adicional de armazenamento para backup de VMs Premium, exceto para o primeiro backup.  

* Reduz os tempos de backup e restauração mantendo os instantâneos localmente por sete dias.

* Suporte para tamanhos de disco de até 4 TB.

* Capacidade de usar as contas de armazenamento originais de uma VM não gerenciada ao restaurar. Essa capacidade existe mesmo quando a VM possui discos distribuídos em contas de armazenamento. Isso acelera as operações de restauração para uma ampla variedade de configurações de VM.
    > [!NOTE]
    > Essa capacidade não é a mesma que substituir os discos da VM pelos dados do ponto de recuperação.


## <a name="whats-changing-in-the-new-stack"></a>O que está alterado na nova pilha?
Hoje, o trabalho de backup consiste em duas fases:
1.  Obter instantâneo da VM.
2.  Transferir o instantâneo da VM para o cofre de Backup do Azure.

Um ponto de recuperação será considerado criado somente após as fases 1 e 2 tiverem sido concluídas. Como parte da nova pilha, um ponto de recuperação é criado assim que o instantâneo é concluído. Também é possível restaurar a partir desse ponto de recuperação usando o mesmo fluxo de restauração. É possível identificar esse ponto de recuperação no Portal do Azure, utilizando “instantâneo” como tipo de ponto de recuperação. Depois que o instantâneo é transferido para o cofre, o tipo de ponto de recuperação é alterado para “instantâneo e cofre”.

![Trabalho de backup na pilha de backup de VM, modelo de implantação do Gerenciador de Recursos - armazenar e cofre](./media/backup-azure-vms/instant-rp-flow.jpg)

Por padrão, os instantâneos são mantidos por sete dias. Esse recurso permite a restauração para concluir mais rapidamente do que esses instantâneos. Reduz o tempo necessário para copiar dados do cofre para a conta de armazenamento do cliente.

## <a name="considerations-before-upgrade"></a>Considerações antes de atualizar

* O upgrade da pilha de backup da VM é unidirecional, todos os backups entram nesse fluxo. Como a alteração ocorre no nível da assinatura, todas as VMs entram nesse fluxo. Todas as novas adições de recursos serão baseadas na mesma pilha. Atualmente, não é possível controlar a pilha no nível de política.

* Instantâneos são armazenados localmente para aumentar a criação do ponto de recuperação e também acelerar as operações de restauração. Como resultado, você verá os custos de armazenamento correspondentes aos instantâneos obtidos durante o período de sete dias.

* Os instantâneos incrementais são armazenados como blobs de página. Todos os clientes que usam discos não gerenciados serão cobrados pelos instantâneos de sete dias armazenados na conta de armazenamento local do cliente. Uma vez que as coleções de ponto de restauração usadas por backups de VM gerenciada usam instantâneos de blob no nível de armazenamento subjacente, para discos gerenciados, você verá os custos correspondentes a [preços de instantâneo de blob](https://docs.microsoft.com/rest/api/storageservices/understanding-how-snapshots-accrue-charges), e eles são incrementais.

* Se você restaurar uma VM premium de um ponto de recuperação de instantâneo, um local de armazenamento temporário será usado enquanto a VM é criada.

* Para contas de armazenamento premium, os instantâneos obtidos para pontos de recuperação instantâneos contam para o limite de 10 TB de espaço alocado.

> [!NOTE]
> Atualize para a pilha V2 do Backup de VM do Azure para obter suporte ao Backup do Azure para os [Discos gerenciados SSD Standard](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

## <a name="upgrade"></a>Atualizar
### <a name="the-azure-portal"></a>O Portal do Azure
Se você usar o portal do Azure, verá uma notificação no painel do cofre. Essa notificação se relaciona com o suporte a disco grande e melhorias de velocidade de backup e restauração. Como alternativa, você pode ir para página de Propriedades do cofre para obter a opção de atualização.

![Trabalho de backup na pilha de backup de VM, modelo de implantação do Gerenciador de Recursos - suporte e cofre](./media/backup-azure-vms/instant-rp-banner.png)

Para abrir uma tela para atualização da nova pilha, selecione a faixa.

![Trabalho de backup na pilha de backup de VM, modelo de implantação do Gerenciador de Recursos - atualização](./media/backup-azure-vms/instant-rp.png)

### <a name="powershell"></a>PowerShell
Execute os cmdlets a seguir de um terminal do PowerShell elevado:
1.  Entre na sua conta do Azure:

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Selecione a assinatura que você deseja registrar:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Registrar esta assinatura:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```
### <a name="cli"></a>CLI
Execute os seguintes comandos em um shell:
1.  Entre na sua conta do Azure:

    ```
    az login
    ```

2.  Selecione a assinatura que você deseja registrar:

    ```
    az account set --subscription "Subscription Name"
    ```

3.  Registrar esta assinatura:

    ```
    az feature register --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>Verificar se a atualização foi concluída
### <a name="powershell"></a>PowerShell
De um terminal do PowerShell elevado, execute o cmdlet a seguir:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" -ProviderNamespace Microsoft.RecoveryServices
```

### <a name="cli"></a>CLI
Em ashell, execute o seguinte comando:

```
az feature show --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
```

Se ele diz: "Registrado", em seguida, sua assinatura é atualizada para a V2 da pilha de backup.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

As perguntas e respostas a seguir foram coletadas em fóruns e perguntas de clientes.

### <a name="will-upgrading-to-v2-impact-current-backups"></a>Fazer upgrade para V2 impactará os backups atuais?
Se fizer upgrade para a V2 não haverá impacto nos backups atuais e não será necessário reconfigurar o ambiente. O upgrade e o ambiente de backup continuam funcionando da mesma forma.

### <a name="what-does-it-cost-to-upgrade-to-azure-vm-backup-stack-v2"></a>Quanto custa atualizar para a pilha de Backup de VM do Azure v2?
Não há nenhum custo para atualizar a pilha para a v2. Os instantâneos são armazenados localmente para acelerar a criação do ponto de recuperação e restaurar as operações. Como resultado, você verá os custos de armazenamento que correspondem aos instantâneos obtidos durante o período de sete dias.

### <a name="does-upgrading-to-stack-v2-increase-the-premium-storage-account-snapshot-limit-by-10-tb"></a>Fazer upgrade para a pilha v2 aumenta o limite de instantâneos da conta de armazenamento premium em 10 TB?
Não, o limite do total de instantâneos por conta de armazenamento ainda permanece em 10TB.

### <a name="in-premium-storage-accounts-do-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Nas contas de Armazenamento Premium, os instantâneos obtidos para o ponto de recuperação instantâneo ocupam o limite de instantâneos de 10 TB?
Sim, para contas de armazenamento premium, os instantâneos obtidos para o ponto de recuperação instantâneo ocupam os 10 TB de espaço alocados.

### <a name="how-does-the-snapshot-work-during-the-seven-day-period"></a>Como o instantâneo funciona durante o período de sete dias?
Cada dia um novo instantâneo é tirado. Há sete instantâneos individuais. O serviço **não** tira uma cópia no primeiro dia e adiciona alterações nos próximos seis dias.

### <a name="is-a-v2-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Um instantâneo v2 é um instantâneo incremental ou um instantâneo completo?
Instantâneos incrementais são usados para discos não gerenciados. Para discos gerenciados, a coleção de ponto de restauração criada pelo Backup do Azure usa instantâneos de blob e, portanto, é incremental.

### <a name="how-to-get-standard-ssd-managed-disk-support-for-a-virtual-machine"></a>Como obter suporte a disco gerenciado SSD Standard para uma máquina virtual?
Atualize para a pilha V2 do Backup de VM do Azure para obter suporte ao Backup do Azure para os [Discos gerenciados SSD Standard](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-snapshot-tier1"></a>O que acontece se eu selecionar o período de retenção do ponto de restauração (Tier2) menor que o instantâneo (Tier1)?
A pilha de backup da VM v2 não permite excluir o ponto de restauração (Tier2), a menos que o instantâneo (Tier1) seja excluído. Atualmente, há suporte para período de retenção de 7 dias para exclusão de instantâneo (Tier1), portanto, o período de retenção do ponto de restauração (Tier2) menor que 7 dias não é atendido. É recomendável agendar período de retenção do ponto de restauração (Tier2) maior que 7 dias.
