---
title: Unir o tempo de execução de integração do Azure-SSIS a uma rede virtual | Microsoft Docs
description: Saiba como unir o tempo de execução de integração do Azure-SSIS a uma rede virtual do Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: cdda3fbe2aff40e26c6086e87ef3e05670c3419f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Unir o tempo de execução de integração do Azure-SSIS a uma rede virtual
Una o IR (tempo de execução de integração) do Azure-SSIS a uma rede virtual do Azure nos cenários a seguir: 

- Você está hospedando o banco de dados do catálogo SSIS (SQL Server Integration Services) na Instância Gerenciada do Banco de Dados SQL do Azure (versão prévia) em uma rede virtual.
- Você deseja se conectar a armazenamentos de dados locais de pacotes SSIS em execução em um tempo de execução de integração do Azure-SSIS.

 O Azure Data Factory versão 2 (versão prévia) permite unir o tempo de execução de integração do Azure-SSIS a uma rede virtual criada através do modelo de implantação clássico ou modelo de implantação do Azure Resource Manager. 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço do Data Factory, que está em GA (disponibilidade geral), consulte a [documentação do Data Factory versão 1](v1/data-factory-introduction.md).

## <a name="access-to-on-premises-data-stores"></a>Acessar armazenamentos de dados locais
Se os pacotes SSIS acessam apenas os armazenamentos de dados de nuvem pública, não é necessário unir o IR do Azure-SSIS em uma rede virtual. Se os pacotes SSIS acessarem armazenamentos de dados locais, será necessário unir o IR do Azure-SSIS a uma rede virtual conectada à rede local. 

Se o catálogo do SSIS estiver hospedado em uma instância do Banco de Dados SQL do Azure que não está na rede virtual, será necessário abrir portas apropriadas. 

Se o catálogo do SSIS estiver hospedado na Instância Gerenciada do Banco de Dados SQL em uma rede virtual, será possível unir um IR do Azure-SSIS para:

- A mesma rede virtual.
- Uma rede virtual diferente que tenha uma conexão de rede a rede com aquela que tenha Instância Gerenciada do Banco de Dados SQL. 

A rede virtual pode ser implantada através do modelo de implantação clássico ou do modelo de implantação do Azure Resource Manager. Se você pretende unir o IR do Azure-SSIS na *mesma rede virtual* que tem a Instância Gerenciada do Banco de Dados SQL, verifique se o IR do Azure-SSIS está em uma *sub-rede diferente* daquela que tem a Instância Gerenciada do Banco de Dados SQL.   

As seções a seguir apresentam mais detalhes.

Alguns pontos importantes a serem considerados: 

- Se não houver uma rede virtual conectada existente à sua rede local, primeiro crie uma [rede virtual do Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) ou uma [rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para o tempo de execução de integração do Azure-SSIS a unir. Em seguida, configure uma [conexão de gateway de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) site a site ou conexão [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) dessa rede virtual à sua rede local.
- Se houver um Azure Resource Manager existente ou uma rede virtual clássica conectada à sua rede local no mesmo local do IR do Azure-SSIS, será possível unir o IR nessa rede virtual.
- Se houver uma rede virtual clássica existente conectada à rede local em um local diferente do IR do Azure-SSIS, será possível primeiro criar uma [rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para o IR do Azure-SSIS a unir. Em seguida, configure uma conexão entre [redes virtuais clássicas](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md). Ou você poderá criar uma [rede virtual do Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para o tempo de execução de integração do Azure-SSIS a unir. Em seguida, configure uma conexão entre [rede virtual clássica e Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).
- Se houver uma rede virtual do Azure Resource Manager existente conectada à sua rede local em um local diferente do IR do Azure-SSIS, você poderá primeiro criar uma [rede virtual do Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) para unir o IR do Azure-SSIS a ela. Em seguida, configure uma conexão de rede virtual do Azure Resource Manager para o Azure Resource Manager. Ou você pode criar uma [rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para unir o IR do Azure-SSIS a ela. Em seguida, configure uma conexão entre [rede virtual clássica e Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="domain-name-services-server"></a>Servidor de Serviços de Nome de Domínio 
Se for necessário usar seu próprio servidor DNS (Serviços de Nomes de Domínio) em uma rede virtual unida pelo tempo de execução de integração do Azure-SSIS, siga as diretrizes na seção "Resolução de nomes usando seu próprio servidor DNS" do artigo [Resolução de nomes para instâncias de função e máquinas virtuais](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="network-security-group"></a>Grupo de segurança de rede
Se você precisar implementar o NSG (Grupo de Segurança de Rede) em uma rede virtual ingressada por seu tempo de execução de integração do Azure-SSIS, permita o tráfego de entrada/saída pelas portas a seguir:

| Portas | Direção | Protocolo de transporte | Finalidade | Origem da entrada/destino da saída |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100, 20100, 30100 (se você unir o IR a uma rede virtual clássica)<br/><br/>29876, 29877 (se você unir o IR a uma rede virtual do Azure Resource Manager) | Entrada | TCP | Os serviços do Azure usam essas portas para comunicarem-se com os nós de seu tempo de execução de integração do Azure-SSIS na rede virtual. | Internet | 
| 443 | Saída | TCP | Os nós do tempo de execução de integração do Azure-SSIS na rede virtual usam essa porta para acessar os serviços do Azure, como o Armazenamento do Microsoft Azure e Hubs de Eventos do Azure. | Internet | 
| 1433<br/>11000-11999<br/>14000-14999  | Saída | TCP | Os nós do tempo de execução de integração do Azure-SSIS na rede virtual usam essas portas para acessar o SSISDB hospedado pelo 	servidor do Banco de Dados SQL do Microsoft Azure (essa finalidade não é aplicável ao SSISDB hospedado pela Instância Gerenciada do Banco de Dados SQL.) | Internet | 

## <a name="azure-portal-data-factory-ui"></a>Portal do Azure (Interface do usuário do Data Factory)
Esta seção mostra como unir um tempo de execução do Azure-SSIS existente a uma rede virtual (clássica ou do Azure Resource Manager) usando o Portal do Azure e a interface do usuário do Data Factory. Primeiro, é necessário configurar a rede virtual corretamente antes de unir o IR do Azure-SSIS a ela. Percorra uma das próximas duas seções com base no tipo de sua rede virtual (clássica ou Azure Resource Manager). Em seguida, continue na terceira seção para unir o IR do Azure-SSIS à rede virtual. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Usar o portal para configurar uma rede virtual clássica
Primeiro você precisa configurar a rede virtual antes de adicionar um IR do Azure-SSIS a ela.

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, a interface do usuário do Data Factory tem suporte apenas nesses navegadores da Web.
2. Entre no [Portal do Azure](https://portal.azure.com).
3. Escolha **Mais serviços**. Filtre e selecione **Redes virtuais (clássicas)**.
4. Filtre e selecione sua rede virtual na lista. 
5. Na página **Rede virtual (clássica)**, selecione **Propriedades**. 

    ![ID do recurso de rede virtual clássica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Selecione o botão de cópia para que **ID DO RECURSO** copie a ID do recurso da rede clássica para a área de transferência. Salve a ID da área de transferência no OneNote ou em um arquivo.
6. Selecione **Sub-redes** no menu esquerdo. Certifique-se de que o número de **endereços disponíveis** seja maior do que os nós no tempo de execução de integração do Azure-SSIS.

    ![Número de endereços disponíveis na rede virtual](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Una **MicrosoftAzureBatch** à função **Colaborador da Máquina Virtual Clássica** para a rede virtual.

    a. Selecione **IAM (Controle de acesso)** no menu esquerdo e selecione **Adicionar** na barra de ferramentas. 
       !["Controle de Acesso" e botões "Adicionar"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Na página **Adicionar permissões**, selecione **Colaborador da Máquina Virtual Clássica** para **Função**. Cole **ddbf3205-c6bd-46ae-8127-60eb93363864** na caixa **Selecionar** e, em seguida, selecione **Lote do Microsoft Azure** da lista de resultados de pesquisa.   
       ![Resultados da pesquisa na página "Adicionar permissões"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. Selecione **Salvar** para salvar as configurações e fechar a página.  
       ![Salvar configurações de acesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. Confirme se você visualiza o **Lote do Microsoft Azure** na lista de colaboradores.  
       ![Confirmar acesso ao Lote do Azure](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

5. Verifique se o provedor do Lote do Azure está registrado na assinatura do Azure que possui a rede virtual. Ou, registre o fornecedor do Lote do Azure. Se você já tiver uma conta de lote do Azure em sua assinatura, sua assinatura está registrada para o lote do Azure.

   a. No Portal do Azure, selecione **Assinaturas** no menu esquerdo.

   b. Selecione sua assinatura.

   c. Selecione **Provedores de recurso** à esquerda e confirme se **Microsoft.Batch** é um provedor registrado.     
      ![Confirmação do status "Registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se você não visualizar **Microsoft.Batch** na lista, para registrá-lo [crie uma conta do Lote do Azure vazia](../batch/batch-account-create-portal.md) na sua assinatura. Você pode excluí-lo depois. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Usar o portal para configurar uma rede virtual do Azure Resource Manager
Primeiro você precisa configurar a rede virtual antes de adicionar um IR do Azure-SSIS a ela.

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, a interface do usuário do Data Factory tem suporte apenas nesses navegadores da Web.
2. Entre no [Portal do Azure](https://portal.azure.com).
3. Escolha **Mais serviços**. Filtre e selecione **Redes virtuais**.
4. Filtre e selecione sua rede virtual na lista. 
5. Na página **Rede virtual**, selecione **Propriedades**. 
6. Selecione o botão de cópia para que a **ID DO RECURSO** copie a ID do recurso da rede virtual para a área de transferência. Salve a ID da área de transferência no OneNote ou em um arquivo.
7. Selecione **Sub-redes** no menu esquerdo. Certifique-se de que o número de **endereços disponíveis** seja maior do que os nós no tempo de execução de integração do Azure-SSIS.
8. Verifique se o provedor do Lote do Azure está registrado na assinatura do Azure que possui a rede virtual. Ou, registre o fornecedor do Lote do Azure. Se você já tiver uma conta de lote do Azure em sua assinatura, sua assinatura está registrada para o lote do Azure.

   a. No Portal do Azure, selecione **Assinaturas** no menu esquerdo.

   b. Selecione sua assinatura. 
   
   c. Selecione **Provedores de recurso** à esquerda e confirme se **Microsoft.Batch** é um provedor registrado.     
      ![Confirmação do status "Registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se você não visualizar **Microsoft.Batch** na lista, para registrá-lo [crie uma conta do Lote do Azure vazia](../batch/batch-account-create-portal.md) na sua assinatura. Você pode excluí-lo depois.

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Unir o IR do Azure-SSIS a uma rede virtual


1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, a interface do usuário do Data Factory tem suporte apenas nesses navegadores da Web.
2. No [portal do Azure](https://portal.azure.com), selecione **Data factories** no menu à esquerda. Se não visualizar **Fábricas de dados** no menu, selecione **Mais serviços** e, em seguida, selecione **Fábricas de dados** na seção **INTELIGÊNCIA + ANÁLISE**. 
    
   ![Lista de fábricas de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. Selecione o data factory com o tempo de execução de integração do Azure-SSIS na lista. Você verá a home page do seu data factory. Selecione o bloco **Criar & Implantar**. Você visualiza a interface do usuário do Data Factory em uma guia separada. 

   ![Página inicial da data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. Na interface de usuário do Data Factory, alterne para a guia **Editar**, selecione **Conexões** e alterne para a guia **Tempos de execução de integração**. 

   ![Guia "tempos de execução de integração"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Se estiver executando o IR do Azure-SSIS, na lista de tempo de execução de integração, selecione o botão **Parar** na coluna **Ações** para o IR do Azure-SSIS. Você não pode editar um IR até que ela tenha sido interrompida. 

   ![Parar o IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. Na lista do tempo de execução de integração, selecione o botão **Editar** na coluna **Ações** para o IR do Azure-SSIS.

   ![Editar o tempo de execução de integração](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. Na página **Configurações Gerais** da janela **Instalação do Integration Runtime**, siga selecione **Avançar**. 

   ![Configurações gerais para configuração do IR](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. Na página **Configurações do SQL**, insira a senha do administrador e selecione **Avançar**.

   ![Configurações do SQL Server para configuração do IR](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. Na página **Configurações Avançadas**, faça as ações a seguir: 

   a. Selecione a caixa de seleção para **Selecionar uma VNet para o Tempo de Execução de Integração do Azure-SSIS unir e permitir que os serviços do Azure configurem as permissões/configurações da VNet**.

   b. Para **Tipo**, especifique se a rede virtual é uma rede virtual clássica ou uma rede virtual do Azure Resource Manager. 

   c. Para **Nome da VNET**, selecione a rede virtual.

   d. Para **Nome da Sub-rede**, selecione a sub-rede na rede virtual.

   e. Selecione **Atualização**. 

   ![Configurações avançadas para configuração do IR](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. Agora, você pode iniciar o IR usando o botão **Iniciar** na coluna **Ações** para o IR do Azure-SSIS. Demora aproximadamente 20 minutos para iniciar um IR do Azure-SSIS. 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>Configurar uma rede virtual
Primeiro você precisa configurar a rede virtual antes de adicionar um IR do Azure-SSIS a ela. Para configurar automaticamente as permissões/configurações da rede virtual para o tempo de execução de integração do Azure-SSIS unir a rede virtual, adicione o script a seguir:

```powershell
# Register to the Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Criar um IR do Azure-SSIS e uni-lo a uma rede virtual
Você pode criar um IR do Azure-SSIS e uni-lo a uma rede virtual ao mesmo tempo. Para obter as instruções e o script completo, consulte [Criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Unir um IR do Azure-SSIS existente a uma rede virtual
O script no artigo [Criar um tempo de execução de integração do Azure-SSIS](create-azure-ssis-integration-runtime.md) mostra como criar um IR do Azure-SSIS e uni-lo a uma rede virtual no mesmo script. Se você tiver um IR do Azure-SSIS existente, execute as seguintes etapas para uni-lo à rede virtual: 

1. Interrompa o IR do Azure-SSIS.
2. Configure o IR do Azure-SSIS para unir à rede virtual. 
3. Inicie o IR do Azure-SSIS. 

### <a name="define-the-variables"></a>Defina as variáveis

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a virtual network and Azure SQL Database Managed Instance (private preview) 
# Specify information about your classic or Azure Resource Manager virtual network.
$VnetId = "<Name of your Azure virtual network>"
$SubnetName = "<Name of the subnet in the virtual network>"
```

#### <a name="guidelines-for-selecting-a-subnet"></a>Diretrizes para selecionar uma sub-rede
-   Não selecione GatewaySubnet para implantar um Microsoft Integration Runtime do Azure-SSIS, porque ele é dedicado para gateways de rede virtual.
-   Verifique se a sub-rede que você selecionar tem espaço de endereço disponível suficiente para o IR do Azure-SSIS usar. Deixe pelo menos 2 * número de nós do IR em endereços IP disponíveis. O Azure reserva alguns endereços IP em cada sub-rede, os quais não podem ser usados. O primeiro e o último endereço IP das sub-redes são reservados para fins de conformidade de protocolo, juntamente com três outros endereços usados para os serviços do Azure. Para saber mais, veja [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets).


### <a name="stop-the-azure-ssis-ir"></a>Interrompa o IR do Azure-SSIS
Pare o tempo de execução de integração do Azure-SSIS antes de uni-lo a uma rede virtual. Esse comando libera todos os nós e para a cobrança:

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configurar as configurações de rede virtual para o IR do Azure-SSIS a unir
Registre o provedor de recursos do lote do Azure:

```powershell
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Configure o IR do Azure-SSIS
Para configurar o tempo de execução de integração do Azure-SSIS para unir à rede virtual, execute o comando `Set-AzureRmDataFactoryV2IntegrationRuntime`: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Inicie o IR do Azure-SSIS
Para iniciar o tempo de execução de integração do Azure-SSIS, execute o comando a seguir: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
Esse comando demora de 20 a 30 minutos para concluir.

## <a name="use-azure-expressroute-with-the-azure-ssis-ir"></a>Usar o ExpressRoute do Azure com o IR do Azure-SSIS

Você pode conectar um circuito do [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à sua infraestrutura de rede virtual para estender a rede local até o Azure. 

Uma configuração comum é usar o túnel forçado (anunciar um roteamento BGP, 0.0.0.0/0 para a rede virtual) que força o tráfego de Internet de saída do fluxo da rede virtual para o dispositivo da rede local para inspeção e registro em log. Este fluxo de tráfego quebra a conectividade entre o IR do Azure-SSIS na rede virtual com os serviços dependentes do Azure Data Factory. A solução é definir uma (ou mais) [UDRs (rotas definidas pelo usuário)](../virtual-network/virtual-networks-udr-overview.md) na sub-rede que contém o IR do Azure-SSIS. Uma UDR define rotas específicas de sub-rede que são consideradas no lugar da rota BGP.

Se for possível, use a seguinte configuração:
-   A configuração de ExpressRoute anuncia 0.0.0.0/0 e, por padrão, encapsula à força todo o tráfego de saída no local.
-   O UDR aplicado à sub-rede que contém o IR do Azure-SSIS define a rota 0.0.0.0/0 com o tipo do próximo salto para 'Internet'.
- 
O efeito combinado dessas etapas é que a UDR do nível de sub-rede tem precedência sobre o túnel forçado do ExpressRoute, garantindo acesso de Internet de saída do IR do Azure-SSIS.

Se você estiver preocupado em perder a capacidade de inspecionar o tráfego de Internet de saída dessa sub-rede, também poderá adicionar uma regra NSG na sub-rede para restringir os destinos de saída para [Endereços IP do data center do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

Veja [este script do PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) para obter um exemplo. Você precisa executar o script semanalmente para manter atualizada a lista de endereços IP do data center do Azure.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o tempo de execução do Azure-SSIS, consulte os tópicos a seguir: 

- [Tempo de execução de integração do Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais sobre tempos de execução de integração em geral, incluindo o IR do Azure-SSIS. 
- [Tutorial: implantar pacotes do SSIS para o Azure](tutorial-create-azure-ssis-runtime-portal.md). Este artigo fornece instruções passo a passo para criar um IR do Azure-SSIS. Ele usa um Banco de Dados SQL do Microsoft Azure para hospedar o catálogo do SSIS. 
- [Criar um Integration Runtime do Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo expande o tutorial e fornece instruções sobre como usar a Instância Gerenciada do Banco de Dados SQL do Azure (versão prévia privada) e unir o IR a uma rede virtual. 
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como recuperar informações sobre um IR do Azure-SSIS e descrições de status nas informações retornadas. 
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou remover um IR do Azure-SSIS. Adicionalmente, mostra como escalar horizontalmente o IR do Azure-SSIS adicionando nós. 
