<properties
	pageTitle="Criar uma máquina virtual do Azure executando o Linux no portal clássico do Azure | Microsoft Azure"
	description="Use o portal clássico do Azure para criar uma máquina virtual (VM) do Azure executando o Linux com os grupos de recursos do Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/21/2015"
	ms.author="rasquill"/>

# Criar uma Máquina Virtual executando o Linux usando o portal do Azure


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

É fácil criar uma VM (Virtual Machine - Máquina Virtual) que executa o Linux. Este tutorial mostra como usar o portal do Azure para criar uma VM rapidamente e usa o `~/.ssh/id_rsa.pub`arquivo de chave pública para proteger a sua conexão **SSH** com a VM. Você também pode criar VMs do Linux usando [suas próprias imagens como modelos](virtual-machines-linux-classic-create-upload-vhd.md).


[AZURE.NOTE] Este tutorial cria uma máquina virtual do Azure gerenciada pela API do grupo de recursos do Azure. Para obter detalhes, consulte [Visão geral do grupo de recursos do Azure](../resource-group-overview.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Selecionar a imagem

Vá para o Azure Marketplace no portal de Visualização para localizar a imagem de VM do Windows Server desejada.

1. Entre no [Portal de visualização](https://portal.azure.com).

2. No menu Hub, clique em **Novo** > **Computação** > **Ubuntu Server 14.04 LTS**.

	![escolhendo uma imagem de VM](media/virtual-machines-linux-portal-create/chooseubuntuvm.png)

	> [AZURE.TIP] Para localizar outras imagens, clique em **Marketplace** e pesquise ou filtre os itens disponíveis.

3. Na parte inferior da página **Ubuntu Server 14.04 LTS**, selecione **Usar a pilha do Gerenciador de Recursos** para criar a VM no Gerenciador de Recursos do Azure. Observe que, para a maioria das novas cargas de trabalho, recomendamos a pilha do Gerenciador de Recursos. Para obter considerações, consulte [Computação do Azure, Provedores de Rede e Armazenamento no Gerenciador de Recursos do Azure](virtual-machines-windows-compare-deployment-models.md).

4. Em seguida, clique em ![botão criar](media/virtual-machines-linux-portal-create/createbutton.png).

	![alterar para a pilha de computação do gerenciador de recursos](media/virtual-machines-linux-portal-create/changetoresourcestack.png)

## Criar a máquina virtual

Depois de selecionar a imagem, você poderá usar as configurações padrão do Azure para a maior parte da configuração e criar rapidamente a VM.

1. Na folha **Criar máquina virtual**, clique em **Noções básicas**. Insira o **Nome** desejado para a máquina virtual e um arquivo de chave pública (no formato **ssh-rsa**; nesse caso, do arquivo `~/.ssh/id_rsa.pub`). Se você tiver mais de uma assinatura, especifique a assinatura certa para a nova VM, bem como um **Grupo de recursos** novo ou existente e um **Local** de data center do Azure.

	![](media/virtual-machines-linux-portal-create/step-1-thebasics.png)

	> [AZURE.NOTE] Você também pode escolher a autenticação de nome de usuário e de senha aqui e inserir essas informações se não quiser proteger sua sessão do **ssh** com uma troca de chaves pública e privada.

2. Clique em **Tamanho** e selecione um tamanho de VM apropriado para suas necessidades. Cada tamanho especifica o número de núcleos de computação, memória e outros recursos, como suporte para Armazenamento Premium, o que afetará o preço. O Azure recomenda determinados tamanhos automaticamente dependendo da imagem que você escolher. Quando terminar, clique em ![botão selecionar](media/virtual-machines-linux-portal-create/selectbutton-size.png).

	>[AZURE.NOTE] O Armazenamento Premium está disponível para máquinas virtuais de série DS em determinadas regiões. O Armazenamento Premium é a melhor opção de armazenamento para cargas de trabalho de uso intensivo de dados, como um banco de dados. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../storage/storage-premium-storage.md).

3. Clique em **Configurações** para ver as configurações de rede e de armazenamento da nova VM. Para uma primeira VM, geralmente você aceita as configurações padrão. Se você tiver selecionado um tamanho de VM que dê suporte a ela, poderá experimentar o Armazenamento Premium selecionando **Premium (SSD)** em **Tipo de disco**. Quando terminar, clique em ![Botão OK](media/virtual-machines-linux-portal-create/okbutton.png).

	![](media/virtual-machines-linux-portal-create/step-3-settings.png)

6. Clique em **Resumo** para revisar suas opções de configuração. Quando você terminar a revisão ou a atualização das configurações, clique em ![Botão OK](media/virtual-machines-linux-portal-create/createbutton.png).

	![resumo da criação](media/virtual-machines-linux-portal-create/summarybeforecreation.png)

8. Enquanto o Azure cria a VM, é possível acompanhar o andamento em **Notificações**, no menu Hub. Após o Azure criar a máquina virtual, você a verá no Quadro Inicial, a menos que tenha desmarcado **Fixar no Quadro Inicial** na folha **Criar máquina virtual**.

	> [AZURE.NOTE] Observe que o resumo não contém um nome de DNS público da forma que o faz quando uma máquina virtual é criada em um Serviço de Nuvem usando a pilha de computação do gerenciamento de serviço.

## Conecte-se à sua VM do Linux do Azure usando o **ssh**

Agora você pode se conectar à VM do Ubuntu usando o **ssh** da maneira padrão. No entanto, você precisará descobrir o endereço IP alocado à VM do Azure, abrindo o bloco para a VM e seus recursos. Você pode fazê-lo clicando em **Procurar**, selecionando **Recente** e procurando pela VM criada ou clicando no bloco criado para você no Quadro Inicial. Em ambos os casos, localize e copie o valor de **Endereço IP Público**, como mostrado no gráfico a seguir.

![resumo da criação bem-sucedida](media/virtual-machines-linux-portal-create/successresultwithip.png)

Agora você pode se conectar por **ssh** em sua VM do Azure e estará pronto para começar.

	ssh ops@23.96.106.1 -p 22
	The authenticity of host '23.96.106.1 (23.96.106.1)' can't be established.
	ECDSA key fingerprint is bc:ee:50:2b:ca:67:b0:1a:24:2f:8a:cb:42:00:42:55.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added '23.96.106.1' (ECDSA) to the list of known hosts.
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-43-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	  System information as of Mon Jul 13 21:36:28 UTC 2015

	  System load: 0.31              Memory usage: 5%   Processes:       208
	  Usage of /:  42.1% of 1.94GB   Swap usage:   0%   Users logged in: 0

	  Graph this data and manage this system at:
	    https://landscape.canonical.com/

	  Get cloud support with Ubuntu Advantage Cloud Guest:
	    http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.

	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

	ops@ubuntuvm:~$


> [AZURE.NOTE] Você também pode configurar um FQDN (nome de domínio totalmente qualificado) para sua máquina virtual no portal. Leia mais sobre [como criar FQDNs no portal](virtual-machines-linux-portal-create-fqdn.md).

## Próximas etapas

Para saber mais sobre o Linux no Azure, confira:

- [Computação Linux e Software Livre no Azure](virtual-machines-linux-opensource-links.md)

- [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux](azure-cli-arm-commands.md)

- [Implantar um aplicativo LAMP usando a extensão CustomScript do Azure para Linux](virtual-machines-linux-classic-lamp-script.md)

- [A extensão de máquina virtual Docker para Linux no Azure](virtual-machines-linux-dockerextension.md)

<!---HONumber=AcomDC_0323_2016-->