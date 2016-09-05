<properties
	pageTitle="Clonar uma Máquina Virtual clássica para o Azure Resource Manager usando scripts do PowerShell"
	description="Este artigo mostra como clonar uma única Máquina Virtual clássica para o Azure Resource Manager usando scripts do PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkays"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="singhkay"/>

# Clonar uma Máquina Virtual clássica para o Azure Resource Manager usando scripts do PowerShell

Este artigo mostrará como usar os scripts localizados em [Azure/classic-iaas-resourcemanager-migration](https://github.com/Azure/classic-iaas-resourcemanager-migration) para clonar uma **única** Máquina Virtual clássica para o modelo de implantação do Azure Resource Manager.

>[AZURE.IMPORTANT]Fazer a clonagem com esses scripts causará um tempo de inatividade da sua Máquina Virtual clássica. Se você estiver procurando por uma migração da plataforma com suporte, visite
- [Platform supported migration of IaaS resources from Classic to Azure Resource Manager stack (Migração de recursos de IaaS com suporte da plataforma da pilha Clássica para o Azure Resource Manager)](./virtual-machines-windows-migration-classic-resource-manager.md)
- [Technical Deep Dive on Platform supported migration from Classic to Azure Resource Manager (Análise técnica aprofundada sobre a migração com suporte da plataforma do Clássico para o Azure Resource Manager)](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Migrate IaaS resources from Classic to Azure Resource Manager using Azure PowerShell (Migrar recursos de IaaS do Clássico para o Azure Resource Manager usando o Azure PowerShell)](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## Como obter os scripts

>[AZURE.NOTE]Os scripts do PowerShell não têm suporte oficial do Suporte da Microsoft. Portanto, são software livre no Github e aceitamos PRs para correções ou cenários adicionais.

Você pode obter os scripts baixando o arquivo zip do repositório especificado acima ou clonando o repositório usando **um** dos comandos a seguir

```
git clone https://github.com/Azure/classic-iaas-resourcemanager-migration.git
```

**OR**

```
git clone git@github.com:Azure/classic-iaas-resourcemanager-migration.git
```

## Importar o módulo de migração

A próxima etapa é importar o módulo para sua sessão do PowerShell. Isso pode ser feito com o comando abaixo

```
Import-Module .\asm2arm.psd1
```

## Autenticar com assinaturas do modelo de implantação Clássico e com assinaturas do Azure Resource Manager

Antes que você possa clonar sua Máquina Virtual clássica, será necessário autenticar sua sessão do PowerShell para as pilhas do Modelo de Implantação Clássico e do Azure Resource Manager. Isso pode ser feito usando os cmdlets a seguir

```
Add-AzureAccount
Login-AzureRmAccount
```

>[AZURE.IMPORTANT]Verifique se as assinaturas padrão foram selecionadas usando `Select-AzureSubscription` para o Modelo de Implantação Clássico e Set-AzureRmContext para o Azure Resource Manager

## Como usar os scripts

### Os cmdlets

A importação do módulo adicionará os dois cmdlets abaixo à sua sessão.

```
Add-AzureSMVmToRM
New-AzureSmToRMDeployment
```

Esses cmdlets realizam a funcionalidade especificada abaixo.

#### Add-AzureSMVmToRM
- Gera um conjunto de modelos do Azure Resource Manager e scripts obrigatórios do PowerShell (para copiar os blobs de disco e se a VM tiver extensões do Agente de VM), dada uma Máquina Virtual

>[AZURE.IMPORTANT]Se a opção `-Deploy` for especificada, esse cmdlet chamará o cmdlet `New-AzureSmToRMDeployment` para implantar os modelos do Azure Resource Manager e os scripts obrigatórios do PowerShell gerados acima.

#### New-AzureSmToRMDeployment
- Implanta os modelos e os scripts obrigatórios do PowerShell gerados pelo cmdlet `Add-AzureSMVmToRM` para iniciar a migração.

### Identificar a Máquina Virtual clássica a ser clonada

Você pode fazer isso armazenando o estado da Máquina Virtual clássica em uma variável e passando-a para o cmdlet `Add-AzureSMVmToRM` ou usando o `ServiceName` e os parâmetros `Name` da VM diretamente. Depois de identificar a Máquina Virtual clássica, você poderá usar o cmdlet `Add-AzureSMVmToRM` para cloná-la.

```
$vm = Get-AzureVm -ServiceName acloudservice -Name atestvm
Add-AzureSMVmToRM -VM $vm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy
```

**OR**

```
Add-AzureSMVmToRM -ServiceName acloudservice -Name atestvm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy
```

>[AZURE.IMPORTANT]Nos exemplos acima, como estamos usando a opção `-Deploy`, não será necessário usar o cmdlet `New-AzureSmToRMDeployment`.

## Como funciona a migração com estes scripts?

Os cmdlets passam por etapas para clonar a classVM e gerar recursos como tabelas de hash do PowerShell personalizadas para provedores de recursos de Armazenamento, Rede e Computação. Essas tabelas de hash que representam os recursos são anexadas a uma matriz, posteriormente transformada em um modelo ao serializar para JSON, e gravadas em um arquivo.

O modelo cria arquivos dependendo da existência de extensões do agente da Máquina Virtual clássica e do valor da opção DiskAction. Esses arquivos são colocados no diretório especificado pelo parâmetro OutputFileFolder. Os arquivos são:

1. `<ServiceName>-<VMName>-setup<optional timestamp>.json`: representa os recursos que precisam estar preparados antes da clonagem da Máquina Virtual clássica, e potencialmente serão os mesmos para todas as VMs subsequentes (nós não mantemos o estado entre execuções subsequentes, mas como uma conta de armazenamento precisa ser provisionada antes que uma operação de cópia de blob aconteça, o que é feito de forma obrigatória, é sensato agrupar recursos semelhantes em um só)

2.  `<ServiceName>-<VMName>-deploy<optional timestamp>.json`: contém o modelo para a Máquina Virtual do Resource Manager
3.  `<ServiceName>-<VMName>-parameters<optional timestamp>.json`: contém os parâmetros reais passados para os modelos
4.  `<ServiceName>-<VMName>-setextensions<optional timestamp>.json`: um conjunto de cmdlets do PowerShell a serem executados para a definição das extensões do agente de Máquina Virtual.
4.  `<ServiceName>-<VMName>-copydisks<optional timestamp>.json`: um conjunto de cmdlets do PowerShell a serem executados para a cópia de blobs de disco, caso a opção CopyDisks seja especificada.

Se o sinalizador -Deploy estiver definido, após a geração dos arquivos, o cmdlet implantará o modelo <NomeDoServiço>-<NomeDaVM>-setup.json, copiará os blobs de disco da Máquina Virtual de origem caso o parâmetro DiskAction esteja definido como CopyDisks e então implantará o modelo <NomeDoServiço>-<NomeDaVM>-deploy.json usando o arquivo <NomeDoServiço>-<NomeDaVM>-parameters.json para parâmetros. Após a conclusão da implantação da Máquina Virtual, se houver um script obrigatório (para extensões do Agente de máquina Virtual) ou um script para a cópia dos discos, eles serão executados.

### Detalhes de rede
A intenção do cmdlet não é clonar as configurações da rede clássica para o Resource Manager. Ele utiliza os recursos de rede da forma mais conveniente para clonar a Máquina Virtual em si. Veja o que acontece em condições diferentes:

1.  Nenhuma rede virtual no grupo de recursos de destino
    - A Máquina Virtual de origem não está em uma sub-rede: uma rede virtual padrão com 10.0.0.0/16 é criada junto com uma sub-rede, como espaço de endereço 10.0.0.0/22.
    - A Máquina Virtual de origem está em uma sub-rede: a rede virtual em que a Máquina Virtual está é descoberta, a especificação da rede virtual e as sub-redes são copiadas
2.  O grupo de recursos de destino tem uma rede virtual com um nome `<VM virtual network>arm` (a cadeia de caracteres 'arm' é acrescentada)
    - Se a rede virtual tiver uma sub-rede com os mesmos nome e espaço de endereço, use-a
    - Se nenhuma sub-rede apropriada for encontrada, localize um bloco de endereços nas sub-redes existentes com a máscara de bits 22 e use-o.

## Clonagem versus migração de plataforma

Há algumas diferenças entre a abordagem atual de clonagem versus a migração de plataforma com suporte.

### Clonagem


| Prós | Contras |
|--------------------------------------------------------|:----------------------------------------------------------------:|
| Clonar qualquer Máquina Virtual em um serviço de nuvem | Tempo de inatividade para as VMs, já que elas precisam ser desligadas |
| Clonar qualquer Máquina Virtual em uma Rede Virtual ou fora de uma Rede Virtual | Longos atrasos no cenário onde a cópia de blobs de disco é necessária |
| | Alteração na configuração de rede para a VM - IPs Internos, VIPs. |


### Migração de plataforma


| Prós | Contras |
|----------------------------------|:-------------------------------------------:|
| A maioria das configurações de VM em uma Rede Virtual não incorrerá em tempo de inatividade. | Ou todas as VM em um Serviço de Nuvem ou a Rede Virtual com todos os recursos implantados nela precisam ser migrados em conjunto. |
 
 
## Cenário sem suporte

**A seguir, os cenários que não estão no escopo dos scripts de clonagem**

 1. Parar uma Máquina Virtual em execução
 2. Alterar seus dados/discos
 3. Clonar VMs em execução
 4. Clonar automaticamente várias VMs em um cenário complexo
 5. Clonar toda a configuração de rede do ASM
 6. Cria VMs com balanceamento de carga. Vamos supor que essa seja uma configuração com que o especialista do Azure precisa lidar explicitamente
 
 
## Configurações testadas

O cmdlet _Add-AzureSMVmToRM_ foi validado com os seguintes casos de teste:

| # | Descrição |
|:---|:---|
| 1 | Máquina Virtual do Windows com um disco de sistema operacional existente |
| 2 | Máquina Virtual Linux com um disco de sistema operacional existente |
| 3 | Máquina Virtual do Windows com discos de sistema operacional e de dados existentes |
| 4 | Máquina Virtual Linux com discos de sistema operacional e de dados existentes |
| 5 | Máquina Virtual do Windows com um novo disco de SO correspondido na Galeria de Imagens |
| 6 | Máquina Virtual Linux com um novo disco de sistema operacional com correspondência na Galeria de Imagens |
| 7 | Máquina Virtual do Windows com um novo disco de sistema operacional e discos de dados vazios |
| 8 | Máquina Virtual Linux com um novo disco de sistema operacional e discos de dados vazios |
| 9 | Máquina Virtual do Windows com pontos de extremidade públicos |
| 10 | Máquina Virtual Linux com pontos de extremidade públicos |
| 11 | Máquina Virtual do Windows com um certificado do WinRM |
| 12 | Máquina Virtual do Windows em uma rede virtual e uma sub-rede |
| 13 | Máquina Virtual Linux em uma rede virtual e uma sub-rede |
| 14 | Máquina Virtual do Windows com extensões personalizadas |
| 15 | Máquina Virtual do Windows em um conjunto de disponibilidade |
| 16 | Máquina Virtual do Windows em um conjunto de disponibilidade, com vários discos de dados, pontos de extremidade públicos, em uma rede virtual e uma sub-rede e com extensões personalizadas |
| 17 | Máquina Virtual Linux em um conjunto de disponibilidade, com vários discos de dados, pontos de extremidade públicos, em uma rede virtual e uma sub-rede e com extensões personalizadas |

## Observações
1. Se várias VMs forem clonadas uma após a outra com pequenos intervalos entre elas, poderá haver conflitos de nome DNS para os endereços IP públicos, devido ao tempo de atualização de cache do DNS.

<!---HONumber=AcomDC_0824_2016-->