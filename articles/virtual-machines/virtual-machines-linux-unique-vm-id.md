<properties
   pageTitle="Acessando a ID da VM"
   description="Descreve como acessar e usar a ID exclusiva da VM do Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="drewm"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# Acessando e usando a ID exclusiva da VM do Azure

A ID exclusiva da VM do Azure é um identificador de 128 bits codificado e armazenado em todos os SMBIOS da VM IaaS do Azure e, atualmente, pode ser lida usando comandos BIOS da plataforma.

A ID exclusiva da VM do Azure é uma propriedade somente leitura. A ID exclusiva da VM do Azure não mudará no desligamento para reinicialização (planejado ou não planejado), no início/parada de desalocação, na recuperação de serviço ou na restauração in-loco. No entanto, se a VM for um instantâneo e se for copiada para criar uma nova instância, uma nova ID da VM do Azure será configurada.

> [AZURE.NOTE] Se você tiver VMs antigas criadas e em execução desde o lançamento desse novo recurso (18 de setembro de 2014), reinicie sua VM para obter automaticamente uma ID exclusiva do Azure.


Para acessar a ID exclusiva da VM do Azure de dentro da VM:


## Criar uma máquina virtual
 

Para saber mais, confira [Criar uma máquina virtual](virtual-machines-linux-creation-choices.md)


## Conectar-se à VM
 

Para saber mais, confira [SSH no Linux](virtual-machines-linux-ssh-from-linux.md)


## Consultar ID exclusiva da VM

Comando (o exemplo usa o **Ubuntu**):

    sudo dmidecode | grep UUID
    
Exemplo de resultados esperados:

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
    
Devido à ordenação de bits Big Endian, a ID exclusiva da VM, neste caso, será:

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
    
    
A ID exclusiva da VM do Azure poderá ser usada em cenários diferentes se a VM estiver em execução no Azure ou no local, além de poder ajudar com os requisitos de licenciamento, de relatórios ou de rastreamento geral que você tenha em suas implantações IaaS do Azure. Muitos fornecedores independentes de software que criam aplicativos e os certificam no Azure podem exigir a identificação de uma VM do Azure durante todo seu ciclo de vida, bem como para informar se a VM está em execução no Azure, no local ou em outros provedores de nuvem. Esse identificador de plataforma pode, por exemplo, ajudar a detectar se o software está licenciado corretamente ou ajudar a correlacionar quaisquer dados da VM com sua fonte, como auxiliar na configuração das métricas certas para a plataforma certa e rastrear e correlacionar essas métricas entre outros usuários.

<!---HONumber=AcomDC_0323_2016-->