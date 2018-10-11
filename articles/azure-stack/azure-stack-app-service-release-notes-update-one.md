---
title: Serviço de aplicativo no Azure Stack update notas de versão 1 | Microsoft Docs
description: Saiba mais sobre as novidades na atualização para o serviço de aplicativo no Azure Stack, os problemas conhecidos e onde baixar a atualização.
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
ms.reviewer: sethm
ms.openlocfilehash: ee6e4397345b4cb169e7e22d951d4c4fdff5b7b7
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078708"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Serviço de aplicativo no Azure Stack update notas de versão 1

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Essas notas de versão descrevem os aperfeiçoamentos e correções no serviço de aplicativo do Azure no Azure Stack Update 1 e todos os problemas conhecidos. Problemas conhecidos são divididos em questões relacionadas diretamente para a implantação, o processo de atualização e a problemas com a compilação (após a instalação).

> [!IMPORTANT]
> Aplicar a atualização 1802 para o seu sistema integrado do Azure Stack ou implantar o kit de desenvolvimento mais recente do Azure Stack antes de implantar o serviço de aplicativo do Azure.
>
>

## <a name="build-reference"></a>Referência de build

O serviço de aplicativo no Azure Stack Update 1 número de build é **69.0.13698.9**

### <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Novas implantações do serviço de aplicativo do Azure no Azure Stack agora exigem uma [certificado curinga três assunto](azure-stack-app-service-before-you-get-started.md#get-certificates) devido a melhorias da maneira em que o SSO para o Kudu agora é tratado no serviço de aplicativo do Azure. É o novo assunto  **\*. sso.appservice.\< região\>.\< DomainName\>.\< extensão\>**
>
>

Consulte a [documentação antes de você começar a usar](azure-stack-app-service-before-you-get-started.md) antes de iniciar a implantação.

### <a name="new-features-and-fixes"></a>Novos recursos e correções

Serviço de aplicativo do Azure no Azure Stack Update 1 inclui os seguintes aprimoramentos e correções:

- **Alta disponibilidade do serviço de aplicativo Azure** -1802 de pilha do Azure a cargas de trabalho de atualização habilitada para ser implantada em domínios de falha. Portanto, infraestrutura de serviço de aplicativo é capaz de ser tolerante a falhas, pois ela será implantada em domínios de falha. Por padrão, todas as novas implantações do serviço de aplicativo do Azure tem essa funcionalidade no entanto, para implantações concluídas antes do Azure Stack 1802 atualização aplicada consulte o [documentação do domínio de falha do serviço de aplicativo](azure-stack-app-service-fault-domain-update.md)

- **Implantar na rede virtual existente** -os clientes agora podem implantar o serviço de aplicativo no Azure Stack em uma rede virtual existente. Implantando em uma rede virtual existente permite que os clientes para se conectar ao SQL Server e do servidor de arquivos, necessárias para o serviço de aplicativo do Azure, sobre portas privadas. Durante a implantação, os clientes podem selecionar para implantar em uma rede virtual existente, no entanto [deve criar sub-redes para uso pelo serviço de aplicativo](azure-stack-app-service-before-you-get-started.md#virtual-network) antes da implantação.

- As atualizações **aplicativo de locatário de serviço, Admin, portais de funções e ferramentas de Kudu**. Consistente com a versão do SDK do Portal do Azure Stack.

- **Atualizações para os seguintes estruturas de aplicativo e as ferramentas**:
    - Adicionado **.Net Core 2.0** suporte
    - Adicionado **Node. js** versões:
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

  - Adicionado suporte para **somente HTTPS** recurso dentro do recurso de domínio personalizado no Portal de locatário de serviço de aplicativo. 

  - Validação adicional de conexão de armazenamento no seletor de armazenamento personalizado para o Azure Functions 

#### <a name="fixes"></a>Correções

- Ao criar um pacote de implantação offline, os clientes não receberão mais um acesso negado a mensagem de erro ao abrir a pasta do instalador do serviço de aplicativo

- Problemas resolvidos ao trabalhar no recurso de domínios personalizados no Portal de locatário de serviço de aplicativo.

- Impedir que os clientes que usam nomes reservados do administrador durante a instalação

- Habilitado a implantação com o serviço de aplicativo **ingressado no domínio** servidor de arquivos

- Recuperação aprimorada da raiz do Azure Stack de certificado no script e agora validar o certificado raiz no instalador do serviço de aplicativo.

- Status fixo incorreto, que está sendo retornado para o Azure Resource Manager quando uma assinatura é excluída que os recursos contidos no namespace Microsoft. Web.

### <a name="known-issues-with-the-deployment-process"></a>Problemas conhecidos com o processo de implantação

- Erros de validação de certificado

Alguns clientes encontraram problemas ao fornecer certificados para o instalador do serviço de aplicativo ao implantar em um sistema integrado, devido à validação exageradamente restritiva no instalador. O instalador do serviço de aplicativo tiver sido lançado novamente, os clientes devem [baixar o instalador atualizado](https://aka.ms/appsvconmasinstaller). Se você continuar a ter problemas de validação de certificados com o instalador atualizado, contate o suporte.

- Problema ao recuperar o certificado do Azure Stack raiz do sistema integrado.

Um erro em Get-AzureStackRootCert.ps1 fez com que os clientes não conseguir recuperar o certificado raiz do Azure Stack ao executar o script em um computador que não tem o certificado raiz instalado. O script agora também foi relançado resolver esse problema e os clientes de solicitação [baixar os scripts de Ajuda atualizados](https://aka.ms/appsvconmashelpers). Se você continuar a ter problemas ao recuperar o certificado raiz com o script atualizado, contate o suporte.

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Não há nenhum problema conhecido para a atualização do serviço de aplicativo do Azure no Azure Stack Update 1.

### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

- Troca de slot não funciona

Troca de slot de site é dividida nesta versão. Para restaurar a funcionalidade, conclua estas etapas:

1. Modificar grupo de segurança de rede ControllersNSG para **permitir** conexões de área de trabalho remota para as instâncias do controlador de serviço de aplicativo. Substitua AppService.local com o nome do grupo de recursos que você implantou o serviço de aplicativo.

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

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

2. Navegue até a **CN0-VM** em máquinas virtuais no portal do administrador do Azure Stack e **clique em conectar** para abrir uma sessão de área de trabalho remota com a instância do controlador. Use as credenciais especificadas durante a implantação do serviço de aplicativo.
3. Inicie **PowerShell como administrador** e execute o seguinte script

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
5. Reverter o grupo de segurança de rede ControllersNSG para **Deny** conexões de área de trabalho remota para as instâncias do controlador de serviço de aplicativo. Substitua AppService.local com o nome do grupo de recursos que você implantou o serviço de aplicativo.

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

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
- Trabalhadores são incapazes de alcançar o servidor de arquivos quando o serviço de aplicativo é implantado em uma rede virtual existente e o servidor de arquivos só está disponível na rede privada.
 
Se você optar por implantar em uma rede virtual existente e um endereço IP interno para se conectar ao seu servidor de arquivos, você deve adicionar uma regra de segurança de saída, permitindo o tráfego entre a sub-rede de trabalho e o servidor de arquivos SMB. Para fazer isso, vá para o WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:
 * Fonte: qualquer
 * Intervalo de porta de origem: *
 * Destino: Endereços IP
 * Intervalo de endereços IP de destino: intervalo de IPs para seu servidor de arquivos
 * Intervalo de porta de destino: 445
 * Protocolo: TCP
 * Ação: permitir
 * Prioridade: 700
 * Nome: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conhecidos para administradores de nuvem, a operação de serviço de aplicativo do Azure no Azure Stack

Consulte a documentação no [notas da versão 1802 de pilha do Azure](azure-stack-update-1802.md)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do serviço de aplicativo do Azure, consulte [serviço de aplicativo do Azure na visão geral do Azure Stack](azure-stack-app-service-overview.md).
- Para obter mais informações sobre como se preparar implantar o serviço de aplicativo no Azure Stack, consulte [antes de começar com o serviço de aplicativo no Azure Stack](azure-stack-app-service-before-you-get-started.md).
