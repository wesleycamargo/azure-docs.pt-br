---
title: Upgrade para a pilha de backup de VM V2 do Azure
description: Atualizar o processo e perguntas frequentes para pilha de backup de VM, modelo de implantação do Gerenciador de Recursos
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 7/18/2018
ms.author: trinadhk
ms.openlocfilehash: c9dff77f6b9fffc02ec94caa3454500772651195
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136886"
---
# <a name="upgrade-to-azure-vm-backup-stack-v2"></a>Upgrade para pilha de Backup de VM V2 do Azure

O modelo de implantação do Gerenciador de Recursos para a atualização para a pilha de backup de máquina virtual (VM) fornece os seguintes aprimoramentos:

* Capacidade de visualizar o instantâneo tirado como parte do trabalho de backup que está disponível para recuperação sem aguardar a conclusão da transferência de dados. Isso reduz o tempo de espera para instantâneos para copiar para o cofre antes de disparar a restauração. Além disso, isso eliminará o requisito adicional de armazenamento para backup de VMs Premium, exceto para o primeiro backup.  

* Reduz os tempos de backup e restauração mantendo os instantâneos localmente por sete dias.

* Suporte para tamanhos de disco de até 4 TB.

* Capacidade de usar as contas de armazenamento originais de uma VM não gerenciada ao restaurar. Essa capacidade existe mesmo quando a VM possui discos distribuídos em contas de armazenamento. Isso acelera as operações de restauração para uma ampla variedade de configurações de VM.
    > [!NOTE]
    > Essa capacidade não é o mesmo que substituir a VM original. 
    >

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

* Os instantâneos incrementais são armazenados como blobs de página. Todos os clientes que usam discos não gerenciados serão cobrados pelos instantâneos de sete dias armazenados na conta de armazenamento local do cliente. De acordo com o modelo de preços atual, não há custos para os clientes em discos gerenciados.

* Se você restaurar uma VM premium de um ponto de recuperação de instantâneo, um local de armazenamento temporário será usado enquanto a VM é criada.

* Para contas de armazenamento premium, os instantâneos obtidos para pontos de recuperação instantâneos contam para o limite de 10 TB de espaço alocado.

## <a name="upgrade"></a>Atualizar
### <a name="the-azure-portal"></a>O Portal do Azure
Se você usar o portal do Azure, verá uma notificação no painel do cofre. Essa notificação se relaciona com o suporte a disco grande e melhorias de velocidade de backup e restauração.

![Trabalho de backup na pilha de backup de VM, modelo de implantação do Gerenciador de Recursos - suporte e cofre](./media/backup-azure-vms/instant-rp-banner.png) 

Para abrir uma tela para atualização da nova pilha, selecione a faixa. 

![Trabalho de backup na pilha de backup de VM, modelo de implantação do Gerenciador de Recursos - atualização](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Execute os cmdlets a seguir de um terminal do PowerShell elevado:
1.  Entre na sua conta do Azure: 

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Selecione a assinatura que você deseja registrar para versão prévia:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Registre essa assinatura para versão prévia privada:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>Verificar se a atualização foi concluída
De um terminal do PowerShell elevado, execute o cmdlet a seguir:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

Se informar “Registrada”, a assinatura será atualizada para o modelo de implantação do Gerenciador de Recursos de pilha de backup VM

## <a name="frequently-asked-questions"></a>Perguntas frequentes

As perguntas e respostas a seguir foram coletadas em fóruns e perguntas de clientes.

### <a name="will-upgrading-to-v2-impact-current-backups"></a>Fazer upgrade para V2 impactará os backups atuais?

Se fizer upgrade para a V2 não haverá impacto nos backups atuais e não será necessário reconfigurar o ambiente. O upgrade e o ambiente de backup continuam funcionando da mesma forma.

### <a name="what-does-it-cost-to-upgrade-to-azure-backup-stack-v2"></a>Quanto custa fazer upgrade para pilha de backup v2 do Azure?

Não há custo de upgrade para pilha de backup v2 do Azure. Os instantâneos são armazenados localmente para acelerar a criação do ponto de recuperação e restaurar as operações. Como resultado, você verá os custos de armazenamento que correspondem aos instantâneos obtidos durante o período de sete dias.

### <a name="does-upgrading-to-stack-v2-increase-the-premium-storage-account-snapshot-limit-by-10-tb"></a>Fazer upgrade para a pilha v2 aumenta o limite de instantâneos da conta de armazenamento premium em 10 TB?

Não.

### <a name="in-premium-storage-accounts-do-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Nas contas de Armazenamento Premium, os instantâneos obtidos para o ponto de recuperação instantâneo ocupam o limite de instantâneos de 10 TB?

Sim, para contas de armazenamento premium, os instantâneos obtidos para o ponto de recuperação instantâneo ocupam os 10 TB de espaço alocados.

### <a name="how-does-the-snapshot-work-during-the-seven-day-period"></a>Como o instantâneo funciona durante o período de sete dias? 

Cada dia um novo instantâneo é tirado. Há sete instantâneos individuais. O serviço **não** tira uma cópia no primeiro dia e adiciona alterações nos próximos seis dias.

### <a name="what-happens-if-the-default-resource-group-is-deleted-accidentally"></a>O que acontecerá se o grupo de recursos padrão for excluído acidentalmente?

Se o grupo de recursos for excluído, os pontos de recuperação instantâneos de todas as VMs protegidas nessa região serão perdidos. Quando o próximo backup ocorrer, o grupo de recursos será criado novamente e os backups continuarão conforme o esperado. Essa funcionalidade não é exclusiva para pontos de recuperação instantâneos.

### <a name="can-i-delete-the-default-resource-group-created-for-instant-recovery-points"></a>Posso excluir o grupo de recursos padrão criado para pontos de recuperação instantâneos?

O serviço de Backup do Azure cria o grupo de recursos gerenciados. Atualmente, não é possível alterar ou modificar o grupo de recursos. Além disso, você não deve bloquear o grupo de recursos. Essa orientação não é apenas para a pilha V2.
 
### <a name="is-a-v2-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Um instantâneo v2 é um instantâneo incremental ou um instantâneo completo?

Instantâneos incrementais são usados para discos não gerenciados. Para discos gerenciados, o instantâneo é um instantâneo completo.
