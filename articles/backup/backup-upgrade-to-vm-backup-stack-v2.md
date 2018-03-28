---
title: Atualizar para a V2 da pilha de backup da VM do Azure| Microsoft Docs
description: Processo de atualização e perguntas frequentes sobre a V2 da pilha de backup da VM
services: backup, virtual-machines
documentationcenter: ''
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/08/2018
ms.author: trinadhk
ms.openlocfilehash: b7e9f45c61d2af1940be50a368b87cd35c85b1dd
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="upgrade-to-vm-backup-stack-v2"></a>Atualizar para a V2 da pilha de backup da VM
A atualização para a V2 da pilha de backup da VM (máquina virtual) fornece os aprimoramentos de recursos a seguir:
* Capacidade de visualizar o instantâneo tirado como parte do trabalho de backup para estar disponível para recuperação sem aguardar a conclusão da transferência de dados.
Isso reduzirá a espera no instantâneo a ser copiado para o cofre antes de disparar a restauração. Além disso, isso eliminará o requisito adicional de armazenamento para backup de VMs Premium, exceto para o primeiro backup.  

* Redução nos tempos de restauração e backup, mantendo os instantâneos localmente por 7 dias. 

* Suporte para tamanhos de disco de até 4 TB.  

* Capacidade de usar contas de armazenamento originais (mesmo quando a VM tiver discos distribuídos entre contas de armazenamento) ao fazer uma restauração de uma VM não gerenciada. Isso tornará as restaurações mais rápidas para uma ampla variedade de configurações de VM. 
    > [!NOTE] 
    > Isso não é o mesmo que substituir a VM original. 
    > 
    >

## <a name="what-is-changing-in-the-new-stack"></a>O que está alterado na nova pilha?
Hoje, o trabalho de backup consiste em duas fases:
1.  Obtendo instantâneo da VM. 
2.  Transferindo o instantâneo da VM para o cofre de Backup do Azure. 

Um ponto de recuperação será considerado criado somente após as fases 1 e 2 tiverem sido concluídas. Como parte da nova pilha, um ponto de recuperação é criado assim que o instantâneo é concluído. Também é possível restaurar a partir desse ponto de recuperação usando o mesmo fluxo de restauração. É possível identificar esse ponto de recuperação no Portal do Azure, utilizando “instantâneo” como tipo de ponto de recuperação. Depois que o instantâneo é transferido para o cofre, o tipo de ponto de recuperação é alterado para “Instantâneo e Cofre”. 

![Trabalho de backup na V2 da pilha de backup da VM](./media/backup-azure-vms/instant-rp-flow.jpg) 

Por padrão, os instantâneos serão retidos por sete dias. Isso permite que a restauração seja concluída mais rapidamente a partir desses instantâneos, reduzindo o tempo necessário para copiar os dados do cofre para a conta de armazenamento do cliente. 

## <a name="considerations-before-upgrade"></a>Considerações antes de atualizar
* Essa é uma atualização unidirecional da pilha de backup da VM. Assim, todos os backups futuros entrarão nesse fluxo. Como  **está habilitado em um nível de assinatura, todas as VMs irão para esse fluxo**. Todas as novas adições de recursos serão baseadas na mesma pilha. A capacidade de controlar isso no nível de política está chegando em versões futuras. 
* Para VMs com discos Premium, durante o primeiro backup verifique se o espaço de armazenamento equivalente ao tamanho da VM estará disponível na conta de armazenamento até que o primeiro backup seja concluído. 
* Como os instantâneos são armazenados localmente para impulsionar a criação do ponto de recuperação e também para acelerar a restauração, você verá os custos de armazenamento correspondentes aos instantâneos durante o período de sete dias.
Para discos gerenciados, não há *aumento no preço* porque restorePointCollections é gratuito. 
* Se você estiver fazendo uma restauração do ponto de recuperação do Instantâneo para uma VM Premium, verá um local de armazenamento temporário sendo utilizado enquanto a VM estiver sendo criada como parte da restauração. 

## <a name="how-to-upgrade"></a>Como atualizar?
### <a name="the-azure-portal"></a>O Portal do Azure
Se estiver utilizando o Portal do Azure, você verá uma notificação no painel do cofre relacionado ao suporte a discos grandes e às melhorias de velocidade de restauração e backup.

![Trabalho de backup na V2 da pilha de backup da VM](./media/backup-azure-vms/instant-rp-banner.png) 

Para abrir uma tela para atualização da nova pilha, selecione a faixa. 

![Trabalho de backup na V2 da pilha de backup da VM](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Execute os cmdlets a seguir de um terminal do PowerShell elevado:
1.  Entre na sua conta do Azure. 

```
PS C:> Login-AzureRmAccount
```

2.  Selecione a assinatura que você deseja registrar para versão prévia:

```
PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
```

3.  Registre essa assinatura para versão prévia privada:

```
PS C:>  Register-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

## <a name="verify-whether-the-upgrade-is-complete"></a>Verifique se a atualização está completa
De um terminal do PowerShell elevado, execute o cmdlet a seguir:

```
Get-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

Se estiver Registrada, a assinatura será atualizada para a V2 da pilha de backup da VM. 



