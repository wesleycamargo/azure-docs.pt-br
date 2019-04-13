---
title: Como alterar o modelo de licenciamento de uma VM do SQL Server no Azure | Microsoft Docs
description: Saiba como alternar o licenciamento de uma máquina virtual SQL no Azure.
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
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d5b0ff70baaba0b409cbd91ec2c68c77b98ba085
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548059"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure
Este artigo descreve como alterar o modelo de licenciamento de uma máquina virtual do SQL Server no Azure usando o novo provedor de recursos da VM do SQL – **Microsoft.SqlVirtualMachine**. Há dois modelos para uma máquina virtual (VM) que hospeda o SQL Server - pago conforme o uso, de licenciamento e Traga sua própria licença (BYOL). E agora, usando o portal do Azure ou a CLI do Azure, você pode modificar sua VM do SQL Server usa qual modelo de licenciamento. 

O **pré-pago** modelo (PAYG) significa que o custo por segundo da execução da VM do Azure inclui o custo da licença do SQL Server.

O **traga-your-própria licença** modelo (BYOL) também é conhecido como o [benefício de híbrido do Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), e permite que você use sua própria licença do SQL Server com uma VM executando o SQL Server. Para obter mais informações sobre preços, confira [Guia de precificação da VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Alternar entre os dois modelos de licença não resulta em **nenhum tempo de inatividade**, não reinicia a VM, não gera **nenhum custo adicional** (na verdade, ativar o AHB *reduz* o custo) e **entra em vigor imediatamente**. 

## <a name="remarks"></a>Comentários

 - Os clientes de CSP podem utilizar o AHB primeiro implantando uma VM pré-paga e, em seguida, convertendo-a para Traga sua própria licença. 
 - Ao registrar uma imagem personalizada de VM do SQL Server com o provedor de recursos, especifique o tipo de licença como = 'AHUB'. Deixando a licença de tipo em branco, ou especificar 'PAYG' fará com que o registro falha. 
 - Se você remover o recurso de VM do SQL Server, você voltará para a configuração de embutido em código de licença da imagem. 
 - A capacidade de alterar o modelo de licenciamento é um recurso do provedor de recursos de VM do SQL. Manipular uma VM do SQL Server dentro do portal automaticamente registra sua VM do SQL Server com o provedor de recursos. No entanto, _algumas_ os clientes podem precisar manualmente [registrar sua VM do SQL Server](#register-sql-server-vm-with-the-sql-vm-resource-provider) com o provedor de recursos, tais como:
     - Clientes que implantaram a sua VM do SQL Server usando o PowerShell ou CLI do Azure. 
     - Clientes que é instalado automaticamente o SQL Server em uma imagem do SQL Server. 
     - Clientes que implantaram suas VMs usando VHDs personalizados. 

 
## <a name="limitations"></a>Limitações

 - A capacidade de converter o modelo de licenciamento está disponível atualmente somente na inicialização com uma imagem de VM do SQL Server pré-paga. Se você iniciar com uma imagem Traga sua própria licença no portal, não poderá converter essa imagem para pré-paga.
 - Somente há suporte para alterar o modelo de licenciamento para máquinas virtuais implantadas usando o modelo do Resource Manager. Não há suporte para VMs implantadas usando o modelo clássico. 
 - Alterar o modelo de licenciamento é habilitado apenas para instalações de nuvem pública.
 - Alterar o modelo de licenciamento tem suporte apenas em máquinas virtuais que têm uma única NIC (interface de rede). Em máquinas virtuais que têm mais de uma NIC, você deve primeiro remover uma das NICs (usando o portal do Azure) antes de tentar o procedimento. Caso contrário, você enfrentará um erro semelhante ao seguinte: ` The virtual machine '\<vmname\>' has more than one NIC associated.` Embora você possa ser capaz de adicionar o NIC para a VM depois de alterar o modo de licenciamento, operações realizadas por meio da folha de configuração do SQL, como aplicação de patch automática e de backup, não serão consideradas com suporte.

## <a name="prerequisites"></a>Pré-requisitos

O uso do provedor de recursos de VM do SQL requer a extensão IaaS do SQL. Assim, para continuar utilizando o provedor de recursos de VM do SQL, você precisará do seguinte:
- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- [Do software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Um *pré-pagas* [VM do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) com o [extensão SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) instalado. 

## <a name="with-the-azure-portal"></a>Com o portal do Azure

Você pode modificar o modelo de licenciamento diretamente do portal. 

1. Navegue até sua VM do SQL Server dentro de [portal do Azure](https://portal.azure.com). 
1. Selecione **configuração do SQL Server** na **configurações** painel. 
1. Selecione **edite** na **licença do SQL Server** painel para modificar a licença. 

![AHB no Portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Essa opção não está disponível para imagens traga-your-própria licença. 

## <a name="with-azure-cli"></a>Com a CLI do Azure

Você pode usar a CLI do Azure para alterar seu modelo de licenciamento.  

O trecho de código a seguir alterna o modelo pago conforme o uso de licença para BYOL (ou usando o benefício híbrido do Azure):

```azurecli
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

O trecho de código a seguir alterna o modelo traga-your-própria licença para pré-pago: 

```azurecli
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

## <a name="register-sql-server-vm-with-the-sql-vm-resource-provider"></a>Registre-se a VM do SQL Server com o provedor de recursos de VM do SQL
Em determinadas situações, talvez você precise registrar manualmente sua VM do SQL Server com o provedor de recursos de VM do SQL. Para fazer isso, talvez também precise registrar manualmente o provedor de recursos com sua assinatura. 


### <a name="register-sql-vm-resource-provider-with-subscription"></a>Registrar o provedor de recursos de VM do SQL com a assinatura 

Para registrar sua VM do SQL Server no provedor de recursos do SQL, você deverá registrar o provedor de recursos em sua assinatura. Você pode fazer isso com o portal do Azure ou CLI do Azure. 

#### <a name="with-the-azure-portal"></a>Com o portal do Azure
As etapas a seguir registrará o provedor de recursos do SQL à sua assinatura do Azure usando o portal do Azure. 

1. Abra o portal do Azure e navegue até **All Services**. 
1. Navegue até **assinaturas** e selecione a assinatura de seu interesse.  
1. Na folha **Assinaturas**, navegue para **Provedor de recursos**. 
1. Tipo `sql` no filtro para exibir os provedores de recursos relacionados ao SQL. 
1. Selecione *Registrar*, *Registrar novamente* ou *Cancelar registro* de para o provedor **Microsoft.SqlVirtualMachine**, dependendo da ação desejada. 

   ![Modificar o provedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

#### <a name="with-azure-cli"></a>Com a CLI do Azure
O trecho de código a seguir irá registrar o provedor de recursos de VM do SQL à sua assinatura do Azure. 

```azurecli
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```


### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Registrar a VM do SQL Server no provedor de recursos do SQL
Depois que o provedor de recursos de VM do SQL tiver sido registrado para sua assinatura, você pode registrar sua VM do SQL Server com o provedor de recursos usando a CLI do Azure. 

Registre-se a VM do SQL Server usando a CLI do Azure com o trecho de código a seguir: 

```azurecli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```


## <a name="known-errors"></a>Erros conhecidos

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>A Extensão IaaS do SQL não está instalada na máquina virtual
A extensão IaaS do SQL é um pré-requisito necessário para registrar sua VM do SQL Server com o provedor de recursos de VM do SQL. Se tentar registrar a VM do SQL Server antes de instalar a extensão IaaS do SQL, você encontrará o seguinte erro:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Para resolver esse problema, instale a extensão IaaS do SQL antes de tentar registrar a VM do SQL Server. 

  > [!NOTE]
  > A instalação da extensão IaaS do SQL reiniciará o serviço do SQL Server e só deve ser realizada durante uma janela de manutenção. Para obter mais informações, confira [Instalação da extensão de IaaS do SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 


### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>O recurso 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/ < resource-group >' no grupo de recursos '< resource-group >' não foi encontrado. A propriedade 'sqlServerLicenseType' não pode ser encontrada neste objeto. Verifique se a propriedade existe e pode ser definida.
Esse erro ocorre ao tentar alterar o modelo de licenciamento em uma VM do SQL Server que não foi registrado com o provedor de recursos do SQL. Você precisará registrar o provedor de recursos em sua [assinatura](#register-sql-vm-resource-provider-with-subscription)e, em seguida, registre sua VM do SQL Server com o SQL [provedor de recursos](#register-sql-server-vm-with-sql-resource-provider). 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server em um FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientações sobre preço do SQL Server em uma VM Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server em um notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


