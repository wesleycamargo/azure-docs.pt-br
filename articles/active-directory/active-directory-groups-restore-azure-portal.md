---
title: "Restaurar um grupo do Office 365 excluído na visualização do Azure Active Directory | Microsoft Docs"
description: "Como restaurar um grupo excluído, exibir grupos restauráveis e excluir permanentemente um grupo no Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 4e2fe22953ff74214c0c20fdf2fabceeda63be35
ms.lasthandoff: 04/27/2017


---

# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Restaurar um grupo do Office 365 excluído na visualização do Azure Active Directory

Quando você exclui um grupo do Office 365 na visualização do Azure Active Directory (Azure AD), o grupo excluído é mantido, mas não fica visível por 30 dias a partir da data de exclusão. Isso é para que o grupo e seu conteúdo possam ser restaurados se necessário. Essa funcionalidade é restrita exclusivamente a grupos do Office 365 no Azure AD. Ele não fica disponível para grupos de segurança e de distribuição.

As permissões necessárias para restaurar um grupo podem ser qualquer uma das seguintes:

Função  | Permissões 
--------- | ---------
Administrador da Empresa, suporte de Camada 2 do Parceiro e Administradores de Serviço do InTune | Pode restaurar qualquer grupo do Office 365 excluído 
Administrador da Conta de Usuário e suporte de Camada 1 do Parceiro | Pode restaurar qualquer grupo do Office 365 excluído, exceto aqueles atribuídos à função de Administrador de Empresa 
Usuário | Pode restaurar qualquer grupo do Office 365 excluído que era propriedade dele 


## <a name="how-to-view-deleted-office-365-groups-that-are-available-to-restore"></a>Como exibir grupos do Office 365 excluídos que estão disponíveis para restauração
Os cmdlets a seguir podem ser usados para exibir os grupos excluídos para verificar que aqueles em que você está interessado ainda não foram permanentemente removidos. Esses cmdlets fazem parte do [módulo de Visualização do Azure Active Directory PowerShell V2](https://www.powershellgallery.com/packages/AzureADPreview). Mais informações sobre esse módulo podem ser encontradas no artigo [Azure Active Directory PowerShell Versão 2](/powershell/azure/install-adv2?view=azureadps-2.0).
Observe que os cmdlets para gerenciamento de recuperação e exclusão reversível estão em Visualização Pública e, às vezes, precisamos fazer alterações significativas para visualizar os cmdlets. Por esse motivo, não é recomendado usar esses cmdlets em um ambiente de produção.

1.    Execute o cmdlet a seguir para exibir todos os grupos do Office 365 excluídos em seu locatário que ainda estão disponíveis para restauração.
  ```
  Get-AzureADMSDeletedGroup
  ```

2.    Como alternativa, se você souber a objectID de um grupo específico (e você pode obtê-la do cmdlet na etapa 1), execute o cmdlet a seguir para verificar se o grupo excluído específico não ainda foi permanentemente removido.
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```



## <a name="how-to-restore-an-office-365-group"></a>Como restaurar um grupo do Office 365
Após ter verificado que o grupo ainda está disponível para restauração, restaure o grupo excluído com uma das etapas a seguir. Se o grupo contém documentos, sites de SP ou outros objetos persistentes, pode levar até 24 horas para restaurar completamente um grupo e seu conteúdo.

1.    Execute o cmdlet a seguir para restaurar o grupo e seu conteúdo.
  
  ```
  Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
  ``` 

Como alternativa, o cmdlet a seguir pode ser executado para remover permanentemente o grupo excluído.
  ```
  Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
  ```

## <a name="how-do-you-know-this-worked"></a>Como saber se isso funcionou?
Para verificar se você restaurou com êxito um grupo do Office 365, execute o cmdlet `Get-AzureADGroup –ObjectId <objectId>` para exibir informações sobre o grupo. Após a solicitação de restauração ser concluída:
- O grupo será exibido na barra de navegação esquerda no Exchange
- O plano para o grupo será exibido no Planner
- Os sites do Sharepoint e todo o seu conteúdo estarão disponíveis
- O grupo pode ser acessado de qualquer um dos pontos de extremidade do Exchange e outras cargas de trabalho do Office 365 que dão suporte a grupos do Office 365


## <a name="next-steps"></a>Próximas etapas
Esses artigos fornecem mais informações sobre grupos do Azure Active Directory.

* [Consultar grupos existentes](active-directory-groups-view-azure-portal.md)
* [Gerenciar configurações de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerenciar membros de um grupo](active-directory-groups-members-azure-portal.md)
* [Gerenciar associações de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerenciar regras dinâmicas para usuários em um grupo](active-directory-groups-dynamic-membership-azure-portal.md)

