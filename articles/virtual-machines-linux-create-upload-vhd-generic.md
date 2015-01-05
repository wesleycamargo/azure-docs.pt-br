<properties urlDisplayName="Upload a Linux VHD" pageTitle="Criar e carregar um VHD Linux no Azure" metaKeywords="Azure VHD, uploading Linux VHD" description="Learn to create and upload an Azure virtual hard disk (VHD) that contains a Linux operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains a Linux Operating System" authors="szarkos" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="szarkos" />


# <a id="nonendorsed"> </a>Informações para as distribuições não endossadas #

**Importante**: O SLA (contrato de nível de serviço) da plataforma do Azure aplica-se a máquinas virtuais com o sistema operacional Linux somente quando uma das [distribuições endossadas](../virtual-machines-linux-endorsed-distributions). Todas as distribuições do Linux fornecidas na galeria de imagens do Azure são distribuições endossadas com a configuração necessária.

- [Linux em Azure - Distribuições endossadas](../virtual-machines-linux-endorsed-distributions)
- [Suporte para imagens Linux no Microsoft Azure](http://support2.microsoft.com/kb/2941892)

Todas as distribuições em execução no Azure precisam atender a diversos pré-requisitos para ter a chance de serem executadas corretamente na plataforma.  Este artigo não é conclusivo, já que cada distribuição é diferente. Dessa forma, mesmo que você atenda a todos os critérios abaixo, talvez seja necessário ajustar significativamente o seu sistema Linux para garantir que ele seja executado corretamente na plataforma.

Por isso, recomendamos que você inicie com uma das nossas [distribuições endossadas do Linux no Azure](../linux-endorsed-distributions) sempre que possível. Os artigos a seguir guiam você pela preparação das diversas distribuições endossadas do Linux que são compatíveis com o Azure:

- **[Distribuições com base em CentOS](../virtual-machines-linux-create-upload-vhd-centos)**
- **[Oracle Linux](../virtual-machines-linux-create-upload-vhd-oracle)**
- **[SLES e openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](../virtual-machines-linux-create-upload-vhd-ubuntu)**

O restante deste artigo traz orientações gerais para execução da sua distribuição do Linux no Azure.


##Notas de instalação gerais do Linux##

- Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.

- Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso evitará conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas.  LVM ou [RAID](../virtual-machines-linux-configure-raid) pode ser usado em discos de dados, se preferir.

- Não há suporte para NUMA para tamanhos de máquinas virtuais maiores devido a um bug nas versões do kernel Linux abaixo de 2.6.37. Esse problema afeta principalmente distribuições que usam o kernel upstream do Red Hat 2.6.32. A instalação manual do agente Linux do Azure (waagent) desabilita a NUMA automaticamente na configuração do GRUB para o kernel do Linux.

- Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários.  Verifique as etapas a seguir para obter mais informações a esse respeito.

- Todos os VHDs devem ter tamanhos que são múltiplos de 1 MB.


## Requisitos do kernel do Linux ##

Os drivers LIS (Serviços de Integração do Linux) para Hyper-V e Azure são obtidos diretamente no kernel upstream do Linux. Muitas distribuições que contam com uma versão recente do kernel do Linux (por exemplo, versões 3.x) já terão esses drivers ou fornecerão versões revertidas desses drivers com seus kernels.  Os drivers são atualizados constantemente no kernel upstream com reparos e recursos. Por isso, recomendamos que você execute, sempre que possível, uma [distribuição endossada](../linux-endorsed-distributions) que inclua esses reparos e essas atualizações.

Se você estiver executando uma variante das versões **de 6.0 a 6.3** do Red Hat Enterprise Linux, será necessário instalar os drivers LIS mais recentes para o Hyper-V. Os drivers estão disponíveis [aqui](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Os drivers LIS com o kernel já estão incluídos nas versões **6.4 e posteriores** do RHEL (e derivados). Por isso, não é necessário ter outros pacotes de instalação para executar esses sistemas no Azure.

Se for necessário usar um kernel personalizado, recomendamos que você use uma versão mais recente (**3.8 ou posterior**).  No caso das distribuições ou dos fornecedores que mantêm seus próprios kernels, recomendamos a reversão dos drivers LIS do kernel upstream para seu kernel personalizado. Recomendamos que você acompanhe os reparos upstream nos drivers LIS e faça as reversões necessárias, mesmo se estiver executando uma versão relativamente recente do kernel.O local dos arquivos da fonte do driver LIS está disponível no arquivo [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) na árvore da fonte de kernel Linux:

	F:	arch/x86/include/asm/mshyperv.h
	F:	arch/x86/include/uapi/asm/hyperv.h
	F:	arch/x86/kernel/cpu/mshyperv.c
	F:	drivers/hid/hid-hyperv.c
	F:	drivers/hv/
	F:	drivers/input/serio/hyperv-keyboard.c
	F:	drivers/net/hyperv/
	F:	drivers/scsi/storvsc_drv.c
	F:	drivers/video/fbdev/hyperv_fb.c
	F:	include/linux/hyperv.h
	F:	tools/hv/

Sabemos que a ausência dos patches a seguir resulta em problemas no Azure. Por isso, eles devem constar no kernel. Essa lista pode não estar completa para todas as distribuições:

- [ata_piix: adie os discos para os drivers Hyper-V por padrão](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc: Conta para pacotes em trânsito no caminho RESET](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)


## O agente Linux do Azure ##

O [agente Linux do Azure](../virtual-machines-linux-agent-user-guide) (waagent) é necessário para garantir o provisionamento correto da máquina virtual Linux no Azure. Você pode obter a versão mais recente, verificar os problemas com arquivos ou enviar solicitações pull no [repositório GitHub do agente Linux](https://github.com/Azure/WALinuxAgent).

- O agente Linux consta na licença do Apache 2.0. Diversas distribuições já fornecem pacotes RPM ou DEB para o agente. Assim, em alguns casos, é possível instalá-los e atualizá-los sem dificuldades.

- O agente Linux do Azure requer Python v2.6+.

- Além disso, o agente requer o módulo python-pyasn1. A maioria das distribuições fornece esse módulo como uma pacote autônomo instalável.

- Em alguns casos, é possível que o agente Linux do Azure não seja compatível com o NetworkManager. Muitos pacotes RPM/DEB fornecidos pelas distribuições configuram o NetworkManager como um conflito para o pacote do waagent e, portanto, desinstalam o NetworkManager quando você instala o pacote do agente Linux.


## Requisitos gerais do sistema Linux ##

- Modifique a linha de inicialização do kernel no GRUB ou GRUB2 para incluir os parâmetros a seguir. Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração.

	Além disso, recomendamos que você *remova* os seguintes parâmetros, se eles existirem:

		rhgb quiet crashkernel=auto

	As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.

	Você pode configurar a opção `crashkernel`, mas esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

- Instalando o agente Linux do Azure

	O agente Linux do Azure é necessário para garantir o provisionamento de uma imagem Linux no Azure.  Muitas distribuições fornecem o agente como um pacote RPM ou DEB (o pacote geralmente recebe o nome de "WALinuxAgent" ou "walinuxagent").  Também é possível instalar o agente manualmente seguindo as etapa no [Guia do agente Linux](../virtual-machines-linux-agent-user-guide).

- Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Geralmente, esse é o padrão.

- Não crie espaço swap no disco do sistema operacional

	O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- Em "/etc/sudoers", exclua o comentário ou remova as seguintes linhas, se elas estiverem presentes:

		Defaults targetpw
		ALL    ALL=(ALL) ALL

- Por fim, execute os comandos a seguir para desprovisionar a máquina virtual:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

- Em seguida, você deverá desligar a máquina virtual e carregar o VHD no Azure.



<!--HONumber=35.1-->
