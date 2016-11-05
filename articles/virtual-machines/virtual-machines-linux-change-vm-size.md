---
title: Como redimensionar uma VM Linux | Microsoft Docs
description: Como escalar ou reduzir verticalmente uma máquina virtual Linux, alterando o tamanho da VM.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: ''
tags: ''

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mikewasson

---
# Como redimensionar uma VM Linux
## Visão geral
Depois de provisionar uma máquina virtual (VM), você pode expandir ou reduzir a VM, alterando o [tamanho da VM][vm-sizes]. Em alguns casos, você deverá desalocar a VM primeiro. Isso pode acontecer se o novo tamanho não estiver disponível no cluster de hardware que hospeda a VM.

Este artigo mostra como redimensionar uma VM Linux usando a [CLI do Azure][azure-cli].

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

modelo de implantação clássica.

## Redimensionar uma VM Linux
Para redimensionar uma VM, execute as etapas a seguir.

1. Execute o comando da CLI a seguir. Esse comando lista os tamanhos de VM que estão disponíveis no cluster do hardware onde a VM está hospedada.
   
    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```
2. Se o tamanho desejado estiver listado, execute o comando a seguir para redimensionar a VM.
   
    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```
   
    A VM será reiniciada durante esse processo. Após a reinicialização, os discos do sistema operacional e de dados serão remapeados. Qualquer coisa no disco temporário será perdida.
   
    O uso da opção `--enable-boot-diagnostics` habilita os [diagnósticos de inicialização][boot-diagnostics] para registrar erros relacionados à inicialização.
3. Caso contrário, se o tamanho desejado não estiver listado, execute os comandos a seguir para desalocar a máquina virtual, redimensioná-la e, em seguida, reinicie a máquina virtual.
   
    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```
   
   > [!WARNING]
   > Desalocar a VM também libera os endereços IP dinâmicos atribuídos à VM. Os discos do sistema operacional e de dados não são afetados.
   > 
   > 

## Próximas etapas
Para obter escalabilidade adicional, execute várias instâncias de VM e expanda. Para saber mais, veja [Dimensionar automaticamente computadores Linux em um conjunto de escala de máquina virtual][scale-set].

<!-- links -->

[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md
[vm-sizes]: virtual-machines-linux-sizes.md

<!---HONumber=AcomDC_0824_2016-->