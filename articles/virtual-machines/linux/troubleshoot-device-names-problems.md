---
title: "Os nomes de dispositivo de VM Linux são alterados no Azure | Microsoft Docs"
description: "Explica o porquê nomes de dispositivo são alterados e apresenta uma solução para esse problema."
services: virtual-machines-linux
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: 
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 789f4580901a22dc3aaae9599c7205c76f268403
ms.contentlocale: pt-br
ms.lasthandoff: 07/13/2017

---

# <a name="troubleshooting-linux-vm-device-names-are-changed"></a>Solução de problemas: os nomes de dispositivo de VM do Linux são alterados

Este artigo explica por que os nomes de dispositivo são alterados depois de você reiniciar uma máquina virtual do Linux (VM) ou anexar novamente os discos. Ele também apresenta a solução para esse problema.

## <a name="symptom"></a>Sintoma

Você pode enfrentar os seguintes problemas ao executar VMs do Linux no Microsoft Azure.

- A VM falha em inicializar após uma reinicialização.

- Se os discos de dados for desanexados e reanexados, os nomes de dispositivos para os discos serão alterados.

- Um aplicativo ou script que faz referência a um disco usando o nome do dispositivo falha. Você descobre que o nome do dispositivo do disco mudou.

## <a name="cause"></a>Causa

Não há garantia que caminhos de dispositivo no Linux serão consistentes entre as reinicializações. Nomes de dispositivo consistem em números principais (letra) e secundários.  Quando o driver de dispositivo de armazenamento do Linux detecta um novo dispositivo, ele atribui números de dispositivo principais e secundárias a ele do intervalo disponível. Quando um dispositivo é removido, os números de dispositivo são liberados para serem reutilizados posteriormente.

O problema ocorre porque a varredura de dispositivo no Linux agendada pelo subsistema SCSI ocorre de maneira assíncrona. A nomenclatura do caminho de dispositivo final pode variar entre as reinicializações. 

## <a name="solution"></a>Solução

Para resolver esse problema, use nomenclatura persistente. Há quatro métodos para nomenclatura persistente: por rótulo de sistema de arquivos, por uuid, por id e por caminho. Recomendamos o rótulo do sistema de arquivos e os métodos UUID para VMs Linux do Azure. 

A maioria das distribuições também fornece as opções de fstab **nofail** ou **nobootwait**. Essas opções permitem que um sistema inicialize mesmo que o disco falhe em ser montado na inicialização. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros. Para obter mais informações sobre como configurar uma VM do Linux para usar um UUID quando você adiciona um disco de dados, consulte [Conectar-se à VM Linux para montar o novo disco](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk). 

Quando o agente Linux do Azure é instalado em uma VM, ele usa regras Udev para construir um conjunto de links simbólicos em **/dev/disk/azure**. Essas regras Udev podem ser usadas por aplicativos e scripts para identificar se os discos estão anexados à VM, seu tipo e o LUN.

## <a name="more-information"></a>Mais informações

### <a name="identify-disk-luns"></a>Identificar LUNs de disco

Um aplicativo pode usar LUNs para localizar todos os discos anexados e criar links simbólicos. O agente Linux do Azure agora inclui regras udev que configuram links simbólicos de um LUN para os dispositivos da seguinte maneira:

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3                                    
                                 

Informações de LUN também podem ser recuperadas do convidado Linux usando lsscsi ou uma ferramenta semelhante da seguinte maneira.

       $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Essas informações do LUN convidado podem ser usadas com os metadados de assinatura do Azure para identificar a localização no armazenamento do Azure do VHD, que armazena os dados da partição. Por exemplo, use a cli az:

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks                                        
    [                                                                                                                                                                  
    {                                                                                                                                                                  
    "caching": "None",                                                                                                                                              
      "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 1023,                                                                                                                                             
      "image": null,                                                                                                                                                   
    "lun": 0,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-114353",                                                                                                                    
    "vhd": {                                                                                                                                                          
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"                                                       
    }                                                                                                                                                              
    },                                                                                                                                                                
    {                                                                                                                                                                   
    "caching": "None",                                                                                                                                               
    "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 512,                                                                                                                                              
    "image": null,                                                                                                                                                   
    "lun": 1,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-121516",                                                                                                                    
    "vhd": {                                                                                                                                                           
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"                                                       
      }                                                                                                                                                             
      }                                                                                                                                                             
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Descobrir UUIDs de sistema de arquivos usando o blkid

Um script ou aplicativo pode ler a saída de blkid ou fontes semelhantes de informações e criar links simbólicos em **/dev** para uso. A saída mostrará UUIDs de todos os discos anexados à VM e o arquivo de dispositivo ao qual estão associados:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

As regras waagent udev constroem um conjunto de links simbólicos em **/dev/disk/azure**:


    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1


O aplicativo pode usar essas informações para identificar o dispositivo de disco de inicialização e o disco de recurso (efêmero). No Azure, os aplicativos devem consultar **/dev/disk/azure/root-part1** ou **/dev/disk/azure-resource-part1** para descobrir essas partições.

Se houver partições adicionais na lista blkid, elas residirão em um disco de dados. Os aplicativos podem manter o UUID para essas partições e usar um caminho como aquele a seguir para descobrir o nome do dispositivo no tempo de execução:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>Obter as regras mais recentes do armazenamento do Azure

Para as regras de armazenamento do Azure mais recentes, execute os seguintes comandos:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block


Para obter mais informações, consulte os seguintes artigos:

- [Ubuntu: usando UUID](https://help.ubuntu.com/community/UsingUUID)

- [Red Hat: nomenclatura persistente](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)

- [Linux: o que UUIDs podem fazer por você](https://www.linux.com/news/what-uuids-can-do-you)

- [UDev: introdução ao gerenciamento de dispositivo no sistema Linux moderno](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)


