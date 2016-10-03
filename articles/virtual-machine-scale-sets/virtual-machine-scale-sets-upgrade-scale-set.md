<properties
	pageTitle="Implantar um aplicativo em Conjuntos de escala de máquina virtual | Microsoft Azure"
	description="Implantar um aplicativo em Conjuntos de escala de máquina virtual"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="guybo"/>


# Atualizar um Conjunto de Escala de Máquina Virtual

Este artigo descreve como você pode implementar uma atualização de sistema operacional para um Conjunto de Escala de VM do Azure sem nenhum tempo de inatividade. Nesse contexto, uma atualização do sistema operacional pode ser uma alteração da versão/sku do sistema operacional, ou alteração do URI de uma imagem personalizada. Atualizar sem tempo de inatividade significa atualizar uma VMs por vez ou em grupos (por exemplo, um domínio de falha por vez), em vez de todas de uma só vez. Ao fazer isso, todas as máquinas virtuais que não estiverem sendo atualizadas podem continuar em execução.

Para evitar ambiguidade, vamos distinguir três tipos de atualização de sistema operacional que talvez você queira fazer:

1. Alteração da versão ou do sku de uma imagem de plataforma. Por exemplo, alterar a versão 14.04.2-LTS do Ubuntu de 14.04.201506100 para 14.04.201507060, ou alterar o sku 15.10/mais recente do Ubuntu para 16.04.0-LTS/mais recente. Esse cenário é abordado neste artigo.

2. Você criou uma nova versão de uma imagem personalizada e deseja alterar o URI que aponta para a imagem (properties->virtualMachineProfile->storageProfile->osDisk->image->uri). Esse cenário é abordado neste artigo.

3. Corrigir o sistema operacional de dentro de uma VM (por exemplo: instalando um patch de segurança, usando o Windows Update etc.). Esse cenário tem suporte, mas não é abordado neste artigo.

Os dois primeiros requisitos têm suporte. Para o terceiro, pelo menos por enquanto, você precisaria criar um novo conjunto de escala para fazer isso. Este artigo aborda as opções 1 e 2. Observação: Conjuntos de Escala de VM que são implantados como parte de um [Service Fabric](https://azure.microsoft.com/services/service-fabric/) não são abordados aqui.

A sequência básica para alterar a versão/sku do sistema operacional de uma imagem de plataforma, ou o URI de uma imagem personalizada será semelhante ao seguinte:

* Obtenha o modelo do VMSS.

* Altere a versão, o sku ou o valor do URI no modelo.

* Atualize o modelo.

* Faça uma chamada a manualUpgrade nas VMs no conjunto de escala. Esta etapa só será relevante se a propriedade upgradePolicy de seu Conjunto de Escala for definida como "Manual". Se ela estiver definida como "Automática", todas as VMs serão atualizadas ao mesmo tempo, causando tempo de inatividade.


Com essas informações em mente, vamos ver como você pode atualizar a versão de um conjunto de escala no PowerShell, e usando a API REST. Esses exemplos abordam o caso de uma imagem de plataforma, mas esperamos ter fornecido informações suficientes para adaptar esse processo a uma imagem personalizada.

## PowerShell

Este exemplo atualiza um Conjunto de Escala de VM do Windows para uma nova versão, "4.0.20160229". Depois de atualizar o modelo, ele atualiza uma instância da VM por vez.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the VMSS model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Se você estivesse atualizando o URI de uma imagem personalizada em vez de alterar uma versão da imagem de plataforma, você poderia substituir a linha "definir a nova versão" por algo semelhante ao seguinte:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## Usando a API REST

Veja alguns exemplos de Python que usam a API REST do Azure para implantar uma atualização de versão do sistema operacional. Ambos usam a biblioteca leve de funções de wrapper da API REST do Azure, [azurerm](https://pypi.python.org/pypi/azurerm), para executar um GET no modelo de conjunto de escala e, em seguida, um PUT com um modelo atualizado. Eles também examinam os modos de exibição de instâncias da VM para identificar as VMs por domínio de atualização.

### vmssupgrade

O vmssupgrade é o script em Python para distribuir uma atualização do sistema operacional para um Conjunto de Escala de VM em execução, um domínio de atualização por vez. Você pode encontrar mais informações [aqui](https://github.com/gbowerman/vmsstools).

![Captura de tela de vmssupgrade](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Esse script permite que você escolha VMs específicas para atualizar, ou especifique um domínio de atualização, e oferece suporte à alteração de uma versão de imagem da plataforma OU à alteração do URI de uma imagem personalizada.

### vmsseditor

Essa ferramenta é um editor para fins gerais para Conjuntos de Escala de VM que mostra o status da VM como um mapa de dados no qual uma linha representa um domínio de atualização. Entre outras coisas, você pode atualizar o modelo de um VMSS com uma nova versão, sku ou URI de imagem personalizada e, em seguida, selecionar domínios de falha para atualização. Ao fazer isso, todas as VMs nesse domínio de atualização são atualizadas para o novo modelo. Como alternativa, você pode fazer uma atualização sem interrupção com base no tamanho do lote de sua escolha. O vmsseditor pode ser encontrado no seguinte [repositório github](https://github.com/gbowerman/vmssdashboard)

Por exemplo, aqui eu atualizei o modelo de um conjunto de escala para Ubuntu 14.04-2LTS versão 14.04.201507060. Observe que essa captura de tela é antiga; muitas outras opções já foram adicionadas a essa ferramenta.

![Captura de tela 1 de vmsseditor](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Depois de clicar em Atualizar e, em seguida, em Obter Detalhes novamente, as VMs no UD 0 começarão a ser atualizadas.

![Captura de tela 2 de vmsseditor](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

<!---HONumber=AcomDC_0921_2016-->