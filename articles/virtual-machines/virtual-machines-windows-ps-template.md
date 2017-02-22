<properties
  pageTitle="Criar uma VM com o modelo do Resource Manager | Microsoft Docs"
  description="Use um modelo do Resource Manager e o PowerShell para criar facilmente uma nova máquina virtual do Windows."
  services="virtual-machines-windows" 
  documentationcenter=""
  author="davidmu1"
  manager="timlt"
  editor=""
  tags="azure-resource-manager"/>

<tags
  ms.assetid="19129d61-8c04-4aa9-a01f-361a09466805"
  ms.service="virtual-machines-windows"
  ms.workload="na"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="01/06/2017"
  ms.author="davidmu"/>


# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>Criar uma máquina virtual do Windows com um modelo do Gerenciador de Recursos

Este artigo mostra como implantar um modelo do Azure Resource Manager usando o PowerShell. O [modelo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json) implanta uma única máquina virtual que executa o Windows Server em uma nova rede virtual com uma única sub-rede.

Para obter uma descrição detalhada do recurso de máquina virtual, confira [Virtual machines in an Azure Resource Manager template](virtual-machines-windows-template-description.md) (Máquinas virtuais em um modelo do Azure Resource Manager). Para saber mais sobre todos os recursos em um modelo, confira [Passo a passo do modelo do Azure Resource Manager](../resource-manager-template-walkthrough.md).

A execução das etapas deste artigo deve levar aproximadamente cinco minutos.

## <a name="step-1-install-azure-powershell"></a>Etapa 1: instalar o PowerShell do Azure
Confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura e entrar em sua conta.

## <a name="step-2-create-a-resource-group"></a>Etapa 2: criar um grupo de recursos
Todos os recursos devem estar implantados em um [grupo de recursos](../azure-resource-manager/resource-group-overview.md).

1. Obtenha uma lista dos locais disponíveis nos quais os recursos podem ser criados.
   
  ```powershell   
  Get-AzureRmLocation | sort DisplayName | Select DisplayName
  ```

2. Crie o grupo de recursos no local selecionado. Este exemplo mostra a criação de um grupo de recursos chamado **myResourceGroup** no local **EUA Central**:

  ```powershell   
  New-AzureRmResourceGroup -Name "myResourceGroup" -Location "Central US"
  ```
   
  Você deverá ver algo como este exemplo:

  ```
  ResourceGroupName : myResourceGroup
  Location          : centralus
  ProvisioningState : Succeeded
  Tags              :
  ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup
  ```

## <a name="step-3-create-the-resources"></a>Etapa 3: Criar os recursos
Implante o modelo e forneça valores de parâmetro quando solicitado. Este exemplo implanta o modelo 101-vm-simple-windows no grupo de recursos que você criou:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" 
```
É solicitado que você forneça o nome da conta do administrador na VM, a senha da conta e o prefixo DNS.

Você deverá ver algo como este exemplo:

```
DeploymentName    : azuredeploy
ResourceGroupName : myResourceGroup
ProvisioningState : Succeeded
Timestamp         : 12/29/2016 8:11:37 PM
Mode              : Incremental
TemplateLink      :
                    Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/
                    101-vm-simple-windows/azuredeploy.json
                    ContentVersion : 1.0.0.0
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     myAdminUser
                    adminPassword    SecureString
                    dnsLabelPrefix   String                     myDomain
                    windowsOSVersion String                     2016-Datacenter
Outputs           :
                    Name             Type                       Value
                    ===============  =========================  ===========
                    hostname         String                     myDomain.centralus.cloudapp.azure.com
DeploymentDebugLogLevel :
```

> [!NOTE]
> Também é possível implantar modelos e parâmetros de arquivos locais. Para saber mais, consulte [Usando o Azure PowerShell com o Armazenamento do Azure](../storage/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Próximas etapas
* Se houver problemas com a implantação, a próxima etapa será examinar [Troubleshoot common Azure deployment errors with Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md) (Solucionar erros comuns de implantação do Azure com o Azure Resource Manager)
* Saiba como gerenciar a máquina virtual que você criou examinando [Gerenciar Máquinas Virtuais usando o Azure Resource Manager e o PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Jan17_HO2-->


