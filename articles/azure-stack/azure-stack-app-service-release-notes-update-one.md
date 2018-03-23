---
title: Serviço de aplicativo no Azure pilha update 1 notas | Microsoft Docs
description: Saiba mais sobre novidades da atualização para o serviço de aplicativo na pilha do Azure, os problemas conhecidos e onde baixar a atualização.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 538d31f5b50ee22c06ba22c78e1aa92281a3b212
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Serviço de aplicativo no Azure pilha update 1 notas

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Essas notas de versão descrevem as melhorias e correções no serviço de aplicativo do Azure no Azure pilha atualização 1 e todos os problemas conhecidos. Problemas conhecidos são divididos em problemas relacionados diretamente para a implantação, o processo de atualização e a problemas com a compilação (após a instalação).

> [!IMPORTANT]
> Aplicar a atualização 1802 para seu sistema de pilha do Azure integradas ou implantar o kit de desenvolvimento de pilha do Azure mais recente antes de implantar o serviço de aplicativo do Azure.
>
>

## <a name="build-reference"></a>Referência de build

O serviço de aplicativo no Azure pilha Update 1 número de compilação está **69.0.13698.9**

### <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Novas implantações de serviço de aplicativo do Azure na pilha do Azure agora precisam de um [certificado curinga com três entidades](azure-stack-app-service-before-you-get-started.md#get-certificates) devido a melhorias da maneira na qual o SSO para Kudu agora é manipulado no serviço de aplicativo do Azure.  A nova entidade é * * *. sso.appservice.<region>. <domainname>.<extension>**
>
>

Consulte o [documentação antes de você começar](azure-stack-app-service-before-you-get-started.md) antes de iniciar a implantação.

### <a name="new-features-and-fixes"></a>Novos recursos e correções

Serviço de aplicativo do Azure no Azure pilha Update 1 inclui os seguintes aprimoramentos e correções:

- **Alta disponibilidade do serviço de aplicativo Azure** -1802 de pilha do Azure a cargas de trabalho de atualização habilitada para ser implantada em domínios de falha.  Portanto infraestrutura de serviço de aplicativo é capaz de ser tolerante a falhas, como ele será implantado em domínios de falha.  Por padrão todas as novas implantações de serviço de aplicativo do Azure terá esse recurso no entanto, para implantações concluídas antes do Azure pilha 1802 atualização sendo aplicada, consulte o [documentação do domínio de falha do serviço de aplicativo](azure-stack-app-service-fault-domain-update.md)

- **Implantar na rede virtual existente** -os clientes agora podem implantar o serviço de aplicativo na pilha do Azure dentro de uma rede virtual existente.  Implantando em uma rede virtual existente permite que os clientes se conectem ao SQL Server e do servidor de arquivos, necessárias para o serviço de aplicativo do Azure, sobre portas privadas.  Durante a implantação, os clientes podem selecionar para implantar em uma rede virtual existente, no entanto [devem criar sub-redes para uso pelo serviço de aplicativo](azure-stack-app-service-before-you-get-started.md#virtual-network) antes da implantação.

- Atualiza a **locatário de serviço de aplicativo, Admin, portais de funções e ferramentas de Kudu**.  Consistente com a versão do SDK do Azure pilha Portal.

- **Atualizações para os seguintes estruturas de aplicativo e as ferramentas**:
    - Adicionado **.Net Core 2.0** suporte
    - Adicionado **Node.JS** versões:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Adicionado **NPM** versões:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Adicionado **PHP** atualizações:
        - 5.6.32
        - 7.0.26 (x86 e x64)
        - 7.1.12 (x86 e x64)
    - Atualizado **Git para Windows** para v 2.14.1
    - Atualizado **Mercurial** para v4.5.0

  - Adicionado suporte para **HTTPS apenas** recurso dentro do recurso de domínio personalizado no Portal de locatário de serviço de aplicativo. 

  - Validação adicional da conexão de armazenamento no seletor de armazenamento personalizado para funções do Azure 

#### <a name="fixes"></a>Correções

- Ao criar um pacote de implantação offline, os clientes não receberão um acesso negado ao abrir a pasta do instalador do serviço de aplicativo

- Resolver problemas ao trabalhar no recurso de domínios personalizados no Portal de locatário de serviço de aplicativo.

- Impedir que os clientes que usam nomes reservados administrador durante a instalação

- Habilitada a implantação do serviço de aplicativo com **ingressado no domínio** servidor de arquivos

- Recuperação aprimorada de raiz de pilha do Azure no script de certificado e agora validar o certificado raiz no instalador do serviço de aplicativo.

- Fixado status incorreto retornado para o Azure Resource Manager quando uma assinatura é excluída que os recursos contidos no namespace Microsoft.

### <a name="known-issues-with-the-deployment-process"></a>Problemas conhecidos com o processo de implantação

- Erros de validação de certificado

Alguns clientes encontraram problemas ao fornecer certificados para o instalador do serviço de aplicativo ao implantar em um sistema integrado, devido à validação excessivamente restritiva no instalador.  O instalador do serviço de aplicativo foi lançado novamente, os clientes devem [baixar o instalador atualizado](https://aka.ms/appsvconmasinstaller).  Se você continuar a ter problemas de validação de certificados com o instalador atualizado, contate o suporte.

- Problema ao recuperar o certificado de raiz de pilha do Azure de sistema integrado.

Um erro em Get-AzureStackRootCert.ps1 causou clientes Falha ao recuperar o certificado de raiz de pilha do Azure ao executar o script em um computador que não tenha o certificado raiz instalado.  O script agora também foi lançado novamente, resolver esse problema e os clientes de solicitação [Baixe os scripts de auxiliar atualizado](https://aka.ms/appsvconmashelpers).  Se você continuar a ter problemas ao recuperar o certificado raiz ao script atualizado, contate o suporte.

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Não há nenhum problema conhecido para a atualização do serviço de aplicativo do Azure no Azure pilha atualização 1.

### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

- Troca de slot não funciona

Troca de slot de site é dividida nesta versão.  Para restaurar a funcionalidade, conclua estas etapas:

1. Modificar o grupo de segurança de rede ControllersNSG para **permitir** conexões de área de trabalho remota para as instâncias de controlador de serviço de aplicativo.  Substitua AppService.local com o nome do grupo de recursos que você implantou o aplicativo de serviço no.

    ```powershell
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

2. Navegue até o **CN0 VM** em máquinas virtuais no portal do administrador de pilha do Azure e **clique em conectar** para abrir uma sessão de área de trabalho remota com a instância do controlador.  Use as credenciais especificadas durante a implantação do serviço de aplicativo.
3. Iniciar **PowerShell como administrador** e execute o script a seguir

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. Feche a sessão de área de trabalho remota.
5. Reverter o grupo de segurança de rede ControllersNSG para **Deny** conexões de área de trabalho remota para as instâncias de controlador de serviço de aplicativo.  Substitua AppService.local com o nome do grupo de recursos que você implantou o aplicativo de serviço no.

    ```powershell

        Login-AzureRMAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conhecidos para que os administradores de nuvem operacional do serviço de aplicativo do Azure na pilha do Azure

Consulte a documentação do [Azure pilha 1802 notas de versão](azure-stack-update-1802.md)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do serviço de aplicativo do Azure, consulte [do serviço de aplicativo do Azure na visão geral do Azure pilha](azure-stack-app-service-overview.md).
- Para obter mais informações sobre como preparar a implantação do serviço de aplicativo na pilha do Azure, consulte [antes de iniciar o serviço de aplicativo no Azure pilha](azure-stack-app-service-before-you-get-started.md).
