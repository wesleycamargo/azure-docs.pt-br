---
title: Atualização da pilha do Azure 1807 | Microsoft Docs
description: Saiba mais sobre quais são as novidades na atualização 1807 para sistemas integrados do Azure Stack, incluindo os problemas conhecidos e onde baixar a atualização.
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
ms.date: 10/07/2018
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 10/07/2018
ms.openlocfilehash: 2b36c5d31e9377335a9cc8a37d4e4e0e890bfc3b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245076"
---
# <a name="azure-stack-1807-update"></a>Atualização de 1807 de pilha do Azure

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo descreve o conteúdo do pacote de atualização de 1807. Esta atualização inclui aprimoramentos, correções e problemas conhecidos nesta versão do Azure Stack e onde baixar a atualização. Problemas conhecidos são divididos em questões relacionadas diretamente para o processo de atualização e os problemas com a compilação (após a instalação).

> [!IMPORTANT]  
> Este pacote de atualização é somente para os sistemas integrados do Azure Stack. Não aplique esse pacote de atualização para o Kit de desenvolvimento do Azure Stack.

## <a name="build-reference"></a>Referência de build

É o número de compilação de atualização do Azure Stack 1807 **1.1807.0.76**.  

### <a name="new-features"></a>Novos recursos

Esta atualização inclui os seguintes aprimoramentos para o Azure Stack.

<!-- 1658937 | ASDK, IS --> 
- **Iniciar backups em um agendamento previamente definido** -como um dispositivo do Azure Stack agora podem disparar automaticamente backups de infra-estrutura periodicamente para eliminar a intervenção humana. O Azure Stack limpará automaticamente o compartilhamento externo para backups mais antigos que o período de retenção definido. Para obter mais informações, consulte [habilitar o Backup para o Azure Stack com o PowerShell](azure-stack-backup-enable-backup-powershell.md).

<!-- 2496385 | ASDK, IS -->  
- **Hora em que o tempo de backup total de transferência de dados adicionados.** Para obter mais informações, consulte [habilitar o Backup para o Azure Stack com o PowerShell](azure-stack-backup-enable-backup-powershell.md).

<!-- 1702130 | ASDK, IS -->  
- **De capacidade de backup externa agora mostra a capacidade correta do compartilhamento externo.** (Isso era difícil de código até 10 GB.) Para obter mais informações, consulte [habilitar o Backup para o Azure Stack com o PowerShell](azure-stack-backup-enable-backup-powershell.md).

<!-- 2508488 |  IS   -->
- **Expandir a capacidade** pela [Adicionando nós de unidade de escala adicional](azure-stack-add-scale-node.md).

<!-- 2753130 |  IS, ASDK   -->  
- **Modelos do Gerenciador de recursos do Azure agora dão suporte ao elemento de condição** -agora você pode implantar um recurso em um modelo do Gerenciador de recursos do Azure usando uma condição. Você pode criar seu modelo para implantar um recurso com base em uma condição, como avaliar se um valor de parâmetro estiver presente. Para obter informações sobre como usar um modelo como uma condição, consulte [implantar condicionalmente um recurso](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) e [seção variáveis dos modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) na documentação do Azure. 

   Você também pode usar modelos para [implantar recursos em mais de uma assinatura ou grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

<!--2753073 | IS, ASDK -->  
- **O suporte de versão do recurso Microsoft. Network API foi atualizado** para incluir suporte para a API versão 2017-10-01 de 2015-06-15 para recursos de rede do Azure Stack.  Suporte para versões de recurso entre 2017-10-01 e 2015-06-15 não está incluído nesta versão.  Consulte a [considerações sobre a rede do Azure Stack](user/azure-stack-network-differences.md) para diferenças de funcionalidade.

<!-- 2272116 | IS, ASDK   -->  
- **O Azure Stack adicionou suporte para pesquisas inversas de DNS para pontos de extremidade de infraestrutura do Azure Stack com face externa** (que é para o portal, adminportal, gerenciamento e adminmanagement). Isso permite que os nomes de ponto de extremidade externo do Azure Stack ser resolvido a partir de um endereço IP.

<!-- 2780899 |  IS, ASDK   --> 
- **O Azure Stack agora dá suporte à adição de adaptadores de rede adicionais a uma VM existente.**  Essa funcionalidade está disponível por meio do portal, PowerShell e CLI. Para obter mais informações, consulte [adicionar ou remover adaptadores de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) na documentação do Azure. 

<!-- 2222444 | IS, ASDK   -->  
- **Foram feitas melhorias na precisão e a resiliência para os medidores de uso de rede**.  Medidores de uso de rede agora são mais precisos e levam em assinaturas de conta suspenso, pontos de interrupção e as condições de corrida.

<!-- 2753080 | IS -->  
- **Atualize notificação disponível.** Conectado implantações verificará agora periodicamente um ponto de extremidade seguro e determinar se uma atualização está disponível para a sua nuvem do Azure Stack. Essa notificação é exibida no bloco de atualização, como faria com depois de verificar manualmente e importar uma nova atualização. Leia mais sobre [Gerenciando atualizações para o Azure Stack](azure-stack-updates.md).

<!-- 2297790 | IS, ASDK -->  
- **Melhorias para o cliente do Azure Stack syslog (recurso de visualização)**. Esse cliente permite o encaminhamento de auditoria e logs relacionados à infraestrutura do Azure Stack para um syslog server ou segurança informações e eventos (SIEM) software de gerenciamento externo para o Azure Stack. O cliente de syslog agora oferece suporte o protocolo TCP com texto sem formatação ou criptografia de TLS 1.2, o segundo sendo a configuração padrão. Você pode configurar a conexão TLS com a autenticação somente do servidor ou mútua.

  Para configurar como o cliente de syslog se comunica (como protocolo, criptografia e autenticação) com o servidor syslog, use o *SyslogServer conjunto* cmdlet. Este cmdlet está disponível do ponto de extremidade (PEP) com privilégios.

  Para adicionar o certificado de cliente para a autenticação mútua do cliente TLS 1.2 de syslog, use o cmdlet Set-SyslogClient no PEP.

  Com essa visualização, você pode ver um número muito maior de auditorias e alertas. 

  Porque esse recurso ainda está em visualização, não confie nele em ambientes de produção.

  Para obter mais informações, consulte [encaminhamento de syslog do Azure Stack](azure-stack-integrate-security.md).

<!-- ####### | IS, ASDK | --> 
- **O Azure Resource Manager inclui o nome da região.** Com esta versão, os objetos recuperados do Azure Resource Manager agora incluirá o atributo de nome de região. Se um script PowerShell existente diretamente passa o objeto para outro cmdlet, o script pode produzir um erro e falha. Isso é o comportamento compatível com o Azure Resource Manager e exige que o cliente da chamada subtrair o atributo região. Para obter mais informações sobre o Azure Resource Manager, consulte [documentação do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/). verify 8-10 mdb -->

<!-- TBD | IS, ASDK -->  
- **Alterações na funcionalidade de provedores de delegado.** Começando com 1807 os provedores delegada de modelo é simplificado para melhor se alinhar com o modelo de revendedor do Azure e provedores delegados não será capaz de criar outros provedores de delegado, essencialmente nivelar o modelo e tornando o provedor delegada recurso disponível em um único nível. Para habilitar a transição para o novo modelo e o gerenciamento de assinaturas, as assinaturas de usuário agora podem ser movidas entre assinaturas de provedor delegada novas ou existentes que pertencem ao mesmo locatário do diretório. Assinaturas de usuário que pertencem à assinatura do provedor padrão também podem ser movidas para as assinaturas de provedor delegada no mesmo locatário de diretório.  Para obter mais informações, consulte [delegar ofertas no Azure Stack](azure-stack-delegated-provider.md).

<!-- 2536808 IS ASDK --> 
- **Aprimorada a hora de criação da VM** para VMs que são criadas com imagens, você pode baixar do marketplace do Azure.

<!-- TBD | IS, ASDK -->  
- **Melhorias de usabilidade Planejador de capacidade de pilha do Azure**. O Azure Stack [Planejador de capacidade](https://aka.ms/azstackcapacityplanner) agora oferece uma experiência simplificada para a entrada de cache de S2D e a capacidade de S2D ao definir a solução de SKUs. O limite de 1000 VM foi removido.


### <a name="fixed-issues"></a>Problemas corrigidos

<!-- TBD | ASDK, IS --> 
- Várias melhorias foram feitas para o processo de atualização para torná-lo mais confiável. Além disso, correções foram feitas para a infraestrutura subjacente, que minimizar o tempo de inatividade potencial para cargas de trabalho durante a atualização.

<!--2292271 | ASDK, IS --> 
- Corrigimos um problema em que um limite de cota modificado não foi aplicada a assinaturas existentes. Agora, quando você gerar um limite de cota para um recurso de rede que faz parte de uma oferta e plano associado com uma assinatura de usuário, o novo limite se aplica ao assinaturas já existentes, bem como novas assinaturas.

<!-- 448955 | IS ASDK --> 
- Agora você pode consultar com êxito os logs de atividades para sistemas que são implantados em um fuso horário de UTC + N.    

<!-- 2319627 |  ASDK, IS --> 
- Pré-verificação para parâmetros de configuração de backup (caminho/nome de usuário/senha/criptografia de chave) não define configurações incorretas para a configuração de backup. (Anteriormente, configurações incorretas foram definidas para o backup e backup, em seguida, falharia quando disparado).

<!-- 2215948 |  ASDK, IS -->
- A lista de backup agora é atualizada quando você excluir manualmente o backup do compartilhamento externo.

<!-- 2332636 | IS -->  
- Atualização para esta versão não redefine o proprietário padrão de assinatura do provedor padrão para internos **CloudAdmin** usuário quando implantado com o AD FS.

<!-- 2360715 |  ASDK, IS -->  
- Quando você configura a integração do datacenter, você não mais acessar o arquivo de metadados do AD FS de um compartilhamento de arquivos do Azure. Para obter mais informações, consulte [configurar a integração do AD FS, fornecendo o arquivo de metadados de Federação](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

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

- **Várias correções de** para desempenho, estabilidade, segurança e o sistema operacional que é usado pelo Azure Stack.


<!-- ### Additional releases timed with this update    -->

### <a name="common-vulnerabilities-and-exposures"></a>Vulnerabilidades e exposições comuns
O Azure Stack usa as instalações do Server Core do Windows Server 2016 a infraestrutura de chave de host. Esta versão instala as seguintes atualizações do Windows Server 2016 nos servidores de infra-estrutura para o Azure Stack: 
- [CVE-2018-8206](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8206)
- [CVE-2018-8222](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8222)
- [CVE-2018-8282](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8282)
- [CVE-2018-8304](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8304)
- [CVE-2018-8307](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8307)
- [CVE-2018-8308](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8308) 
- [CVE-2018-8309](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8309)
- [CVE-2018-8313](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8313)  

Para obter mais informações sobre essas vulnerabilidades, clique nos links acima ou consulte os artigos da Base de dados de Conhecimento da Microsoft [4338814](https://support.microsoft.com/help/4338814) e [4345418](https://support.microsoft.com/help/4345418).



## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

- Instalar o Azure Stack [1805 atualizar](azure-stack-update-1805.md) antes de aplicar a atualização do Azure Stack 1807. Não houve nenhuma atualização 1806.  
 
  > [!TIP]  
  > Inscrever-se ao seguinte *RRS* ou *Atom* feeds manter-se atualizado com Hotfixes de pilha do Azure:
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- Antes de iniciar a instalação dessa atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com os seguintes parâmetros para validar o status do Azure Stack e resolva os problemas operacionais encontrados, incluindo todos os avisos e falhas. Também examinar os alertas ativos e resolva todos os que exigem ação.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

<!-- 2468613 - IS --> 
- Durante a instalação dessa atualização, você pode ver os alertas com o título *erro – o modelo para FaultType UserAccounts.New está ausente.*  Você pode ignorar com segurança esses alertas. Esses alertas serão fechada automaticamente após a conclusão da instalação dessa atualização.

<!-- 2489559 - IS --> 
- Não tente criar máquinas virtuais durante a instalação dessa atualização. Para obter mais informações sobre como gerenciar atualizações, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md#plan-for-updates).

<!-- 2830461 - IS --> 
- Em determinadas circunstâncias quando uma atualização requer atenção, o alerta correspondente pode não ser gerado. O status preciso ainda será refletido no portal e não é afetado.

### <a name="post-update-steps"></a>Etapas de pós-atualização
Após a instalação dessa atualização, instale os Hotfixes aplicáveis. Para obter mais informações, consulte os seguintes artigos de base de Conhecimento, bem como nossos [política de manutenção](azure-stack-servicing-policy.md). 
- [KB 4467061 – o Azure Stack Hotfix Azure Stack Hotfix 1.1807.3.82](https://support.microsoft.com/help/4467061/)

<!-- 2933866 – IS --> Após a instalação dessa atualização, você pode ver **aprimorado de status para instalações de atualização com falha.** Isso pode incluir informações sobre falhas de instalação de atualização anterior que são revisados para refletir as duas novas categorias de estado. As novas categorias de estado são *PreparationFailed*, e *InstallationFailed*.  

## <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

A seguir estão os problemas conhecidos de pós-instalação para esta versão de compilação.

### <a name="portal"></a>Portal

- A documentação técnica do Azure Stack enfoca a versão mais recente. Devido a alterações portal entre as versões, o que você vê ao usar os portais do Azure Stack podem ser diferentes daqueles que você vê na documentação. 

- A capacidade [abra uma nova solicitação de suporte na lista suspensa](azure-stack-manage-portals.md#quick-access-to-help-and-support) dentro do administrador do portal não está disponível. Em vez disso, para o Azure Stack sistemas integrados, use o seguinte link: [ https://aka.ms/newsupportrequest ](https://aka.ms/newsupportrequest).

<!-- 2931230 – IS  ASDK --> 
- Os planos são adicionados a uma assinatura de usuário como um plano de complemento não podem ser excluídos, mesmo quando você remove o plano da assinatura do usuário. O plano permanecerá até que as assinaturas que o plano de complemento de referência também são excluídas. 

<!--2760466 – IS  ASDK --> 
- Quando você instala um novo ambiente do Azure Stack que executa esta versão, o alerta indicar *ativação necessária* podem não ser exibidos. [Ativação](azure-stack-registration.md) é necessária antes que você pode usar a distribuição do marketplace.  

<!-- TBD - IS ASDK --> 
- Os dois tipos de assinatura administrativos que foram introduzidos com a versão 1804 não devem ser usados. Os tipos de assinatura são **assinatura de medição**, e **assinatura de consumo**. Esses tipos de assinatura são visíveis em novos ambientes Azure Stack, começando com a versão 1804, mas ainda não estão prontos para uso. Você deve continuar a usar o **provedor padrão** tipo de assinatura.

<!-- 2403291 - IS ASDK --> 
- Você pode não ter o uso da barra de rolagem horizontal na parte inferior do portais do administrador e usuário. Se você não pode acessar a barra de rolagem horizontal, use a trilha para navegar até uma folha anterior no portal, selecionando o nome da folha você quer exibir na lista de navegação estrutural na parte superior esquerda do portal.

  ![Navegação estrutural](media/azure-stack-update-1804/breadcrumb.png)

<!-- TBD - IS --> 
- Pode não ser possível exibir os recursos de computação ou armazenamento no portal do administrador. A causa desse problema é um erro durante a instalação da atualização que faz com que a atualização a ser relatado incorretamente como bem-sucedido. Se esse problema ocorrer, entre em contato com o Atendimento Microsoft para obter assistência.

<!-- TBD - IS --> 
- Você pode ver um painel em branco no portal. Para recuperar o painel, selecione o ícone de engrenagem no canto superior direito do portal e, em seguida, selecione **restaurar as configurações padrão**.

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
   - NOME:  Função de infraestrutura não íntegro
   - GRAVIDADE: Aviso
   - COMPONENTE: Controlador de integridade
   - DESCRIÇÃO: O Scanner de pulsação do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.  

  #2 do alerta:
   - NOME:  Função de infraestrutura não íntegro
   - GRAVIDADE: Aviso
   - COMPONENTE: Controlador de integridade
   - DESCRIÇÃO: O Scanner de falha do controlador de integridade não está disponível. Isso pode afetar as métricas e relatórios de integridade.

  Ambos os alertas podem ser ignorados com segurança e será fechado automaticamente ao longo do tempo.  


<!-- 2812138 | IS --> 
- Você pode ver um alerta para **armazenamento** componente que tem os seguintes detalhes:

   - NOME: Erro de comunicação interna do serviço de armazenamento  
   - GRAVIDADE: Crítico  
   - COMPONENTE: Armazenamento  
   - DESCRIÇÃO: Erro de comunicação interna do serviço de armazenamento ocorreu ao enviar solicitações para os nós a seguir.  

    O alerta pode ser ignorado, mas você precisa fechar o alerta manualmente.

<!-- 2368581 - IS. ASDK --> 
- Um operador do Azure Stack, se você receber um alerta de memória insuficiente e máquinas virtuais de locatário não conseguir implantar com um **erro de criação de VM do Fabric**, é possível que o carimbo de data / Azure Stack está fora de memória disponível. Use o [Planejador de capacidade do Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para entender melhor a capacidade disponível para suas cargas de trabalho.


### <a name="compute"></a>Computação

<!-- 2724873 - IS --> 
- Ao usar os cmdlets do PowerShell **Start-AzsScaleUnitNode** ou **AzsScaleunitNode Stop** para gerenciar unidades de escala, a primeira tentativa de iniciar ou parar a unidade de escala poderá falhar. Se o cmdlet falhar na primeira execução, execute o cmdlet uma segunda vez. A segunda execução deve ser bem-sucedida para concluir a operação. 

<!-- 2494144 - IS, ASDK --> 
- Ao selecionar um tamanho de máquina virtual para uma implantação de máquina virtual, alguns tamanhos de VM da série F não são visíveis como parte do seletor de tamanho quando você cria uma VM. Os seguintes tamanhos VM não aparecem no seletor de: *F8s_v2*, *F16s_v2*, *F32s_v2*, e *F64s_v2*.  
  Como alternativa, use um dos seguintes métodos para implantar uma VM. Em cada método, você precisará especificar o tamanho da VM que deseja usar.

  - **Modelo do Gerenciador de recursos do Azure:** Quando você usa um modelo, defina as *vmSize* no modelo para ser igual ao tamanho VM que você deseja usar. Por exemplo, a seguinte entrada é usada para implantar uma VM que usa o *F32s_v2* tamanho:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **CLI do Azure:** Você pode usar o [criar vm az](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) comando e especifique o tamanho da VM como um parâmetro, semelhante ao `--size "Standard_F32s_v2"`.

  - **PowerShell:** Com o PowerShell, você pode usar [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) com o parâmetro que especifica o tamanho da VM, semelhante ao `-VMSize "Standard_F32s_v2"`.


<!-- TBD - IS ASDK --> 
- As configurações de escala para conjuntos de dimensionamento de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Devido às diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

<!-- TBD - IS --> 
- Quando você cria uma conjunto de disponibilidade no portal acessando **New** > **computação** > **conjunto de disponibilidade**, você só pode criar um conjunto de disponibilidade com um domínio de falha e domínio de atualização de 1. Como alternativa, ao criar uma nova máquina virtual, crie a disponibilidade definida por meio do PowerShell, CLI, ou de dentro do portal.

<!-- TBD - IS ASDK --> 
- Quando você cria máquinas virtuais no portal do usuário do Azure Stack, o portal exibe um número incorreto de discos de dados que podem anexar a uma VM da série DS. As VMs da série DS podem acomodar a tantos discos de dados como a configuração do Azure.

<!-- TBD - IS ASDK --> 
- Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

<!-- 1662991 IS ASDK --> 
- Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico.  

<!-- 2724961- IS ASDK --> 
- Quando você registra o **Microsoft.Insight** provedor de recursos em configurações de assinatura e criar uma VM do Windows com o convidado SO diagnóstico habilitado, a página de visão geral VM não mostra dados de métricas. 

   Para localizar dados de métricas, como o gráfico de percentual de CPU para a VM, vá para o **métricas** métricas de convidado de folha e mostrar toda a VM Windows com suporte.

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


### <a name="sql-and-mysql"></a>SQL e MySQL



<!-- No Number - IS, ASDK -->  
- Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** nome quando você criar uma SKU para os provedores de recursos do SQL e MySQL. 

<!-- TBD - IS --> 
- O provedor de recursos é suportado para criar itens em servidores, esse host SQL ou MySQL. Criado em um servidor de host de itens que não são criados pelo provedor de recursos podem resultar em um estado incompatível.  

<!-- TBD - IS -->
> [!NOTE]   
> Depois de atualizar para esta versão do Azure Stack, você pode continuar a usar os provedores de recursos SQL e MySQL implantado anteriormente.  É recomendável que você atualize o SQL e MySQL quando uma nova versão está disponível. Como o Azure Stack, aplica atualizações a provedores de recursos do SQL e MySQL em sequência. Por exemplo, se você usar a versão 1804, primeiro aplique versão 1805 e, em seguida, atualize para 1807.  
>  
> A instalação desta atualização não afeta o uso atual de provedores de recursos do SQL ou MySQL pelos usuários. 
> Independentemente da versão dos provedores de recursos que você usar, seus dados de usuários em seus bancos de dados não não tocados e permanecem acessíveis.  

### <a name="app-service"></a>Serviço de Aplicativo

<!-- 2352906 - IS ASDK --> 
- Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

<!-- 2489178 - IS ASDK --> 
- Para escalar horizontalmente a infraestrutura (funcionários, gerenciamento, as funções front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.

<!-- TBD - IS ASDK --> 
- O serviço de aplicativo só pode ser implantado para o *assinatura do provedor padrão* neste momento. 


### <a name="usage"></a>Uso  
<!-- TBD - IS ASDK --> 
- Dados de medidor uso do endereço de IP público de uso mostram a mesma *EventDateTime* valor para cada registro, em vez da *TimeDate* carimbo que mostra que o registro foi criado. Atualmente, é possível usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Baixe a atualização
Você pode baixar o pacote de atualização no Azure Stack 1807 [aqui](https://aka.ms/azurestackupdatedownload).


## <a name="next-steps"></a>Próximas etapas
- Para revisar a política de manutenção para sistemas integrados do Azure Stack e o que você deve fazer para manter seu sistema em um estado com suporte, consulte [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).  
- Para usar o ponto de extremidade com privilégios (PEP) para monitorar e retomar as atualizações, consulte [monitorar as atualizações no Azure Stack usando o ponto de extremidade com privilégios](azure-stack-monitor-update.md).  
- Para uma visão geral do gerenciamento de atualizações no Azure Stack, consulte [gerenciar atualizações na visão geral do Azure Stack](azure-stack-updates.md).  
- Para obter mais informações sobre como aplicar atualizações com o Azure Stack, consulte [aplicar as atualizações no Azure Stack](azure-stack-apply-updates.md).  
