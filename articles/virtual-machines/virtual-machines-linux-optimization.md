<properties
	pageTitle="Otimizando sua VM do Linux no Azure | Microsoft Azure"
	description="Saiba algumas dicas de otimização para certificar-se de que você configurou sua VM do Linux para otimizar o desempenho no Azure"
	keywords="máquina virtual linux, linux máquina virtual, máquina virtual ubuntu" 
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rickstercdn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/21/2016"
	ms.author="rclaus"/>

# Otimizar sua VM do Linux no Azure

É fácil criar uma máquina virtual (VM) Linux desde a linha de comando ou do portal. Este tutorial mostra como garantir que você configurou uma VM para otimizar o desempenho na plataforma Microsoft Azure. Este tópico usa uma VM do Ubuntu Server, mas é possível também criar uma máquina virtual do Linux usando [suas próprias imagens como modelos](virtual-machines-linux-create-upload-generic.md).

## Pré-requisitos

Este tópico pressupõe que você já tenha uma Assinatura do Azure ativa ([inscrição de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)), [instalado a CLI do Azure](../xplat-cli-install.md) e já tenha provisionado uma VM na sua Assinatura do Azure.. Antes de fazer qualquer coisa com o Azure é preciso autenticar a sua assinatura. Para fazer isso com a CLI do Azure, basta digitar `azure login` para iniciar o processo interativo.

## Disco do sistema operacional do Azure

Depois de criar uma Vm do Linux no Azure, ele tem dois discos associados a ele. /dev/sda é o disco do sistema operacional, /dev/sdb é o disco temporário. Use o disco do sistema operacional principal (/dev/sda) somente para o sistema operacional pois ele é otimizado para um tempo de inicialização rápido da VM e não fornecerá bom desempenho para suas cargas de trabalho. Você deve anexar um ou mais discos à sua VM para obter um armazenamento persistente e otimizado para seus dados.

## Adicionando discos para metas de desempenho e tamanho 

Com base no tamanho da VM escolhida, é possível anexar até 16 discos adicionais em uma máquina da série A, 32 discos em uma da série D e 64 discos em uma da série G, cada uma com até 1 TB de tamanho. É recomendável adicionar discos adicionais conforme necessário para seus requisitos de IOps e espaço. Cada disco tem uma meta de desempenho de 500 IOps para o Armazenamento Standard e até 5000 IOps por disco para Armazenamento Premium. Para saber mais sobre os discos de Armazenamento Premium, consulte [Armazenamento Premium: armazenamento de alto desempenho para VMs do Azure](../storage/storage-premium-storage.md)

Para atingir os IOps mais altos nos discos de Armazenamento Premium com configuração de cache como "ReadOnly" ou "None", você deve desabilitar "barreirs" durante a montagem do sistema de arquivos no Linux. Você não precisa de barreiras, pois as gravações em discos de backup do Armazenamento Premium são duráveis para essas configurações de cache.

- Se você usar **reiserFS**, desabilite as barreiras usando a opção de montagem "barrier=none" (para habilitar as barreiras, use "barrier=flush")
- Se você usar **ext3/ext4**, desabilite as barreiras usando a opção de montagem “barrier=0” (para habilitar as barreiras, use “barrier=1”)
- Se você usar **XFS**, desabilite as barreiras usando a opção de montagem “nobarrier” (para habilitar as barreiras, use a opção “barrier”)

## Considerações de conta de armazenamento

Quando você cria sua VM do Linux no Azure, assegure-se de anexar discos de contas de armazenamento que residem na mesma região que sua VM para garantir a proximidade e minimizar a latência de rede. Cada conta de Armazenamento Standard tem um máximo de 20k IOps e uma capacidade de tamanho de 500 TB. Isso funciona para aproximadamente 40 discos muito usados, incluindo o disco do sistema operacional e discos de dados que você criar. Para contas de Armazenamento Premium, não há nenhum limite máximo de IOps, mas há um limite de tamanho de 32 TB.

Ao lidar com cargas de trabalho de IOps muito altas e se tiver escolhido o Armazenamento Standard para seus discos, talvez seja necessário dividir os discos em várias contas de armazenamento para certificar-se de que você não tenha atingido o limite de 20.000 IOps para contas de Armazenamento Standard. Sua VM pode conter uma combinação de discos em diferentes contas de armazenamento e tipos de conta de armazenamento para atingir a configuração ideal.

## Unidade temporária da VM

Por padrão quando você cria uma nova VM, o Azure fornece um disco de sistema operacional (/dev/sda) e um disco temporário (/dev/sdb). Todos os discos adicionais que você adicionar aparecerão como /dev/sdc, /dev/sdd, /dev/sde e assim por diante. Todos os dados no disco temporário (/dev/sdb) não são duráveis e podem ser perdidos e eventos específicos como o redimensionamento de VM, reimplantação ou manutenção forçarão uma reinicialização da VM. O tamanho e tipo de disco temporário está relacionado ao tamanho da VM escolhida no momento da implantação. No caso das VMs de tamanho premium (séries DS, G e DS\_V2) a unidade temporária será feita por um SSD local para desempenho adicional de até 48k IOps.

## Arquivo de permuta do Linux

Imagens de VM implantadas no Azure Marketplace possuem um agente Linux de VM integrado com o sistema operacional que permite que a VM interaja com vários serviços do Azure. Supondo que você implantou uma imagem padrão do Azure Marketplace, você precisaria fazer o seguinte para configurar corretamente as configurações de arquivo de permuta do Linux:

Localizar e modificar duas entradas no arquivo **/etc/waagent.conf**. Eles controlam a existência de um arquivo de permuta dedicado e o tamanho do arquivo de permuta. Os parâmetros que você quer modificar são `ResourceDisk.EnableSwap=N` e `ResourceDisk.SwapSizeMB=0`

Você precisará alterá-los para o seguinte:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={tamanho em MB para atender às suas necessidades}

Depois de fazer a alteração, você precisará reiniciar o waagent ou sua VM Linux para refletir essas alterações. Você sabe que as alterações foram implementadas e que um arquivo de permuta foi criado ao usar o comando `free` para exibir o espaço livre. O exemplo a seguir possui um arquivo de permuta de 512MB criado como resultado da modificação do arquivo waagent.conf.

    admin@mylinuxvm:~$ free
                total       used       free     shared    buffers     cached
    Mem:       3525156     804168    2720988        408       8428     633192
    -/+ buffers/cache:     162548    3362608
    Swap:       524284          0     524284
    admin@mylinuxvm:~$
 
## Algoritmo de agendamento de E/S para o Armazenamento Premium

Com o kernel do Linux 2.6.18, o padrão de algoritmo de agendamento de E/S foi alterado de Deadline para CFQ (algoritmo de fila completamente justa). Para padrões de E/S de acesso aleatório, há uma diferença insignificante nas diferenças de desempenho entre CFQ e Deadline. Para discos baseados SSD nos quais o padrão de E/S de disco é predominantemente sequencial, alternar de volta para o algoritmo NOOP ou Deadline pode resultar um melhor desempenho de E/S.

### Exibir o agendador de E/S atual

Use o seguinte comando:

	admin@mylinuxvm:~# cat /sys/block/sda/queue/scheduler

Você verá o seguinte resultado, que indica o agendador atual.

	noop [deadline] cfq

###Alterar o dispositivo atual (/dev/sda) do algoritmo de agendamento de e/s

Use os seguintes comandos:

	azureuser@mylinuxvm:~$ sudo su -
	root@mylinuxvm:~# echo "noop" >/sys/block/sda/queue/scheduler
	root@mylinuxvm:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
	root@mylinuxvm:~# update-grub

>[AZURE.NOTE] Configurar este somente para /dev/sda não é útil. Ele precisa ser definido em todos os discos de dados onde a E/S sequencial domina o padrão de E/S.

Você verá o resultado, indicando que o grub.cfg foi recriado com êxito e que o agendador padrão foi atualizado para o NOOP.

	Generating grub configuration file ...
	Found linux image: /boot/vmlinuz-3.13.0-34-generic
	Found initrd image: /boot/initrd.img-3.13.0-34-generic
	Found linux image: /boot/vmlinuz-3.13.0-32-generic
	Found initrd image: /boot/initrd.img-3.13.0-32-generic
	Found memtest86+ image: /memtest86+.elf
	Found memtest86+ image: /memtest86+.bin
	done

Para a família de distribuição Redhat, só é necessário o seguinte comando:

	echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## Usando o RAID de software para alcançar I/Ops mais altos

Se suas cargas de trabalho exigem mais IOps que um único disco pode fornecer, você precisará usar uma configuração de RAID de software de vários discos. Como o Azure já executa a resiliência do disco na camada de malha local, você obterá o maior nível de desempenho de uma configuração de distribuição de RAID-0. Você precisará provisionar e criar novos discos no ambiente do Azure e anexá-los à sua VM do Linux antes do particionamento, formatação e montagem das unidades. Mais detalhes sobre como definir uma configuração de RAID de software em sua VM Linux no Azure podem ser encontrados no documento **[Configurando o RAID de software no Linux](virtual-machines-linux-configure-raid.md)**.


## Próximas etapas

Lembre-se de que, como com todas as discussões de otimização, você precisará executar testes antes e após cada alteração para medir o impacto que terá a alteração. Otimização é um processo passo a passo que terá resultados diferentes em diferentes computadores no seu ambiente. O que funciona para uma configuração pode não funcionar para outras.

Alguns links úteis para recursos adicionais:

- [Armazenamento Premium: Armazenamento de Alto Desempenho para as Cargas de Trabalho da Máquina Virtual do Azure](../storage/storage-premium-storage.md)
- [Guia do usuário do agente Linux para o Azure](virtual-machines-linux-agent-user-guide.md)
- [Otimizando o desempenho do MySQL nas VMs Linux do Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [Configurar RAID de software no Linux](virtual-machines-linux-configure-raid.md)

<!---HONumber=AcomDC_0706_2016-->