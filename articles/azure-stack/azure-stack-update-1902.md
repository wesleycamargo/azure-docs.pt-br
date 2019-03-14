---
title: Atualização de 1902 de pilha do Azure | Microsoft Docs
description: Saiba mais sobre a atualização de 1902 para sistemas integrados do Azure Stack, incluindo novidades, problemas conhecidos e onde baixar a atualização.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 66dfdf3a88a4bacdc118fed00d79f02b22da7869
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792456"
---
# <a name="azure-stack-1902-update"></a>Atualização de 1902 de pilha do Azure

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo descreve o conteúdo do pacote de atualização de 1902. A atualização inclui aprimoramentos, correções e novos recursos para esta versão do Azure Stack. Este artigo também descreve problemas conhecidos nesta versão e inclui um link para baixar a atualização. Problemas conhecidos são divididos em problemas diretamente relacionados ao processo de atualização e os problemas com a compilação (após a instalação).

> [!IMPORTANT]  
> Este pacote de atualização é somente para os sistemas integrados do Azure Stack. Não aplique esse pacote de atualização para o Kit de desenvolvimento do Azure Stack.

## <a name="build-reference"></a>Referência de build

É o número de compilação de atualização do Azure Stack 1902 **1.1902.0.69**.

## <a name="hotfixes"></a>Hotfixes

O Azure Stack libera hotfixes regularmente. Certifique-se de instalar o [hotfix mais recente do Azure Stack](#azure-stack-hotfixes) para 1901 antes de atualizar o Azure Stack para 1902.

Os hotfixes de pilha do Azure são aplicáveis apenas aos sistemas integrados do Azure Stack. Não tente instalar os hotfixes a ASDK.

> [!TIP]  
> Inscrever-se ao seguinte *RSS* ou *Atom* feeds manter-se atualizado com hotfixes do Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Hotfixes de pilha do Azure

- **1809**: [KB 4481548 – o Azure Stack hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Nenhum hotfix atual disponível.
- **1901**: Nenhum hotfix atual disponível.
- **1902**: Nenhum hotfix atual disponível.

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
- Instalar o [hotfix mais recente do Azure Stack](#azure-stack-hotfixes) para 1901 (se houver) antes de atualizar para 1902.

- Antes de iniciar a instalação dessa atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com os seguintes parâmetros para validar o status do Azure Stack e resolva os problemas operacionais encontrados, incluindo todos os avisos e falhas. Também examine os alertas ativos e resolver todos os que exigem ação:

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Quando o Azure Stack é gerenciado pelo SCOM System Center Operations Manager (), certifique-se de atualizar o [pacote de gerenciamento para Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) para versão 1.0.3.11 antes da aplicação de 1902.

- O formato de pacote para a atualização do Azure Stack foi alterado de **.bin/.exe/.xml** à **.zip/.xml** começando com a versão de 1902. Os clientes com unidades de escala do Azure Stack conectados verá o **atualização disponível** mensagem no portal. Os clientes que não estão conectados podem agora basta baixar e importar o arquivo. zip com o. XML correspondente.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>Melhorias

- A compilação de 1902 introduz uma nova interface de usuário no portal do administrador de pilha do Azure para a criação de planos, ofertas, cotas e planos de complemento. Para obter mais informações, incluindo capturas de tela, consulte [criar planos, ofertas e cotas](azure-stack-create-plan.md).

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- Para melhorar a segurança e integridade do pacote, bem como gerenciamento mais fácil para ingestão off-line, Microsoft alterou o formato do pacote de atualização de arquivos. .exe e. bin para um arquivo. zip. O novo formato adiciona confiabilidade adicional do processo de descompactação que às vezes, pode fazer com que a preparação da atualização paralisará. O mesmo formato de pacote também se aplica para atualizar pacotes do seu OEM.
- Para melhorar a experiência do operador do Azure Stack durante a execução de teste AzureStack, operadores agora podem simplesmente usar, "Test-AzureStack-UpdateReadiness de grupo" em vez de passar parâmetros adicionais de dez após uma instrução de inclusão.

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- Para melhorar a confiabilidade e a disponibilidade dos serviços de infraestrutura de núcleo geral durante o processo de atualização, o provedor de recursos de atualização nativo como parte do plano de ação de atualização detectará e invocar as correções globais automática conforme necessário. Fluxos de trabalho de "Reparar" correção globais incluem:
    - Procurando máquinas virtuais de infraestrutura que estiverem em um estado não ideal e tentar repará-los conforme necessário 
    - Verifique se há problemas de serviço do SQL como parte do plano de controle e tentar repará-los conforme necessário
    - Verifique o estado do serviço balanceador de carga de Software (SLB) como parte do controlador de rede (NC) e tentar repará-lo conforme necessário
    - Verifique o estado do serviço de controlador de rede (NC) e tentar repará-la conforme necessário
    - Verifique o estado de nós de malha do serviço de Console de recuperação de emergência (ERCS) service e repará-los conforme necessário
    - Verifique o estado de nós do service fabric XRP e repará-los conforme necessário
    - Verifique o estado de nós de armazenamento consistente (ACS) do Azure service fabric e repará-los conforme necessário



## <a name="common-vulnerabilities-and-exposures"></a>Vulnerabilidades e exposições comuns

Esta atualização instala as atualizações de segurança a seguir:  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)


Para obter mais informações sobre essas vulnerabilidades, clique nos links acima ou consulte os artigos da Base de dados de Conhecimento da Microsoft [4487006](https://support.microsoft.com/en-us/help/4487006).

## <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Quando você executa [AzureStack teste](azure-stack-diagnostic-test.md), será exibida uma mensagem de aviso do Baseboard Management Controller (BMC). Você pode ignorar com segurança esse aviso.

- <!-- 2468613 - IS --> Durante a instalação dessa atualização, você pode ver os alertas com o título `Error – Template for FaultType UserAccounts.New is missing.` você pode ignorar com segurança esses alertas. Os alertas fecham automaticamente após a instalação dessa atualização.

## <a name="post-update-steps"></a>Etapas de pós-atualização

- Após a instalação dessa atualização, instale os hotfixes aplicáveis. Para obter mais informações, consulte [Hotfixes](#hotfixes), bem como nosso [política de manutenção](azure-stack-servicing-policy.md).  

- Recuperar os dados em chaves de criptografia do rest e armazene-os com segurança fora da sua implantação do Azure Stack. Siga as [obter instruções sobre como recuperar as chaves](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

A seguir estão os problemas conhecidos de pós-instalação para esta versão de compilação.

### <a name="portal"></a>Portal

<!-- 2930820 - IS ASDK --> 
- Em portais de administrador e o usuário, se você procurar por "Docker", o item incorretamente é retornado. Ele não está disponível no Azure Stack. Se você tentar criá-la, uma folha com uma indicação de erro é exibida. 

<!-- 2931230 – IS  ASDK --> 
- Os planos são adicionados a uma assinatura de usuário como um plano de complemento não podem ser excluídos, mesmo quando você remove o plano da assinatura do usuário. O plano permanecerá até que as assinaturas que o plano de complemento de referência também são excluídas. 

<!-- TBD - IS ASDK --> 
- Os dois tipos de assinatura administrativos que foram introduzidos com a versão 1804 não devem ser usados. Os tipos de assinatura são **assinatura de medição**, e **assinatura de consumo**. Esses tipos de assinatura são visíveis em novos ambientes Azure Stack, começando com a versão 1804, mas ainda não estão prontos para uso. Você deve continuar a usar o **provedor padrão** tipo de assinatura.

<!-- 3557860 - IS ASDK --> 
- Excluindo resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos de usuário ou grupo de recursos inteiro e, em seguida, exclua as assinaturas de usuário.

<!-- 1663805 - IS ASDK --> 
- Você não pode exibir as permissões para sua assinatura usando os portais do Azure Stack. Como alternativa, use [PowerShell para verificar permissões](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- ### Health and monitoring -->

### <a name="compute"></a>Computação

- Ao criar um novo Windows VM (Máquina Virtual), o seguinte erro pode ser exibido:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   O erro ocorre se você habilitar o diagnóstico de inicialização em uma VM, mas excluir sua conta de armazenamento de diagnóstico de inicialização. Para contornar esse problema, recrie a conta de armazenamento com o mesmo nome que você usou anteriormente.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- A experiência de criação de conjunto (VMSS) de escala de máquina virtual oferece a opção para implantação baseada em CentOS 7.2. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para sua implantação, ou usar um modelo do Azure Resource Manager especificando outra imagem do CentOS que tiver sido baixada antes da implantação no marketplace, o operador.  

<!-- TBD - IS ASDK --> 
- Depois de aplicar o 1902 de atualização, você pode encontrar os seguintes problemas ao implantar VMs com discos gerenciados:

   - Se a assinatura foi criada antes da atualização 1808, implantando uma VM com discos gerenciados pode falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas para cada assinatura:
      1. No portal do locatário, vá para **assinaturas** e localize a assinatura. Selecione **provedores de recursos**, em seguida, selecione **Microsoft. Compute**e, em seguida, clique em **registrar novamente**.
      2. Sob a mesma assinatura, vá para **controle de acesso (IAM)**, verifique se **do Azure Stack – Managed Disk** está listado.
   - Se você tiver configurado um ambiente multilocatário, implantar as VMs em uma assinatura associada a um diretório de convidado pode falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas no [deste artigo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) para reconfigurar a cada um dos seus diretórios de convidado.

- Uma VM do Ubuntu 18.04 criado com autorização SSH habilitada não permitirá que você use as chaves SSH para fazer logon no. Como alternativa, use o acesso de VM para a extensão do Linux para implementar as chaves SSH após o provisionamento ou usar a autenticação baseada em senha.

- Crie 1902, a memória exigida pela infraestrutura ERCS VM foi aumentada de 8 GB para 12 GB. Em um ASDK, isso resulta em um aumento de 4 GB. Em uma instalação de sistemas integrados do Azure Stack, ele é um aumento de 12 GB.

   Para garantir que o processo de patch e atualização resulta em menor quantidade de tempo de inatividade de locatário, verifique se seu carimbo de data / Azure Stack tem mais de 12 GB de espaço disponível na **capacidade** folha. Você pode ver essa memória aumentar refletido na **capacidade** folha após uma instalação bem-sucedida da atualização.

### <a name="networking"></a>Rede  

<!-- 3239127 - IS, ASDK -->
- No portal do Azure Stack, quando você altera um endereço IP estático para uma configuração de IP que está associado a um adaptador de rede anexado a uma instância VM, você verá uma mensagem de aviso dizendo que 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Você pode ignorar com segurança essa mensagem; o endereço IP será alterado, mesmo se a instância de VM não reinicia.

<!-- 3632798 - IS, ASDK -->
- No portal, se você adicionar uma regra de segurança de entrada e selecione **marca de serviço** como a origem, várias opções são exibidas na **marca de origem** lista que não estão disponíveis para o Azure Stack. As únicas opções que são válidas no Azure Stack são da seguinte maneira:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  Não há suporte para as outras opções como marcas de origem no Azure Stack. Da mesma forma, se você adicionar uma regra de segurança de saída e selecione **marca de serviço** como o destino, a mesma lista de opções para **marca de origem** é exibida. As únicas opções válidas são os mesmos para **marca de origem**, conforme descrito na lista anterior.

- Grupos de segurança de rede (NSGs) não funcionam no Azure Stack no Azure mesma forma como global. No Azure, você pode definir várias portas em uma regra NSG (usando o portal, PowerShell e modelos do Resource Manager). No entanto, no Azure Stack é possível definir várias portas em uma regra NSG por meio do portal. Para contornar esse problema, use um modelo do Resource Manager ou o PowerShell para definir essas regras adicionais.

<!-- 3203799 - IS, ASDK -->
- O Azure Stack não dá suporte a anexar mais de 4 adaptadores de rede (NICs) a uma instâncias de VM hoje em dia, independentemente do tamanho da instância.

- Foi identificado um problema no qual os pacotes mais 1450 bytes para um balanceador de carga interno (ILB) são descartados. O problema é devido à configuração de MTU no host que está sendo muito baixas para acomodar os pacotes VXLAN encapsulada que atravessam a função, que foi movida para o host a partir de 1901. Há pelo menos dois cenários que você pode encontrar em que vimos esse problema se manifestar:

  - Consultas SQL para SQL AlwaysOn que está por trás de um balanceador de carga interno (ILB) e são mais de 660 bytes.
  - Implantações de Kubernetes falhar se você tentar habilitar vários mestres.  

  O problema ocorre quando você tem a comunicação entre uma máquina virtual e um ILB na mesma rede virtual, mas em sub-redes diferentes. Você pode contornar esse problema, executando os seguintes comandos no prompt de comandos com privilégios elevados no host ASDK:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>Serviço de Aplicativo

<!-- 2352906 - IS ASDK --> 
- Você deve registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>Baixe a atualização

Você pode baixar o pacote de atualização no Azure Stack 1902 [aqui](https://aka.ms/azurestackupdatedownload). 

Em cenários conectados apenas, as implantações do Azure Stack verificar periodicamente um ponto de extremidade seguro em notificá-lo automaticamente se uma atualização está disponível para a sua nuvem. Para obter mais informações, consulte [Gerenciando atualizações para o Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Próximas etapas

- Para uma visão geral do gerenciamento de atualizações no Azure Stack, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md).  
- Para obter mais informações sobre como aplicar atualizações com o Azure Stack, consulte [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).
- Para revisar a política de manutenção para sistemas integrados do Azure Stack e o que você deve fazer para manter seu sistema em um estado com suporte, consulte [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).  
- Para usar o ponto de extremidade com privilégios (PEP) para monitorar e retomar as atualizações, consulte [monitorar as atualizações no Azure Stack usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).  
