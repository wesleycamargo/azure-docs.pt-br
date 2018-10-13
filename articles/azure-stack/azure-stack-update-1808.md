---
title: Atualização da pilha do Azure 1808 | Microsoft Docs
description: Saiba mais sobre quais são as novidades na atualização 1808 para sistemas integrados do Azure Stack, incluindo os problemas conhecidos e onde baixar a atualização.
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
ms.date: 10/12/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 2e913881faadd4892ad1ebc8cb404efe6489eb0d
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310891"
---
# <a name="azure-stack-1808-update"></a>Atualização da pilha 1808 do Azure

*Aplica-se a: sistemas integrados do Azure Stack*

Este artigo descreve o conteúdo do pacote de atualização de 1808. O pacote de atualização inclui aprimoramentos, correções e problemas conhecidos nesta versão do Azure Stack. Este artigo também inclui um link para que você pode baixar a atualização. Problemas conhecidos são divididos em questões relacionadas diretamente para o processo de atualização e os problemas com a compilação (após a instalação).

> [!IMPORTANT]  
> Este pacote de atualização é somente para os sistemas integrados do Azure Stack. Não aplique esse pacote de atualização para o Kit de desenvolvimento do Azure Stack.

## <a name="build-reference"></a>Referência de build

É o número de compilação de atualização do Azure Stack 1808 **1.1808.0.97**.  

### <a name="new-features"></a>Novos recursos

Esta atualização inclui os seguintes aprimoramentos para o Azure Stack.

<!--  2682594   | IS  --> 
- **Todos os ambientes do Azure Stack agora usam o formato de fuso horário do tempo Universal Coordenado (UTC).**  Todos os dados de log e informações relacionadas agora exibem no formato UTC. Se você atualizar de uma versão anterior que não foi instalada usando o formato UTC, seu ambiente é atualizado para usar o UTC. 

<!-- 2437250  | IS  ASDK --> 
- **Há suporte para discos gerenciados.** Agora você pode usar o Managed Disks em máquinas virtuais do Azure Stack e conjuntos de dimensionamento de máquina virtual. Para obter mais informações, consulte [Managed Disks do Azure Stack: diferenças e considerações](/azure/azure-stack/user/azure-stack-managed-disk-considerations).

<!-- 2563799  | IS  ASDK --> 
- **Azure Monitor**. Como o Azure Monitor no Azure, o Azure Monitor no Azure Stack fornece logs e métricas de infraestrutura de nível básico para a maioria dos serviços. Para obter mais informações, consulte [do Azure Monitor no Azure Stack](/azure/azure-stack/user/azure-stack-metrics-azure-data).

<!-- 2487932| IS --> 
- **Preparar para o host de extensão**. Você pode usar o host de extensão para ajudar a proteger Azure Stack, reduzindo o número de portas TCP/IP. Com a atualização 1808, você pode preparar, Prepare sua pilha do Azure para o host de extensão. Para obter mais informações, consulte [preparar para o host de extensão para o Azure Stack](/azure/azure-stack/azure-stack-extension-host-prepare).

<!-- IS --> 
- **Itens da Galeria para conjuntos de dimensionamento de máquinas virtuais agora são internos**. O item da Galeria de conjunto de dimensionamento de máquina Virtual agora ficam disponível nos portais do usuário e administrador sem precisar baixá-lo.  Se você atualizar para o 1808, ele está disponível após a conclusão da atualização.  

<!-- IS, ASDK --> 
- **Conjunto de dimensionamento de máquina virtual dimensionamento**. Você pode usar o portal para [escalar um conjunto de dimensionamento de máquinas virtuais](azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).    

<!-- 2489570 | IS ASDK--> 
- **Suporte para configurações de política de IPSec/IKE personalizadas** para [gateways de VPN no Azure Stack](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).

<!-- | IS ASDK--> 
- **Item do marketplace Kubernetes**. Agora você pode implantar um cluster Kubernetes usando o [item do Marketplace do Kubernetes](azure-stack-solution-template-kubernetes-cluster-add.md). Os usuários podem selecionar o item de Kubernetes e preencha alguns parâmetros ao implantar um cluster Kubernetes no Azure Stack. A finalidade dos modelos é tornar isso simples para os usuários para configurar implantações de Kubernetes de desenvolvimento/teste em poucas etapas.

<!-- | IS ASDK--> 
- **Modelos de Blockchain**. Agora você pode executar [implantações de consortium Ethereum](azure-stack-ethereum.md) no Azure Stack. Você pode encontrar três novos modelos na [do Azure Stack modelos de início rápido](https://github.com/Azure/AzureStack-QuickStart-Templates). Eles permitem que o usuário implantar e configurar uma rede de Ethereum consortium vários membros com o mínimo de conhecimento do Azure e Ethereum. A finalidade dos modelos é tornar isso simples para os usuários para configurar implantações de Blockchain de desenvolvimento/teste em poucas etapas.

<!-- | IS ASDK--> 
- **O API versão 2017-03-09-perfil foi atualizado para 2018-03-01-hybrid**. Perfis de API especificar o provedor de recursos do Azure e a versão da API para pontos de extremidade REST do Azure. Para obter mais informações sobre perfis, consulte [perfis de versão da API de gerenciar no Azure Stack](/azure/azure-stack/user/azure-stack-version-profiles).

 ### <a name="fixed-issues"></a>Problemas corrigidos
<!-- IS ASDK--> 
- Corrigimos o problema para a criação de uma conjunto de disponibilidade no portal do que resultou em conjunto com um domínio de falha e um domínio de atualização de 1. 

<!-- IS ASDK --> 
- Configurações para escalar conjuntos de dimensionamento de máquinas virtuais agora estão disponíveis no portal.  

<!-- 2494144- IS, ASDK --> 
- O problema que impedia a que aparece ao selecionar um tamanho de VM para a implantação de alguns tamanhos de máquinas virtuais F-series agora está resolvido. 

<!-- IS, ASDK --> 
- Melhorias de desempenho durante a criação de máquinas virtuais e muito mais otimizada o uso de armazenamento subjacente.

- **Várias correções de** para desempenho, estabilidade, segurança e o sistema operacional que é usado pelo Azure Stack.


### <a name="changes"></a>Alterações
<!-- 1697698  | IS, ASDK --> 
- *Tutoriais de início rápido* no link agora de painel do portal de usuário para os artigos relevantes na documentação do Azure Stack on-line.

<!-- 2515955   | IS ,ASDK--> 
- *Todos os serviços* substitui *mais serviços* nos portais de administrador e usuário do Azure Stack. Agora você pode usar *todos os serviços* como uma alternativa para navegar nos portais do Azure Stack da mesma maneira que faria em portais do Azure.

<!-- TBD | IS, ASDK --> 
- *+ Criar um recurso* substitui *+ novo* nos portais de administrador e usuário do Azure Stack.  Agora você pode usar *+ criar um recurso* como uma alternativa para navegar nos portais do Azure Stack da mesma maneira que faria em portais do Azure.  

<!--  TBD – IS, ASDK --> 
- *Um básico* tamanhos de máquina virtual estão desativados para [criando conjuntos de dimensionamento de máquina virtual](azure-stack-compute-add-scalesets.md) (VMSS) por meio do portal. Para criar um VMSS com esse tamanho, use o PowerShell ou um modelo.  

### <a name="common-vulnerabilities-and-exposures"></a>Vulnerabilidades e exposições comuns

Esta atualização instala as seguintes atualizações:  

- [CVE-2018-0952](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0952)
- [CVE-2018-8200](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8200)
- [CVE-2018-8204](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8204)
- [CVE-2018-8253](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8253)
- [CVE-2018-8339](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8339)
- [CVE-2018-8340](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8340)
- [CVE-2018-8341](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8341)
- [CVE-2018-8343](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8343)
- [CVE-2018-8344](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8344)
- [CVE-2018-8345](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8345)
- [CVE-2018-8347](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8347)
- [CVE-2018-8348](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8348)
- [CVE-2018-8349](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8349)
- [CVE-2018-8394](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8394)
- [CVE-2018-8398](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8398)
- [CVE-2018-8401](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8401)
- [CVE-2018-8404](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8404)
- [CVE-2018-8405](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8405)
- [CVE-2018-8406](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8406)  

Para obter mais informações sobre essas vulnerabilidades, clique nos links acima, ou consulte o artigo Microsoft Knowledge Base [4343887](https://support.microsoft.com/help/4343887).

Essa atualização também contém atenuação para a vulnerabilidade de canal de lado a execução especulativa conhecida como falha de Terminal de L1 (L1TF), descrito na [Microsoft Security Advisory ADV180018](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018).  

### <a name="prerequisites"></a>Pré-requisitos

- Instalar o Azure Stack [1807 atualizar](azure-stack-update-1807.md) antes de aplicar a atualização do Azure Stack 1808. 

- Instalar a versão mais recente disponível [atualização ou hotfix para a versão 1807](azure-stack-update-1807.md#post-update-steps).  
  > [!TIP]  
  > Inscrever-se ao seguinte *RRS* ou *Atom* feeds manter-se atualizado com Hotfixes de pilha do Azure:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- Antes de iniciar a instalação dessa atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com os seguintes parâmetros para validar o status do Azure Stack e resolva os problemas operacionais encontrados, incluindo todos os avisos e falhas. Também examinar os alertas ativos e resolva todos os que exigem ação.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```   

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

<!-- TBD - IS -->
- Talvez você veja os seguintes alertas repetidamente aparecem e desaparecem no sistema do Azure Stack:
   - *Instância de função de infraestrutura não está disponível*
   - *Nó de unidade de escala está offline*
   
  Execute o [AzureStack teste](azure-stack-diagnostic-test.md) cmdlet para verificar a integridade das instâncias de função de infraestrutura e dimensionar nós de unidade. Se nenhum problema for detectado pelo [AzureStack teste](azure-stack-diagnostic-test.md), você pode ignorar esses alertas. Se for detectado um problema, você pode tentar iniciar a instância de função de infraestrutura ou de um nó usando o portal de administração do PowerShell.

- Quando você executa [AzureStack teste](azure-stack-diagnostic-test.md) após a atualização 1808, será exibida uma mensagem de aviso do Baseboard Management Controller (BMC). Você pode ignorar com segurança esse aviso.

<!-- 2468613 - IS --> 
- Durante a instalação dessa atualização, você pode ver os alertas com o título *erro – o modelo para FaultType UserAccounts.New está ausente.*  Você pode ignorar com segurança esses alertas. Esses alertas serão fechada automaticamente após a conclusão da instalação dessa atualização.

<!-- 2489559 - IS --> 
- Não tente criar máquinas virtuais durante a instalação dessa atualização. Para obter mais informações sobre como gerenciar atualizações, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md#plan-for-updates).

<!-- 2830461 - IS --> 
- Em determinadas circunstâncias quando uma atualização requer atenção, o alerta correspondente pode não ser gerado. O status preciso ainda será refletido no portal e não é afetado.

### <a name="post-update-steps"></a>Etapas de pós-atualização
Após a instalação dessa atualização, instale os Hotfixes aplicáveis. Para obter mais informações, consulte os seguintes artigos de base de Conhecimento, bem como nossos [política de manutenção](azure-stack-servicing-policy.md). 
- [KB 4467062 – o Azure Stack Hotfix Azure Stack Hotfix 1.1808.4.108](https://support.microsoft.com/help/4467062/)


## <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

A seguir estão os problemas conhecidos de pós-instalação para esta versão de compilação.

### <a name="portal"></a>Portal

- A documentação técnica do Azure Stack enfoca a versão mais recente. Devido a alterações portal entre as versões, o que você vê ao usar os portais do Azure Stack podem ser diferentes daqueles que você vê na documentação. 

<!-- TBD - IS ASDK --> 
- Você pode ver um painel em branco no portal. Para recuperar o painel, clique em **Editar painel**, em seguida, clique com botão direito e selecione **retornarão ao estado padrão**.

<!-- 2930718 - IS ASDK --> 
- No portal do administrador ao acessar os detalhes de qualquer assinatura de usuário, depois de fechar a folha e clicando em **recentes**, o nome da assinatura de usuário não aparece.

<!-- 3060156 - IS ASDK --> 
- Nos portais de administrador e o usuário, clicando em configurações do portal e selecionando **excluir todas as configurações e painéis privados** não funciona conforme o esperado. Uma notificação de erro é exibida. 

<!-- 2930799 - IS ASDK --> 
- Nos portais de administrador e o usuário, sob **todos os serviços**, o ativo **planos de proteção contra DDoS** incorretamente está listado. Não é realmente disponível no Azure Stack. Se você tentar criá-lo, um erro será exibido informando que o portal não pôde criar o item do marketplace. 

<!-- 2930820 - IS ASDK --> 
- Em portais de administrador e o usuário, se você procurar por "Docker", o item incorretamente é retornado. Não é realmente disponível no Azure Stack. Se você tentar criá-la, uma folha com uma indicação de erro é exibida. 

<!-- 2967387 – IS, ASDK --> 
- A conta usada para entrar no portal de administrador ou usuário do Azure Stack é exibido como **usuário não identificado**. Isso ocorre quando a conta não tenha um uma *primeira* ou *última* nome especificado. Para contornar esse problema, edite a conta de usuário para fornecer o primeiro ou último nome. Você deve, em seguida, saia e entre novamente no portal. 

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
- Você pode ver um alerta para **armazenamento** componente que contém os seguintes detalhes:

   - NOME: Erro de comunicação interna do serviço de armazenamento  
   - GRAVIDADE: crítico  
   - COMPONENTE: armazenamento  
   - Descrição: Erro de comunicação interna do serviço de armazenamento ocorreu ao enviar solicitações para os nós a seguir.  

    O alerta pode ser ignorado, mas você precisa fechar o alerta manualmente.

<!-- 2368581 - IS. ASDK --> 
- Um operador do Azure Stack, se você receber um alerta de memória insuficiente e máquinas virtuais de locatário não conseguir implantar com um **erro de criação de VM do Fabric**, é possível que o carimbo de data / Azure Stack está fora de memória disponível. Use o [Planejador de capacidade do Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para entender melhor a capacidade disponível para suas cargas de trabalho.


### <a name="compute"></a>Computação

<!-- 3099544 – IS, ASDK --> 
- Quando você cria uma nova máquina virtual (VM) usando o portal do Azure Stack, e você selecionar o tamanho da VM, a coluna USD por mês é exibida com um **indisponível** mensagem. Esta coluna não deve aparecer; exibindo a VM coluna preço não é suportada no Azure Stack.

<!-- 3090289 – IS, ASDK --> 
- Depois de aplicar o 1808 de atualização, você pode encontrar os seguintes problemas ao implantar VMs com discos gerenciados:

   1. Se a assinatura foi criada antes da atualização 1808, implantação de VM com discos gerenciados podem falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas para cada assinatura:
      1. No portal do locatário, vá para **assinaturas** e localize a assinatura. Clique em **provedores de recursos**, em seguida, clique em **Microsoft. Compute**e, em seguida, clique em **registrar novamente**.
      2. Sob a mesma assinatura, vá para **controle de acesso (IAM)**, verifique se **do Azure Stack – Managed Disk** está listado.
   2. Se você tiver configurado um ambiente multilocatário, implantar as VMs em uma assinatura associada a um diretório de convidado pode falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas:
      1. Aplicar a [1808 Azure Stack Hotfix](https://support.microsoft.com/help/4467062/).
      2. Siga as etapas em [deste artigo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) para reconfigurar a cada um dos seus diretórios de convidado.

<!-- 2869209 – IS, ASDK --> 
- Ao usar o [ **Add AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), você deve usar o **- OsUri** parâmetro como a conta de armazenamento em que o disco é carregado do URI. Se você usar o caminho local do disco, o cmdlet falhará com o seguinte erro: *operação de execução longa falhou com status 'Failed'*. 

<!--  2966665 – IS, ASDK --> 
- Anexar discos de dados do SSD para tamanho premium managed disk máquinas virtuais (DS, DSv2, Fs e Fs_V2) falhará com um erro: *Falha ao atualizar discos para a máquina virtual 'vmname' erro: operação solicitada não pode ser executada porque o tipo de conta de armazenamento ' Premium_LRS' não há suporte para o tamanho da VM ' Standard_DS/Ds_V2/FS/Fs_v2)*

   Para contornar esse problema, use *Standard_LRS* discos de dados em vez de *Premium_LRS discos*. Uso de *Standard_LRS* discos de dados não altera o custo de cobrança ou de IOPs. 

<!--  2795678 – IS, ASDK --> 
- Quando você usa o portal para criar máquinas virtuais (VM) em um tamanho VM premium (DS, Ds_v2, FS e FSv2), a VM é criada em uma conta de armazenamento padrão. Criação de uma conta de armazenamento padrão não afeta funcionalmente, IOPs, ou de cobrança. 

   Você pode ignorar com segurança o aviso que diz: *você optou por usar um disco padrão em um tamanho que dá suporte a discos premium. Isso pode afetar o desempenho do sistema operacional e não é recomendado. Considere usar o armazenamento premium (SSD).*

<!-- 2967447 - IS, ASDK --> 
- Experiência de criar o conjunto de dimensionamento de máquinas virtuais (VMSS) oferece a opção de implantação baseado em CentOS 7.2. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para sua implantação ou usar um modelo do Azure Resource Manager especificando outra imagem do CentOS que foi baixada pelo operador antes da implantação do marketplace.  

<!-- 2724873 - IS --> 
- Ao usar os cmdlets do PowerShell **Start-AzsScaleUnitNode** ou **AzsScaleunitNode Stop** para gerenciar unidades de escala, a primeira tentativa de iniciar ou parar a unidade de escala poderá falhar. Se o cmdlet falhar na primeira execução, execute o cmdlet uma segunda vez. A segunda execução deve ser bem-sucedida para concluir a operação. 

<!-- TBD - IS ASDK --> 
- Quando você cria máquinas virtuais no portal do usuário do Azure Stack, o portal exibe um número incorreto de discos de dados que podem anexar a uma VM da série DS. As VMs da série DS podem acomodar a tantos discos de dados como a configuração do Azure.

<!-- TBD - IS ASDK --> 
- Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

<!-- 1662991 IS ASDK --> 
- Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico.  

<!-- 2724961- IS ASDK --> 
- Quando você registra o **Microsoft.Insight** provedor de recursos em configurações de assinatura e criar uma VM do Windows com o sistema operacional convidado diagnóstico habilitado, o gráfico de percentual de CPU na página de visão geral de VM não será capaz de mostrar dados de métrica.

   Para localizar o gráfico de percentual de CPU para a VM, vá para o **métricas** métricas de convidado de folha e mostrar toda a VM Windows com suporte.



### <a name="networking"></a>Rede  

<!-- 1766332 - IS ASDK --> 
- Sob **Networking**, se você clicar em **criar o Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo de VPN. Não selecione essa opção. Somente o **com base em rota** opção tem suporte no Azure Stack.

<!-- 1902460 - IS ASDK --> 
- O Azure Stack dá suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatário. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

<!-- 16309153 - IS ASDK --> 
- Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automática*, alterar para uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.

<!-- 2702741 -  IS ASDK --> 
- IPs públicos que são implantados usando o método de alocação dinâmica não é garantido para ser preservada após a emissão de uma interrupção da desalocação.

<!-- 2529607 - IS ASDK --> 
- Durante o Azure Stack *rotação do segredo*, há um período em que os endereços IP públicos estão inacessíveis por dois a cinco minutos.

<!-- 2664148 - IS ASDK --> 
- Em cenários em que o locatário está acessando suas máquinas virtuais usando um túnel VPN S2S, podem ocorrer um cenário em que as tentativas de conexão falharem se a sub-rede local foi adicionada ao Gateway de rede Local depois de gateway já foi criado. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>Serviço de Aplicativo

<!-- 2352906 - IS ASDK --> 
- Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

<!-- 2489178 - IS ASDK --> 
- Para escalar horizontalmente a infraestrutura (funcionários, gerenciamento, as funções front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.



### <a name="usage"></a>Uso  
<!-- TBD - IS ASDK --> 
- Dados de medidor uso do endereço de IP público de uso mostram a mesma *EventDateTime* valor para cada registro, em vez da *TimeDate* carimbo que mostra que o registro foi criado. Atualmente, é possível usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Baixe a atualização
Você pode baixar o pacote de atualização no Azure Stack 1808 [aqui](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>Próximas etapas
- Para revisar a política de manutenção para sistemas integrados do Azure Stack e o que você deve fazer para manter seu sistema em um estado com suporte, consulte [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).  
- Para usar o ponto de extremidade com privilégios (PEP) para monitorar e retomar as atualizações, consulte [monitorar as atualizações no Azure Stack usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).  
- Para uma visão geral do gerenciamento de atualizações no Azure Stack, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md).  
- Para obter mais informações sobre como aplicar atualizações com o Azure Stack, consulte [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).  
