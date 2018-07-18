---
title: Atualizar o modelo de implantação do Gerenciador de Recursos do Azure para a pilha de backup de VM do Azure
description: Atualizar o processo e perguntas frequentes para pilha de backup de VM, modelo de implantação do Gerenciador de Recursos
services: backup, virtual-machines
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: trinadhk
ms.openlocfilehash: e822e0c354fd671ee2802506e0e268d4078b395e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606895"
---
# <a name="upgrade-to-the-azure-resource-manager-deployment-model-for-azure-vm-backup-stack"></a>Atualizar o modelo de implantação do Gerenciador de Recursos do Azure para a pilha de backup de VM do Azure
O modelo de implantação do Gerenciador de Recursos para a atualização para a pilha de backup de máquina virtual (VM) fornece os seguintes aprimoramentos:
* Capacidade de visualizar o instantâneo tirado como parte do trabalho de backup que está disponível para recuperação sem aguardar a conclusão da transferência de dados. Isso reduz o tempo de espera para instantâneos para copiar para o cofre antes de disparar a restauração. Além disso, isso eliminará o requisito adicional de armazenamento para backup de VMs Premium, exceto para o primeiro backup.  

* Redução nos tempos de restauração e backup, mantendo os instantâneos localmente por sete dias.

* Suporte para tamanhos de disco de até 4 TB.

* Capacidade de usar contas de armazenamento originais de uma VM não gerenciada durante a restauração. Essa capacidade existe mesmo quando a VM possuir discos que são distribuídos entre contas de armazenamento. Isso tornará as restaurações mais rápidas para uma ampla variedade de configurações de VM.
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
* A atualização da pilha de backup VM é direcional. Portanto, para todos os backups para esse fluxo. Uma vez que esta desabilitada no nível de assinatura, todas as VMs vão para esse fluxo. Todas as novas adições de recursos serão baseadas na mesma pilha. A capacidade de controlar isso no nível de política está chegando em versões futuras.

* Instantâneos são armazenados localmente para aumentar a criação do ponto de recuperação e também para acelerar a restauração. Portanto, você verá os custos de armazenamento que correspondem aos instantâneos durante o período de sete dias.

* Os instantâneos incrementais são armazenados como blobs de página. Todos os clientes que usam discos não gerenciados serão cobrados pelos instantâneos de sete dias armazenados na conta de armazenamento local do cliente. De acordo com o modelo de preços atual, não há nenhum custo para clientes em discos gerenciados.

* Se você fizer uma restauração do ponto de recuperação do Instantâneo para uma VM Premium, verá um local de armazenamento temporário sendo utilizado enquanto a VM estiver sendo criada como parte da restauração.

* Para contas de armazenamento premium, os instantâneos que são usados para recuperação imediata ocupam 10 TB de espaço alocado.

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
