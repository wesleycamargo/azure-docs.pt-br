---
title: Atualização da pilha do Azure 1809 | Microsoft Docs
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
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 4b83d3743f65a3ad1b757c2e6dee8416701b24f3
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48243526"
---
# <a name="azure-stack-1809-update"></a>Atualização da pilha 1809 do Azure

*Aplica-se a: sistemas integrados do Azure Stack*

Este artigo descreve o conteúdo do pacote de atualização de 1809. O pacote de atualização inclui aprimoramentos, correções e problemas conhecidos nesta versão do Azure Stack. Este artigo também inclui um link para que você pode baixar a atualização. Problemas conhecidos são divididos em questões relacionadas diretamente para o processo de atualização e os problemas com a compilação (após a instalação).

> [!IMPORTANT]  
> Este pacote de atualização é somente para os sistemas integrados do Azure Stack. Não aplique esse pacote de atualização para o Kit de desenvolvimento do Azure Stack.

## <a name="build-reference"></a>Referência de build

É o número de compilação de atualização do Azure Stack 1809 **1.1809.x.xx**.  

### <a name="new-features"></a>Novos recursos

Esta atualização inclui os seguintes aprimoramentos para o Azure Stack:

- <!--  2712869   | IS  ASDK -->  **Cliente de syslog do Azure Stack (disponibilidade geral)** esse cliente permite o encaminhamento de auditorias, alertas e logs de segurança relacionados à infraestrutura do Azure Stack para um syslog server ou segurança informações e eventos (SIEM) software de gerenciamento externo para o Azure Stack. O cliente de syslog agora dá suporte a especificação da porta na qual o servidor syslog está escutando.

Com esta versão, o cliente do syslog está geralmente disponível, e ele pode ser usado em ambientes de produção.

Para obter mais informações, consulte [encaminhamento de syslog do Azure Stack](azure-stack-integrate-security.md).

### <a name="fixed-issues"></a>Problemas corrigidos

- <!-- 2702741 -  IS ASDK --> Corrigido um problema no qual IPs públicos que foram implantados usando a alocação dinâmica o método não eram garantia de ser preservada após a emissão de uma interrupção da desalocação. Agora são preservados.

- <!-- 3078022 - IS ASDK --> Se uma máquina virtual foi interrompida e desalocada antes 1808 pode não ser realocada após a atualização 1808.  Esse problema foi corrigido no 1809. Instâncias que foram nesse estado e não pôde ser iniciadas podem ser iniciadas no 1809 com essa correção. A correção também impede que esse problema ocorra.

### <a name="changes"></a>Alterações

- <!-- 1697698  | IS, ASDK --> *Tutoriais de início rápido* no link agora de painel do portal de usuário para os artigos relevantes na documentação do Azure Stack on-line.

- <!-- 2515955   | IS ,ASDK--> *Todos os serviços* substitui *mais serviços* nos portais de administrador e usuário do Azure Stack. Agora você pode usar *todos os serviços* como uma alternativa para navegar nos portais do Azure Stack da mesma maneira que faria em portais do Azure.

- <!--  TBD – IS, ASDK --> *Um básico* tamanhos de máquina virtual estão desativados para [criando conjuntos de dimensionamento de máquina virtual](azure-stack-compute-add-scalesets.md) (VMSS) por meio do portal. Para criar um VMSS com esse tamanho, use o PowerShell ou um modelo.  

### <a name="common-vulnerabilities-and-exposures"></a>Vulnerabilidades e exposições comuns

Esta atualização instala as atualizações de segurança a seguir:  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
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
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)

Para obter mais informações sobre essas vulnerabilidades, clique nos links acima, ou consulte o artigo Microsoft Knowledge Base [4457131](https://support.microsoft.com/help/4457131).

### <a name="prerequisites"></a>Pré-requisitos

- Instalar o Azure Stack [1808 atualizar](azure-stack-update-1808.md) antes de aplicar a atualização do Azure Stack 1809. 
- Instalar a versão mais recente disponível [atualização ou hotfix para a versão 1808](azure-stack-update-1808.md#post-update-steps).

  > [!TIP]  
  > Inscrever-se ao seguinte *RRS* ou *Atom* feeds manter-se atualizado com Hotfixes de pilha do Azure:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- Antes de iniciar a instalação dessa atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com os seguintes parâmetros para validar o status do Azure Stack e resolva os problemas operacionais encontrados, incluindo todos os avisos e falhas. Também examinar os alertas ativos e resolva todos os que exigem ação.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- <!-- 2468613 - IS --> Durante a instalação dessa atualização, você pode ver os alertas com o título *erro – o modelo para FaultType UserAccounts.New está ausente.*  Você pode ignorar com segurança esses alertas. Esses alertas serão fechada automaticamente após a conclusão da instalação dessa atualização.

- <!-- 2489559 - IS --> Não tente criar máquinas virtuais durante a instalação dessa atualização. Para obter mais informações sobre como gerenciar atualizações, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md#plan-for-updates).

- <!-- 2830461 - IS --> Em determinadas circunstâncias quando uma atualização requer atenção, o alerta correspondente pode não ser gerado. O status preciso ainda será refletido no portal e não é afetado.

### <a name="post-update-steps"></a>Etapas de pós-atualização

*Não há nenhuma etapa de pós-atualização para atualização 1809.*

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - [Link to KB]()  
 -->

## <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

A seguir estão os problemas conhecidos de pós-instalação para esta versão de compilação.

### <a name="portal"></a>Portal

- A documentação técnica do Azure Stack enfoca a versão mais recente. Devido a alterações portal entre as versões, o que você vê ao usar os portais do Azure Stack podem ser diferentes daqueles que você vê na documentação.

- <!-- 2930718 - IS ASDK --> No portal do administrador ao acessar os detalhes de qualquer assinatura de usuário, depois de fechar a folha e clicando em **recentes**, o nome da assinatura de usuário não aparece.

- <!-- 3060156 - IS ASDK --> Nos portais de administrador e o usuário, clicando em configurações do portal e selecionando **excluir todas as configurações e painéis privados** não funciona conforme o esperado. Uma notificação de erro é exibida. 

- <!-- 2930799 - IS ASDK --> Nos portais de administrador e o usuário, sob **todos os serviços**, o ativo **planos de proteção contra DDoS** incorretamente está listado. Não é realmente disponível no Azure Stack. Se você tentar criá-lo, um erro será exibido informando que o portal não pôde criar o item do marketplace. 

- <!-- 2930820 - IS ASDK --> Em portais de administrador e o usuário, se você procurar por "Docker", o item incorretamente é retornado. Não é realmente disponível no Azure Stack. Se você tentar criá-la, uma folha com uma indicação de erro é exibida. 

- <!-- 2967387 – IS, ASDK --> A conta usada para entrar no portal de administrador ou usuário do Azure Stack é exibido como **usuário não identificado**. Isso ocorre quando a conta não tenha um uma *primeira* ou *última* nome especificado. Para contornar esse problema, edite a conta de usuário para fornecer o primeiro ou último nome. Você deve, em seguida, saia e entre novamente no portal.  

-  <!--  2873083 - IS ASDK --> Ao usar o portal para criar uma escala de máquina virtual definido (VMSS), o *tamanho da instância* suspenso não carrega corretamente quando você usa o Internet Explorer. Para contornar esse problema, use outro navegador ao usar o portal para criar um VMSS.  

- <!-- TBD  ASDK --> O fuso horário de padrão para todas as implantações do Azure Stack agora são definidos para o tempo Universal Coordenado (UTC). Você pode selecionar um fuso horário ao instalar o Azure Stack, no entanto, ele será revertido automaticamente para UTC como padrão durante a instalação.

- <!-- 2931230 – IS  ASDK --> Os planos são adicionados a uma assinatura de usuário como um plano de complemento não podem ser excluídos, mesmo quando você remove o plano da assinatura do usuário. O plano permanecerá até que as assinaturas que o plano de complemento de referência também são excluídas. 

- <!--2760466 – IS  ASDK --> Quando você instala um novo ambiente do Azure Stack que executa esta versão, o alerta indicar *ativação necessária* podem não ser exibidos. [Ativação](azure-stack-registration.md) é necessária antes que você pode usar a distribuição do marketplace.  

- <!-- TBD - IS ASDK --> Os dois tipos de assinatura administrativos foram [introduzido na versão 1804](azure-stack-update-1804.md#new-features) não deve ser usado. Os tipos de assinatura são **assinatura de medição**, e **assinatura de consumo**. Esses tipos de assinatura são visíveis em novos ambientes Azure Stack, começando com a versão 1804, mas ainda não estão prontos para uso. Você deve continuar a usar o **provedor padrão** tipo de assinatura.

- <!-- TBD - IS ASDK --> Excluindo resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos de usuário ou grupo de recursos inteiro e, em seguida, exclua as assinaturas do usuário.

- <!-- TBD - IS ASDK --> Você não pode exibir as permissões para sua assinatura usando os portais do Azure Stack. Como alternativa, use o PowerShell para verificar permissões.


### <a name="health-and-monitoring"></a>Monitoramento e integridade

- <!-- 1264761 - IS ASDK -->  Você pode ver os alertas para o **controlador de integridade** componente que tem os seguintes detalhes:  

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


- <!-- 2812138 | IS --> Você pode ver um alerta para **armazenamento** componente que tem os seguintes detalhes:

   - NOME: Erro de comunicação interna do serviço de armazenamento  
   - GRAVIDADE: crítico  
   - COMPONENTE: armazenamento  
   - Descrição: Erro de comunicação interna do serviço de armazenamento ocorreu ao enviar solicitações para os nós a seguir.  

    O alerta pode ser ignorado, mas você precisa fechar o alerta manualmente.

- <!-- 2368581 - IS. ASDK --> Um operador do Azure Stack, se você receber um alerta de memória insuficiente e máquinas virtuais de locatário não conseguir implantar com um **erro de criação de VM do Fabric**, é possível que o carimbo de data / Azure Stack está fora de memória disponível. Use o [Planejador de capacidade do Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para entender melhor a capacidade disponível para suas cargas de trabalho.


### <a name="compute"></a>Computação

- <!-- 2869209 – IS, ASDK --> Ao usar o [ **Add AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), você deve usar o **- OsUri** parâmetro como a conta de armazenamento em que o disco é carregado do URI. Se você usar o caminho local do disco, o cmdlet falhará com o seguinte erro: *operação de execução longa falhou com status 'Failed'*. 

- <!--  2966665 – IS, ASDK --> Anexar discos de dados do SSD para tamanho premium managed disk máquinas virtuais (DS, DSv2, Fs e Fs_V2) falhará com um erro: *Falha ao atualizar discos para a máquina virtual 'vmname' erro: operação solicitada não pode ser executada porque o tipo de conta de armazenamento ' Premium_LRS' não há suporte para o tamanho da VM ' Standard_DS/Ds_V2/FS/Fs_v2)*

   Para contornar esse problema, use *Standard_LRS* discos de dados em vez de *Premium_LRS discos*. Uso de *Standard_LRS* discos de dados não altera o custo de cobrança ou de IOPs. 

- <!--  2795678 – IS, ASDK --> Quando você usa o portal para criar máquinas virtuais (VM) em um tamanho VM premium (DS, Ds_v2, FS e FSv2), a VM é criada em uma conta de armazenamento padrão. Criação de uma conta de armazenamento padrão não afeta funcionalmente, IOPs, ou de cobrança. 

   Você pode ignorar com segurança o aviso que diz: *você optou por usar um disco padrão em um tamanho que dá suporte a discos premium. Isso pode afetar o desempenho do sistema operacional e não é recomendado. Considere usar o armazenamento premium (SSD).*

- <!-- 2967447 - IS, ASDK --> A experiência de criação de conjunto (VMSS) de escala de máquina virtual oferece a opção para implantação baseada em CentOS 7.2. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para sua implantação ou usar um modelo do Azure Resource Manager especificando outra imagem do CentOS que foi baixada pelo operador antes da implantação do marketplace.  

- <!-- 2724873 - IS --> Ao usar os cmdlets do PowerShell **Start-AzsScaleUnitNode** ou **AzsScaleunitNode Stop** para gerenciar unidades de escala, a primeira tentativa de iniciar ou parar a unidade de escala poderá falhar. Se o cmdlet falhar na primeira execução, execute o cmdlet uma segunda vez. A segunda execução deve ser bem-sucedida para concluir a operação. 

- <!-- TBD - IS ASDK --> Quando você cria máquinas virtuais no portal do usuário do Azure Stack, o portal exibe um número incorreto de discos de dados que podem anexar a uma VM da série DS. As VMs da série DS podem acomodar a tantos discos de dados como a configuração do Azure.

- <!-- TBD - IS ASDK --> Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

- <!-- 1662991 IS ASDK --> Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico.  

- <!-- 2724961- IS ASDK --> Quando você registra o **Microsoft.Insight** provedor de recursos em configurações de assinatura e criar uma VM do Windows com o convidado SO diagnóstico habilitado, o gráfico de percentual de CPU na página de visão geral de VM não mostra dados de métricas.

   Para localizar os dados de métricas, como o gráfico de percentual de CPU para a VM, vá para a janela de métricas e mostrar todos os as métricas com suporte VM Windows convidado.



### <a name="networking"></a>Rede  

- <!-- 1766332 - IS ASDK --> Sob **Networking**, se você clicar em **criar o Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo de VPN. Não selecione essa opção. Somente o **com base em rota** opção tem suporte no Azure Stack.

- <!-- 1902460 - IS ASDK --> O Azure Stack dá suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatário. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- <!-- 16309153 - IS ASDK --> Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automática*, alterar para uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.

- <!-- 2529607 - IS ASDK --> Durante o Azure Stack *rotação do segredo*, há um período em que os endereços IP públicos estão inacessíveis por dois a cinco minutos.

-   <!-- 2664148 - IS ASDK --> Em cenários em que o locatário está acessando suas máquinas virtuais usando um túnel VPN S2S, podem ocorrer um cenário em que as tentativas de conexão falharem se a sub-rede local foi adicionada ao Gateway de rede Local depois de gateway já foi criado. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>Serviço de Aplicativo

- <!-- 2352906 - IS ASDK --> Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.


### <a name="usage"></a>Uso  

- <!-- TBD - IS ASDK --> Os dados de medição do uso de endereço IP públicos mostram a mesma *EventDateTime* valor para cada registro, em vez da *TimeDate* carimbo que mostra que o registro foi criado. Atualmente, é possível usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Baixe a atualização

Você pode baixar o pacote de atualização no Azure Stack 1809 [aqui](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>Próximas etapas

- Para revisar a política de manutenção para sistemas integrados do Azure Stack e o que você deve fazer para manter seu sistema em um estado com suporte, consulte [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).  
- Para usar o ponto de extremidade com privilégios (PEP) para monitorar e retomar as atualizações, consulte [monitorar as atualizações no Azure Stack usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).  
- Para uma visão geral do gerenciamento de atualizações no Azure Stack, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md).  
- Para obter mais informações sobre como aplicar atualizações com o Azure Stack, consulte [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).  
