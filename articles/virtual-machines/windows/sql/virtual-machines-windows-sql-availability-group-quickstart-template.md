---
title: Criar o WSFC, o ouvinte e configurar o ILB para um grupo de disponibilidade Always On em uma VM do SQL Server com os Modelos de início rápido do Azure
description: Use Modelos de início rápido do Azure para simplificar o processo de criação de grupos de disponibilidade para VMs do SQL Server no Azure usando um modelo para criar o cluster, unir VMs do SQL ao cluster, criar o ouvinte e configurar o ILB.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 9be8717bc9b1d15a59486edf206dd0657a711c06
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360401"
---
# <a name="create-wsfc-listener-and-configure-ilb-for-an-always-on-availability-group-on-a-sql-server-vm-with-azure-quickstart-template"></a>Criar o WSFC, o ouvinte e configurar o ILB para um grupo de disponibilidade Always On em uma VM do SQL Server com os Modelos de início rápido do Azure
Este artigo descreve como usar os Modelos de início rápido do Azure para automatizar parcialmente a implantação de uma configuração de grupo de disponibilidade Always On para máquinas virtuais do SQL Server no Azure. Há dois Modelos de início rápido do Azure que são usados nesse processo. 

   | Modelo | DESCRIÇÃO |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Cria o Cluster de Failover do Windows e une as VMs do SQL Server a ele. |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Cria o ouvinte do grupo de disponibilidade e configura o balanceador de carga interno. |
   | &nbsp; | &nbsp; |

Outras partes da configuração do grupo de disponibilidade devem ser feitas manualmente, como criar o grupo de disponibilidade e criar o balanceador de carga interno. Este artigo fornece a sequência de etapas manuais e automatizadas.
 

## <a name="prerequisites"></a>Pré-requisitos 
Para automatizar a instalação de um grupo de disponibilidade Always On usando modelos de início rápido, você já deve ter os seguintes pré-requisitos: 
- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Um grupo de recursos com um [controlador de domínio](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-forest). 
- Uma ou mais [VMs ingressadas no domínio no Azure executando o SQL Server 2016 Enterprise Edition (ou superior)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) no mesmo conjunto de disponibilidade ou zona de disponibilidade que foram [registrados com o provedor de recursos da VM do SQL](#register-existing-sql-vm-with-new-resource-provider).  

## <a name="register-existing-sql-vm-with-new-resource-provider"></a>Registrar VM do SQL existente com o novo provedor de recursos
Como esses Modelos de início rápido do Azure para o grupo de disponibilidade dependem do provedor de recursos da VM do SQL (Microsoft.SqlVirtualMachine), as VMs existentes do SQL Server devem ser registradas com o provedor de recursos de VM do SQL. Ignore esta etapa se você criou sua VM do SQL Server depois de dezembro de 2018, porque todas as VMs do SQL Server criadas após essa data são registradas automaticamente. Esta seção fornece etapas para registrar o provedor usando o portal do Azure, mas você também pode usar o [PowerShell](virtual-machines-windows-sql-ahb.md#powershell). 

  >[!IMPORTANT]
  > Se você eliminar seu recurso de VM do SQL Server, voltará para a configuração de licença codificada da imagem. 

1. Abra o portal do Azure e navegue até **All Services**. 
1. Navegue até **assinaturas** e selecione a assinatura de seu interesse.  
1. Na folha **Assinaturas**, navegue para **Provedor de Recursos**. 
1. Tipo `sql` no filtro para exibir os provedores de recursos relacionados ao SQL. 
1. Selecione *Registrar*, *Registrar novamente* ou *Cancelar registro* de para o provedor **Microsoft.SqlVirtualMachine**, dependendo da ação desejada. 

  ![Modificar o provedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

## <a name="step-1---create-the-wsfc-and-join-sql-server-vms-to-the-cluster-using-quickstart-template"></a>Etapa 1: Criar o WSFC e unir VMs do SQL Server ao cluster usando o modelo de início rápido 
Depois que suas VMs do SQL Server forem registradas com o novo provedor de recursos da VM do SQL, é possível unir suas VMs do SQL Server no *SqlVirtualMachineGroup*. Este recurso define os metadados do Cluster de Failover do Windows, incluindo versão, edição, nome de domínio totalmente qualificado, contas do Active Directory para gerenciar o cluster e a conta de armazenamento como a testemunha em nuvem. Adicionar a VM do SQL Server a *SqlVirtualMachineGroup* inicia o serviço de Cluster de Failover do Windows e ingressa as VMs do SQL Server no cluster. Esta etapa é automatizada com o modelo de início rápido **101-sql-vm-ag-setup** e pode ser implementada com as seguintes etapas:

1. Navegue até o modelo de início rápido [**101-sql-vm-ag-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) e selecione **Implantar no Azure** para iniciar o modelo de início rápido no portal do Azure.
1. Preencha os campos obrigatórios para configurar os metadados do Cluster de Failover do Windows. Os campos opcionais podem ficar em branco.

    A tabela a seguir mostra os valores necessários para o modelo: 

   | **Campo** | Valor |
   | --- | --- |
   | **Assinatura** |  A assinatura em que estão suas VMs do SQL Server. |
   |**Grupo de recursos** | O grupo de recursos em que suas VMs do SQL Server residem. | 
   |**Nome do cluster de failover** | O nome desejado para o novo Cluster de Failover do Windows. |
   | **Lista de VMs existentes** | As VMs do SQL Server que você deseja que participem do grupo de disponibilidade e, assim, façam parte desse novo cluster. Separe esses valores com uma vírgula e um espaço (por exemplo: SQLVM1, SQLVM2). |
   | **Versão do SQL Server** | Selecione a versão do SQL Server de suas VMs na lista suspensa. No momento, há suporte apenas para imagens do SQL 2016 e do SQL 2017. |
   | **Nome de domínio totalmente qualificado existente** | O FQDN existente para o domínio no qual residem suas VMs do SQL Server. |
   | **Conta de domínio existente** | Uma conta de domínio existente que tenha acesso de administrador do sistema para o SQL Server. | 
   | **Senha da conta de domínio** | A senha da conta de domínio mencionada anteriormente. | 
   | **Conta do serviço SQL existente** | A conta de usuário de domínio que está sendo usada para controlar o serviço do SQL Server. Essas informações podem ser encontradas usando o [**SQL Server Configuration Manager**](https://docs.microsoft.com/sql/relational-databases/sql-server-configuration-manager?view=sql-server-2017). |
   | **Senha de serviço do SQL** | A senha usada pela conta de usuário de domínio que controla o serviço do SQL Server. |
   | &nbsp; | &nbsp; |

1. Se você concorda com os termos e as condições, marque a caixa de seleção ao lado de **Concordo com os termos e condições acima** e selecione **Comprar** para finalizar a implantação do Modelo de início rápido. 
1. Para monitorar sua implantação, selecione-a no ícone de sino **Notificações** em sua faixa de navegação superior ou navegue até seu **Grupo de recursos** no portal do Azure, selecione **Implantações** no campo **Configurações** e escolha a implantação “Microsoft.Template”. 

## <a name="step-2---manually-create-the-availability-group"></a>Etapa 2: Criar manualmente o grupo de disponibilidade 
Crie manualmente o grupo de disponibilidade, como faria normalmente, usando [PowerShell](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell?view=sql-server-2017), [SQL Server Management Studio](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio?view=sql-server-2017) ou [Transact-SQL](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql?view=sql-server-2017). 

  >[!IMPORTANT]
  > **Não** crie um ouvinte neste momento porque isso é automatizado com o modelo de início rápido **101-sql-vm-aglistener-setup** na etapa 4. 

## <a name="step-3---manually-create-the-internal-load-balancer-ilb"></a>Etapa 3: Criar manualmente o balanceador de carga interno (ILB)
O ouvinte do grupo de disponibilidade Always On exige um balanceador de carga interno (ILB) do Azure. O ILB fornece um endereço IP "flutuante" para o ouvinte do grupo de disponibilidade, que oferece failover e reconexão mais rápidos. Se as VMs do SQL Server em um grupo de disponibilidade fizerem parte do mesmo conjunto de disponibilidade, use um balanceador de carga básico; caso contrário, use um balanceador de carga padrão.  **O ILB deve estar na mesma vNet que as instâncias de VM do SQL Server.** O ILB só precisa ser criado, o restante da configuração (como o pool de back-end, a investigação de integridade e as regras de balanceamento de carga) é tratado pelo modelo de início rápido **101-sql-vm-aglistener-setup** na etapa 4. 

1. No Portal do Azure, abra o grupo de recursos que contém as máquinas virtuais do SQL Server. 
2. No grupo de recursos, clique em **Adicionar**.
3. Pesquise por **balanceador de carga** e, em seguida, nos resultados da pesquisa, selecione **Balanceador de Carga**, que é publicado pela **Microsoft**.
4. Na folha **Balanceador de Carga**, clique em **Criar**.
5. Na caixa de diálogo **Criar balanceador de carga**, configure o balanceador de carga da seguinte maneira:

   | Configuração | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa o balanceador de carga. Por exemplo, **sqlLB**. |
   | **Tipo** |**Interna**: a maioria das implementações usa um balanceador de carga interno que permite a conexão dos aplicativos na mesma rede virtual ao grupo de disponibilidade.  </br> **Externa**: permite que os aplicativos se conectem ao grupo de disponibilidade por meio de uma conexão à Internet pública. |
   | **Rede virtual** |Selecione a rede virtual na qual estão as instâncias do SQL Server. |
   | **Sub-rede** |Selecione a sub-rede na qual estão as instâncias do SQL Server. |
   | **Atribuição de endereço IP** |**Estático** |
   | **Endereço IP privado** |Especifique um endereço IP disponível na sub-rede. Você usa esse endereço IP ao criar um ouvinte no cluster.|
   | **Assinatura** |Se você tiver várias assinaturas, este campo poderá aparecer. Selecione a assinatura que você deseja associar a esse recurso. Normalmente, trata-se da mesma assinatura de todos os recursos do grupo de disponibilidade. |
   | **Grupo de recursos** |Selecione o grupo de recursos no qual estão as instâncias do SQL Server. |
   | **Localidade** |Selecione o local do Azure no qual estão as instâncias do SQL Server. |
   | &nbsp; | &nbsp; |

6. Selecione **Criar**. 


  >[!NOTE]
  > O recurso de IP público de cada VM do SQL Server deve ter uma SKU padrão para ser compatível com o balanceador de carga padrão. Para determinar a SKU do recurso de IP público da sua VM, navegue até seu **Grupo de recursos**, selecione seu recurso de **Endereço IP público** para a VM do SQL Server desejada e localize o valor em **SKU** no dashboard **Visão geral**. 

## <a name="step-4---create-the-ag-listener-and-configure-the-ilb-with-the-quickstart-template"></a>Etapa 4: Criar o ouvinte do grupo de disponibilidade e configurar o ILB com o modelo de início rápido

Crie o ouvinte do grupo de disponibilidade e configure o balanceador de carga interno (ILB) automaticamente com o modelo de início rápido **101-sql-vm-aglistener-setup**, pois ele provisiona o recurso Microsoft.SqlVirtualMachine/Grupos de máquina virtual do SQL/Ouvinte do grupo de disponibilidade. O modelo de início rápido **101-sql-vm-aglistener-setup**, por meio do provedor de recursos de VM do SQL, faz o seguintes:

 - Define as configurações de rede para o cluster e o ILB. 
 - Configura o pool de back-end do ILB, a investigação de integridade e as regras de balanceamento de carga.
 - Cria o ouvinte do grupo de disponibilidade com o nome e o endereço IP fornecidos.

Para configurar o ILB e criar o ouvinte do grupo de disponibilidade, faça o seguinte:
1. Navegue até o modelo de início rápido [**101-sql-vm-aglistener-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) e selecione **Implantar no Azure** para iniciar o modelo de início rápido no portal do Azure.
1. Preencha os campos obrigatórios para configurar o ILB e crie o ouvinte do grupo de disponibilidade. Os campos opcionais podem ficar em branco. 

    A tabela a seguir mostra os valores necessários para o modelo: 

   | **Campo** | Valor |
   | --- | --- |
   |**Grupo de recursos** | O grupo de recursos em que estão suas VMs do SQL Server e o grupo de disponibilidade. | 
   |**Nome do cluster de failover existente** | O nome do cluster em que suas VMs do SQL Server ingressaram. |
   | **Grupo de disponibilidade do SQL existente**| O nome do grupo de disponibilidade de que suas VMs do SQL Server fazem parte. |
   | **Lista de VMs existentes** | Os nomes das VMs do SQL Server que fazem parte do grupo de disponibilidade mencionado anteriormente. Os nomes devem ser separados por uma vírgula e um espaço (por exemplo: SQLVM1, SQLVM2). |
   | **Nome de domínio totalmente qualificado existente** | O FQDN existente para o domínio no qual residem suas VMs do SQL Server. |
   | **Ouvinte** | O nome de DNS que você deseja atribuir ao ouvinte. Por padrão, esse modelo especifica o nome “aglistener”, mas isso pode ser alterado. |
   | **Porta do ouvinte** | A porta que você deseja que o ouvinte use. Normalmente, essa porta deve ser a porta padrão 1433 e, como tal, esse é o número da porta especificado por este modelo. No entanto, se a porta padrão for alterada, a porta do ouvinte deve usar esse valor em vez disso. | 
   | **vNet existente** | O nome da rede virtual em que residem as suas VMs do SQL Server e o ILB. |
   | **Sub-rede existente** | O *nome* da sub-rede interna das suas VMs do SQL Server (ex: padrão). Esse valor pode ser determinado navegando até seu **Grupo de recursos**, selecionando sua **vNet** e as **Sub-redes** no dashboard **Configurações** e copiando o valor em **Nome**. |
   | **Balanceador de carga interno existente** | O nome do ILB que você criou na etapa 3. |
   | **Porta de investigação** | A porta de investigação que você deseja que o ILB use. O modelo usa 59999 por padrão, mas esse valor pode ser alterado. |
   | &nbsp; | &nbsp; |

1. Se você concordar com os termos e as condições, marque a caixa de seleção ao lado de **Concordo com os termos e condições acima** e selecione **Comprar** para finalizar a implantação do modelo de início rápido. 
1. Para monitorar sua implantação, selecione-a no ícone de sino **Notificações** em sua faixa de navegação superior ou navegue até seu **Grupo de recursos** no portal do Azure, selecione **Implantações** no campo **Configurações** e escolha a implantação “Microsoft.Template”. 

  >[!NOTE]
  >Se sua implantação falhar na metade do caminho, você precisará [remover manualmente o ouvinte recém-criado](#remove-availability-group-listener) usando o PowerShell antes de reimplantar o modelo de início rápido **101-sql-vm-aglistener-setup**. 

## <a name="remove-availability-group-listener"></a>Remover o ouvinte do grupo de disponibilidade
Se você precisar remover posteriormente o ouvinte do grupo de disponibilidade configurado pelo modelo, deve percorrer o provedor de recursos de VM do SQL. Uma vez que o ouvinte é registrado por meio do provedor de recursos de VM do SQL, apenas excluí-lo por meio do SQL Server Management Studio é insuficiente. Na verdade, ele deve ser excluído por meio do provedor de recursos de VM do SQL usando o PowerShell. Isso remove os metadados do ouvinte do grupo de disponibilidade do provedor de recursos de VM do SQL e exclui fisicamente o ouvinte do grupo de disponibilidade. 

O trecho de código a seguir exclui o ouvinte de grupo de disponibilidade do SQL do provedor de recursos do SQL e do seu grupo de disponibilidade: 

```PowerShell
# Remove the AG listener
# example: Remove-AzureRmResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzureRmResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="known-issues-and-errors"></a>Erros e problemas conhecidos
Esta seção aborda alguns problemas conhecidos e suas soluções possíveis. 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>O ouvinte de grupo de disponibilidade para o grupo de disponibilidade “\<AG-Name>” já existe
O grupo de disponibilidade selecionado usado no Modelo de início rápido do Azure para o ouvinte de grupo de disponibilidade já contém um ouvinte, fisicamente dentro do grupo de disponibilidade ou como metadados no provedor de recursos de VM do SQL.  Remova o ouvinte usando o [PowerShell](#remove-availability-group-listener) antes de reimplantar o modelo de início rápido **101-sql-vm-aglistener-setup**. 

### <a name="connection-only-works-from-primary-replica"></a>A conexão funciona apenas na réplica primária
Esse comportamento é provavelmente devido a uma implantação de modelo **101-sql-vm-aglistener-setup** que deixou a configuração de ILB em um estado inconsistente. Verifique se o pool de back-end lista o conjunto de disponibilidade, e se há regras para a análise da integridade e o balanceamento de carga. Se alguma coisa estiver faltando, isso significa que a configuração do ILB está em um estado inconsistente. 

Para resolver esse comportamento, remova o ouvinte usando o [PowerShell](#remove-availability-group-listener), exclua o balanceador de carga interno no portal do Azure e comece novamente na [etapa 3](#step-3---manually-create-the-internal-load-balanced-ilb). 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - apenas a lista da máquina virtual do SQL pode ser atualizada
Esse erro pode ocorrer ao implantar o modelo **101-sql-vm-aglistener-setup** se o ouvinte tiver sido excluído por meio do SQL Server Management Studio (SSMS), mas não tiver sido excluído do provedor de recursos de VM do SQL. Excluir o ouvinte por meio do SSMS não remove os metadados do ouvinte do provedor de recursos de VM do SQL; o ouvinte deve ser excluído do provedor de recursos usando o [PowerShell](#remove-availability-group-listener). 


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral da VM do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Perguntas frequentes da VM do SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guia de preços da VM do SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão da VM do SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Alternar modelos de licenciamento para uma VM do SQL Server](virtual-machines-windows-sql-ahb.md)



