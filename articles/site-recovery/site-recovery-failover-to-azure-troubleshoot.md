---
title: Solução de falhas de failover para o Azure | Microsoft Docs
description: Este artigo descreve maneiras de solucionar erros comuns no failover para o Azure
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: ad8b69bfe6f3261f00cd33846efc86ce3b198954
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919685"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Solução de erros durante o failover de uma máquina virtual para o Azure

Você poderá receber um dos erros a seguir ao fazer failover de uma máquina virtual para o Azure. Para solucionar problemas, use as etapas descritas para cada condição de erro.

## <a name="failover-failed-with-error-id-28031"></a>Falha no failover com a ID de Erro 28031

O Site Recovery não pôde criar uma máquina virtual com failover no Azure. Isso pode ocorrer devido a um dos seguintes motivos:

* Não há cota suficiente disponível para criar a máquina virtual: verifique a cota disponível acessando Assinatura -> Uso + cotas. Abra uma [nova solicitação de suporte](http://aka.ms/getazuresupport) para aumentar a cota.

* Você está tentando fazer failover em máquinas virtuais de famílias de tamanho diferentes no mesmo conjunto de disponibilidade. Escolha a mesma família de tamanho para todas as máquinas virtuais no mesmo conjunto de disponibilidade. Altere o tamanho acessando as configurações Computação e Rede da máquina virtual e, em seguida, repita o failover.

* Há uma política na assinatura que impede a criação de uma máquina virtual. Altere a política para permitir a criação de uma máquina virtual e, em seguida, repita o failover.

## <a name="failover-failed-with-error-id-28092"></a>Falha no failover com a ID de Erro 28092

O Site Recovery não pôde criar um adaptador de rede para a máquina virtual com failover. Verifique se você tem uma cota suficiente disponível para criar adaptadores de rede na assinatura. Verifique a cota disponível acessando Assinatura -> Uso + cotas. Abra uma [nova solicitação de suporte](http://aka.ms/getazuresupport) para aumentar a cota. Se você tem uma cota suficiente, isso pode ser um problema intermitente. Repita a operação. Se o problema persistir mesmo após várias tentativas, deixe um comentário ao final deste documento.  

## <a name="failover-failed-with-error-id-70038"></a>Falha no failover com a ID de Erro 70038

O Site Recovery não pôde criar uma máquina virtual Clássica com failover no Azure. Isso pode ocorre porque:

* Um dos recursos, como uma rede virtual que é necessária para a criação da máquina virtual, não existe. Crie a rede virtual, conforme fornecido nas configurações Computação e Rede da máquina virtual ou modifique a configuração para uma rede virtual que já existe e, em seguida, repita o failover.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Não é possível conectar/RDP/SSH para a máquina virtual com failover devido ao botão Conectar esmaecido na máquina virtual

Se o botão Conectar estiver esmaecido e você não estiver conectado ao Azure por meio de uma conexão VPN Site a Site ou do Express Route,

1. Vá para **Máquina Virtual** > **Rede** e clique no nome da interface de rede necessária.  ![network-interface](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Navegue até **Configurações de Ip** e clique no campo de nome da configuração de IP necessária. ![IPConfigurations](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Para habilitar o endereço IP público, clique em **Habilitar**. ![Habilitar 3D](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Clique em **Definir as configurações necessárias** > **Criar novo**. ![Criar novo](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Insira o nome do endereço público, escolha as opções padrão para **SKU** e **atribuição** e clique em **OK**.
6. Agora, para salvar as alterações feitas, clique em **Salvar**.
7. Feche os painéis e navegue até a seção **Visão geral** de máquina virtual para conectar-se/RDP.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-even-though-connect-button-is-available-not-grayed-out-on-the-virtual-machine"></a>Não é possível conectar/RDP/SSH para a máquina virtual com failover, embora o botão Conectar esteja disponível (não esmaecido) na máquina virtual

Verifique **Diagnósticos de inicialização** na Máquina Virtual e confira se há erros conforme listado neste artigo.

1. Se a máquina virtual não foi iniciada, tente fazer o failover para um ponto de recuperação mais antigo.
2. Se o aplicativo na máquina virtual não responder, tente o failover para um ponto de recuperação consistente do aplicativo.
3. Se a máquina virtual estiver ingressada no domínio, verifique se esse controlador de domínio está funcionando corretamente. Isso pode ser feito seguindo as etapas fornecidas abaixo.
    a. criar uma nova máquina virtual na mesma rede

    b.  verifique se ele é capaz de ingressar no mesmo domínio no qual a máquina virtual com failover deve aparecer.

    c. Se o controlador de domínio **não** estiver funcionando corretamente, tente fazer logon na máquina virtual com failover usando uma conta do administrador local
4. Se você estiver usando um servidor DNS personalizado, verifique se ele é acessível. Isso pode ser feito seguindo as etapas fornecidas abaixo.
    a. Crie uma nova máquina virtual na mesma rede e b. Verifique se a máquina virtual é capaz de executar a resolução de nome usando o servidor DNS personalizado

>[!Note]
>Habilitar qualquer configuração diferente do Diagnóstico de Inicialização exigiria um Agente de VM do Azure instalado na máquina virtual antes do failover

## <a name="next-steps"></a>Próximas etapas

Se precisar de mais ajuda, poste sua consulta no [fórum do Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) ou deixe um comentário ao final deste documento. Temos uma comunidade ativa que deve conseguir ajudá-lo.
