<properties
	pageTitle="Capturar uma VM do Linux para usar como um modelo | Microsoft Azure"
	description="Saiba como capturar uma imagem de uma VM (máquina virtual) do Azure baseada no Linux criada com o modelo de implantação do Gerenciador de Recursos do Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/15/2016"
	ms.author="danlep"/>


# Como capturar uma máquina virtual Linux para ser usada como um modelo do Gerenciador de Recursos

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-linux-classic-capture-image.md).


Este artigo mostra como usar a CLI do Azure (Interface de Linha de Comando) do Azure para capturar uma máquina virtual do Azure que executa o Linux para poder usá-la como um modelo do Azure Resource Manager para criar outras máquinas virtuais. Esse modelo especifica o disco do SO e os discos de dados anexados à máquina virtual. Ele não inclui os recursos da rede virtual que você precisará para criar uma VM do Gerenciador de Recursos do Azure, portanto, na maioria dos casos você precisará defini-los separadamente antes de criar outra máquina virtual que usa o modelo.

## Antes de começar

Estas etapas pressupõem que você já criou uma máquina virtual do Azure no modelo de implantação do Gerenciador de Recursos do Azure e configurou o sistema operacional, inclusive anexou quaisquer discos de dados e fez outras personalizações, como a instalação de aplicativos. Você pode fazer isso de diversas maneiras, inclusive por meio da CLI do Azure. Se você ainda não fez isso, confira estas instruções para usar a CLI do Azure no modo do Gerenciador de Recursos do Azure:

- [Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e a CLI do Azure](virtual-machines-linux-cli-deploy-templates.md)

Por exemplo, você pode criar um grupo de recursos denominado *MyResourceGroup* na região Central dos Estados Unidos. Em seguida, use um comando **azure vm quick-create** semelhante ao seguinte para implantar uma VM do Ubuntu 14.04 LTS no grupo de recursos.

 	azure vm quick-create -g MyResourceGroup -n <your-virtual-machine-name> "centralus" -y Linux -Q canonical:ubuntuserver:14.04.2-LTS:latest -u <your-user-name> -p <your-password>

Depois da VM ser provisionada e estar em execução, você poderá anexar e montar um disco de dados. Consulte as instruções [aqui](virtual-machines-linux-add-disk.md).


## Capturar a VM

1. Quando você estiver pronto para capturar a VM, conecte-a usando o cliente SSH.

2. Na janela SSH, digite o comando a seguir. Observe que a saída do **waagent** pode variar um pouco dependendo da versão do utilitário:

	`sudo waagent -deprovision+user`

	Esse comando tentará limpar o sistema e torná-lo adequado para reprovisionamento. Essa operação realiza as seguintes tarefas:

	- Remove as chaves de host SSH (se Provisioning.RegenerateSshHostKeyPair for 'y' no arquivo de configuração)
	- Limpa a configuração de servidor de nomes em /etc/resolv.conf
	- Remove a senha do usuário `root` de /etc/shadow (se Provisioning.DeleteRootPassword for 'y' no arquivo de configuração)
	- Remove concessões de cliente DHCP em cache
	- Reinicia o nome de host para localdomain.localdomain
	- Exclui a última conta de usuário provisionada (obtida em /var/lib/waagent) e os dados associados.

	>[AZURE.NOTE] O desprovisionamento exclui arquivos e dados em um esforço para "generalizar" a imagem. Execute o comando apenas em uma VM que você pretende capturar como uma imagem. Ele não garante que a imagem esteja sem nenhuma informação confidencial ou seja adequada para redistribuição a terceiros.

3. Digite **y** para continuar. Você pode adicionar o parâmetro **-force** para evitar essa etapa de confirmação.

4. Digite **exit** para fechar o cliente SSH.

	>[AZURE.NOTE] Nas próximas etapas, presumimos que você já [instalou a CLI do Azure](../xplat-cli-install.md) no computador cliente.

5. No computador cliente, abra a CLI do Azure e faça logon com sua assinatura do Azure. Para obter detalhes, leia [Conectar-se a uma assinatura do Azure da CLI do Azure](../xplat-cli-connect.md).

6. Verifique se você está no modo Gerenciador de Recursos:

	`azure config mode arm`

7. Pare a VM cujo provisionamento você já cancelou usando o seguinte comando:

	`azure vm stop –g <your-resource-group-name> -n <your-virtual-machine-name>`

8. Generalize a VM com o seguinte comando:

	`azure vm generalize –g <your-resource-group-name> -n <your-virtual-machine-name>`

9. Agora, capture a imagem e um modelo de arquivo local com o seguinte comando:

	`azure vm capture <your-resource-group-name>  <your-virtual-machine-name> <your-vhd-name-prefix> -t <your-template-file-name.json>`

	Este comando cria uma imagem generalizada do sistema operacional usando o prefixo do nome do VHD especificado para os discos da VM. Os arquivos de imagem VHD são criados por padrão na mesma conta de armazenamento da VM original usada. A opção **-t** cria um modelo de arquivo JSON local você pode usar para criar uma nova VM a partir da imagem.

>[AZURE.TIP] Para encontrar o local de uma imagem, abra o modelo de arquivo JSON. Em **storageProfile**, encontre o **uri** da **imagem** localizado no contêiner do **sistema**. Por exemplo, o uri da imagem de disco do sistema operacional é semelhante a `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/<your-image-prefix>-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.

## Implantar uma nova VM a partir da imagem capturada
Agora, use a imagem com um modelo para criar uma nova VM do Linux. Essas etapas mostram como usar a CLI do Azure e o modelo de arquivo JSON criado com o comando `azure vm capture` para criar a VM em uma nova rede virtual.

### Criar recursos da rede

Para usar o modelo, primeiro é necessário configurar uma rede virtual e uma NIC para sua nova VM. Recomendamos que você crie um novo grupo de recursos para esses recursos. Execute comandos semelhantes aos seguintes, substituindo os nomes dos seus recursos e um local apropriado do Azure ("centralus" nesses comandos):

	azure group create <your-new-resource-group-name> -l "centralus"

	azure network vnet create <your-new-resource-group-name> <your-vnet-name> -l "centralus"

	azure network vnet subnet create <your-new-resource-group-name> <your-vnet-name> <your-subnet-name>

	azure network public-ip create <your-new-resource-group-name> <your-ip-name> -l "centralus"

	azure network nic create <your-new-resource-group-name> <your-nic-name> -k <your-subnetname> -m <your-vnet-name> -p <your-ip-name> -l "centralus"

Para implantar uma VM a partir da imagem usando o JSON salvo durante a captura, será necessário a ID do NIC. Obtenha-a executando o seguinte comando.

	azure network nic show <your-new-resource-group-name> <your-nic-name>

A **ID** na saída é uma cadeia de caracteres semelhante a esta.

	/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<your-new-resource-group-name>/providers/Microsoft.Network/networkInterfaces/<your-nic-name>



### Criar uma nova implantação
Agora, execute o seguinte comando para criar sua VM a partir da imagem capturada da VM e do arquivo de modelo JSON salvo.

	azure group deployment create <your-new-resource-group-name> <your-new-deployment-name> -f <your-template-file-name.json>

Você precisará fornecer um novo nome da VM, nome de usuário administrador, senha e a ID da NIC criada anteriormente.

	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	vmName: mynewvm
	adminUserName: myadminuser
	adminPassword: ********
	networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic

Se sua implantação for bem-sucedida, você verá uma saída semelhante à seguinte.

	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "dlnewdeploy"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mynewdeploy
	data:    ResourceGroupName  : mynewrg
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-29T16:35:47.3419991Z
	data:    Mode               : Incremental
	data:    Name                Type          Value


	data:    ------------------  ------------  -------------------------------------

	data:    vmName              String        mynewvm


	data:    vmSize              String        Standard_D1


	data:    adminUserName       String        myadminuser


	data:    adminPassword       SecureString  undefined


	data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic
	info:    group deployment create command OK

### Verificar a implantação

Agora, use o SSH na máquina virtual que você criou para verificar a implantação e começar a usar a nova VM. Para se conectar via SSH, localize o endereço IP da VM criada executando o seguinte comando:

	azure network public-ip show <your-new-resource-group-name> <your-ip-name>

O endereço IP público é listado na saída do comando. Por padrão, você conecta a VM Linux por SSH na porta 22.

## Criar VMs adicionais com o modelo

Use a imagem capturada e o modelo para implantar VMs adicionais usando as etapas descritas na seção anterior.

* Verifique se a imagem da VM está na mesma conta de armazenamento que irá hospedar o VHD da VM
* Copie o arquivo de modelo JSON e insira um valor exclusivo para o **uri** do VHD de cada VM
* Criar uma nova NIC na mesma rede virtual ou em uma diferente
* Crie uma implantação no grupo de recursos no qual você configurou a rede virtual usando o arquivo de modelo modificado JSON

Se você quiser a rede configurada automaticamente ao criar uma VM a partir da imagem, use o [101-vm-from-user-image template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) do GitHub. Esse modelo cria uma VM de sua imagem personalizada e da rede virtual necessária, endereço IP público e recursos da NIC. Para obter uma explicação sobre como usar o modelo no portal do Azure, confira [Como criar uma máquina virtual de uma imagem personalizada usando um modelo ARM](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

## Usar o comando azure vm create

Geralmente, você desejará usar um modelo do Gerenciador de Recursos para criar uma VM a partir da imagem. No entanto, você pode criar a VM _de modo forçado_ usando o comando **azure vm create** com o parâmetro**-Q** (**--image-urn**). Você também passará o parâmetro **-d** (**--os-disk-vhd**) para especificar o local do arquivo. vhd do SO para a nova VM. Ele deve estar no contêiner de vhds da conta de armazenamento na qual o arquivo VHD da imagem está armazenado. O comando copiará o VHD para a nova VM automaticamente para o contêiner de vhds.

Faça o seguinte antes de executar o **azure vm create** com a imagem:

1.	Crie um novo grupo de recursos ou identifique um grupo de recursos existente para a implantação.

2.	Crie um recurso do endereço IP público e um recurso NIC para a nova VM. Para obter as etapas para criar uma rede virtual, endereço IP público e NIC usando a CLI, consulte anteriormente neste artigo. (o **azure vm create** também pode criar uma nova NIC, mas você precisará passar parâmetros adicionais para uma rede virtual e sub-rede.)


Em seguida, execute um comando semelhante ao seguinte, passando URIs para o novo arquivo de VHD do sistema operacional e a imagem existente.

	azure vm create <your-resource-group-name> <your-new-vm-name> eastus Linux -d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/<your-new-VM-prefix>.vhd" -Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/<your-image-prefix>-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd" -z Standard_A1 -u <your-admin-name> -p <your-admin-password> -f <your-nic-name>

Para obter opções adicionais de comando, execute `azure help vm create`.

## Próximas etapas

Para gerenciar suas VMs com a CLI, consulte as tarefas em [Implantar e gerenciar máquinas virtuais usando modelos do Gerenciador de Recursos do Azure e a CLI do Azure](virtual-machines-linux-cli-deploy-templates.md).

<!---HONumber=AcomDC_0427_2016-->