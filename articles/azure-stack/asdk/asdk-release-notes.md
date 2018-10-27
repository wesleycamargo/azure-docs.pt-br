---
title: Notas de versão do Kit de desenvolvimento do Microsoft Azure Stack | Microsoft Docs
description: Problemas conhecidos do Kit de desenvolvimento do Azure Stack, correções e aprimoramentos.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2018
ms.author: sethm
ms.reviewer: misainat
ms.openlocfilehash: 284a964162a2374287b42698b9a2021be36590dd
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158152"
---
# <a name="asdk-release-notes"></a>Notas de versão ASDK  
Este artigo fornece informações sobre problemas conhecidos no Azure Stack desenvolvimento ASDK (Kit), correções e aprimoramentos. Se você não tiver certeza de qual versão você está executando, você poderá [usar o portal para verificar](.\.\azure-stack-updates.md#determine-the-current-version).

> Mantenha-se atualizado com o que há de novo no ASDK assinando o [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11809090"></a>Compilação 1.1809.0.90

### <a name="new-features"></a>Novos recursos
Esta compilação inclui os seguintes aprimoramentos e correções para o Azure Stack.  

<!--  2712869   | IS  ASDK -->  
- **Cliente de syslog do Azure Stack (disponibilidade geral)** esse cliente permite o encaminhamento de auditorias, alertas e logs de segurança relacionados à infraestrutura do Azure Stack para um syslog server ou segurança informações e eventos (SIEM) software de gerenciamento externo para o Azure Stack. O cliente de syslog agora dá suporte a especificação da porta na qual o servidor syslog está escutando.

Com esta versão, o cliente do syslog está geralmente disponível, e ele pode ser usado em ambientes de produção.

Para obter mais informações, consulte [encaminhamento de syslog do Azure Stack](../azure-stack-integrate-security.md).

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

- **Várias correções de** para desempenho, estabilidade, segurança e o sistema operacional que é usado pelo Azure Stack


### <a name="changes"></a>Alterações

### <a name="known-issues"></a>Problemas conhecidos

#### <a name="portal"></a>Portal  

<!-- 2515955   | IS ,ASDK--> 
- *Todos os serviços* substitui *mais serviços* nos portais de administrador e usuário do Azure Stack. Agora você pode usar *todos os serviços* como uma alternativa para navegar nos portais do Azure Stack da mesma maneira que faria em portais do Azure.

<!--  TBD – IS, ASDK --> 
- *Um básico* tamanhos de máquina virtual estão desativados para [criando conjuntos de dimensionamento de máquina virtual](../azure-stack-compute-add-scalesets.md) (VMSS) por meio do portal. Para criar um VMSS com esse tamanho, use o PowerShell ou um modelo.

#### <a name="health-and-monitoring"></a>Monitoramento e integridade

<!-- 1264761 - IS ASDK -->  
- Você pode ver os alertas para o *controlador de integridade* componente que tem os seguintes detalhes:  

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

  Ambos os alertas podem ser ignorados com segurança e serão fechada automaticamente ao longo do tempo.  

<!-- 2368581 - IS. ASDK --> 
- Um operador do Azure Stack, se você receber um alerta de memória insuficiente e máquinas virtuais de locatário não conseguir implantar com um *erro de criação de VM do Fabric*, é possível que o carimbo de data / Azure Stack está fora de memória disponível. Use o [Planejador de capacidade do Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para entender melhor a capacidade disponível para suas cargas de trabalho.


#### <a name="compute"></a>Computação 

<!-- 3235634 – IS, ASDK -->
- Implantar VMs com tamanhos que contém um **v2** sufixo; por exemplo, **Standard_A2_v2**, especifique o sufixo como **Standard_A2_v2** (v minúsculo). Não use **Standard_A2_V2** (V maiusculo). Isso funciona no Azure global e uma inconsistência no Azure Stack.

<!-- 3099544 – IS, ASDK --> 
- Quando você cria uma nova máquina virtual (VM) usando o portal do Azure Stack, e você selecionar o tamanho da VM, a coluna USD por mês é exibida com um **indisponível** mensagem. Esta coluna não deve aparecer; exibindo a VM coluna preço não é suportada no Azure Stack.

<!-- 3090289 – IS, ASDK --> 
- Depois de aplicar o 1808 de atualização, você pode encontrar os seguintes problemas ao implantar VMs com discos gerenciados:

   1. Se a assinatura foi criada antes da atualização 1808, implantação de VM com discos gerenciados podem falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas para cada assinatura:
      1. No portal do locatário, vá para **assinaturas** e localize a assinatura. Clique em **provedores de recursos**, em seguida, clique em **Microsoft. Compute**e, em seguida, clique em **registrar novamente**.
      2. Sob a mesma assinatura, vá para **controle de acesso (IAM)**, verifique se **do Azure Stack – Managed Disk** está listado.
   2. Se você tiver configurado um ambiente multilocatário, implantar as VMs em uma assinatura associada a um diretório de convidado pode falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas:
      1. Aplicar a [1808 Azure Stack Hotfix](https://support.microsoft.com/help/4468920).
      2. Siga as etapas em [deste artigo](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) para reconfigurar a cada um dos seus diretórios de convidado.

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

<!-- TBD -  IS ASDK --> 
- As configurações de escala para conjuntos de dimensionamento de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Devido às diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

<!-- TBD -  IS ASDK --> 
- Quando uma imagem de VM não consegue ser criado, um item com falha que não é possível excluir pode ser adicionado até a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado por meio do Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão de item com falha. Em seguida, 15 minutos após a criação da imagem fictícia, você pode excluí-la.

  Em seguida, você pode repetir o download da imagem da VM que falharam anteriormente.

<!-- TBD -  IS ASDK --> 
- Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

<!-- 1662991 - IS ASDK --> 
- Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico.

<!-- 2724961- IS ASDK --> 
- Quando você registra o **Microsoft.Insight** provedor de recursos em configurações de assinatura e criar uma VM do Windows com o sistema operacional convidado diagnóstico habilitado, o gráfico de percentual de CPU na página de visão geral de VM não será capaz de mostrar dados de métrica.
 
  Para localizar o gráfico de percentual de CPU para a VM, vá para o **métricas** métricas de convidado de folha e mostrar toda a VM Windows com suporte.

 

#### <a name="networking"></a>Rede
<!-- 1766332 - IS, ASDK --> 
- Sob **Networking**, se você clicar em **criar o Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo de VPN. Não selecione essa opção. Somente o **com base em rota** opção tem suporte no Azure Stack.

<!-- 1902460 -  IS ASDK --> 
- O Azure Stack dá suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatário. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

<!-- 16309153 -  IS ASDK --> 
- Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automática*, alterar para uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.

<!-- 2702741 -  IS ASDK --> 
- IPs públicos que são implantados usando o método de alocação dinâmica não é garantido para ser preservada após a emissão de uma interrupção da desalocação.

<!-- 2529607 - IS ASDK --> 
- Durante o Azure Stack *rotação do segredo*, há um período em que os endereços IP públicos estão inacessíveis por dois a cinco minutos.

<!-- 2664148 - IS ASDK --> 
- Em cenários em que o locatário está acessando suas máquinas virtuais usando um túnel VPN S2S, podem ocorrer um cenário em que as tentativas de conexão falharem se a sub-rede local foi adicionada ao Gateway de rede Local depois de gateway já foi criado. 


<!--  #### SQL and MySQL  -->


#### <a name="app-service"></a>Serviço de Aplicativo
<!-- 2352906 - IS ASDK --> 
- Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

<!-- TBD - IS ASDK --> 
- Para escalar horizontalmente a infraestrutura (funcionários, gerenciamento, as funções front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.  


#### <a name="usage"></a>Uso  
<!-- TBD -  IS ASDK --> 
- Dados de medidor uso do endereço de IP público de uso mostram a mesma *EventDateTime* valor para cada registro, em vez da *TimeDate* carimbo que mostra que o registro foi criado. Atualmente, é possível usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.

<!-- #### Identity -->

## <a name="build-11808097"></a>Compilação 1.1808.0.97

### <a name="new-features"></a>Novos recursos
Esta compilação inclui os seguintes aprimoramentos e correções para o Azure Stack.  

<!-- 1658937 | ASDK, IS --> 
- **Iniciar backups em um agendamento previamente definido** -como um dispositivo do Azure Stack agora podem disparar automaticamente backups de infra-estrutura periodicamente para eliminar a intervenção humana. O Azure Stack limpará automaticamente o compartilhamento externo para backups mais antigos que o período de retenção definido. Para obter mais informações, consulte [habilitar o Backup para o Azure Stack com o PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).

<!-- 2496385 | ASDK, IS -->  
- **Hora em que o tempo de backup total de transferência de dados adicionados.** Para obter mais informações, consulte [habilitar o Backup para o Azure Stack com o PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).

<!-- 1702130 | ASDK, IS --> 
- **De capacidade de backup externa agora mostra a capacidade correta do compartilhamento externo.** (Isso era difícil de código até 10 GB.) Para obter mais informações, consulte [habilitar o Backup para o Azure Stack com o PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).
 
<!-- 2753130 |  IS, ASDK   -->  
- **Modelos do Gerenciador de recursos do Azure agora dão suporte ao elemento de condição** -agora você pode implantar um recurso em um modelo do Gerenciador de recursos do Azure usando uma condição. Você pode criar seu modelo para implantar um recurso com base em uma condição, como avaliar se um valor de parâmetro estiver presente. Para obter informações sobre como usar um modelo como uma condição, consulte [implantar condicionalmente um recurso](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) e [seção variáveis dos modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) na documentação do Azure. 

   Você também pode usar modelos para [implantar recursos em mais de uma assinatura ou grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

<!--2753073 | IS, ASDK -->  
- **O suporte de versão do recurso Microsoft. Network API foi atualizado** para incluir suporte para a API versão 2017-10-01 de 2015-06-15 para recursos de rede do Azure Stack. Suporte para versões de recurso entre 2017-10-01 e 2015-06-15 não está incluído nesta versão. Consulte a [considerações sobre a rede do Azure Stack](.\.\user\azure-stack-network-differences.md) para diferenças de funcionalidade.

<!-- 2272116 | IS, ASDK   -->  
- **O Azure Stack adicionou suporte para pesquisas inversas de DNS para pontos de extremidade de infraestrutura do Azure Stack com face externa** (que é para o portal, adminportal, gerenciamento e adminmanagement). Isso permite que os nomes de ponto de extremidade externo do Azure Stack ser resolvido a partir de um endereço IP.

<!-- 2780899 |  IS, ASDK   --> 
- **O Azure Stack agora dá suporte à adição de adaptadores de rede adicionais a uma VM existente.**  Essa funcionalidade está disponível por meio do portal, PowerShell e CLI. Para obter mais informações, consulte [adicionar ou remover adaptadores de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) na documentação do Azure. 

<!-- 2222444 | IS, ASDK   -->  
- **Foram feitas melhorias na precisão e a resiliência para os medidores de uso de rede**. Medidores de uso de rede agora são mais precisos e levam em assinaturas de conta suspenso, pontos de interrupção e as condições de corrida.

<!-- 2297790 | IS, ASDK -->  
- **Melhorias para o cliente do Azure Stack syslog (recurso de visualização)**. Esse cliente permite o encaminhamento de auditoria e logs relacionados à infraestrutura do Azure Stack para um syslog server ou segurança informações e eventos (SIEM) software de gerenciamento externo para o Azure Stack. O cliente de syslog agora oferece suporte o protocolo TCP com texto sem formatação ou criptografia de TLS 1.2, o segundo sendo a configuração padrão. Você pode configurar a conexão TLS com a autenticação somente do servidor ou mútua.

  Para configurar como o cliente de syslog se comunica (como protocolo, criptografia e autenticação) com o servidor syslog, use o cmdlet Set-SyslogServer. Este cmdlet está disponível do ponto de extremidade (PEP) com privilégios. 

- <!-- ASDK --> **Itens da Galeria para conjuntos de dimensionamento de máquinas virtuais agora são internos**.  Conjunto de dimensionamento de máquina virtual de itens de galeria agora ficam disponíveis nos portais do usuário e administrador sem precisar baixá-los. 

- <!-- IS, ASDK --> **Conjunto de dimensionamento de máquina virtual dimensionamento**.  Você pode usar o portal para [escalar um conjunto de dimensionamento de máquinas virtuais](/azure/azure-stack/azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).   

- <!-- 2489570 | IS ASDK--> **Suporte para configurações de política de IPSec/IKE personalizadas** para [gateways de VPN no Azure Stack](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).

- <!-- | IS ASDK--> **Item do marketplace Kubernetes**. Agora você pode implantar um cluster Kubernetes usando o [item do Marketplace do Kubernetes](/azure/azure-stack/azure-stack-solution-template-kubernetes-cluster-add). Os usuários podem selecionar o item de Kubernetes e preencha alguns parâmetros ao implantar um cluster Kubernetes no Azure Stack. A finalidade dos modelos é tornar isso simples para os usuários para implantações de Kubernetes de desenvolvimento/teste de instalação em poucas etapas.

<!-- ####### | IS, ASDK -->  
- **O Azure Resource Manager inclui o nome da região.** Com esta versão, os objetos recuperados do Azure Resource Manager agora incluirá o atributo de nome de região. Se um script PowerShell existente diretamente passa o objeto para outro cmdlet, o script pode produzir um erro e falha. Isso é o comportamento compatível com o Azure Resource Manager e exige que o cliente da chamada subtrair o atributo região. Para obter mais informações sobre o Azure Resource Manager, consulte [documentação do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

<!-- TBD | IS, ASDK -->  
- **Mova assinaturas entre provedores de delegado.** Agora você pode mover assinaturas entre as assinaturas de provedor delegada novas ou existentes que pertencem ao mesmo locatário do diretório. As assinaturas que pertencem à assinatura do provedor padrão também podem ser movidas para as assinaturas de provedor delegada no mesmo locatário de diretório. Para obter mais informações, consulte [delegar ofertas no Azure Stack](.\.\azure-stack-delegated-provider.md).
 
<!-- 2536808 IS ASDK --> 
- **Aprimorada a hora de criação da VM** para VMs que são criadas com imagens, você pode baixar do marketplace do Azure.

### <a name="fixed-issues"></a>Problemas corrigidos
- <!-- IS ASDK--> Corrigimos o problema para a criação de uma conjunto de disponibilidade no portal do que resultou em conjunto com um domínio de falha e um domínio de atualização de 1.

<!-- TBD | ASDK, IS --> 
- Várias melhorias foram feitas para o processo de atualização para torná-lo mais confiável. Além disso, foram feitas correções a infraestrutura subjacente, o que melhora a drenagem de nó, minimizando o tempo de inatividade potencial para cargas de trabalho durante a atualização.

<!--2292271 | ASDK, IS --> 
- Corrigimos um problema em que um limite de cota modificado não foi aplicada a assinaturas existentes.  Agora, quando você gerar um limite de cota para um recurso de rede que faz parte de uma oferta e plano associado com uma assinatura de usuário, o novo limite se aplica ao assinaturas já existentes, bem como novas assinaturas.

<!-- 2448955 | IS ASDK --> 
- Agora você pode consultar com êxito os logs de atividades para sistemas que são implantados em um fuso horário de UTC + N.    

<!-- 2319627 |  ASDK, IS --> 
- Pré-verificação para parâmetros de configuração de backup (caminho/nome de usuário/senha/criptografia de chave) não define configurações incorretas para a configuração de backup. (Anteriormente, configurações incorretas foram definidas para o backup e backup, em seguida, falharia quando disparado).

<!-- 2215948 |  ASDK, IS --> 
- A lista de backup agora é atualizada quando você excluir manualmente o backup do compartilhamento externo.

<!-- 2360715 |  ASDK, IS -->  
- Quando você configura a integração do datacenter, você não mais acessar o arquivo de metadados do AD FS de um compartilhamento. Para obter mais informações, consulte [configurar a integração do AD FS, fornecendo o arquivo de metadados de Federação](.\.\azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

<!-- 2388980 | ASDK, IS --> 
- Corrigimos um problema que impedia os usuários de atribuído um endereço IP público existente que foi anteriormente atribuído a uma Interface de rede ou um balanceador de carga para uma nova Interface de rede ou balanceador de carga.  

<!-- 2551834 - IS, ASDK --> 
- Quando você seleciona *visão geral* para uma conta de armazenamento nos portais de administrador ou usuário, o *Essentials* painel agora exibe todas as informações esperadas corretamente. 

<!-- 2551834 - IS, ASDK --> 
- Quando você seleciona *marcas* para uma conta de armazenamento nos portais de administrador ou usuário, as informações agora exibe corretamente.

<!-- TBD - IS ASDK --> 
- Esta versão do Azure Stack corrige o problema que impediu a aplicação das atualizações de driver do pacotes de extensão do OEM.

<!-- 2055809- IS ASDK --> 
- Corrigimos um problema que impedia a exclusão de VMs na folha computação quando a VM não pôde ser criada.  

<!--  2643962 IS ASDK -->  
- O alerta de *capacidade de memória de baixa* não é mais exibido incorretamente.

<!--  TBD ASDK --> 
- A máquina virtual que hospeda o ponto de extremidade de privilégio (PEP) aumentou para 4GB. ASDK, essa máquina virtual é chamada AzS-ERCS01.

- <!--  TBD – IS, ASDK --> *Um básico* tamanhos de máquina virtual estão desativados para [criando conjuntos de dimensionamento de máquina virtual](.\.\azure-stack-compute-add-scalesets.md) (VMSS) por meio do portal. Para criar um VMSS com esse tamanho, use o PowerShell ou um modelo. 

### <a name="known-issues"></a>Problemas conhecidos

#### <a name="portal"></a>Portal  
- <!-- 2967387 – IS, ASDK --> A conta usada para entrar no portal de administrador ou usuário do Azure Stack é exibido como **usuário não identificado**. Isso ocorre quando a conta não tenha um uma *primeira* ou *última* nome especificado. Para contornar esse problema, edite a conta de usuário para fornecer o primeiro ou último nome. Você deve, em seguida, saia e entre novamente no portal. 

-  <!--  2873083 - IS ASDK --> Ao usar o portal para criar uma escala de máquina virtual definido (VMSS), o *tamanho da instância* suspenso não carrega corretamente quando você usa o Internet Explorer. Para contornar esse problema, use outro navegador ao usar o portal para criar um VMSS.  

#### <a name="portal"></a>Portal  
<!-- 2931230 – IS  ASDK --> 
- Os planos são adicionados a uma assinatura de usuário como um plano de complemento não podem ser excluídos, mesmo quando você remove o plano da assinatura do usuário. O plano permanecerá até que as assinaturas que o plano de complemento de referência também são excluídas. 

<!--2760466 – IS  ASDK --> 
- Quando você instala um novo ambiente do Azure Stack que executa esta versão, o alerta indicar *ativação necessária* podem não ser exibidos. [Ativação](.\.\azure-stack-registration.md) é necessária antes que você pode usar a distribuição do marketplace. 

<!-- TBD - IS ASDK --> 
- Os dois tipos de assinatura administrativos que foram introduzidos com a versão 1804 não devem ser usados. Os tipos de assinatura são **assinatura de medição**, e **assinatura de consumo**. Esses tipos de assinatura são **assinatura de medição**, e **assinatura de consumo**. Esses tipos de assinatura são visíveis em novos ambientes Azure Stack, começando com a versão 1804, mas ainda não estão prontos para uso. Você deve continuar a usar o **assinatura do provedor padrão** tipo.

<!-- 2403291 - IS ASDK --> 
- Você pode não ter o uso da barra de rolagem horizontal na parte inferior do portais do administrador e usuário. Se você não pode acessar a barra de rolagem horizontal, use a trilha para navegar até uma folha anterior no portal, selecionando o nome da folha você quer exibir na lista de navegação estrutural na parte superior esquerda do portal.
  ![Navegação estrutural](media/asdk-release-notes/breadcrumb.png)

<!-- TBD -  IS ASDK --> 
- Excluindo resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos de usuário ou grupo de recursos inteiro e, em seguida, exclua as assinaturas do usuário.

<!-- TBD -  IS ASDK --> 
- Você não pode exibir as permissões para sua assinatura usando os portais do Azure Stack. Como alternativa, use o PowerShell para verificar permissões.

<!--  TBD | ASDK -->  
- O fuso horário de padrão para sua implantação do Azure Stack agora será obter definido como UTC. Você pode selecionar um fuso horário ao instalar o Azure Stack, no entanto, ela será revertida automaticamente para UTC como padrão durante a instalação.

#### <a name="health-and-monitoring"></a>Monitoramento e integridade
<!-- 1264761 - IS ASDK -->  
- Você pode ver os alertas para o *controlador de integridade* componente que tem os seguintes detalhes:  

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

  Ambos os alertas podem ser ignorados com segurança e serão fechada automaticamente ao longo do tempo.  

<!-- 2368581 - IS. ASDK --> 
- Um operador do Azure Stack, se você receber um alerta de memória insuficiente e máquinas virtuais de locatário não conseguir implantar com um *erro de criação de VM do Fabric*, é possível que o carimbo de data / Azure Stack está fora de memória disponível. Use o [Planejador de capacidade do Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para entender melhor a capacidade disponível para suas cargas de trabalho.

<!-- TBD - IS. ASDK --> 
- Ao executar o **teste AzureStack** cmdlet no ponto de extremidade com privilégios (PEP), o **o desempenho de instância de função do Azure Stack infraestrutura** teste irá gerar uma mensagem de aviso para a VM ERCS. Você pode ignorar a mensagem de aviso e continuar a usar o ASDK.

#### <a name="compute"></a>Computação
<!-- 2494144 - IS, ASDK --> 
- Ao selecionar um tamanho de máquina virtual para uma implantação de máquina virtual, alguns tamanhos de VM da série F não são visíveis como parte do seletor de tamanho quando você cria uma VM. Os seguintes tamanhos VM não aparecem no seletor de: *F8s_v2*, *F16s_v2*, *F32s_v2*, e *F64s_v2*.  
  Como alternativa, use um dos seguintes métodos para implantar uma VM. Em cada método, você precisará especificar o tamanho da VM que deseja usar.

- <!-- 3099544 – IS, ASDK --> Quando você cria uma nova máquina virtual (VM) usando o portal do Azure Stack, e você selecionar o tamanho da VM, a coluna USD por mês é exibida com um **indisponível** mensagem. Esta coluna não deve aparecer; exibindo a VM coluna preço não é suportada no Azure Stack.

- <!-- 2869209 – IS, ASDK --> Ao usar o [ **Add AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), você deve usar o **- OsUri** parâmetro como a conta de armazenamento em que o disco é carregado do URI. Se você usar o caminho local do disco, o cmdlet falhará com o seguinte erro: *operação de execução longa falhou com status 'Failed'*. 

- <!--  2966665 – IS, ASDK --> Anexar discos de dados do SSD para tamanho premium managed disk máquinas virtuais (DS, DSv2, Fs e Fs_V2) falhará com um erro: *Falha ao atualizar discos para a máquina virtual 'vmname' erro: operação solicitada não pode ser executada porque o tipo de conta de armazenamento ' Premium_LRS' não há suporte para o tamanho da VM ' Standard_DS/Ds_V2/FS/Fs_v2)*

   Para contornar esse problema, use *Standard_LRS* discos de dados em vez de *Premium_LRS discos*. Uso de *Standard_LRS* discos de dados não altera o custo de cobrança ou de IOPs.  

- <!--  2795678 – IS, ASDK --> Quando você usa o portal para criar máquinas virtuais (VM) em um tamanho VM premium (DS, Ds_v2, FS e FSv2), a VM é criada em uma conta de armazenamento padrão. Criação de uma conta de armazenamento padrão não afeta funcionalmente, IOPs, ou de cobrança. 

   Você pode ignorar com segurança o aviso que diz: *você optou por usar um disco padrão em um tamanho que dá suporte a discos premium. Isso pode afetar o desempenho do sistema operacional e não é recomendado. Considere usar o armazenamento premium (SSD).*

- <!-- 2967447 - IS, ASDK --> Experiência de criar o conjunto de dimensionamento de máquinas virtuais (VMSS) oferece a opção de implantação baseado em CentOS 7.2. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para sua implantação ou usar um modelo ARM especificando outra imagem do CentOS que foi baixada pelo operador antes da implantação do marketplace.

<!-- TBD -  IS ASDK --> 
- As configurações de escala para conjuntos de dimensionamento de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Devido às diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

<!-- TBD -  IS ASDK --> 
- Quando você cria máquinas virtuais no portal do usuário do Azure Stack, o portal exibe um número incorreto de discos de dados que podem anexar a uma VM da série D. Todas as séries D com suporte VMs podem acomodar a tantos discos de dados como a configuração do Azure.

<!-- TBD -  IS ASDK --> 
- Quando uma imagem de VM não consegue ser criado, um item com falha que não é possível excluir pode ser adicionado até a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado por meio do Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão de item com falha. Em seguida, 15 minutos após a criação da imagem fictícia, você pode excluí-la.

  Em seguida, você pode repetir o download da imagem da VM que falharam anteriormente.

<!-- TBD -  IS ASDK --> 
- Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

<!-- 1662991 - IS ASDK --> 
- Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico.

<!-- 2724961- IS ASDK --> 
- Quando você registra o **Microsoft.Insight** provedor de recursos em configurações de assinatura e criar uma VM do Windows com o convidado SO diagnóstico habilitado, a página de visão geral VM não mostra dados de métricas. 

 

#### <a name="networking"></a>Rede
<!-- 1766332 - IS, ASDK --> 
- Sob **Networking**, se você clicar em **criar o Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo de VPN. Não selecione essa opção. Somente o **com base em rota** opção tem suporte no Azure Stack.

<!-- 1902460 -  IS ASDK --> 
- O Azure Stack dá suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatário. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

<!-- 16309153 -  IS ASDK --> 
- Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automática*, alterar para uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.

<!-- 2702741 -  IS ASDK --> 
- IPs públicos que são implantados usando o método de alocação dinâmica não é garantido para ser preservada após a emissão de uma interrupção da desalocação.

<!-- 2529607 - IS ASDK --> 
- Durante o Azure Stack *rotação do segredo*, há um período em que os endereços IP públicos estão inacessíveis por dois a cinco minutos.

<!-- 2664148 - IS ASDK --> 
- Em cenários em que o locatário está acessando suas máquinas virtuais usando um túnel VPN S2S, podem ocorrer um cenário em que as tentativas de conexão falharem se a sub-rede local foi adicionada ao Gateway de rede Local depois de gateway já foi criado. 


#### <a name="sql-and-mysql"></a>SQL e MySQL
<!-- TBD - ASDK --> 
- O banco de dados de servidores de hospedagem deve ser dedicado para uso pelas cargas de trabalho de usuário e o provedor de recursos. Você não pode usar uma instância que está sendo usada por outro consumidor, incluindo serviços de aplicativos.

<!-- IS, ASDK --> 
- Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** nome quando você criar uma SKU para os provedores de recursos do SQL e MySQL.

#### <a name="app-service"></a>Serviço de Aplicativo
<!-- 2352906 - IS ASDK --> 
- Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

<!-- TBD - IS ASDK --> 
- Para escalar horizontalmente a infraestrutura (funcionários, gerenciamento, as funções front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.  

<!-- TBD - IS ASDK --> 
- O serviço de aplicativo só pode ser implantado para o *assinatura do provedor padrão* neste momento.

#### <a name="usage"></a>Uso  
<!-- TBD -  IS ASDK --> 
- Dados de medidor uso do endereço de IP público de uso mostram a mesma *EventDateTime* valor para cada registro, em vez da *TimeDate* carimbo que mostra que o registro foi criado. Atualmente, é possível usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.

<!-- #### Identity -->


## <a name="build-11807076"></a>Compilação 1.1807.0.76

### <a name="new-features"></a>Novos recursos
Esta compilação inclui os seguintes aprimoramentos e correções para o Azure Stack.  

- <!-- 1658937 | ASDK, IS --> **Iniciar backups em um agendamento previamente definido** -como um dispositivo do Azure Stack agora podem disparar automaticamente backups de infra-estrutura periodicamente para eliminar a intervenção humana. O Azure Stack limpará automaticamente o compartilhamento externo para backups mais antigos que o período de retenção definido. Para obter mais informações, consulte [habilitar o Backup para o Azure Stack com o PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).

- <!-- 2496385 | ASDK, IS -->  **Hora em que o tempo de backup total de transferência de dados adicionados.** Para obter mais informações, consulte [habilitar o Backup para o Azure Stack com o PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).

-   <!-- 1702130 | ASDK, IS -->  **De capacidade de backup externa agora mostra a capacidade correta do compartilhamento externo.** (Isso era difícil de código até 10 GB.) Para obter mais informações, consulte [habilitar o Backup para o Azure Stack com o PowerShell](.\.\azure-stack-backup-enable-backup-powershell.md).
 
- <!-- 2753130 |  IS, ASDK   -->  **Modelos do Gerenciador de recursos do Azure agora dão suporte ao elemento de condição** -agora você pode implantar um recurso em um modelo do Gerenciador de recursos do Azure usando uma condição. Você pode criar seu modelo para implantar um recurso com base em uma condição, como avaliar se um valor de parâmetro estiver presente. Para obter informações sobre como usar um modelo como uma condição, consulte [implantar condicionalmente um recurso](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) e [seção variáveis dos modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) na documentação do Azure. 

   Você também pode usar modelos para [implantar recursos em mais de uma assinatura ou grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

- <!--2753073 | IS, ASDK -->  **O suporte de versão do recurso Microsoft. Network API foi atualizado** para incluir suporte para a API versão 2017-10-01 de 2015-06-15 para recursos de rede do Azure Stack.  Suporte para versões de recurso entre 2017-10-01 e 2015-06-15 não está incluído nesta versão, mas será incluído em uma versão futura.  Consulte a [considerações sobre a rede do Azure Stack](.\.\user\azure-stack-network-differences.md) para diferenças de funcionalidade.

- <!-- 2272116 | IS, ASDK   -->  **O Azure Stack adicionou suporte para pesquisas inversas de DNS para pontos de extremidade de infraestrutura do Azure Stack com face externa** (que é para o portal, adminportal, gerenciamento e adminmanagement). Isso permite que os nomes de ponto de extremidade externo do Azure Stack ser resolvido a partir de um endereço IP.

- <!-- 2780899 |  IS, ASDK   --> **O Azure Stack agora dá suporte à adição de adaptadores de rede adicionais a uma VM existente.**  Essa funcionalidade está disponível por meio do portal, PowerShell e CLI. Para obter mais informações, consulte [adicionar ou remover adaptadores de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) na documentação do Azure. 

- <!-- 2222444 | IS, ASDK   -->  **Foram feitas melhorias na precisão e a resiliência para os medidores de uso de rede**. Medidores de uso de rede agora são mais precisos e levam em assinaturas de conta suspenso, pontos de interrupção e as condições de corrida.

- <!-- 2297790 | IS, ASDK -->  **Melhorias para o cliente do Azure Stack syslog (recurso de visualização)**. Esse cliente permite o encaminhamento de auditoria e logs relacionados à infraestrutura do Azure Stack para um syslog server ou segurança informações e eventos (SIEM) software de gerenciamento externo para o Azure Stack. O cliente de syslog agora oferece suporte o protocolo TCP com texto sem formatação ou criptografia de TLS 1.2, o segundo sendo a configuração padrão. Você pode configurar a conexão TLS com a autenticação somente do servidor ou mútua.

<!-- 2297790 - IS, ASDK --> 
- **O Azure Stack agora inclui um *Syslog* cliente** como um *versão prévia do recurso*. Esse cliente permite o encaminhamento de logs de auditoria e segurança relacionados à infraestrutura do Azure Stack para um Syslog server ou segurança informações e eventos (SIEM) software de gerenciamento que é externo ao Azure Stack. Atualmente, o cliente de Syslog só dá suporte a conexões de UDP não autenticadas através da porta padrão 514. A carga de cada mensagem do Syslog é formatada em comum (CEF) do formato de evento.

  Com essa visualização, você pode ver um número muito maior de auditorias e alertas. 

  Porque esse recurso ainda está em visualização, você não confiar em ambientes de produção.

  Para obter mais informações, consulte [encaminhamento de syslog do Azure Stack](.\.\azure-stack-integrate-security.md).

- <!-- ####### | IS, ASDK -->  **O Azure Resource Manager inclui o nome da região.** Com esta versão, os objetos recuperados do Azure Resource Manager agora incluirá o atributo de nome de região. Se um script PowerShell existente diretamente passa o objeto para outro cmdlet, o script pode produzir um erro e falha. Isso é o comportamento compatível com o Azure Resource Manager e exige que o cliente da chamada subtrair o atributo região. Para obter mais informações sobre o Azure Resource Manager, consulte [documentação do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

- <!-- TBD | IS, ASDK -->  **Mova assinaturas entre provedores de delegado.** Agora você pode mover assinaturas entre as assinaturas de provedor delegada novas ou existentes que pertencem ao mesmo locatário do diretório. As assinaturas que pertencem à assinatura do provedor padrão também podem ser movidas para as assinaturas de provedor delegada no mesmo locatário de diretório. Para obter mais informações, consulte [delegar ofertas no Azure Stack](.\.\azure-stack-delegated-provider.md).
 
- <!-- 2536808 IS ASDK --> **Aprimorada a hora de criação da VM** para VMs que são criadas com imagens, você pode baixar do marketplace do Azure.

### <a name="fixed-issues"></a>Problemas corrigidos

- <!-- TBD | ASDK, IS --> Várias melhorias foram feitas para o processo de atualização para torná-lo mais confiável. Além disso, foram feitas correções a infraestrutura subjacente, o que melhora a drenagem de nó, minimizando o tempo de inatividade potencial para cargas de trabalho durante a atualização.

-   <!--2292271 | ASDK, IS --> Corrigimos um problema em que um limite de cota modificado não foi aplicada a assinaturas existentes.  Agora, quando você gerar um limite de cota para um recurso de rede que faz parte de uma oferta e plano associado com uma assinatura de usuário, o novo limite se aplica ao assinaturas já existentes, bem como novas assinaturas.

- <!-- 2448955 | IS ASDK --> Agora você pode consultar com êxito os logs de atividades para sistemas que são implantados em um fuso horário de UTC + N.    

- <!-- 2319627 |  ASDK, IS --> Pré-verificação para parâmetros de configuração de backup (caminho/nome de usuário/senha/criptografia de chave) não define configurações incorretas para a configuração de backup. (Anteriormente, configurações incorretas foram definidas para o backup e backup, em seguida, falharia quando disparado).

- <!-- 2215948 |  ASDK, IS --> A lista de backup agora é atualizada quando você excluir manualmente o backup do compartilhamento externo.

- <!-- 2360715 |  ASDK, IS -->  Quando você configura a integração do datacenter, você não mais acessar o arquivo de metadados do AD FS de um compartilhamento. Para obter mais informações, consulte [configurar a integração do AD FS, fornecendo o arquivo de metadados de Federação](.\.\azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

- <!-- 2388980 | ASDK, IS --> Corrigimos um problema que impedia os usuários de atribuído um endereço IP público existente que foi anteriormente atribuído a uma Interface de rede ou um balanceador de carga para uma nova Interface de rede ou balanceador de carga.  

- <!-- 2551834 - IS, ASDK --> Quando você seleciona *visão geral* para uma conta de armazenamento nos portais de administrador ou usuário, o *Essentials* painel agora exibe todas as informações esperadas corretamente. 

- <!-- 2551834 - IS, ASDK --> Quando você seleciona *marcas* para uma conta de armazenamento nos portais de administrador ou usuário, as informações agora exibe corretamente.

- <!-- TBD - IS ASDK --> Esta versão do Azure Stack corrige o problema que impediu a aplicação das atualizações de driver do pacotes de extensão do OEM.

-   <!-- 2055809- IS ASDK --> Corrigimos um problema que impedia a exclusão de VMs na folha computação quando a VM não pôde ser criada.  

- <!--  2643962 IS ASDK -->  O alerta de *capacidade de memória de baixa* não é mais exibido incorretamente.

<!--  TBD ASDK --> 
- A máquina virtual que hospeda o ponto de extremidade de privilégio (PEP) aumentou para 4GB. ASDK, essa máquina virtual é chamada AzS-ERCS01.

- **Várias correções de** para desempenho, estabilidade, segurança e o sistema operacional que é usado pelo Azure Stack


<!-- ### Changes  -->
<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Problemas conhecidos

#### <a name="portal"></a>Portal
<!-- 2931230 – IS  ASDK --> 
- Os planos são adicionados a uma assinatura de usuário como um plano de complemento não podem ser excluídos, mesmo quando você remove o plano da assinatura do usuário. O plano permanecerá até que as assinaturas que o plano de complemento de referência também são excluídas. 

<!-- 2551834 - IS, ASDK --> 
- Quando você seleciona **visão geral** nos portais de administrador ou usuário, as informações da conta de armazenamento a *Essentials* painel não será exibido.  O painel do Essentials exibe informações sobre a conta, como seu *grupo de recursos*, *local*, e *ID da assinatura*.  Outras opções de visão geral são acessíveis como *serviços* e *monitoramento*, bem como opções para *abrir no Gerenciador de* ou *excluir conta de armazenamento* .  

  Para exibir as informações disponíveis, use o [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) cmdlet do PowerShell.

<!-- 2551834 - IS, ASDK --> 
- Quando você seleciona **marcas** para uma conta de armazenamento nos portais de administrador ou usuário, as informações de falha ao carregar e não será exibido.  

  Para exibir as informações disponíveis, use o [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) cmdlet do PowerShell.

<!-- TBD - IS ASDK --> 
- Não use os novos tipos de assinatura administrativos do *assinatura de medição*, e *assinatura de consumo*. Esses novos tipos de assinatura foram introduzidos com a versão 1804, mas ainda não estão prontos para uso. Você deve continuar a usar o *provedor padrão* tipo de assinatura.
  
<!-- TBD - IS ASDK --> 
- É possível aplicar as atualizações de driver por meio de um pacote de extensão de OEM com esta versão do Azure Stack.  Não há nenhuma solução alternativa para esse problema.
 
<!-- TBD - IS ASDK --> 
- A capacidade [para abrir uma nova solicitação de suporte na lista suspensa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do administrador do portal não está disponível. Em vez disso, use o seguinte link:     
    - Para o Kit de desenvolvimento do Azure Stack, use https://aka.ms/azurestackforum.    

<!-- 2403291 - IS ASDK --> 
- Você pode não ter o uso da barra de rolagem horizontal na parte inferior do portais do administrador e usuário. Se você não pode acessar a barra de rolagem horizontal, use a trilha para navegar até uma folha anterior no portal, selecionando o nome da folha você quer exibir na lista de navegação estrutural na parte superior esquerda do portal.
  ![Navegação estrutural](media/asdk-release-notes/breadcrumb.png)

<!-- TBD -  IS ASDK --> 
- Excluindo resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos de usuário ou grupo de recursos inteiro e, em seguida, exclua as assinaturas do usuário.

<!-- TBD -  IS ASDK --> 
- Você não pode exibir as permissões para sua assinatura usando os portais do Azure Stack. Como alternativa, use o PowerShell para verificar permissões.

- <!--  TBD | ASDK -->  O fuso horário de padrão para sua implantação do Azure Stack agora será obter definido como UTC. Você pode selecionar um fuso horário ao instalar o Azure Stack, no entanto, ela será revertida automaticamente para UTC como padrão durante a instalação.

#### <a name="health-and-monitoring"></a>Monitoramento e integridade
<!-- 1264761 - IS ASDK -->  
- Você pode ver os alertas para o *controlador de integridade* componente que tem os seguintes detalhes:  

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

  Ambos os alertas podem ser ignorados com segurança e serão fechada automaticamente ao longo do tempo.  

<!-- 2368581 - IS. ASDK --> 
- Um operador do Azure Stack, se você receber um alerta de memória insuficiente e máquinas virtuais de locatário não conseguir implantar com um *erro de criação de VM do Fabric*, é possível que o carimbo de data / Azure Stack está fora de memória disponível. Use o [Planejador de capacidade do Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para entender melhor a capacidade disponível para suas cargas de trabalho.

<!-- TBD - IS. ASDK --> 
- Ao executar o cmdlet Test-AzureStack no ponto de extremidade de privilégio (PEP), irá gerar uma mensagem de aviso para a VM ERCS. Você pode continuar a usar o ASDK. 


#### <a name="compute"></a>Computação
<!-- TBD - IS, ASDK --> 
- Ao selecionar um tamanho de máquina virtual para uma implantação de máquina virtual, alguns tamanhos de VM da série F não são visíveis como parte do seletor de tamanho quando você cria uma VM. Os seguintes tamanhos VM não aparecem no seletor de: *F8s_v2*, *F16s_v2*, *F32s_v2*, e *F64s_v2*.  
  Como alternativa, use um dos seguintes métodos para implantar uma VM. Em cada método, você precisará especificar o tamanho da VM que deseja usar.

  - **Modelo do Gerenciador de recursos do Azure:** quando você usa um modelo, defina a *vmSize* no modelo para ser igual ao tamanho VM que você deseja usar. Por exemplo, a seguinte entrada é usada para implantar uma VM que usa o *F32s_v2* tamanho:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **CLI do Azure:** você pode usar o [criar vm az](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) comando e especifique o tamanho da VM como um parâmetro, semelhante ao `--size "Standard_F32s_v2"`.

  - **PowerShell:** com o PowerShell, você pode usar [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) com o parâmetro que especifica o tamanho da VM, semelhante ao `-VMSize "Standard_F32s_v2"`.


<!-- TBD -  IS ASDK --> 
- As configurações de escala para conjuntos de dimensionamento de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Devido às diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

<!-- TBD -  IS ASDK --> 
- Quando você cria máquinas virtuais no portal do usuário do Azure Stack, o portal exibe um número incorreto de discos de dados que podem anexar a uma VM da série D. Todas as séries D com suporte VMs podem acomodar a tantos discos de dados como a configuração do Azure.

<!-- TBD -  IS ASDK --> 
- Quando uma imagem de VM não consegue ser criado, um item com falha que não é possível excluir pode ser adicionado até a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado por meio do Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão de item com falha. Em seguida, 15 minutos após a criação da imagem fictícia, você pode excluí-la.

  Em seguida, você pode repetir o download da imagem da VM que falharam anteriormente.

<!-- TBD -  IS ASDK --> 
- Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

<!-- 1662991 - IS ASDK --> 
- Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico.

#### <a name="networking"></a>Rede
<!-- TBD - IS ASDK --> 
- É possível criar rotas definidas pelo usuário no portal do administrador ou usuário. Como alternativa, use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

<!-- 1766332 - IS, ASDK --> 
- Sob **Networking**, se você clicar em **criar o Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo de VPN. Não selecione essa opção. Somente o **com base em rota** opção tem suporte no Azure Stack.

<!-- 2388980 -  IS ASDK --> 
- Depois que uma VM é criada e associada com um endereço IP público, você não pode desassociar dessa VM para esse endereço IP. Dissociação parece funcionar, mas o endereço IP público atribuído anteriormente permanece associado com a VM original.

  Atualmente, você deve usar somente novos endereços IP públicos para novas VMs que você cria.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tenta se conectar por meio desse resultado de endereço IP em uma conexão para a VM original e não para o novo.

<!-- 2292271 - IS ASDK --> 
- Se você aumentar um limite de cota para um recurso de rede que faz parte de uma oferta e o plano que está associado uma assinatura de locatário, o novo limite não é aplicado a essa assinatura. No entanto, o novo limite se aplica às novas assinaturas que são criadas depois que a cota for aumentada.

  Para contornar esse problema, use um plano de complemento para aumentar a cota de rede quando o plano já está associado uma assinatura. Para obter mais informações, consulte como [disponibilizar um plano de complemento](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

<!-- 2304134 IS ASDK --> 
- Você não pode excluir uma assinatura que tem recursos de zona DNS ou recursos de tabela de rotas associados a ele. Para excluir com êxito a assinatura, primeiro você deve excluir os recursos de zona DNS e a tabela de rotas da assinatura do locatário.

<!-- 1902460 -  IS ASDK --> 
- O Azure Stack dá suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatário. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

<!-- 16309153 -  IS ASDK --> 
- Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automática*, alterar para uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.

<!-- TBD -  IS ASDK --> 
- O Azure Stack não dá suporte para adicionar outros adaptadores de rede a uma instância VM depois que a VM é implantada. Se a máquina virtual exigir mais de uma interface de rede, deve ser definidos no momento da implantação.


#### <a name="sql-and-mysql"></a>SQL e MySQL
<!-- TBD - ASDK --> 
- O banco de dados de servidores de hospedagem deve ser dedicado para uso pelas cargas de trabalho de usuário e o provedor de recursos. Você não pode usar uma instância que está sendo usada por outro consumidor, incluindo serviços de aplicativos.

<!-- IS, ASDK --> 
- Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** nome quando você criar uma SKU para os provedores de recursos do SQL e MySQL.

#### <a name="app-service"></a>Serviço de Aplicativo
<!-- 2352906 - IS ASDK --> 
- Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

<!-- TBD - IS ASDK --> 
- Para escalar horizontalmente a infraestrutura (funcionários, gerenciamento, as funções front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.  

<!-- TBD - IS ASDK --> 
- O serviço de aplicativo só pode ser implantado para o *assinatura do provedor padrão* neste momento. <!-- In a future update, App Service will deploy into the new *Metering subscription* that was introduced in Azure Stack 1804. When Metering is supported for use, all existing deployments will be migrated to this new subscription type. -->

#### <a name="usage"></a>Uso  
<!-- TBD -  IS ASDK --> 
- Dados de medidor uso do endereço de IP público de uso mostram a mesma *EventDateTime* valor para cada registro, em vez da *TimeDate* carimbo que mostra que o registro foi criado. Atualmente, é possível usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.

<!-- #### Identity -->
