---
title: Atualização da pilha 1901 do Azure | Microsoft Docs
description: Saiba mais sobre a atualização de 1901 para sistemas integrados do Azure Stack, incluindo novidades, problemas conhecidos e onde baixar a atualização.
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
ms.date: 02/11/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 0865227a05d5cb25a44114fc053f61b8f70ffb94
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100084"
---
# <a name="azure-stack-1901-update"></a>Atualização da pilha 1901 do Azure

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo descreve o conteúdo do pacote de atualização de 1901. A atualização inclui aprimoramentos, correções e novos recursos para esta versão do Azure Stack. Este artigo também descreve problemas conhecidos nesta versão e inclui um link para baixar a atualização. Problemas conhecidos são divididos em problemas diretamente relacionados ao processo de atualização e os problemas com a compilação (após a instalação).

> [!IMPORTANT]  
> Este pacote de atualização é somente para os sistemas integrados do Azure Stack. Não aplique esse pacote de atualização para o Kit de desenvolvimento do Azure Stack.

## <a name="build-reference"></a>Referência de build

É o número de compilação de atualização do Azure Stack 1901 **1.1901.0.95**.

## <a name="hotfixes"></a>Hotfixes

O Azure Stack libera hotfixes regularmente. Certifique-se de instalar o [hotfix mais recente do Azure Stack](#azure-stack-hotfixes) para 1811 antes de atualizar o Azure Stack para 1901.

Os hotfixes de pilha do Azure são aplicáveis apenas aos sistemas integrados do Azure Stack. Não tente instalar os hotfixes a ASDK.

> [!TIP]  
> Inscrever-se ao seguinte *RSS* ou *Atom* feeds manter-se atualizado com hotfixes do Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Hotfixes de pilha do Azure

- **1809**: [KB 4481548 – o Azure Stack hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Nenhum hotfix atual disponível.
- **1901**: Nenhum hotfix atual disponível.

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
- Instalar o [hotfix mais recente do Azure Stack](#azure-stack-hotfixes) para 1811 (se houver) antes de atualizar para 1901.

- Antes de iniciar a instalação dessa atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com os seguintes parâmetros para validar o status do Azure Stack e resolva os problemas operacionais encontrados, incluindo todos os avisos e falhas. Também examine os alertas ativos e resolver todos os que exigem ação:

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

## <a name="new-features"></a>Novos recursos

Esta atualização inclui os seguintes novos recursos e melhorias para o Azure Stack:

- Imagens gerenciadas em ativação do Azure Stack de criar um objeto de imagem gerenciada em uma VM generalizada (ambos não gerenciados e gerenciados) que só pode criar gerenciada disco VMs no futuro. Para obter mais informações, consulte [Managed Disks do Azure Stack](user/azure-stack-managed-disk-considerations.md#managed-images).

- **AzureRm 2.4.0**
   * **AzureRm.Profile**  
         Correção de bug – `Import-AzureRmContext` para desserializar o token salvo corretamente.  
   * **AzureRm.Resources**  
         Correção de bug – `Get-AzureRmResource` ao caso de consulta e minúsculas por tipo de recurso.  
   * **Azure.Storage**  
         Módulo do AzureRm pacote cumulativo de atualizações agora inclui a versão já publicada 4.5.0 que dão suporte à **api-version 2017-07-29**.  
   * **AzureRm.Storage**  
         Módulo do AzureRm pacote cumulativo de atualizações agora inclui a versão já publicada 5.0.4 com suporte a **2017-10-01 de versão de api**.  
   * **AzureRm.Compute**  
         Adicionado o parâmetro simple define `New-AzureRMVM` e `NewAzureRMVMSS`, `-ImageName` parâmetro dá suporte à especificação de imagens de usuário.  
   * **AzureRm.Insights**  
         Módulo do AzureRm pacote cumulativo de atualizações agora inclui a versão já publicada 5.1.5 com suporte a **api-version 2018-01-01** para métricas, tipos de recursos de definições de métrica.

- **AzureStack 1.7.0** esta versão uma alteração significativa. Para obter detalhes sobre as alterações significativas, consulte https://aka.ms/azspshmigration170
   * **Módulo Azs.Backup.Admin**  
         Alterações da falha: Alterações de backup para o modo de criptografia baseada em certificado. Suporte para chaves simétricas é preterido.  
   * **Módulo Azs.Fabric.Admin**  
         `Get-AzsInfrastructureVolume` foi preterido. Usar o novo cmdlet `Get-AzsVolume`.  
         `Get-AzsStorageSystem` foi preterido.  Usar o novo cmdlet novo `Get-AzsStorageSubSystem`.  
         `Get-AzsStoragePool` foi preterido. O `StorageSubSystem` objeto contém a propriedade de capacidade.  
   * **Módulo Azs.Compute.Admin**  
         Correção de bug – `Add-AzsPlatformImage`, `Get-AzsPlatformImage`: Chamar `ConvertTo-PlatformImageObject` somente no caminho do sucesso.  
         Correção de bug - `Add-AzsVmExtension`, `Get-AzsVmExtension`: Chamando ConvertTo-VmExtensionObject somente no caminho do sucesso.  
   * **Módulo Azs.Storage.Admin**  
         Correção de bug – nova cota de armazenamento usa padrões se não houver nenhuma fornecido.

Para examinar a referência para os módulos atualizados, consulte [referência de módulo do Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0).

## <a name="fixed-issues"></a>Problemas corrigidos

- Corrigido um problema em que o portal mostrou uma opção para criar gateways VPN baseado em políticas, que não têm suporte no Azure Stack. Essa opção foi removida do portal.

<!-- 16523695 – IS, ASDK -->
- Corrigido um problema em que, depois de atualizar as configurações de DNS para sua rede Virtual do **usar o DNS do Azure Stack** à **DNS personalizado**, as instâncias não foram atualizadas com a nova configuração.

- <!-- 3235634 – IS, ASDK --> Corrigido um problema no quais implantar VMs com tamanhos que contém um **v2** sufixo; por exemplo, **Standard_A2_v2**, é obrigatório especificar o sufixo como **Standard_A2_v2** ( v minúsculo). Como com o Azure global, agora você pode usar **Standard_A2_V2** (V maiusculo).

<!-- 2869209 – IS, ASDK --> 
- Corrigido um problema ao usar o [cmdlet Add-AzsPlatformImage](/powershell/module/azs.compute.admin/add-azsplatformimage), em que você tinha que usar o **- OsUri** parâmetro como a conta de armazenamento em que o disco é carregado do URI. Agora você pode usar também o caminho local para o disco.

<!--  2795678 – IS, ASDK --> 
- Corrigido um problema que gerou um aviso quando você usou o portal para criar máquinas virtuais (VMs) em um tamanho VM premium (DS, Ds_v2, FS e FSv2). A VM foi criada em uma conta de armazenamento padrão. Embora isso não afetou funcionalmente, IOPs, ou cobrança, o aviso foi corrigido.

<!-- 1264761 - IS ASDK -->  
- Corrigido um problema com o **controlador de integridade** componente que estava gerando os alertas a seguir. Os alertas poderão ser ignorados com segurança:

    - #1 do alerta:
       - NOME:  Função de infraestrutura não íntegro
       - GRAVIDADE: Aviso
       - COMPONENTE: Controlador de integridade
       - DESCRIÇÃO: O Scanner de pulsação do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.  

    - #2 do alerta:
       - NOME:  Função de infraestrutura não íntegro
       - GRAVIDADE: Aviso
       - COMPONENTE: Controlador de integridade
       - DESCRIÇÃO: O Scanner de falha do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.


<!-- 3507629 - IS, ASDK --> 
- Corrigido um problema ao definir o valor de cotas de Managed Disks sob [tipos de cota de computação](azure-stack-quota-types.md#compute-quota-types) como 0, é equivalente ao valor padrão de 2048 GiB. O valor de cota zero agora é respeitado.

<!-- 2724873 - IS --> 
- Corrigido um problema ao usar os cmdlets do PowerShell **início AzsScaleUnitNode** ou **AzsScaleunitNode Stop** para gerenciar unidades de escala, em que a primeira tentativa de iniciar ou parar a unidade de escala poderá falhar.

<!-- 2724961- IS ASDK --> 
- Corrigido um problema em que você registrou o **Microsoft.Insight** provedor de recursos nas configurações de assinatura e criado uma VM do Windows com o sistema operacional convidado diagnóstico habilitado, mas não mostrava o gráfico de percentual de CPU na página de visão geral da VM dados de métricas. Os dados agora exibem corretamente.

- Corrigido um problema no qual executar o **Get-AzureStackLog** cmdlet falhou após a execução **AzureStack teste** na mesma sessão do ponto de extremidade com privilégios (PEP). Agora você pode usar a mesma sessão PEP no qual você executou **AzureStack teste**.

<!-- bug 3615401, IS -->
- Corrigido o problema com backups automáticos em que o serviço de Agendador entravam no estado desabilitado inesperadamente. 

<!--2850083, IS ASDK -->
- Removida a **redefinir Gateway** botão do portal do Azure Stack, que gerou um erro se o botão foi clicado. Esse botão não serve nenhuma função no Azure Stack, como o Azure Stack tem um gateway multilocatário em vez de instâncias VM dedicadas para cada Gateway de VPN, de locatário para que ele foi removido para evitar confusão. 

<!-- 3209594, IS ASDK -->
- Removida a **regras de segurança efetivas** vincular a partir de **propriedades de rede** folha como esse recurso não tem suporte no Azure Stack. Ter um link presente deu a impressão de que esse recurso tinha suporte, mas não está funcionando. Para minimizar a confusão, removemos o link.

<!-- 3139614 | IS -->
- Corrigido um problema em que, após uma atualização foi aplicada para o Azure Stack de um OEM, a **atualização disponível** notificação não apareceu no portal do administrador do Azure Stack.

## <a name="changes"></a>Alterações

<!-- 3083238 IS -->
- Aprimoramentos de segurança nesta atualização resultaram em um aumento no tamanho do backup da função de serviço de diretório. Para atualizada orientação para o local de armazenamento externo de dimensionamento, consulte o [documentação da infraestrutura de backup](azure-stack-backup-reference.md#storage-location-sizing). Essa alteração resulta em mais tempo para concluir o backup devido à transferência de dados de tamanho maior. Essa alteração afeta os sistemas integrados. 

- A partir de janeiro de 2019, você pode implantar um cluster Kubernetes no Active Directory Federated Services (AD FS) registrado, carimbos de pilha do Azure conectados (o acesso à internet é necessário). Siga as instruções [aqui](azure-stack-solution-template-kubernetes-cluster-add.md) para baixar o novo item do Marketplace do Kubernetes. Siga as instruções [aqui](user/azure-stack-solution-template-kubernetes-adfs.md) para implantar um cluster Kubernetes. Observe os novos parâmetros para que indica se o sistema de destino é adicionar ou AD FS registrado. Se for o AD FS, novos campos estão disponíveis para inserir os parâmetros de Key Vault no qual o certificado de implantação é armazenado.

   Observe que, mesmo com suporte do AD FS, a implantação de clusters Kubernetes requer acesso à internet.

- Depois de instalar atualizações ou hotfixes para o Azure Stack, novos recursos podem ser introduzidos que exigem novas permissões a serem concedido a um ou mais aplicativos de identidade. Conceder essas permissões requer acesso administrativo para o diretório base e, portanto, ele não pode ser feito automaticamente. Por exemplo: 

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- Atualmente, há extensões que implantar com êxito sem a necessidade de explicitamente baixar as extensões por meio de sindicalização do marketplace no Azure Stack. As seguintes versões dessas extensões estão sendo removidas. Operadores do Azure Stack agora devem distribuir explicitamente essas extensões do marketplace do Azure Stack:

   | Type                     | Versão        |
   |--------------------------|----------------|
   | DSC                      | 2.19.0.0       |
   | IaaSAntimalware          | 1.4.0.0        |
   | BGInfo                   | 2,1            |
   | VMAccessAgent            | 2,0            |
   | CustomScriptExtension    | 1.8            |
   | MicrosoftMonitoringAgent | 1.0.10900.0    |
   | IaaSDiagnostics          | 1.10.1.1       |
   | VMAccessForLinux         | 1.4.0.0        |
   | CustomScriptForLinux     | 1.5.2.0        |
   | DockerExtension          | 1.1.1606092330 |
   | JsonADDomainExtension    | 1,3            |
   | OSPatchingForLinux       | 2.3.0.1        |
   | WebRole                  | 4.3000.14.0    |

   Recomenda-se que durante a implantação de extensões, os usuários do Azure Stack é definir `autoUpgradeMinorVersion` à **verdadeiro**. Por exemplo: 

   ```json
   "type": "Extension",
           "publisher": "ExtensionPublisher",
           "typeHandlerVersion": "1.2",
           "autoUpgradeMinorVersion": "true"
   ```

- Há uma nova consideração de planejamento com precisão a capacidade do Azure Stack. Definimos limites no número total de VMs que podem ser implantados no Azure Stack, certifique-se de que atender a todos os nossos serviços internos a escala na qual os clientes executam. O limite é de 60 VMs por host, com um máximo de 700 para o carimbo inteiro (se a 60 por limite de host é atingido). Para obter mais informações, consulte o [nova versão do Planejador de capacidade](http://aka.ms/azstackcapacityplanner).

- A versão da API de computação aumentou a 2017-12-01.

- Backup de infraestrutura agora requer um certificado com apenas uma chave pública (. CER) para criptografia de dados de backup. Suporte à criptografia simétrica de chave foi preterido a partir do 1901. Se o backup de infraestrutura é configurado antes de atualizar para 1901, as chaves de criptografia permanecerá em vigor. Você terá pelo menos 2 mais atualiza com versões anteriores suporte de compatibilidade para atualizar as configurações de backup. Para obter mais informações, consulte [do Azure Stack infraestrutura práticas recomendadas de backup](azure-stack-backup-best-practices.md).

## <a name="common-vulnerabilities-and-exposures"></a>Vulnerabilidades e exposições comuns

Esta atualização instala as atualizações de segurança a seguir:  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


Para obter mais informações sobre essas vulnerabilidades, clique nos links acima ou consulte os artigos da Base de dados de Conhecimento da Microsoft [4480977](https://support.microsoft.com/en-us/help/4480977).

## <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Ao executar [teste AzureStack](azure-stack-diagnostic-test.md), se o **AzsInfraRoleSummary** ou o **AzsPortalApiSummary** teste falhar, você será solicitado a executar  **Teste AzureStack** com o `-Repair` sinalizador.  Se você executar esse comando, ele falhará com a seguinte mensagem de erro:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

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
- Depois de aplicar o 1901 de atualização, você pode encontrar os seguintes problemas ao implantar VMs com discos gerenciados:

   - Se a assinatura foi criada antes da atualização 1808, implantando uma VM com discos gerenciados pode falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas para cada assinatura:
      1. No portal do locatário, vá para **assinaturas** e localize a assinatura. Selecione **provedores de recursos**, em seguida, selecione **Microsoft. Compute**e, em seguida, clique em **registrar novamente**.
      2. Sob a mesma assinatura, vá para **controle de acesso (IAM)**, verifique se **do Azure Stack – Managed Disk** está listado.
   - Se você tiver configurado um ambiente multilocatário, implantar as VMs em uma assinatura associada a um diretório de convidado pode falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas no [deste artigo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) para reconfigurar a cada um dos seus diretórios de convidado.

- Uma VM do Ubuntu 18.04 criado com autorização SSH habilitada não permitirá que você use as chaves SSH para fazer logon no. Como alternativa, use o acesso de VM para a extensão do Linux para implementar as chaves SSH após o provisionamento ou usar a autenticação baseada em senha.

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

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>Serviço de Aplicativo

<!-- 2352906 - IS ASDK --> 
- Você deve registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>Baixe a atualização

Você pode baixar o pacote de atualização no Azure Stack 1901 [aqui](https://aka.ms/azurestackupdatedownload). 

Em cenários conectados apenas, as implantações do Azure Stack verificar periodicamente um ponto de extremidade seguro em notificá-lo automaticamente se uma atualização está disponível para a sua nuvem. Para obter mais informações, consulte [Gerenciando atualizações para o Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Próximas etapas

- Para uma visão geral do gerenciamento de atualizações no Azure Stack, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md).  
- Para obter mais informações sobre como aplicar atualizações com o Azure Stack, consulte [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).
- Para revisar a política de manutenção para sistemas integrados do Azure Stack e o que você deve fazer para manter seu sistema em um estado com suporte, consulte [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).  
- Para usar o ponto de extremidade com privilégios (PEP) para monitorar e retomar as atualizações, consulte [monitorar as atualizações no Azure Stack usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).  
