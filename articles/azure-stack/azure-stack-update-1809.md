---
title: Atualização da pilha 1809 do Azure | Microsoft Docs
description: Saiba mais sobre quais são as novidades na atualização 1809 para sistemas integrados do Azure Stack, incluindo os problemas conhecidos e onde baixar a atualização.
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
ms.date: 11/01/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 00ce57dbff749d4ee906e0e7dae1d828ef85326f
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962054"
---
# <a name="azure-stack-1809-update"></a>Atualização da pilha 1809 do Azure

*Aplica-se a: sistemas integrados do Azure Stack*

Este artigo descreve o conteúdo do pacote de atualização de 1809. O pacote de atualização inclui aprimoramentos, correções e problemas conhecidos nesta versão do Azure Stack. Este artigo também inclui um link para que você pode baixar a atualização. Problemas conhecidos são divididos em questões relacionadas diretamente para o processo de atualização e os problemas com a compilação (após a instalação).

> [!IMPORTANT]  
> Este pacote de atualização é somente para os sistemas integrados do Azure Stack. Não aplique esse pacote de atualização para o Kit de desenvolvimento do Azure Stack.

## <a name="build-reference"></a>Referência de build

É o número de compilação de atualização do Azure Stack 1809 **1.1809.0.90**.  

### <a name="new-features"></a>Novos recursos

Esta atualização inclui os seguintes aprimoramentos para o Azure Stack:

- Com esta versão, o Azure Stack integrado sistemas dá suporte a configurações de 4 a 16 nós. Você pode usar o [Planejador de capacidade do Azure Stack](http://aka.ms/azstackcapacityplanner) para ajudar no planejamento de capacidade do Azure Stack e configuração.

- <!--  2712869   | IS  ASDK -->  **Cliente de syslog do Azure Stack (disponibilidade geral)** esse cliente permite o encaminhamento de auditorias, alertas e logs de segurança relacionados à infraestrutura do Azure Stack para um syslog server ou segurança informações e eventos (SIEM) software de gerenciamento externo para o Azure Stack. O cliente de syslog agora dá suporte a especificação da porta na qual o servidor syslog está escutando.

   Com esta versão, o cliente do syslog está geralmente disponível, e ele pode ser usado em ambientes de produção.

   Para obter mais informações, consulte [encaminhamento de syslog do Azure Stack](azure-stack-integrate-security.md).

- Agora você pode [mover o recurso de registro](azure-stack-registration.md#move-a-registration-resource) no Azure entre grupos de recursos sem a necessidade de registrar novamente. Provedores de soluções de nuvem (CSPs) também pode mover o recurso de registro entre assinaturas, desde que as assinaturas novas e antigas são mapeadas para a mesma ID de parceiro CSP. Isso não afeta os mapeamentos existentes de locatário do cliente. 

### <a name="fixed-issues"></a>Problemas corrigidos

<!-- TBD - IS ASDK -->
- No portal, o gráfico de memória livre/utilizada de capacidade de relatórios agora esteja exata. Agora você pode prever com mais confiança quantas VMs você é capaz de criar.

<!-- TBD - IS ASDK --> 
- Corrigido um problema em que você criou as máquinas virtuais no portal do usuário do Azure Stack e portal exibido um número incorreto de discos de dados que podem anexar a uma VM da série DS. As VMs da série DS podem acomodar a tantos discos de dados como a configuração do Azure.

- Os seguintes problemas de disco gerenciado são corrigidos em 1809 e também são corrigidos no 1808 [do Azure Stack Hotfix 1.1808.5.110](https://support.microsoft.com/help/4468920/): 

   <!--  2966665 – IS, ASDK --> 
   - Corrigido o problema no quais anexados discos de dados SSD para máquinas virtuais de disco gerenciado (DS, DSv2, Fs e Fs_V2) falhou com um erro de tamanho de premium: *Falha ao atualizar discos para a máquina virtual 'vmname' erro: operação solicitada não pode ser executada porque tipo de conta de armazenamento 'Premium_LRS' não há suporte para o tamanho da VM ' Standard_DS/Ds_V2/FS/Fs_v2)*. 
   
   - Criando uma VM de disco gerenciado usando **createOption**: **Attach** falha com o seguinte erro: *operação de execução longa falhou com status 'Failed'. Informações adicionais: 'Ocorreu um erro de execução interna'.*
   Código de erro: InternalExecutionError ErrorMessage: Ocorreu um erro de execução interna.
   
   Esse problema foi corrigido.

- <!-- 2702741 -  IS, ASDK --> Corrigido um problema no qual IPs públicos que foram implantados usando a alocação dinâmica o método não eram garantia de ser preservada após a emissão de uma interrupção da desalocação. Agora são preservados.

- <!-- 3078022 - IS, ASDK --> Se uma máquina virtual foi interrompida e desalocada antes 1808 pode não ser realocada após a atualização 1808.  Esse problema foi corrigido no 1809. Instâncias que foram nesse estado e não pôde ser iniciadas podem ser iniciadas no 1809 com essa correção. A correção também impede que esse problema ocorra.

### <a name="changes"></a>Alterações

Nenhuma.

### <a name="common-vulnerabilities-and-exposures"></a>Vulnerabilidades e exposições comuns

Esta atualização instala as atualizações de segurança a seguir:  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8320](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8320)
- [CVE-2018-8330](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8330)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8333](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8333)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8411](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8411)
- [CVE-2018-8413](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8413)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE-2018-8423](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8423)
- [CVE-2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE-2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE-2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE-2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE-2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE-2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE-2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE-2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE-2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE-2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE-2018-8453](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8453)
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8472](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8472)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)
- [CVE-2018-8481](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8481)
- [CVE-2018-8482](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8482)
- [CVE-2018-8484](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8484)
- [CVE-2018-8486](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8486)
- [CVE-2018-8489](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8489)
- [CVE-2018-8490](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8490)
- [CVE-2018-8492](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8492)
- [CVE-2018-8493](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8493)
- [CVE-2018-8494](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8494)
- [CVE-2018-8495](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8495)
- [CVE-2018-8497](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8497)

Para obter mais informações sobre essas vulnerabilidades, clique nos links acima ou consulte os artigos da Base de dados de Conhecimento da Microsoft [4457131](https://support.microsoft.com/help/4457131) e [4462917](https://support.microsoft.com/help/4462917).

### <a name="prerequisites"></a>Pré-requisitos

- Instale o Hotfix mais recente de pilha do Azure para 1808 antes de aplicar 1809. Para obter mais informações, consulte [4468920 KB – o Azure Stack Hotfix do Azure Stack Hotfix 1.1808.5.110](https://support.microsoft.com/en-us/help/4468920).

  > [!TIP]  
  > Inscrever-se ao seguinte *RRS* ou *Atom* feeds manter-se atualizado com Hotfixes de pilha do Azure:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- Antes de iniciar a instalação dessa atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com os seguintes parâmetros para validar o status do Azure Stack e resolva os problemas operacionais encontrados, incluindo todos os avisos e falhas. Também examinar os alertas ativos e resolva todos os que exigem ação.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Quando você executa [AzureStack teste](azure-stack-diagnostic-test.md) após a atualização 1809, será exibida uma mensagem de aviso do Baseboard Management Controller (BMC). Você pode ignorar com segurança esse aviso.

- <!-- 2468613 - IS --> Durante a instalação dessa atualização, você pode ver os alertas com o título *erro – o modelo para FaultType UserAccounts.New está ausente.*  Você pode ignorar com segurança esses alertas. Esses alertas serão fechada automaticamente após a conclusão da instalação dessa atualização.

- <!-- 2489559 - IS --> Não tente criar máquinas virtuais durante a instalação dessa atualização. Para obter mais informações sobre como gerenciar atualizações, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md#plan-for-updates).

- <!-- 3139614 | IS --> Se você tiver aplicado uma atualização para o Azure Stack de seu OEM, a **atualização disponível** notificação não pode aparecer no portal de administração do Azure Stack. Para instalar a Microsoft update, baixe e importe-o manualmente usando as instruções aqui [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).

### <a name="post-update-steps"></a>Etapas de pós-atualização

> [!Important]  
> Prepare sua implantação do Azure Stack para o host de extensão que é habilitada pelo próximo pacote de atualização. Preparar seu sistema usando as diretrizes a seguir, [preparar para o host de extensão para o Azure Stack](azure-stack-extension-host-prepare.md).

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - [Link to KB]()  
 -->

## <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

A seguir estão os problemas conhecidos de pós-instalação para esta versão de compilação.

### <a name="portal"></a>Portal

- A documentação técnica do Azure Stack enfoca a versão mais recente. Devido a alterações portal entre as versões, o que você vê ao usar os portais do Azure Stack podem ser diferentes daqueles que você vê na documentação.

<!-- 2930718 - IS ASDK --> 
- No portal do administrador ao acessar os detalhes de qualquer assinatura de usuário, depois de fechar a folha e clicando em **recentes**, o nome da assinatura de usuário não aparece.

<!-- 3060156 - IS ASDK --> 
- Nos portais de administrador e o usuário, clicando em configurações do portal e selecionando **excluir todas as configurações e painéis privados** não funciona conforme o esperado. Uma notificação de erro é exibida. 

<!-- 2930799 - IS ASDK --> 
- Nos portais de administrador e o usuário, sob **todos os serviços**, o ativo **planos de proteção contra DDoS** incorretamente está listado. Ele não está disponível no Azure Stack. Se você tentar criá-lo, um erro será exibido informando que o portal não pôde criar o item do marketplace. 

<!-- 2930820 - IS ASDK --> 
- Em portais de administrador e o usuário, se você procurar por "Docker", o item incorretamente é retornado. Ele não está disponível no Azure Stack. Se você tentar criá-la, uma folha com uma indicação de erro é exibida. 

<!-- 2967387 – IS, ASDK --> 
- A conta usada para entrar no portal de administrador ou usuário do Azure Stack é exibido como **usuário não identificado**. Esta mensagem é exibida quando a conta não tem qualquer um uma *primeira* ou *última* nome especificado. Para contornar esse problema, edite a conta de usuário para fornecer o primeiro ou último nome. Você deve, em seguida, saia e entre novamente no portal.  

<!--  2873083 - IS ASDK --> 
-  Ao usar o portal para criar uma escala de máquina virtual definido (VMSS), o *tamanho da instância* suspenso não carrega corretamente quando você usa o Internet Explorer. Para contornar esse problema, use outro navegador ao usar o portal para criar um VMSS.  

<!-- 2931230 – IS  ASDK --> 
- Os planos são adicionados a uma assinatura de usuário como um plano de complemento não podem ser excluídos, mesmo quando você remove o plano da assinatura do usuário. O plano permanecerá até que as assinaturas que o plano de complemento de referência também são excluídas. 

<!--2760466 – IS  ASDK --> 
- Quando você instala um novo ambiente do Azure Stack que executa esta versão, o alerta indicar *ativação necessária* podem não ser exibidos. [Ativação](azure-stack-registration.md) é necessária antes que você pode usar a distribuição do marketplace.  

<!-- TBD - IS ASDK --> 
- Os dois tipos de assinatura administrativos que foram introduzidos com a versão 1804 não devem ser usados. Os tipos de assinatura são **assinatura de medição**, e **assinatura de consumo**. Esses tipos de assinatura são visíveis em novos ambientes Azure Stack, começando com a versão 1804, mas ainda não estão prontos para uso. Você deve continuar a usar o **provedor padrão** tipo de assinatura.

<!-- TBD - IS ASDK --> 
- Excluindo resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos de usuário ou grupo de recursos inteiro e, em seguida, exclua as assinaturas do usuário.

<!-- TBD - IS ASDK --> 
- Você não pode exibir as permissões para sua assinatura usando os portais do Azure Stack. Como alternativa, use o PowerShell para verificar permissões.


### <a name="health-and-monitoring"></a>Monitoramento e integridade

<!-- TBD - IS -->
- Talvez você veja os seguintes alertas repetidamente aparecem e desaparecem no sistema do Azure Stack:
   - *Instância de função de infraestrutura não está disponível*
   - *Nó de unidade de escala está offline*
   
  Execute o [AzureStack teste](azure-stack-diagnostic-test.md) cmdlet para verificar a integridade das instâncias de função de infraestrutura e dimensionar nós de unidade. Se nenhum problema for detectado pelo [AzureStack teste](azure-stack-diagnostic-test.md), você pode ignorar esses alertas. Se for detectado um problema, você pode tentar iniciar a instância de função de infraestrutura ou de um nó usando o portal de administração do PowerShell.

<!-- 1264761 - IS ASDK -->  
- Você pode ver os alertas para o **controlador de integridade** componente que tem os seguintes detalhes:  

   #1 do alerta:
   - NOME: Função de infraestrutura não íntegro
   - GRAVIDADE: aviso
   - COMPONENTE: Controlador de integridade
   - Descrição: O Scanner de pulsação do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.  

  #2 do alerta:
   - NOME: Função de infraestrutura não íntegro
   - GRAVIDADE: aviso
   - COMPONENTE: Controlador de integridade
   - Descrição: O Scanner de falha do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.

  Ambos os alertas podem ser ignorados com segurança e será fechado automaticamente ao longo do tempo.  


<!-- 2812138 | IS --> 
- Você pode ver um alerta para o **armazenamento** componente que tem os seguintes detalhes:

   - NOME: Erro de comunicação interna do serviço de armazenamento  
   - GRAVIDADE: crítico  
   - COMPONENTE: armazenamento  
   - Descrição: Erro de comunicação interna do serviço de armazenamento ocorreu ao enviar solicitações para os nós a seguir.  

    O alerta pode ser ignorado, mas você precisa fechar o alerta manualmente.

<!-- 2368581 - IS, ASDK --> 
- Um operador do Azure Stack, se você receber um alerta de memória insuficiente e máquinas virtuais de locatário não conseguir implantar com um **erro de criação de VM do Fabric**, é possível que o carimbo de data / Azure Stack está fora de memória disponível. Use o [Planejador de capacidade do Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para entender melhor a capacidade disponível para suas cargas de trabalho.

### <a name="compute"></a>Computação

<!-- 3235634 – IS, ASDK -->
- Implantar VMs com tamanhos que contém um **v2** sufixo; por exemplo, **Standard_A2_v2**, especifique o sufixo como **Standard_A2_v2** (v minúsculo). Não use **Standard_A2_V2** (V maiusculo). Isso funciona no Azure global e uma inconsistência no Azure Stack.

<!-- 3099544 – IS, ASDK --> 
- Quando você cria uma nova máquina virtual (VM) usando o portal do Azure Stack, e você selecionar o tamanho da VM, a coluna USD por mês é exibida com um **indisponível** mensagem. Esta coluna não deve aparecer; exibindo a VM coluna preço não é suportada no Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Ao usar o [ **Add AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), você deve usar o **- OsUri** parâmetro como a conta de armazenamento em que o disco é carregado do URI. Se você usar o caminho local do disco, o cmdlet falhará com o seguinte erro: *operação de execução longa falhou com status 'Failed'*. 

<!--  2795678 – IS, ASDK --> 
- Quando você usa o portal para criar máquinas virtuais (VM) em um tamanho VM premium (DS, Ds_v2, FS e FSv2), a VM é criada em uma conta de armazenamento padrão. Criação de uma conta de armazenamento padrão não afeta funcionalmente, IOPs, ou de cobrança. 

   Você pode ignorar com segurança o aviso que diz: *você optou por usar um disco padrão em um tamanho que dá suporte a discos premium. Isso pode afetar o desempenho do sistema operacional e não é recomendado. Considere usar o armazenamento premium (SSD).*

<!-- 2967447 - IS, ASDK --> 
- A experiência de criação de conjunto (VMSS) de escala de máquina virtual oferece a opção para implantação baseada em CentOS 7.2. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para sua implantação ou usar um modelo do Azure Resource Manager especificando outra imagem do CentOS que foi baixada pelo operador antes da implantação do marketplace.  

<!-- 2724873 - IS --> 
- Ao usar os cmdlets do PowerShell **Start-AzsScaleUnitNode** ou **AzsScaleunitNode Stop** para gerenciar unidades de escala, a primeira tentativa de iniciar ou parar a unidade de escala poderá falhar. Se o cmdlet falhar na primeira execução, execute o cmdlet uma segunda vez. A segunda execução deve ser bem-sucedida para concluir a operação. 

<!-- TBD - IS ASDK --> 
- Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

<!-- 1662991 IS ASDK --> 
- Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico.  

<!-- 2724961- IS ASDK --> 
- Quando você registra o **Microsoft.Insight** provedor de recursos em configurações de assinatura e criar uma VM do Windows com o convidado SO diagnóstico habilitado, o gráfico de percentual de CPU na página de visão geral de VM não mostra dados de métricas.

   Para localizar os dados de métricas, como o gráfico de percentual de CPU para a VM, vá para a janela de métricas e mostrar todos os as métricas com suporte VM Windows convidado.



### <a name="networking"></a>Rede  

<!-- 1766332 - IS ASDK --> 
- Sob **Networking**, se você clicar em **criar o Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo de VPN. Não selecione essa opção. Somente o **com base em rota** opção tem suporte no Azure Stack.

<!-- 1902460 - IS ASDK --> 
- O Azure Stack dá suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatário. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

<!-- 16309153 - IS ASDK --> 
- Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automática*, alterar para uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.

<!-- 2529607 - IS ASDK --> 
- Durante o Azure Stack *rotação do segredo*, há um período em que os endereços IP públicos estão inacessíveis por dois a cinco minutos.

<!-- 2664148 - IS ASDK --> 
-   Em cenários em que o locatário está acessando suas máquinas virtuais usando um túnel VPN S2S, podem ocorrer um cenário em que as tentativas de conexão falharem se a sub-rede local foi adicionada ao Gateway de rede Local depois de gateway já foi criado. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>Serviço de Aplicativo

<!-- 2352906 - IS ASDK --> 
- Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.


### <a name="usage"></a>Uso  

<!-- TBD - IS ASDK --> 
- Os dados de medição do uso de endereço IP públicos mostram a mesma *EventDateTime* valor para cada registro, em vez da *TimeDate* carimbo que mostra que o registro foi criado. Atualmente, é possível usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Baixe a atualização

Você pode baixar o pacote de atualização no Azure Stack 1809 [aqui](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>Próximas etapas

- Para revisar a política de manutenção para sistemas integrados do Azure Stack e o que você deve fazer para manter seu sistema em um estado com suporte, consulte [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).  
- Para usar o ponto de extremidade com privilégios (PEP) para monitorar e retomar as atualizações, consulte [monitorar as atualizações no Azure Stack usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).  
- Para uma visão geral do gerenciamento de atualizações no Azure Stack, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md).  
- Para obter mais informações sobre como aplicar atualizações com o Azure Stack, consulte [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).  
