---
title: Alterar ID de locatário do cofre de chaves de depois de mover a assinatura | Microsoft Docs
description: Saiba como mudar a ID de locatário para um cofre de chaves depois que uma assinatura é movida para um locatário diferente
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/13/2016
ms.author: ambapat

---
# Alterar a ID de locatário do cofre de chaves depois de mover a assinatura
### P: minha assinatura foi movida do locatário A para o locatário B. Como posso alterar a ID do locatário para o cofre de chaves existente e definir ACLs corretas para as entidades no locatário B?
Quando você cria um novo cofre de chaves em uma assinatura, ele é vinculado automaticamente à ID de locatário do Azure Active Directory padrão para essa assinatura. Todas as entradas de política de acesso também são vinculadas a essa ID de locatário. Quando você move a assinatura do Azure do locatário A para o locatário B, os cofres de chaves existentes ficam inacessíveis para as entidades (usuários e aplicativos) no locatário B. Para corrigir esse problema, você precisa

* alterar a ID do locatário associada a todos os cofres de chaves existentes nessa assinatura para o locatário B
* remover todas as entradas de política de acesso existentes
* adicionar novas entradas de política de acesso que estão associadas ao locatário B.

Por exemplo, se você tiver o cofre de chaves 'myvault' em uma assinatura que foi movida do locatário A para o B, veja como mudar a ID de locatário para o cofre de chaves e remover as antigas políticas de acesso.

<pre>
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Já que o cofre estava no locatário A antes de mover, o valor original de **$vault. Properties.TenantId** é locatário A e **(Get-AzureRmContext).Tenant.TenantId** é locatário B.

Agora que o cofre está associado ao locatário correto e as entradas de política de acesso antigas foram removidas, defina novas entradas de política de acesso com [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx).

## Próximas etapas
* Se você tiver dúvidas sobre o Cofre de Chaves, visite os [Fóruns do Cofre de Chaves do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

<!---HONumber=AcomDC_0921_2016-->