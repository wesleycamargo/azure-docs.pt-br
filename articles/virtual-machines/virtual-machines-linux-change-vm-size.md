---
title: Como redimensionar uma VM Linux | Microsoft Docs
description: "Como escalar ou reduzir verticalmente uma máquina virtual Linux, alterando o tamanho da VM."
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mwasson
translationtype: Human Translation
ms.sourcegitcommit: 6adb1dd25c24b18b834dd921c2586ef29d56dc81
ms.openlocfilehash: 788efb5d1cbbd5fd20096c54ca702b99eb2b5a18


---
# <a name="how-to-resize-a-linux-vm"></a>Como redimensionar uma VM Linux
## <a name="overview"></a>Visão geral
Depois de provisionar uma VM (máquina virtual), é possível escalar ou reduzir verticalmente a VM alterando o [tamanho da VM][vm-sizes]. Em alguns casos, você deverá desalocar a VM primeiro. Isso pode acontecer se o novo tamanho não estiver disponível no cluster de hardware que hospeda a VM.

Este artigo mostra como redimensionar uma VM Linux usando a [CLI do Azure][azure-cli].

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="resize-a-linux-vm"></a>Redimensionar uma VM Linux
Para redimensionar uma VM, execute as etapas a seguir.

1. Execute o comando da CLI a seguir. Esse comando lista os tamanhos de VM que estão disponíveis no cluster do hardware onde a VM está hospedada.
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. Se o tamanho desejado estiver listado, execute o comando a seguir para redimensionar a VM.
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    A VM será reiniciada durante esse processo. Após a reinicialização, os discos do sistema operacional e de dados serão remapeados. Qualquer coisa no disco temporário será perdida.
   
    O uso da opção `--enable-boot-diagnostics` habilita o [diagnóstico de inicialização][boot-diagnostics], para registrar erros relacionados à inicialização.
3. Caso contrário, se o tamanho desejado não estiver listado, execute os comandos a seguir para desalocar a máquina virtual, redimensioná-la e, em seguida, reinicie a máquina virtual.
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > Desalocar a VM também libera os endereços IP dinâmicos atribuídos à VM. Os discos do sistema operacional e de dados não são afetados.
   > 
   > 

## <a name="next-steps"></a>Próximas etapas
Para obter escalabilidade adicional, execute várias instâncias de VM e expanda. Para obter mais informações, consulte [Dimensionar automaticamente computadores Linux em um conjunto de dimensionamento de máquinas virtuais][scale-set]. 

<!-- links -->

[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md



<!--HONumber=Dec16_HO2-->


