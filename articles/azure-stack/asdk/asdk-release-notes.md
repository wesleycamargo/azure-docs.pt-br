---
title: Notas de versão do Kit de desenvolvimento do Microsoft Azure Stack | Microsoft Docs
description: Problemas conhecidos do Kit de desenvolvimento do Azure Stack, correções e aprimoramentos.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 86ac1f1b5433104faa89e1f107fa36fc1da5f70e
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989887"
---
# <a name="azure-stack-development-kit-release-notes"></a>Notas de versão do Kit de desenvolvimento de pilha do Azure  
Essas notas de versão fornecem informações sobre problemas conhecidos no Kit de desenvolvimento do Azure Stack, correções e aprimoramentos. Se você não tiver certeza de qual versão você está executando, você poderá [usar o portal para verificar](.\.\azure-stack-updates.md#determine-the-current-version).

> Mantenha-se atualizado com o que há de novo no ASDK assinando o [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [feed](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11805147"></a>Compilação 1.1805.1.47

> [!TIP]  
> Com base nos comentários dos clientes, há uma atualização para o esquema de versão em uso para o Microsoft Azure Stack. Começando com essa atualização, 1805, o novo esquema representa melhor a versão atual da nuvem.  
> 
> O esquema de versão agora é *Version.YearYearMonthMonth.MinorVersion.BuildNumber* onde os conjuntos de segundo e terceiro indicam a versão e a liberação. Por exemplo, 1805.1 representa o *lançamento para fabricação* versão (RTM) do 1805.  


### <a name="new-features"></a>Novos recursos 
Esta compilação inclui os seguintes aprimoramentos e correções para o Azure Stack.  

- <!-- 2297790 - IS, ASDK --> **O Azure Stack agora inclui um *Syslog* cliente** como um *versão prévia do recurso*. Esse cliente permite o encaminhamento de logs de auditoria e segurança relacionados à infraestrutura do Azure Stack para um Syslog server ou segurança informações e eventos (SIEM) software de gerenciamento que é externo ao Azure Stack. Atualmente, o cliente de Syslog só dá suporte a conexões de UDP não autenticadas através da porta padrão 514. A carga de cada mensagem do Syslog é formatada em comum (CEF) do formato de evento. 

  Para configurar o cliente de Syslog, use o **SyslogServer conjunto** cmdlet exposta no ponto de extremidade com privilégios. 

  Com essa visualização, você poderá ver os três alertas a seguir. Quando apresentadas pela pilha do Azure, esses alertas incluem *descrições* e *correção* diretrizes. 
  - TÍTULO: Integridade de código desativado  
  - TÍTULO: Integridade do código no modo de auditoria 
  - TÍTULO: Conta de usuário criada

  Embora esse recurso está em visualização, ele não deve ser usado em ambientes de produção.   


### <a name="fixed-issues"></a>Problemas corrigidos
- Corrigimos o problema que bloqueados [abrir uma nova solicitação de suporte na lista suspensa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do portal de administração. Essa opção agora funciona conforme o esperado. 

- **Várias correções de** para desempenho, estabilidade, segurança e o sistema operacional que é usado pelo Azure Stack


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>Problemas conhecidos
 
#### <a name="portal"></a>Portal
- <!-- 2551834 - IS, ASDK --> Quando você seleciona **visão geral** nos portais de administrador ou usuário, as informações da conta de armazenamento a *Essentials* painel não será exibido.  O painel do Essentials exibe informações sobre a conta, como seu *grupo de recursos*, *local*, e *ID da assinatura*.  Outras opções de visão geral são acessíveis como *serviços* e *monitoramento*, bem como opções para *abrir no Gerenciador de* ou *excluir conta de armazenamento* .  

  Para exibir as informações disponíveis, use o [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) cmdlet do PowerShell. 

- <!-- 2551834 - IS, ASDK --> Quando você seleciona **marcas** para uma conta de armazenamento nos portais de administrador ou usuário, as informações de falha ao carregar e não será exibido.  

  Para exibir as informações disponíveis, use o [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) cmdlet do PowerShell.

- <!-- TBD - IS ASDK --> Não use os novos tipos de assinatura administrativos do *assinatura de medição*, e *assinatura de consumo*. Esses novos tipos de assinatura foram introduzidos com a versão 1804, mas ainda não estão prontos para uso. Você deve continuar a usar o *provedor padrão* tipo de assinatura.  

- <!-- 2403291 - IS ASDK --> Você pode não ter o uso da barra de rolagem horizontal na parte inferior do portais do administrador e usuário. Se você não pode acessar a barra de rolagem horizontal, use a trilha para navegar até uma folha anterior no portal, selecionando o nome da folha você quer exibir na lista de navegação estrutural na parte superior esquerda do portal.
  ![Navegação estrutural](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Excluindo resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos de usuário ou grupo de recursos inteiro e, em seguida, exclua as assinaturas do usuário.

- <!-- TBD -  IS ASDK --> Você não pode exibir as permissões para sua assinatura usando os portais do Azure Stack. Como alternativa, use o PowerShell para verificar permissões.


#### <a name="health-and-monitoring"></a>Monitoramento e integridade
- <!-- 1264761 - IS ASDK -->  Você pode ver os alertas para o *controlador de integridade* componente que tem os seguintes detalhes:  

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

  Ambos os alertas 1 # e #2 podem ser ignorados e será fechado automaticamente ao longo do tempo. 

  Você também poderá ver o seguinte alerta para *capacidade*. Para esse alerta, a porcentagem de memória disponível identificada na descrição pode variar:  

  #3 do alerta:
   - NOME: Capacidade de memória baixa
   - GRAVIDADE: crítico
   - COMPONENTE: capacidade
   - Descrição: A região consumiu mais de 80,00% da memória disponível. Criação de máquinas virtuais com grandes quantidades de memória pode falhar.  

  Nesta versão do Azure Stack, esse alerta pode ser acionado incorretamente. Se as máquinas virtuais de locatário continuar a implantar com êxito, você pode ignorar com segurança este alerta. 
  
  Não fechar automaticamente alerta #3. Se você fechar este alerta do Azure Stack criará o mesmo alerta dentro de 15 minutos.  

- <!-- 2368581 - IS ASDK --> Um operador do Azure Stack, se você receber um alerta de memória insuficiente e máquinas virtuais de locatário não conseguir implantar com um *erro de criação de VM do Fabric*, é possível que o carimbo de data / Azure Stack está fora de memória disponível. Use o [Planejador de capacidade do Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para entender melhor a capacidade disponível para suas cargas de trabalho. 


#### <a name="compute"></a>Computação
- <!-- TBD - IS, ASDK --> Ao selecionar um tamanho de máquina virtual para uma implantação de máquina virtual, alguns tamanhos de VM da série F não são visíveis como parte do seletor de tamanho quando você cria uma VM. Os seguintes tamanhos VM não aparecem no seletor de: *F8s_v2*, *F16s_v2*, *F32s_v2*, e *F64s_v2*.  
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


- <!-- TBD -  IS ASDK --> As configurações de escala para conjuntos de dimensionamento de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Devido às diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Quando você cria máquinas virtuais no portal do usuário do Azure Stack, o portal exibe um número incorreto de discos de dados que podem anexar a uma VM da série D. Todas as séries D com suporte VMs podem acomodar a tantos discos de dados como a configuração do Azure.

- <!-- TBD -  IS ASDK --> Quando uma imagem de VM não consegue ser criado, um item com falha que não é possível excluir pode ser adicionado até a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado por meio do Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão de item com falha. Em seguida, 15 minutos após a criação da imagem fictícia, você pode excluí-la.

  Em seguida, você pode tentar redownload a imagem da VM que falharam anteriormente.

- <!-- TBD -  IS ASDK --> Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

- <!-- 1662991 - IS ASDK --> Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico. 

#### <a name="networking"></a>Rede
- <!-- TBD - IS ASDK --> É possível criar rotas definidas pelo usuário no portal do administrador ou usuário. Como alternativa, use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

- <!-- 1766332 - IS, ASDK --> Sob **Networking**, se você clicar em **criar o Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo de VPN. Não selecione essa opção. Somente o **com base em rota** opção tem suporte no Azure Stack.

- <!-- 2388980 -  IS ASDK --> Depois que uma VM é criada e associada com um endereço IP público, você não pode desassociar dessa VM para esse endereço IP. Dissociação parece funcionar, mas o endereço IP público atribuído anteriormente permanece associado com a VM original.

  Atualmente, você deve usar somente novos endereços IP públicos para novas VMs que você cria.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tenta se conectar por meio desse resultado de endereço IP em uma conexão para a VM original e não para o novo.


- <!-- 2292271 - IS ASDK --> Se você aumentar um limite de cota para um recurso de rede que faz parte de uma oferta e o plano que está associado uma assinatura de locatário, o novo limite não é aplicado a essa assinatura. No entanto, o novo limite se aplica às novas assinaturas que são criadas depois que a cota for aumentada. 

  Para contornar esse problema, use um plano de complemento para aumentar a cota de rede quando o plano já está associado uma assinatura. Para obter mais informações, consulte como [disponibilizar um plano de complemento](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Você não pode excluir uma assinatura que tem recursos de zona DNS ou recursos de tabela de rotas associados a ele. Para excluir com êxito a assinatura, primeiro você deve excluir os recursos de zona DNS e a tabela de rotas da assinatura do locatário. 


- <!-- 1902460 -  IS ASDK --> O Azure Stack dá suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatário. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- <!-- 16309153 -  IS ASDK --> Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automática*, alterar para uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.
 
- <!-- TBD -  IS ASDK --> O Azure Stack não dá suporte para adicionar outros adaptadores de rede a uma instância VM depois que a VM é implantada. Se a máquina virtual exigir mais de uma interface de rede, deve ser definidos no momento da implantação.


#### <a name="sql-and-mysql"></a>SQL e MySQL 
- <!-- TBD - ASDK --> O banco de dados de servidores de hospedagem deve ser dedicado para uso pelas cargas de trabalho de usuário e o provedor de recursos. Você não pode usar uma instância que está sendo usada por outro consumidor, incluindo serviços de aplicativos.

- <!-- IS, ASDK --> Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** nome quando você criar uma SKU para os provedores de recursos do SQL e MySQL. 

#### <a name="app-service"></a>Serviço de Aplicativo
- <!-- 2352906 - IS ASDK --> Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

- <!-- TBD - IS ASDK --> Para escalar horizontalmente a infraestrutura (funcionários, gerenciamento, as funções front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.  

- <!-- TBD - IS ASDK --> O serviço de aplicativo só pode ser implantado para o *assinatura do provedor padrão* neste momento. Em uma atualização futura, o serviço de aplicativo será implantado no novo *medição assinatura* que foi introduzido no Azure Stack 1804. Quando a medição é suporte para uso, todas as implantações existentes serão migradas para esse novo tipo de assinatura.

#### <a name="usage"></a>Uso  
- <!-- TBD -  IS ASDK --> Dados de medidor uso do endereço de IP público de uso mostram a mesma *EventDateTime* valor para cada registro, em vez da *TimeDate* carimbo que mostra que o registro foi criado. Atualmente, é possível usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.

<!-- #### Identity -->



## <a name="build-201805131"></a>Compilação 20180513.1

### <a name="new-features"></a>Novos recursos 
Esta compilação inclui os seguintes aprimoramentos e correções para o Azure Stack.  

- <!-- 1759172 - IS, ASDK --> **Novas assinaturas administrativas**. Com 1804 há dois novos tipos de assinatura disponíveis no portal. Esses novos tipos de assinatura são além de assinatura do provedor padrão e visíveis em novas instalações do Azure Stack, começando com a versão 1804. *Não use esses novos tipos de assinatura com esta versão do Azure Stack*. Anunciaremos a disponibilidade para usar esses tipos de assinatura com uma atualização futura. 

  Esses novos tipos de assinatura são visíveis, mas faz parte de uma alteração maior para proteger a assinatura do provedor padrão e para torná-lo mais fácil de implantar recursos compartilhados, como servidores de hospedagem SQL. 

  Os tipos de assinatura de três agora disponíveis são:  
  - Padrão de assinatura do provedor: continuar a usar este tipo de assinatura. 
  - Medição de assinatura: *não use este tipo de assinatura.*
  - Assinatura de consumo: *não use este tipo de assinatura*

### <a name="fixed-issues"></a>Problemas corrigidos
- <!-- IS, ASDK -->  No portal de administração, você não precisa atualizar o bloco de atualização antes de exibir informações. 

- <!-- 2050709 - IS, ASDK -->  Agora você pode usar o portal de administração para editar as métricas de armazenamento para o serviço Blob, o serviço tabela e o serviço fila.

- <!-- IS, ASDK --> Sob **Networking**, quando você clica em **Conexão** para configurar uma conexão VPN, **Site a site (IPsec)** agora é a única opção disponível. 

- **Várias correções de** para desempenho, estabilidade, segurança e o sistema operacional que é usado pelo Azure Stack

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>Versões adicionais atingiu o tempo com essa atualização  
O exemplo a seguir agora está disponíveis, mas não exige a atualização do Azure Stack 1804.
- **Atualizar para o pacote de monitoramento do Microsoft Azure Stack System Center Operations Manager**. Uma nova versão (1.0.3.0) do pacote Microsoft System Center Operations Manager de monitoramento para o Azure Stack está disponível para [baixar](https://www.microsoft.com/download/details.aspx?id=55184). Com esta versão, você pode usar as entidades de serviço quando você adiciona uma implantação do Azure Stack conectada. Esta versão também apresenta uma experiência de gerenciamento de atualizações que permite que você aproveite a ação de correção diretamente de dentro do Operations Manager. Também há novos painéis que exibem provedores de recursos, unidades de escala e dimensionar nós de unidade.

- **Novo Azure PowerShell versão 1.3.0 do administrador de pilha**.  O Azure Stack PowerShell 1.3.0 agora está disponível para instalação. Esta versão fornece comandos para todos os provedores de recursos de administração gerenciar o Azure Stack.  Com esta versão, algum conteúdo será preterido a partir do GitHub de ferramentas do Azure Stack [repositório](https://github.com/Azure/AzureStack-Tools). 

   Para obter detalhes de instalação, execute as [instruções](.\.\azure-stack-powershell-install.md) ou o [ajuda](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) conteúdo para o módulo do Azure Stack 1.3.0. 

- **Versão inicial do Azure Stack referência de Rest API**. O [referência de API para todos os provedores de recursos de administração do Azure Stack](https://docs.microsoft.com/rest/api/azure-stack/) agora está publicada. 

### <a name="known-issues"></a>Problemas conhecidos
 
#### <a name="portal"></a>Portal
- <!-- TBD - IS ASDK --> A capacidade [para abrir uma nova solicitação de suporte na lista suspensa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do administrador do portal não está disponível. Em vez disso, use o seguinte link:     
    - Para o Kit de desenvolvimento do Azure Stack, use https://aka.ms/azurestackforum.    

- <!-- 2403291 - IS ASDK --> Você pode não ter o uso da barra de rolagem horizontal na parte inferior do portais do administrador e usuário. Se você não pode acessar a barra de rolagem horizontal, use a trilha para navegar até uma folha anterior no portal, selecionando o nome da folha você quer exibir na lista de navegação estrutural na parte superior esquerda do portal.
  ![Navegação estrutural](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> Excluindo resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos de usuário ou grupo de recursos inteiro e, em seguida, exclua as assinaturas do usuário.

- <!-- TBD -  IS ASDK --> Você não pode exibir as permissões para sua assinatura usando os portais do Azure Stack. Como alternativa, use o PowerShell para verificar permissões.

-   <!-- TBD -  IS ASDK --> No portal de administração, você poderá ver um alerta crítico para o componente Microsoft.Update.Admin. O nome do alerta, descrição e correção todos exibem como:  
    - *Erro - o modelo para FaultType ResourceProviderTimeout está ausente.*

    Esse alerta pode ser ignorado. 

#### <a name="health-and-monitoring"></a>Monitoramento e integridade
- <!-- 1264761 - IS ASDK -->  Você pode ver os alertas para o *controlador de integridade* componente que tem os seguintes detalhes:  

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

  Ambos os alertas podem ser ignorados com segurança. Será fechada automaticamente ao longo do tempo.  

#### <a name="marketplace"></a>Marketplace
- Usuários podem procurar o marketplace completo sem uma assinatura e podem ver itens administrativos, como planos e ofertas. Esses itens não estão funcionando para os usuários.
 
#### <a name="compute"></a>Computação
- <!-- TBD -  IS ASDK --> As configurações de escala para conjuntos de dimensionamento de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Devido às diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

- <!-- TBD -  IS ASDK --> Quando você cria máquinas virtuais no portal do usuário do Azure Stack, o portal exibe um número incorreto de discos de dados que podem anexar a uma VM da série DS. As VMs da série DS podem acomodar a tantos discos de dados como a configuração do Azure.

- <!-- TBD -  IS ASDK --> Quando uma imagem de VM não consegue ser criado, um item com falha que não é possível excluir pode ser adicionado até a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado por meio do Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão de item com falha. Em seguida, 15 minutos após a criação da imagem fictícia, você pode excluí-la.

  Em seguida, você pode tentar redownload a imagem da VM que falharam anteriormente.

- <!-- TBD -  IS ASDK --> Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

- <!-- 1662991 - IS ASDK --> Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico. 

#### <a name="networking"></a>Rede
- <!-- 1766332 - IS, ASDK --> Sob **Networking**, se você clicar em **criar o Gateway de VPN** para configurar uma conexão VPN, **com base na política** está listado como um tipo de VPN. Não selecione essa opção. Somente o **com base em rota** opção tem suporte no Azure Stack.

- <!-- 2388980 -  IS ASDK --> Depois que uma VM é criada e associada com um endereço IP público, você não pode desassociar dessa VM para esse endereço IP. Dissociação parece funcionar, mas o endereço IP público atribuído anteriormente permanece associado com a VM original.

  Atualmente, você deve usar somente novos endereços IP públicos para novas VMs que você cria.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tenta se conectar por meio desse resultado de endereço IP em uma conexão para a VM original e não para o novo.

- <!-- 2292271 - IS ASDK --> Se você aumentar um limite de cota para um recurso de rede que faz parte de uma oferta e o plano que está associado uma assinatura de locatário, o novo limite não é aplicado a essa assinatura. No entanto, o novo limite se aplica às novas assinaturas que são criadas depois que a cota for aumentada. 

  Para contornar esse problema, use um plano de complemento para aumentar a cota de rede quando o plano já está associado uma assinatura. Para obter mais informações, consulte como [disponibilizar um plano de complemento](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

- <!-- 2304134 IS ASDK --> Você não pode excluir uma assinatura que tem recursos de zona DNS ou recursos de tabela de rotas associados a ele. Para excluir com êxito a assinatura, primeiro você deve excluir os recursos de zona DNS e a tabela de rotas da assinatura do locatário. 


- <!-- 1902460 -  IS ASDK --> O Azure Stack dá suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatário. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- <!-- 16309153 -  IS ASDK --> Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automática*, alterar para uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.
 
- <!-- TBD -  IS ASDK --> O Azure Stack não dá suporte para adicionar outros adaptadores de rede a uma instância VM depois que a VM é implantada. Se a máquina virtual exigir mais de uma interface de rede, deve ser definidos no momento da implantação.


#### <a name="sql-and-mysql"></a>SQL e MySQL 
- <!-- TBD - ASDK --> O banco de dados de servidores de hospedagem deve ser dedicado para uso pelas cargas de trabalho de usuário e o provedor de recursos. Você não pode usar uma instância que está sendo usada por outro consumidor, incluindo serviços de aplicativos.

- <!-- IS, ASDK --> Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** nome quando você criar uma SKU para os provedores de recursos do SQL e MySQL. 

#### <a name="app-service"></a>Serviço de Aplicativo
- <!-- TBD -  IS ASDK --> Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

- <!-- TBD -  IS ASDK --> Para escalar horizontalmente a infraestrutura (funcionários, gerenciamento, as funções front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.
 
#### <a name="usage"></a>Uso  
- <!-- TBD -  IS ASDK --> Dados de medidor uso do endereço de IP público de uso mostram a mesma *EventDateTime* valor para cada registro, em vez da *TimeDate* carimbo que mostra que o registro foi criado. Atualmente, é possível usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>Baixar ferramentas do Azure Stack no GitHub
- Ao usar o *invoke-webrequest* das ferramentas de cmdlet do PowerShell para baixar o Azure Stack no Github, você receberá um erro:     
    -  *Invoke-webrequest: A solicitação foi anulada: não foi possível criar o canal seguro SSL/TLS.*     

  Esse erro ocorre devido a uma substituição de suporte do GitHub recente das Tlsv1 e Tlsv1.1 padrões de criptografia (o padrão para o PowerShell). Para obter mais informações, consulte [aviso de remoção de padrões de criptografia fraca](https://githubengineering.com/crypto-removal-notice/).

<!-- #### Identity -->



## <a name="build-201803021"></a>Compilação 20180302.1

### <a name="new-features-and-fixes"></a>Novos recursos e correções
Os novos recursos e correções lançadas para o Azure Stack, sistemas integrados versão 1803 se aplica o Kit de desenvolvimento do Azure Stack. Consulte a [novos recursos](.\.\azure-stack-update-1803.md#new-features) e [problemas foram corrigidos](.\.\azure-stack-update-1803.md#fixed-issues) seções do Azure Stack 1803 atualizar notas de versão para obter detalhes.  
> [!IMPORTANT]    
> Alguns dos itens listados na **novos recursos** e **problemas foram corrigidos** seções são relevantes apenas para sistemas integrados do Azure Stack.

### <a name="changes"></a>Alterações
- A maneira de alterar o estado de uma oferta recém-criada da *privados* para *pública* ou *encerrados* foi alterado. Para obter mais informações, consulte [criar uma oferta](.\.\azure-stack-create-offer.md). 


### <a name="known-issues"></a>Problemas conhecidos
 
#### <a name="portal"></a>Portal
- A capacidade [para abrir uma nova solicitação de suporte na lista suspensa](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support) de dentro do administrador do portal não está disponível. Em vez disso, use o seguinte link:     
    - Para o Kit de desenvolvimento do Azure Stack, use https://aka.ms/azurestackforum.    

- <!-- 2050709 --> No portal de administração, não é possível editar métricas de armazenamento para o serviço Blob, serviço de tabela ou o serviço de fila. Quando você vá para o armazenamento e, em seguida, selecione o blob, tabela ou bloco de serviço de fila, uma nova folha será aberta que exibe um gráfico de métricas para o serviço. Se você selecionar Editar na parte superior do bloco de gráfico de métricas, a folha Editar gráfico abre, mas não exibe opções para editar as métricas.  

- Você verá uma **ativação necessária** alerta de aviso que recomenda que você registre seu Kit de desenvolvimento do Azure Stack. Esse comportamento é esperado.

- Excluindo resultados de assinaturas do usuário em recursos órfãos. Como alternativa, primeiro exclua os recursos de usuário ou grupo de recursos inteiro e, em seguida, exclua as assinaturas do usuário.

- Você não pode exibir as permissões para sua assinatura usando os portais do Azure Stack. Como alternativa, use o PowerShell para verificar permissões.

- No painel do portal de administração, o bloco de atualização não pode exibir informações sobre as atualizações. Para resolver esse problema, clique no bloco para atualizá-la.

-   No portal de administração, você poderá ver um alerta crítico para o componente Microsoft.Update.Admin. O nome do alerta, descrição e correção todos exibem como:  
    - *Erro - o modelo para FaultType ResourceProviderTimeout está ausente.*

    Esse alerta pode ser ignorado. 

- No portal de administração e para portal do usuário, a folha de visão geral não é carregado quando você seleciona a folha de visão geral de contas de armazenamento que foram criados com uma versão de API mais antiga (exemplo: 2015-06-15). 

  Como alternativa, use o PowerShell para executar o **ResourceSynchronization.ps1 início** script para restaurar o acesso para os detalhes da conta de armazenamento. [O script está disponível em GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)e deve executar com credenciais de administrador de serviço no host do kit de desenvolvimento se você usar o ASDK.  

- O **integridade do serviço** folha Falha ao carregar. Quando você abre a folha de integridade do serviço no portal de administrador ou usuário do Azure Stack exibirá um erro e não carregar as informações. Este comportamento é esperado. Embora você possa selecionar e abrir o serviço de integridade, esse recurso ainda não está disponível, mas será implementado em uma versão futura do Azure Stack.


#### <a name="health-and-monitoring"></a>Monitoramento e integridade
- <!-- 1264761 - IS ASDK -->  Você pode ver os alertas para o *controlador de integridade* componente que tem os seguintes detalhes:  

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

  Ambos os alertas podem ser ignorados com segurança. Será fechada automaticamente ao longo do tempo.  

- No portal de administração do Azure Stack, você poderá ver um alerta crítico com o nome **pendentes expiração do certificado externo**.  Esse alerta pode ser ignorado e afetar as operações do Kit de desenvolvimento do Azure Stack. 


#### <a name="marketplace"></a>Marketplace
- Usuários podem procurar o marketplace completo sem uma assinatura e podem ver itens administrativos, como planos e ofertas. Esses itens não estão funcionando para os usuários.
 
#### <a name="compute"></a>Computação
- As configurações de escala para conjuntos de dimensionamento de máquinas virtuais não estão disponíveis no portal. Como alternativa, você pode usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Devido às diferenças de versão do PowerShell, você deve usar o `-Name` parâmetro em vez de `-VMScaleSetName`.

- Quando você cria máquinas virtuais no portal do usuário do Azure Stack, o portal exibe um número incorreto de discos de dados que podem anexar a uma VM da série DS. As VMs da série DS podem acomodar a tantos discos de dados como a configuração do Azure.

- Quando uma imagem de VM não consegue ser criado, um item com falha que não é possível excluir pode ser adicionado até a folha de computação de imagens VM.

  Como alternativa, crie uma nova imagem VM com um VHD fictício que pode ser criado por meio do Hyper-V (New-VHD-caminho C:\dummy.vhd-fixa - SizeBytes 1 GB). Esse processo deve corrigir o problema que impede a exclusão de item com falha. Em seguida, 15 minutos após a criação da imagem fictícia, você pode excluí-la.

  Em seguida, você pode tentar redownload a imagem da VM que falharam anteriormente.

-  Se uma extensão em uma implantação de VM o provisionamento leva muito tempo, os usuários devem permitir que o tempo limite de provisionamento em vez de tentar interromper o processo para desaloque ou exclua a VM.  

- <!-- 1662991 --> Não há suporte para o diagnóstico de VM do Linux no Azure Stack. Quando você implanta uma VM do Linux com o diagnóstico VM habilitado, a implantação falhará. A implantação também falhará se você habilitar as métricas básicas da VM do Linux por meio das configurações de diagnóstico. 


#### <a name="networking"></a>Rede
- Sob **Networking**, se você clicar em **Conexão** para configurar uma conexão VPN, **VNet-to-VNet** está listado como um tipo de conexão possíveis. Não selecione essa opção. Atualmente, apenas o **Site a site (IPsec)** opção tem suporte.

- Depois que uma VM é criada e associada com um endereço IP público, você não pode desassociar dessa VM para esse endereço IP. Dissociação parece funcionar, mas o endereço IP público atribuído anteriormente permanece associado com a VM original.

  Atualmente, você deve usar somente novos endereços IP públicos para novas VMs que você cria.

  Esse comportamento ocorre mesmo se você reatribuir o endereço IP para uma nova VM (conhecido como um *permuta de VIP*). Todas as futuras tenta se conectar por meio desse resultado de endereço IP em uma conexão para a VM original e não para o novo.



- O Azure Stack dá suporte a uma única *gateway de rede local* por endereço IP. Isso é verdadeiro em todas as assinaturas de locatário. Após a criação da primeira conexão de gateway local de rede, subsequente tenta criar um recurso de gateway de rede local com o mesmo endereço IP são bloqueados.

- Em uma rede Virtual que foi criado com uma configuração de servidor DNS de *automática*, alterar para uma falha de servidor DNS personalizado. As configurações atualizadas não são propagadas para as VMs nessa rede virtual.
 
- O Azure Stack não dá suporte para adicionar outros adaptadores de rede a uma instância VM depois que a VM é implantada. Se a máquina virtual exigir mais de uma interface de rede, deve ser definidos no momento da implantação.



#### <a name="sql-and-mysql"></a>SQL e MySQL 
- Pode levar até uma hora antes dos usuários podem criar bancos de dados em um novo SQL ou MySQL SKU.

- O banco de dados de servidores de hospedagem deve ser dedicado para uso pelas cargas de trabalho de usuário e o provedor de recursos. Você não pode usar uma instância que está sendo usada por outro consumidor, incluindo serviços de aplicativos.

- <!-- IS, ASDK --> Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** ou **camada** nomes quando você criar uma SKU para os provedores de recursos do SQL e MySQL.

#### <a name="app-service"></a>Serviço de Aplicativo
- Os usuários devem registrar o provedor de recursos de armazenamento antes de criar sua primeira função do Azure na assinatura.

- Para escalar horizontalmente a infraestrutura (funcionários, gerenciamento, as funções front-end), você deve usar o PowerShell conforme descrito nas notas de versão para a computação.
 
#### <a name="usage"></a>Uso  
- Dados de medidor uso do endereço de IP público de uso mostram a mesma *EventDateTime* valor para cada registro, em vez da *TimeDate* carimbo que mostra que o registro foi criado. Atualmente, é possível usar esses dados para realizar as estatísticas precisas de uso de endereço IP público.
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Baixar ferramentas do Azure Stack no GitHub
- Ao usar o *invoke-webrequest* das ferramentas de cmdlet do PowerShell para baixar o Azure Stack no Github, você receberá um erro:     
    -  *Invoke-webrequest: A solicitação foi anulada: não foi possível criar o canal seguro SSL/TLS.*     

  Esse erro ocorre devido a uma substituição de suporte do GitHub recente das Tlsv1 e Tlsv1.1 padrões de criptografia (o padrão para o PowerShell). Para obter mais informações, consulte [aviso de remoção de padrões de criptografia fraca](https://githubengineering.com/crypto-removal-notice/).

  Para resolver esse problema, adicione `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` na parte superior do script para forçar o console do PowerShell, use o TLSv1.2 ao baixar de repositórios do GitHub.





