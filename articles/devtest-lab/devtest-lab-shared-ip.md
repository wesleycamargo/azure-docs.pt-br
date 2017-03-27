---
title: "Entender os endereços IP compartilhados no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como o Azure DevTest Labs usa endereços IP compartilhados para minimizar os endereços IP públicos necessários para acessar as VMs de seu laboratório."
services: devtest-lab
documentationcenter: na
author: camsoper
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: b4ae6b16880e4a5e53c8f94dc814e25254c20d73
ms.lasthandoff: 03/10/2017

---

# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Entender os endereços IP compartilhados no Azure DevTest Labs

O Azure DevTest Labs usa endereços IP compartilhados para minimizar o número de endereços IP públicos necessários para acessar as VMs de seu laboratório individual.  Este artigo descreve como funcionam os IPs compartilhados e suas opções de configuração relacionadas.

## <a name="shared-ip-setting"></a>Configuração de IP de compartilhado

Quando você cria um laboratório, ele reside em uma sub-rede de uma rede virtual.  Por padrão, essa sub-rede é criada com **Habilitar IP público compartilhado** definido como *Sim*.  Essa configuração cria um endereço IP público para toda a sub-rede.  Você pode desativar isso após a criação de um laboratório [seguindo as etapas neste artigo](devtest-lab-configure-vnet.md).

![Nova sub-rede do laboratório](media/devtest-lab-shared-ip/lab-subnet.png)

Todas as VMs criadas nesse laboratório assumem como padrão o uso de um IP compartilhado.  Ao criar a VM, essa configuração pode ser observada na folha **Configurações avançadas** em **Configuração do endereço IP**.

![Nova VM](media/devtest-lab-shared-ip/new-vm.png)

Sempre que uma VM com IP compartilhado habilitado é adicionada à sub-rede, uma porta TCP é atribuída no endereço IP público para encaminhamento à porta RDP na VM.  

## <a name="using-the-shared-ip"></a>Usar o IP compartilhado

Conecte-se à Área de Trabalho Remota na VM em um cliente RDP usando o endereço IP ou o nome de domínio totalmente qualificado, seguido por dois pontos, e depois pela porta.  Por exemplo, na imagem abaixo, o endereço RDP para se conectar à VM é `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.  Como alternativa, no Portal do Azure, selecione o botão **Conectar** para baixar um arquivo RDP pré-configurado.

![Exemplo de VM](media/devtest-lab-shared-ip/vm-info.png)

## <a name="next-steps"></a>Próximas etapas

* [Definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Configurar uma rede virtual no Azure DevTest Labs](devtest-lab-configure-vnet.md)






