---
title: Atualização de 1903 de pilha do Azure | Microsoft Docs
description: Saiba mais sobre a atualização de 1903 para sistemas integrados do Azure Stack, incluindo novidades, problemas conhecidos e onde baixar a atualização.
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
ms.date: 04/05/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/05/2019
ms.openlocfilehash: 0448f583743f9e5b3f8fd6de3a85b05e16bff126
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269139"
---
# <a name="azure-stack-1903-update"></a>Atualização de 1903 de pilha do Azure

*Aplicável a Sistemas integrados do Azure Stack*

Este artigo descreve o conteúdo do pacote de atualização de 1903. A atualização inclui aprimoramentos, correções e novos recursos para esta versão do Azure Stack. Este artigo também descreve problemas conhecidos nesta versão e inclui um link para baixar a atualização. Problemas conhecidos são divididos em problemas diretamente relacionados ao processo de atualização e os problemas com a compilação (após a instalação).

> [!IMPORTANT]  
> Este pacote de atualização é somente para os sistemas integrados do Azure Stack. Não aplique esse pacote de atualização para o Kit de desenvolvimento do Azure Stack.

## <a name="build-reference"></a>Referência de build

É o número de compilação de atualização do Azure Stack 1903 **1.1903.0.35**.

> [!IMPORTANT]
> A carga de 1903 não inclui uma versão ASDK.

## <a name="hotfixes"></a>Hotfixes

O Azure Stack libera hotfixes regularmente. Certifique-se de instalar o [hotfix mais recente do Azure Stack](#azure-stack-hotfixes) para 1902 antes de atualizar o Azure Stack para 1903.

Os hotfixes de pilha do Azure são aplicáveis apenas aos sistemas integrados do Azure Stack. Não tente instalar os hotfixes a ASDK.

> [!TIP]  
> Inscrever-se ao seguinte *RSS* ou *Atom* feeds manter-se atualizado com hotfixes do Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Hotfixes de pilha do Azure

- **1902**: [KB 4481548 – o Azure Stack hotfix 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="improvements"></a>Melhorias

- O conteúdo de atualização de 1903 contém uma atualização para componentes do Azure Stack que não incluem o sistema operacional subjacente que hospeda do Azure Stack. Isso permite que determinadas atualizações no escopo. Como resultado, o esperado tempo necessário para concluir a atualização de 1903 é menor (aproximadamente 16 horas, mas os tempos exatos podem variar). Essa redução no tempo de execução é específica para a atualização de 1903 e as atualizações subsequentes podem conter atualizações para o sistema operacional, implicando diferentes tempos de execução. Atualizações futuras fornecerá diretrizes semelhantes no tempo esperado, que a atualização leva para ser concluído, dependendo da carga incluída.

- Corrigido um bug no sistema de rede que podem ser alterados para o **tempo limite de ociosidade (minutos)** valor de uma **endereço IP público** de entrarem em vigor. Anteriormente, as alterações a este valor foram ignoradas, para que, independentemente de quaisquer alterações feitas, o valor padrão para 4 minutos. Essa configuração controla quantos minutos manter uma conexão TCP aberta sem depender de clientes para enviar mensagens keep-alive. Observe que esse bug afetada somente a instância de nível IPs públicos, IPs públicos não atribuídos a um balanceador de carga.

- Aprimoramentos para a confiabilidade do mecanismo de atualização, incluindo a correção automática de problemas comuns para que se apliquem as atualizações sem interrupção.

- Melhorias para a detecção e correção de condições de espaço em disco insuficiente.

### <a name="secret-management"></a>Gerenciamento de segredos

- O Azure Stack agora dá suporte à rotação do certificado raiz usado por certificados para a rotação secreta externa. Para obter mais informações, [consulte este artigo](azure-stack-rotate-secrets.md).

- 1903 contém melhorias de desempenho para rotação do segredo que reduzem o tempo que leva para executar a rotação secreta interna.

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Instalar o [hotfix mais recente do Azure Stack](#azure-stack-hotfixes) para 1902 (se houver) antes de atualizar para 1903.

- Certifique-se de usar a versão mais recente do [Planejador de capacidade do Azure Stack](https://aka.ms/azstackcapacityplanner) fazer sua carga de trabalho de planejamento e dimensionamento. A versão mais recente contém correções de bugs e fornece novos recursos que são lançados a cada atualização do Azure Stack.

- Antes de iniciar a instalação dessa atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com o parâmetro a seguir para validar o status do Azure Stack e resolva os problemas operacionais encontrados, incluindo todos os avisos e falhas. Também examine os alertas ativos e resolver todos os que exigem ação:

    ```PowerShell
   Test-AzureStack -Group UpdateReadiness  
    ```

- Quando o Azure Stack é gerenciado pelo System Center Operations Manager, certifique-se de atualizar o [pacote de gerenciamento para Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) para versão 1.0.3.11 antes da aplicação de 1903.

- O formato de pacote para a atualização do Azure Stack foi alterado de **.bin/.exe/.xml** à **.zip/.xml** começando com a versão de 1902. Os clientes com unidades de escala do Azure Stack conectados verá o **atualização disponível** mensagem no portal. Os clientes que não estão conectados podem agora basta baixar e importar o arquivo. zip com o. XML correspondente.

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Quando você executa [AzureStack teste](azure-stack-diagnostic-test.md), será exibida uma mensagem de aviso do Baseboard Management Controller (BMC). Você pode ignorar com segurança esse aviso.

- <!-- 2468613 - IS --> Durante a instalação dessa atualização, você pode ver os alertas com o título **erro – o modelo para FaultType UserAccounts.New está ausente.** Você pode ignorar com segurança esses alertas. Os alertas fecham automaticamente após a instalação dessa atualização.

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

<!-- Daniel 3/28 -->
- No portal do usuário, quando você navegar para um blob dentro de uma conta de armazenamento e tente abrir **política de acesso** na árvore de navegação, a janela subsequente não é carregado.

<!-- Daniel 3/28 -->
- No portal do usuário, quando você tenta carregar um blob usando o **OAuth(preview)** opção, a tarefa falhar com uma mensagem de erro. Para contornar esse problema, carregar o blob usando o **SAS** opção.

- Quando conectado nos portais do Azure Stack, você poderá ver as notificações sobre o portal do Azure público. Você pode ignorar com segurança essas notificações, pois não são aplicadas no momento para o Azure Stack (por exemplo, "nova 1 atualização - as atualizações a seguir agora estão disponíveis: Atualização do Azure portal abril de 2019").

<!-- ### Health and monitoring -->

### <a name="compute"></a>Computação

- Ao criar um novo Windows VM (Máquina Virtual), o seguinte erro pode ser exibido:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   O erro ocorre se você habilitar o diagnóstico de inicialização em uma VM, mas excluir sua conta de armazenamento de diagnóstico de inicialização. Para contornar esse problema, recrie a conta de armazenamento com o mesmo nome que você usou anteriormente.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- A experiência de criação do conjunto de dimensionamento de máquinas virtuais oferece a opção para implantação baseada em CentOS 7.2. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para sua implantação, ou usar um modelo do Azure Resource Manager especificando outra imagem do CentOS que tiver sido baixada antes da implantação no marketplace, o operador.  

<!-- TBD - IS ASDK --> 
- Depois de aplicar o 1903 de atualização, você pode encontrar os seguintes problemas ao implantar VMs com discos gerenciados:

   - Se a assinatura foi criada antes da atualização 1808, implantando uma VM com discos gerenciados pode falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas para cada assinatura:
      1. No portal do locatário, vá para **assinaturas** e localize a assinatura. Selecione **provedores de recursos**, em seguida, selecione **Microsoft. Compute**e, em seguida, clique em **registrar novamente**.
      2. Sob a mesma assinatura, vá para **controle de acesso (IAM)**, verifique se **do Azure Stack – Managed Disk** está listado.
   - Se você tiver configurado um ambiente multilocatário, implantar as VMs em uma assinatura associada a um diretório de convidado pode falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas no [deste artigo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) para reconfigurar a cada um dos seus diretórios de convidado.

- Uma VM do Ubuntu 18.04 criado com autorização SSH habilitada não permitirá que você use as chaves SSH para entrar. Como alternativa, use o acesso de VM para a extensão do Linux para implementar as chaves SSH após o provisionamento ou usar a autenticação baseada em senha.

- O Azure Stack agora dá suporte a agentes do Windows Azure Linux superiores à versão 2.2.20. Esse suporte foi uma parte do hotfix 1901 e 1902 e permite aos clientes manter imagens do linux consistente entre o Azure e o Azure Stack.


- Se você não tiver um Host de ciclo de vida de Hardware (HLH): antes da compilação 1902, você precisava definir a política de grupo **computador Configuration\Windows Settings\Security Settings\Local Policies\Security Options** para **Enviar LM e NTLM – usar segurança de sessão NTLMv2 se negociado**. Desde o build 1902, você deve deixá-lo como **não definido** ou defina-o como **enviar somente resposta NTLMv2** (que é o valor padrão). Caso contrário, não será capaz de estabelecer uma sessão remota do PowerShell e você verá uma **o acesso é negado** erro:

   ```shell
   PS C:\Users\Administrator> $session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint  -Credential $cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the 
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

### <a name="networking"></a>Rede  

<!-- 3239127 - IS, ASDK -->
- No portal do Azure Stack, quando você altera um endereço IP estático para uma configuração de IP que está associado a um adaptador de rede anexado a uma instância VM, você verá uma mensagem de aviso dizendo que 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

    Você pode ignorar com segurança essa mensagem; o endereço IP será alterado, mesmo se a instância de VM não reinicia.

<!-- 3632798 - IS, ASDK -->
- No portal, se você adicionar uma regra de segurança de entrada e selecione **marca de serviço** como a origem, várias opções são exibidas na **marca de origem** lista que não estão disponíveis para o Azure Stack. As únicas opções que são válidas no Azure Stack são da seguinte maneira:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  Não há suporte para as outras opções como marcas de origem no Azure Stack. Da mesma forma, se você adicionar uma regra de segurança de saída e selecione **marca de serviço** como o destino, a mesma lista de opções para **marca de origem** é exibida. As únicas opções válidas são os mesmos para **marca de origem**, conforme descrito na lista anterior.

- Grupos de segurança de rede (NSGs) não funcionam no Azure Stack no Azure mesma forma como global. No Azure, você pode definir várias portas em uma regra NSG (usando o portal, PowerShell e modelos do Resource Manager). No entanto, no Azure Stack é possível definir várias portas em uma regra NSG por meio do portal. Para contornar esse problema, use um modelo do Resource Manager ou o PowerShell para definir essas regras adicionais.

<!-- 3203799 - IS, ASDK -->
- O Azure Stack não dá suporte a anexar mais de 4 adaptadores de rede (NICs) a uma instância de VM hoje em dia, independentemente do tamanho da instância.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>Serviço de Aplicativo

<!-- 2352906 - IS ASDK --> 
- Você deve registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>syslog 
- A configuração de syslog não é mantida por meio de um ciclo de atualização, resultando no cliente de syslog perder sua configuração e as mensagens do syslog para interromper o que está sendo encaminhado. Esse problema se aplica a todas as versões do Azure Stack, desde o GA do cliente syslog (1809).
A solução alternativa é reconfigurar o cliente de syslog após aplicar uma atualização do Azure Stack.

## <a name="download-the-update"></a>Baixe a atualização

Você pode baixar o pacote de atualização no Azure Stack 1903 [aqui](https://aka.ms/azurestackupdatedownload). 

Em cenários conectados apenas, as implantações do Azure Stack verificar periodicamente um ponto de extremidade seguro em notificá-lo automaticamente se uma atualização está disponível para a sua nuvem. Para obter mais informações, consulte [Gerenciando atualizações para o Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Próximas etapas

- Para uma visão geral do gerenciamento de atualizações no Azure Stack, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md).  
- Para obter mais informações sobre como aplicar atualizações com o Azure Stack, consulte [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).
- Para revisar a política de manutenção para sistemas integrados do Azure Stack e o que você deve fazer para manter seu sistema em um estado com suporte, consulte [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).  
- Para usar o ponto de extremidade com privilégios (PEP) para monitorar e retomar as atualizações, consulte [monitorar as atualizações no Azure Stack usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).  
