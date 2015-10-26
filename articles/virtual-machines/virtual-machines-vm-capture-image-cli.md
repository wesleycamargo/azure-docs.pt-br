<properties
	pageTitle="Capturar uma imagem de uma máquina virtual executando Linux usando a CLI | Microsoft Azure"
	description="Saiba como capturar imagens de máquina virtual do Azure que executam Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="madhana"
	manager="timlt"
	editor="tysonn"
    tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="madhana"/>




# Como capturar uma máquina virtual Linux para usar como um modelo com a CLI##

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.
 

Esse artigo mostra como capturar a máquina virtual do Azure executando Linux para que você a use como modelo para criar outras máquinas virtuais. O modelo inclui o disco do SO e qualquer disco de dados anexados à máquina virtual. Isso não inclui a configuração da rede, então você precisará configurá-la quando você criar as outras máquinas virtuais que usam o modelo.



O Azure trata este modelo como uma imagem e o armazena em sua lista de imagens. Esse também é o local em que quaisquer imagens carregadas são armazenadas. Para saber mais sobre imagens, confira [Sobre imagens da máquina virtual no Azure][].



##Antes de começar##



Essas etapas assumem que você já criou uma máquina virtual do Azure e já configurou o sistema operacional, incluindo os anexos de discos de dados. Se você ainda não fez isso, consulte essas instruções:



- [Como criar uma máquina virtual personalizada][]

- [Como anexar um disco de dados à máquina virtual][]



##Capturar a máquina virtual##



1. Conectar-se à máquina virtual. Para mais detalhes, consulte [ Como fazer logon em uma máquina virtual executando o Linux][].



2. A VM pode ser capturada somente se o estado for **StoppedDeallocated**. Na janela SSH, digite o seguinte comando para desligar a VM:



        vm shutdown [options] <vm-name>



    Observe que uma das opções para `vm shutdown` é `-p`, que manterá o recurso de computação após a exibição. **Não** habilite isso, pois a VM deve ser desprovisionada para ser capturada.



3. Execute o seguinte comando para capturar a imagem VM:



        vm capture <vm-name> <target-image-name> --deleted



    A máquina virtual deve ser excluída para capturar a imagem. Você pode usar `--deleted` ou `-t` para fazer isso.



4. Você pode confirmar que a imagem foi capturada verificando a lista de imagens da VM:



        vm image list | grep <target-image-name>



    A parte `| grep <target-image-name>` é opcional, mas ajudará você a localizar a imagem com mais facilidade na lista.



Aqui está um exemplo passo a passo de como capturar uma máquina virtual incluindo a saída de terminal:


    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status            Location  DNS Name                  IP Address

    data:    -----------  ----------------  --------  ------------------------  ------------

    data:    kmorig       RoleStateUnknown  West US   kmorig.cloudapp.net       100.92.56.16

    info:    vm list command OK

    ~$ azure vm shutdown kmorig

    info:    Executing command vm shutdown

    + Getting virtual machines

    + Shutting down VM

    info:    vm shutdown command OK

    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status              Location  DNS Name                  IP Address

    data:    -----------  ------------------  --------  ------------------------  ----------

    data:    kmorig       StoppedDeallocated  West US   kmorig.cloudapp.net

    info:    vm list command OK

    ~$ azure vm capture kmorig kmcaptured --deleted

    info:    Executing command vm capture

    + Getting virtual machines

    + Checking image with name kmcaptured exists

    + Capturing VM

    info:    vm capture command OK

    ~$ azure vm image list | grep kmcaptured

    data:    kmcaptured



Visite a [página de documentação da CLI do Azure][] para obter mais detalhes e comandos adicionais.


[página de documentação da CLI do Azure]: ../virtual-machines-command-line-tools.md

[ Como fazer logon em uma máquina virtual executando o Linux]: virtual-machines-linux-how-to-log-on.md

[Sobre imagens da máquina virtual no Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx

[Como criar uma máquina virtual personalizada]: virtual-machines-create-custom.md

[Como anexar um disco de dados à máquina virtual]: storage-windows-attach-disk.md
 

<!---HONumber=Oct15_HO3-->