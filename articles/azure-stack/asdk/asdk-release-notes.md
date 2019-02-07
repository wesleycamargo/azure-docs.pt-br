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
ms.date: 12/21/2018
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 12/21/2018
ms.openlocfilehash: c60ba4f4106ddd0c3fc643288894fb55d3d27f8c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816451"
---
# <a name="asdk-release-notes"></a>Notas de versão ASDK 
 
Este artigo fornece informações sobre problemas conhecidos no Azure Stack desenvolvimento ASDK (Kit), correções e aprimoramentos. Se você não tiver certeza de qual versão você está executando, você poderá [usar o portal para verificar](../azure-stack-updates.md#determine-the-current-version).

> Mantenha-se atualizado com o que há de novo no ASDK assinando o [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-118110101"></a>Compilação 1.1811.0.101

### <a name="new-features"></a>Novos recursos

Esta compilação inclui os seguintes aprimoramentos e correções para o Azure Stack:  

- Há um conjunto de novo hardware mínimo e recomendado e requisitos de software para o ASDK. Estas novas recomendadas especificações estão documentadas em [considerações de planejamento de implantação do Azure Stack](asdk-deploy-considerations.md). Como a plataforma do Azure Stack evoluiu, mais serviços agora estão disponíveis e podem ser necessários mais recursos. As especificações de aumento refletem essas recomendações revisadas.

### <a name="fixed-issues"></a>Problemas corrigidos

<!-- TBD - IS ASDK --> 
- Corrigido um problema em que os dados de medição do uso de endereço IP públicos mostraram o mesmo **EventDateTime** valor para cada registro, em vez da **TimeDate** carimbo que mostra que o registro foi criado. Agora você pode usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.

<!-- 3099544 – IS, ASDK --> 
- Corrigido um problema que ocorreu durante a criação de uma nova máquina virtual (VM) usando o portal do Azure Stack. Selecionar o tamanho da VM causou a coluna USD por mês exibir uma **indisponível** mensagem. Esta coluna não aparece mais; exibindo a VM coluna preço não é suportada no Azure Stack.

<!-- 2930718 - IS ASDK --> 
- Corrigido um problema em que o portal do administrador, ao acessar os detalhes de qualquer assinatura de usuário, depois de fechar a folha e clicando em **recentes**, o nome da assinatura de usuário não foram exibidos. O nome de usuário da assinatura agora é exibida.

<!-- 3060156 - IS ASDK --> 
- Corrigido um problema em portais de administrador e o usuário: clicar em configurações do portal e selecionando **excluir todas as configurações e painéis privados** não funcionou conforme o esperado e uma notificação de erro foi exibida. 

<!-- 2930799 - IS ASDK --> 
- Corrigido um problema em portais de administrador e o usuário: em **todos os serviços**, o ativo **planos de proteção contra DDoS** foi listado incorretamente, embora não esteja disponível no Azure Stack.
 
<!--2760466 – IS  ASDK --> 
- Corrigido um problema que ocorreu quando você instalou um novo ambiente do Azure Stack no qual o alerta que indica **ativação necessária** não é exibido. Agora exiba corretamente.

<!--1236441 – IS  ASDK --> 
- Corrigido um problema que impedia a aplicação de políticas RBAC a um grupo de usuários ao usar o ADFS.

<!--3463840 - IS, ASDK --> 
- Correção do problema com backups de infraestrutura com falha por servidor de arquivos acessível da rede VIP pública. Essa correção volta o serviço de backup de infraestrutura à rede de infraestrutura pública. Se você aplicou o hotfix mais recente do Azure Stack para 1809 que resolve esse problema, a atualização 1811 não fará as modificações adicionais. 

<!-- 2967387 – IS, ASDK --> 
- Corrigido um problema em que a conta usada para entrar no portal de administrador ou usuário do Azure Stack é exibido como **usuário não identificado**. Esta mensagem foi exibida quando a conta não tem qualquer um uma *primeira* ou *última* nome especificado.   

<!--  2873083 - IS ASDK --> 
- Corrigido um problema no qual o portal para criar uma máquina virtual usando o conjunto de dimensionamento de (VMSS), o *tamanho da instância* lista suspensa não foi carregado corretamente ao usar o Internet Explorer. Este navegador agora funciona corretamente.  

<!-- 3190553 - IS ASDK -->
- Corrigido um problema que geraram alertas barulhentas que indica que uma instância de função de infraestrutura não estava disponível ou nó de unidade de escala estava offline.

### <a name="known-issues"></a>Problemas conhecidos

#### <a name="portal"></a>Portal  

<!-- 2930820 - IS ASDK --> 
- Em portais de administrador e o usuário, se você procurar por "Docker", o item incorretamente é retornado. Ele não está disponível no Azure Stack. Se você tentar criá-la, uma folha com uma indicação de erro é exibida. 

<!-- 2931230 – IS  ASDK --> 
- Os planos são adicionados a uma assinatura de usuário como um plano de complemento não podem ser excluídos, mesmo quando você remove o plano da assinatura do usuário. O plano permanecerá até que as assinaturas que o plano de complemento de referência também são excluídas. 

<!-- TBD - IS ASDK --> 
- Os dois tipos de assinatura administrativos que foram introduzidos com a versão 1804 não devem ser usados. Os tipos de assinatura são **assinatura de medição**, e **assinatura de consumo**. Esses tipos de assinatura são visíveis em novos ambientes Azure Stack, começando com a versão 1804, mas ainda não estão prontos para uso. Você deve continuar a usar o **provedor padrão** tipo de assinatura.

<!-- TBD - IS ASDK --> 
- Excluindo resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos de usuário ou grupo de recursos inteiro e, em seguida, exclua as assinaturas do usuário.

<!-- TBD - IS ASDK --> 
- Você não pode exibir as permissões para sua assinatura usando os portais do Azure Stack. Como alternativa, use o PowerShell para verificar permissões.

#### <a name="compute"></a>Computação 

<!-- 3235634 – IS, ASDK -->
- Implantar VMs com tamanhos que contém um **v2** sufixo; por exemplo, **Standard_A2_v2**, especifique o sufixo como **Standard_A2_v2** (v minúsculo). Não use **Standard_A2_V2** (V maiusculo). Isso funciona no Azure global e uma inconsistência no Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Ao usar o [ **Add AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), você deve usar o **- OsUri** parâmetro como a conta de armazenamento em que o disco é carregado do URI. Se você usar o caminho local do disco, o cmdlet falhar com o seguinte erro: *Operação de execução longa falhou com status 'Failed'*. 

<!--  2795678 – IS, ASDK --> 
- Quando você usa o portal para criar máquinas virtuais (VM) em um tamanho VM premium (DS, Ds_v2, FS e FSv2), a VM é criada em uma conta de armazenamento padrão. Criação de uma conta de armazenamento padrão não afeta funcionalmente, IOPs, ou de cobrança. Você pode ignorar com segurança o aviso que diz: **Você optou por usar um disco padrão em um tamanho que dá suporte a discos premium. Isso pode afetar o desempenho do sistema operacional e não é recomendado. Considere usar o armazenamento premium (SSD).**

<!-- 2967447 - IS, ASDK --> 
- A experiência de criação de conjunto (VMSS) de escala de máquina virtual oferece a opção para implantação baseada em CentOS 7.2. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para sua implantação ou usar um modelo do Azure Resource Manager especificando outra imagem do CentOS que foi baixada pelo operador antes da implantação do marketplace.  

<!-- TBD - IS ASDK --> 
- Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

<!-- 1662991 IS ASDK --> 
- Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico.  

<!-- 2724961- IS ASDK --> 
- Quando você registra o **Microsoft.Insight** provedor de recursos em configurações de assinatura e criar uma VM do Windows com o convidado SO diagnóstico habilitado, o gráfico de percentual de CPU na página de visão geral de VM não mostra dados de métricas.

   Para localizar os dados de métricas, como o gráfico de percentual de CPU para a VM, vá para a janela de métricas e mostrar todos os as métricas com suporte VM Windows convidado.

<!-- 3507629 - IS, ASDK --> 
- Discos gerenciados cria dois novos [tipos de cota de computação](../azure-stack-quota-types.md#compute-quota-types) para limitar a capacidade máxima de discos gerenciados podem ser provisionados. Por padrão, 2048 GiB é alocado para cada tipo de cota de discos gerenciados. No entanto, você pode encontrar os seguintes problemas:

   - Cotas de criados antes da atualização 1808, a cota de Managed Disks mostra valores 0 no portal do administrador, apesar de 2048 GiB é alocada. Você pode aumentar ou diminuir o valor com base em suas necessidades reais e recentemente defina o valor de cota substitui o padrão de GiB de 2048.
   - Se você atualizar o valor da cota para 0, é equivalente ao valor padrão de 2048 GiB. Como alternativa, defina o valor da cota como 1.

<!-- TBD - IS ASDK --> 
- Depois de aplicar o 1811, atualização, você pode encontrar os seguintes problemas ao implantar VMs com discos gerenciados:

   1. Se a assinatura foi criada antes da atualização 1808, implantando uma VM com discos gerenciados pode falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas para cada assinatura:
      1. No portal do locatário, vá para **assinaturas** e localize a assinatura. Clique em **provedores de recursos**, em seguida, clique em **Microsoft. Compute**e, em seguida, clique em **registrar novamente**.
      2. Sob a mesma assinatura, vá para **controle de acesso (IAM)**, verifique se **do Azure Stack – Managed Disk** está listado.
   2. Se você tiver configurado um ambiente multilocatário, implantar as VMs em uma assinatura associada a um diretório de convidado pode falhar com uma mensagem de erro interno. Para resolver o erro, siga estas etapas no [deste artigo](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) para reconfigurar a cada um dos seus diretórios de convidado.

#### <a name="networking"></a>Rede

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

#### <a name="app-service"></a>Serviço de Aplicativo

<!-- 2352906 - IS ASDK --> 
- Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

<!-- #### Usage -->  

<!-- #### Identity -->

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

- <!-- 2702741 -  IS, ASDK --> Corrigido um problema no qual IPs públicos que foram implantados usando a alocação dinâmica o método não eram garantia de ser preservada após a emissão de uma interrupção da desalocação. Agora são preservados.

- <!-- 3078022 - IS, ASDK --> Se uma máquina virtual foi interrompida e desalocada antes 1808 pode não ser realocada após a atualização 1808.  Esse problema foi corrigido no 1809. Instâncias que foram nesse estado e não pôde ser iniciadas podem ser iniciadas no 1809 com essa correção. A correção também impede que esse problema ocorra.

<!-- TBD -  IS ASDK --> 
- Quando uma imagem de VM não consegue ser criado, um item com falha que não é possível excluir pode ser adicionado até a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado por meio do Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão de item com falha. Em seguida, 15 minutos após a criação da imagem fictícia, você pode excluí-la.

  Em seguida, você pode repetir o download da imagem da VM que falharam anteriormente.

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
   - NOME:  Função de infraestrutura não íntegro
   - GRAVIDADE: Aviso
   - COMPONENTE: Controlador de integridade
   - DESCRIÇÃO: O Scanner de pulsação do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.  

  #2 do alerta:
   - NOME:  Função de infraestrutura não íntegro
   - GRAVIDADE: Aviso
   - COMPONENTE: Controlador de integridade
   - DESCRIÇÃO: O Scanner de falha do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.

  Ambos os alertas podem ser ignorados com segurança e serão fechada automaticamente ao longo do tempo.  

<!-- 2368581 - IS. ASDK --> 
- Um operador do Azure Stack, se você receber um alerta de memória insuficiente e máquinas virtuais de locatário não conseguir implantar com um *erro de criação de VM do Fabric*, é possível que o carimbo de data / Azure Stack está fora de memória disponível. Use o [Planejador de capacidade do Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para entender melhor a capacidade disponível para suas cargas de trabalho.


#### <a name="compute"></a>Computação 

<!-- 3164607 – IS, ASDK -->
- Tornar a anexar um disco desanexado a mesma máquina virtual (VM) com o mesmo nome e o LUN falhará com um erro como **não é possível anexar o disco de dados 'datadisk' à VM 'vm1'**. O erro ocorre porque o disco está sendo desanexado atualmente ou a última Falha na operação de desanexação. Aguarde até que o disco está completamente desanexado e tente novamente ou exclua/Desanexe o disco explicitamente novamente. A solução alternativa é anexá-lo novamente com um nome diferente ou em um LUN diferente. 

<!-- 3235634 – IS, ASDK -->
- Implantar VMs com tamanhos que contém um **v2** sufixo; por exemplo, **Standard_A2_v2**, especifique o sufixo como **Standard_A2_v2** (v minúsculo). Não use **Standard_A2_V2** (V maiusculo). Isso funciona no Azure global e uma inconsistência no Azure Stack.

<!-- 3099544 – IS, ASDK --> 
- Quando você cria uma nova máquina virtual (VM) usando o portal do Azure Stack, e você selecionar o tamanho da VM, a coluna USD por mês é exibida com um **indisponível** mensagem. Esta coluna não deve aparecer; exibindo a VM coluna preço não é suportada no Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Ao usar o [ **Add AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), você deve usar o **- OsUri** parâmetro como a conta de armazenamento em que o disco é carregado do URI. Se você usar o caminho local do disco, o cmdlet falhar com o seguinte erro: *Operação de execução longa falhou com status 'Failed'*. 

<!--  2966665 – IS, ASDK --> 
- SSD de anexar discos de dados de tamanho premium gerenciado disco máquinas virtuais (DS, DSv2, Fs e Fs_V2) falhará com um erro:  *Falha ao atualizar discos para a máquina virtual 'vmname' erro: Operação solicitada não pode ser executada porque o tipo de conta de armazenamento 'Premium_LRS' não é suportado para tamanho de VM ' Standard_DS/Ds_V2/FS/Fs_v2)*

   Para contornar esse problema, use *Standard_LRS* discos de dados em vez de *Premium_LRS discos*. Uso de *Standard_LRS* discos de dados não altera o custo de cobrança ou de IOPs.  

<!--  2795678 – IS, ASDK --> 
- Quando você usa o portal para criar máquinas virtuais (VM) em um tamanho VM premium (DS, Ds_v2, FS e FSv2), a VM é criada em uma conta de armazenamento padrão. Criação de uma conta de armazenamento padrão não afeta funcionalmente, IOPs, ou de cobrança. 

   Você pode ignorar com segurança o aviso que diz: *Você optou por usar um disco padrão em um tamanho que dá suporte a discos premium. Isso pode afetar o desempenho do sistema operacional e não é recomendado. Considere usar o armazenamento premium (SSD).*

<!-- 2967447 - IS, ASDK --> 
- Experiência de criar o conjunto de dimensionamento de máquinas virtuais (VMSS) oferece a opção de implantação baseado em CentOS 7.2. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para sua implantação ou usar um modelo do Azure Resource Manager especificando outra imagem do CentOS que foi baixada pelo operador antes da implantação do marketplace.

<!-- TBD -  IS ASDK --> 
- As configurações de escala para conjuntos de dimensionamento de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Devido às diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.



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
- **Iniciar backups em um agendamento previamente definido** -como um dispositivo do Azure Stack agora podem disparar automaticamente backups de infra-estrutura periodicamente para eliminar a intervenção humana. O Azure Stack limpará automaticamente o compartilhamento externo para backups mais antigos que o período de retenção definido. Para obter mais informações, consulte [habilitar o Backup para o Azure Stack com o PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 2496385 | ASDK, IS -->  
- **Hora em que o tempo de backup total de transferência de dados adicionados.** Para obter mais informações, consulte [habilitar o Backup para o Azure Stack com o PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 1702130 | ASDK, IS --> 
- **De capacidade de backup externa agora mostra a capacidade correta do compartilhamento externo.** (Isso era difícil de código até 10 GB.) Para obter mais informações, consulte [habilitar o Backup para o Azure Stack com o PowerShell](../azure-stack-backup-enable-backup-powershell.md).
 
<!-- 2753130 |  IS, ASDK   -->  
- **Modelos do Gerenciador de recursos do Azure agora dão suporte ao elemento de condição** -agora você pode implantar um recurso em um modelo do Gerenciador de recursos do Azure usando uma condição. Você pode criar seu modelo para implantar um recurso com base em uma condição, como avaliar se um valor de parâmetro estiver presente. Para obter informações sobre como usar um modelo como uma condição, consulte [implantar condicionalmente um recurso](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) e [seção variáveis dos modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) na documentação do Azure. 

   Você também pode usar modelos para [implantar recursos em mais de uma assinatura ou grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

<!--2753073 | IS, ASDK -->  
- **O suporte de versão do recurso Microsoft. Network API foi atualizado** para incluir suporte para a API versão 2017-10-01 de 2015-06-15 para recursos de rede do Azure Stack. Suporte para versões de recurso entre 2017-10-01 e 2015-06-15 não está incluído nesta versão. Consulte a [considerações sobre a rede do Azure Stack](../user/azure-stack-network-differences.md) para diferenças de funcionalidade.

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

- <!-- IS, ASDK --> **Conjunto de dimensionamento de máquina virtual dimensionamento**.  Você pode usar o portal para [escalar um conjunto de dimensionamento de máquinas virtuais](../azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).   

- <!-- 2489570 | IS ASDK--> **Suporte para configurações de política de IPSec/IKE personalizadas** para [gateways de VPN no Azure Stack](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways).

- <!-- | IS ASDK--> **Item do marketplace Kubernetes**. Agora você pode implantar um cluster Kubernetes usando o [item do Marketplace do Kubernetes](/azure/azure-stack/azure-stack-solution-template-kubernetes-cluster-add). Os usuários podem selecionar o item de Kubernetes e preencha alguns parâmetros ao implantar um cluster Kubernetes no Azure Stack. A finalidade dos modelos é tornar isso simples para os usuários para implantações de Kubernetes de desenvolvimento/teste de instalação em poucas etapas.

<!-- ####### | IS, ASDK -->  
- **O Azure Resource Manager inclui o nome da região.** Com esta versão, os objetos recuperados do Azure Resource Manager agora incluirá o atributo de nome de região. Se um script PowerShell existente diretamente passa o objeto para outro cmdlet, o script pode produzir um erro e falha. Isso é o comportamento compatível com o Azure Resource Manager e exige que o cliente da chamada subtrair o atributo região. Para obter mais informações sobre o Azure Resource Manager, consulte [documentação do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

<!-- TBD | IS, ASDK -->  
- **Mova assinaturas entre provedores de delegado.** Agora você pode mover assinaturas entre as assinaturas de provedor delegada novas ou existentes que pertencem ao mesmo locatário do diretório. As assinaturas que pertencem à assinatura do provedor padrão também podem ser movidas para as assinaturas de provedor delegada no mesmo locatário de diretório. Para obter mais informações, consulte [delegar ofertas no Azure Stack](../azure-stack-delegated-provider.md).
 
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
- Quando você configura a integração do datacenter, você não mais acessar o arquivo de metadados do AD FS de um compartilhamento. Para obter mais informações, consulte [configurar a integração do AD FS, fornecendo o arquivo de metadados de Federação](../azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

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

- <!--  TBD – IS, ASDK --> *Um básico* tamanhos de máquina virtual estão desativados para [criando conjuntos de dimensionamento de máquina virtual](../azure-stack-compute-add-scalesets.md) (VMSS) por meio do portal. Para criar um VMSS com esse tamanho, use o PowerShell ou um modelo. 

### <a name="known-issues"></a>Problemas conhecidos

#### <a name="portal"></a>Portal  
- <!-- 2967387 – IS, ASDK --> A conta usada para entrar no portal de administrador ou usuário do Azure Stack é exibido como **usuário não identificado**. Isso ocorre quando a conta não tenha um uma *primeira* ou *última* nome especificado. Para contornar esse problema, edite a conta de usuário para fornecer o primeiro ou último nome. Você deve, em seguida, saia e entre novamente no portal. 

-  <!--  2873083 - IS ASDK --> Ao usar o portal para criar uma escala de máquina virtual definido (VMSS), o *tamanho da instância* suspenso não carrega corretamente quando você usa o Internet Explorer. Para contornar esse problema, use outro navegador ao usar o portal para criar um VMSS.  

#### <a name="portal"></a>Portal  
<!-- 2931230 – IS  ASDK --> 
- Os planos são adicionados a uma assinatura de usuário como um plano de complemento não podem ser excluídos, mesmo quando você remove o plano da assinatura do usuário. O plano permanecerá até que as assinaturas que o plano de complemento de referência também são excluídas. 

<!--2760466 – IS  ASDK --> 
- Quando você instala um novo ambiente do Azure Stack que executa esta versão, o alerta indicar *ativação necessária* podem não ser exibidos. [Ativação](../azure-stack-registration.md) é necessária antes que você pode usar a distribuição do marketplace. 

<!-- TBD - IS ASDK --> 
- Os dois tipos de assinatura administrativos que foram introduzidos com a versão 1804 não devem ser usados. Os tipos de assinatura são **assinatura de medição**, e **assinatura de consumo**. Esses tipos de assinatura são **assinatura de medição**, e **assinatura de consumo**. Esses tipos de assinatura são visíveis em novos ambientes Azure Stack, começando com a versão 1804, mas ainda não estão prontos para uso. Você deve continuar a usar o **assinatura do provedor padrão** tipo.

<!-- 2403291 - IS ASDK --> 
- Você pode não ter o uso da barra de rolagem horizontal na parte inferior do portais do administrador e usuário. Se você não pode acessar a barra de rolagem horizontal, use a trilha para navegar até uma folha anterior no portal, selecionando o nome da folha você quer exibir na lista de navegação estrutural na parte superior esquerda do portal.
  ![Breadcrumb](media/asdk-release-notes/breadcrumb.png)

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
   - NOME:  Função de infraestrutura não íntegro
   - GRAVIDADE: Aviso
   - COMPONENTE: Controlador de integridade
   - DESCRIÇÃO: O Scanner de pulsação do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.  

  #2 do alerta:
   - NOME:  Função de infraestrutura não íntegro
   - GRAVIDADE: Aviso
   - COMPONENTE: Controlador de integridade
   - DESCRIÇÃO: O Scanner de falha do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.

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

- <!-- 2869209 – IS, ASDK --> Ao usar o [ **Add AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0), você deve usar o **- OsUri** parâmetro como a conta de armazenamento em que o disco é carregado do URI. Se você usar o caminho local do disco, o cmdlet falhar com o seguinte erro: *Operação de execução longa falhou com status 'Failed'*. 

- <!--  2966665 – IS, ASDK --> SSD de anexar discos de dados de tamanho premium gerenciado disco máquinas virtuais (DS, DSv2, Fs e Fs_V2) falhará com um erro:  *Falha ao atualizar discos para a máquina virtual 'vmname' erro: Operação solicitada não pode ser executada porque o tipo de conta de armazenamento 'Premium_LRS' não é suportado para tamanho de VM ' Standard_DS/Ds_V2/FS/Fs_v2)*

   Para contornar esse problema, use *Standard_LRS* discos de dados em vez de *Premium_LRS discos*. Uso de *Standard_LRS* discos de dados não altera o custo de cobrança ou de IOPs.  

- <!--  2795678 – IS, ASDK --> Quando você usa o portal para criar máquinas virtuais (VM) em um tamanho VM premium (DS, Ds_v2, FS e FSv2), a VM é criada em uma conta de armazenamento padrão. Criação de uma conta de armazenamento padrão não afeta funcionalmente, IOPs, ou de cobrança. 

   Você pode ignorar com segurança o aviso que diz: *Você optou por usar um disco padrão em um tamanho que dá suporte a discos premium. Isso pode afetar o desempenho do sistema operacional e não é recomendado. Considere usar o armazenamento premium (SSD).*

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

