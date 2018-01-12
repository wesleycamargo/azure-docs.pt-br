---
title: "Habilitar o backup para a pilha do Azure no portal de administração | Microsoft Docs"
description: "Habilite o serviço de Backup de infraestrutura por meio do portal de administração para que a pilha do Azure podem ser restaurada, se houver uma falha."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 456a0db9771f5963c8d4375d54a22257f6ca1c56
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2018
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Habilitar o backup para a pilha do Azure no portal de administração

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Habilite o serviço de Backup de infraestrutura por meio do portal de administração para que a pilha do Azure pode gerar backups. Você pode usar esses backups para restaurar o ambiente em caso de falha.

> [!Note]  
> Antes de habilitar o backup por meio do console, você precisa configurar o serviço de backup. Você pode configurar o serviço de backup usando o PowerShell. Para obter mais informações, consulte [habilitar o Backup para a pilha do Azure com o PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Habilitar o backup

1. Abra o portal de administração de pilha do Azure em [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Selecione **mais serviços** > **backup da infra-estrutura**. Escolha **configuração** no **backup da infra-estrutura** folha.

    ![Pilha do Azure - configurações de controlador de Backup](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Digite o caminho para o **local de armazenamento de Backup**. Use uma cadeia de caracteres de convenção de nomenclatura Universal (UNC) para o caminho para um compartilhamento de arquivo hospedado em um dispositivo separado. Uma cadeia de caracteres UNC Especifica o local de recursos, como arquivos compartilhados ou dispositivos. Para o serviço, você pode usar um endereço IP. Para garantir a disponibilidade dos dados de backup após um desastre, o dispositivo deve estar em um local separado.
    > [!Note]  
    > Se seu ambiente oferece suporte à resolução de nome de rede de infraestrutura de pilha do Azure para seu ambiente empresarial, você pode usar um FQDN em vez de IP.
4. Tipo de **Username** usando o nome de usuário e domínio. Por exemplo, `Contoso\administrator`.
5. Tipo de **senha** para o usuário.
5. Digite a senha novamente para **Confirmar senha**.
6. Fornecer uma chave pré-compartilhada no **chave de criptografia** caixa. Arquivos de backup são criptografados usando essa chave. Certifique-se de armazenar essa chave em um local seguro. Depois que você definir essa chave pela primeira vez ou gira a chave no futuro, você não pode exibir essa chave desta interface. Para obter mais instruções gerar uma chave pré-compartilhada, execute os scripts em [habilitar o Backup para a pilha do Azure com o PowerShell](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key). 
7. Selecione **Okey** para salvar suas configurações de backup do controlador.

Para executar um backup, você precisa baixar as ferramentas de pilha do Azure e, em seguida, execute o cmdlet PowerShell **AzSBackup início** no seu nó de administração de pilha do Azure. Para obter mais informações, consulte [Azure pilha](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>Próximas etapas

 - Saiba como executar um backup. Consulte [Azure pilha](azure-stack-backup-back-up-azure-stack.md ).
- Saiba como verificar se o backup foi executado. Consulte [Confirmar backup concluído no portal de administração](azure-stack-backup-back-up-azure-stack.md ).
