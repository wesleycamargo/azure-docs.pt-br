---
title: Reimplantar o Kit de desenvolvimento de pilha do Azure (ASDK) | Microsoft Docs
description: Neste artigo, você aprenderá como reinstalar o ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 91b8a936215e906e6e5b7e6a4fcd0dc88bef6009
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850313"
---
# <a name="redeploy-the-asdk"></a>Reimplantar o ASDK
Neste artigo, você aprenderá como reimplantar o Azure pilha Development Kit (ASDK) em um ambiente de não produção. Como atualizar o ASDK não tem suporte, você precisa completamente reimplantá-lo para mover para uma versão mais recente. Também é possível reimplantar o ASDK a qualquer momento que você apenas deseja começar do zero.

> [!IMPORTANT]
> Atualizar o ASDK para uma nova versão não tem suporte. Você precisa reimplantar o ASDK no computador de host do kit de desenvolvimento cada vez que você deseja avaliar uma versão mais recente da pilha do Azure.

## <a name="remove-azure-registration"></a>Remover o registro do Azure 
Se você já registrou sua instalação ASDK com o Azure, você deve remover o recurso de registro antes de reimplantar o ASDK. Registre novamente o ASDK para habilitar a distribuição de mercado ao reimplantar o ASDK. Se você não registrou anteriormente o ASDK com sua assinatura do Azure, você poderá ignorar esta seção.

Para remover o recurso de registro, use o **AzsRegistration remover** cmdlet para cancelar o registro de pilha do Azure. Em seguida, use o **AzureRMRsourceGroup remover** cmdlet para excluir o grupo de recursos da pilha do Azure da sua assinatura do Azure:

1. Abra um console do PowerShell como administrador em um computador que tem acesso ao ponto de extremidade com privilégios. Para ASDK, que é o computador de host do kit de desenvolvimento.

2. Execute os seguintes comandos do PowerShell para cancelar o registro de sua instalação ASDK e excluir o **azurestack** grupo de recursos da sua assinatura do Azure:

  ```Powershell    
  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Provide Azure subscription admin credentials
  Add-AzureRmAccount

  # Provide ASDK admin credentials
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

  # Unregister Azure Stack
  Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01

  # Remove the Azure Stack resource group
  Remove-AzureRmResourceGroup -Name azurestack -Force
  ```

3. Você precisará entrar sua assinatura do Azure e a instalação local do ASDK quando o script é executado.
4. Quando o script for concluído, você verá mensagens semelhantes aos seguintes exemplos:

    ` De-Activating Azure Stack (this may take up to 10 minutes to complete).` ` Your environment is now unable to syndicate items and is no longer reporting usage data.` ` Remove registration resource from Azure...` ` "Deleting the resource..." on target "/subscriptions/<subscription information>"` ` ********** End Log: Remove-AzsRegistration ********* `



A pilha do Azure agora deve ser cancelada de sua assinatura do Azure com êxito. Além disso, o grupo de recursos azurestack, criado quando você registrou o ASDK com o Azure, também deve ser excluído.

## <a name="deploy-the-asdk"></a>Implantar o ASDK
Para reimplantar a pilha do Azure, você deve começar do zero conforme descrito abaixo. As etapas são diferentes dependendo se você usou o script de instalador (asdk installer.ps1) de pilha do Azure para instalar o ASDK.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Reimplantar o ASDK usando o instalador script
1. No computador ASDK, abra um console do PowerShell com privilégios elevados e navegue até o script asdk installer.ps1 o **AzureStack_Installer** diretório localizado em uma unidade não são do sistema. Execute o script e clique em **reinicializar**.

   ![Execute o script asdk installer.ps1](media/asdk-redeploy/1.png)

2. Selecione o sistema operacional base (não **Azure pilha**) e clique em **próximo**.

   ![Reinicie o sistema operacional do host](media/asdk-redeploy/2.png)

3. Após a reinicialização do host do kit de desenvolvimento para o sistema operacional base, logon como um administrador local. Localize e exclua o **C:\CloudBuilder.vhdx** arquivo que foi usado como parte da implantação anterior. 

4. Repita as mesmas etapas que você colocou a primeira [implantar o ASDK](asdk-install.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Reimplantar o ASDK sem usar o instalador
Se você não usou o script asdk installer.ps1 para instalar o ASDK, você deve reconfigurar manualmente o computador de host do kit de desenvolvimento antes de reimplantar o ASDK.

1. Inicie o utilitário de configuração do sistema executando **msconfig.exe** no computador ASDK. Sobre o **inicialização** , selecione o sistema operacional do computador host (não pilha do Azure), clique em **definido como padrão**e, em seguida, clique em **Okey**. Clique em **reiniciar** quando solicitado.

      ![Defina a configuração de inicialização](media/asdk-redeploy/4.png)

2. Após a reinicialização do host do kit de desenvolvimento para o sistema operacional base, faça logon como um administrador local para o computador de host do kit de desenvolvimento. Localize e exclua o **C:\CloudBuilder.vhdx** arquivo que foi usado como parte da implantação anterior. 

3. Repita as mesmas etapas que você colocou a primeira [implantar o ASDK usando o PowerShell](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Próximas etapas
[Pós-instalação de ASDK tarefas de configuração](asdk-post-deploy.md)




