---
title: "Falhas na solução de problemas durante o failback para locais do Azure e posterior nova proteção para o Azure | Microsoft Docs"
description: "Este artigo descreve maneiras de solucionar erros comuns em failback para locais do Azure e durante a nova proteção"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: rajanaki
ms.openlocfilehash: d487c1fcf7fb6444c2b8489df839a6450715ae0a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshoot-errors-reported-during-the-process-of-failback"></a>Solucionar erros relatados durante o processo de failback
O failback envolve essencialmente duas etapas principais. Uma é proteger novamente máquinas virtuais do Azure para locais, a segunda é executar de fato o failback do Azure para o locais.

## <a name="troubleshoot-errors-when-reprotecting-a-virtual-machine-back-to-on-premises-after-failover"></a>Solucionar erros ao proteger novamente uma máquina virtual para locais, após o failover
Você poderá receber um dos erros a seguir ao executar nova proteção de uma máquina virtual para o Azure. Para solucionar problemas, use as etapas descritas para cada condição de erro.


### <a name="error-code-95226"></a>Código de erro 95226

*A nova proteção falhou porque a máquina virtual do Azure não foi capaz de alcançar o servidor de configuração local.*

Isso ocorre quando 
1. A máquina virtual do Azure não foi capaz de alcançar o servidor de configuração local e, portanto, não pôde ser descoberta e registrada no servidor de configuração. 
2. O serviço de aplicativo do InMage Scout na máquina virtual do Azure que precisa estar em execução para comunicar-se com o servidor de configuração local pode não estar executando após o failover.

Para resolver esse problema
1. Você precisa verificar se a rede de máquina virtual do Azure está configurada de modo que a máquina virtual possa se comunicar com o servidor de configuração local. Para fazer isso, defina uma VPN Site a Site para seu datacenter local ou configure uma conexão ExpressRoute com emparelhamento privado na rede virtual da máquina virtual do Azure. 
2. Se você já tiver uma rede configurada de forma que a máquina virtual do Azure possa se comunicar com o servidor de configuração local, faça logon na máquina virtual e marque o 'Serviço de Aplicativo InMage Scout'. Se você observar que o Serviço de Aplicativo InMage Scout não está em execução, inicie o serviço manualmente e verifique se o tipo de inicialização do serviço está definido como Automático.

### <a name="error-code-78052"></a>Código de erro 78052
A nova proteção falha com a mensagem de erro: *Não foi possível concluir a proteção para a máquina virtual.*

Isso pode acontecer devido a duas razões
1. A máquina virtual que você está protegendo novamente é um Windows Server 2016. Atualmente, esse sistema operacional não tem suporte para failback, mas terá suporte em breve.
2. Já existe uma máquina virtual com o mesmo nome no servidor de destino Mestre para o qual você está fazendo failback.

Para resolver esse problema, você pode selecionar um servidor de destino mestre diferente em um host diferente, de modo que a nova proteção crie a máquina em um host diferente no qual não ocorra conflito entre os nomes. Você também pode fazer vMotion do destino mestre para um host diferente no qual não ocorra conflito entre os nomes. Se a máquina virtual existente for uma máquina isolada, você pode apenas renomeá-la para que a nova máquina virtual possa ser criada no mesmo host ESXi.

### <a name="error-code-78093"></a>Código de erro 78093

*A VM não está em execução, está em um estado suspenso ou não está acessível.*

Para proteger novamente uma máquina virtual que sofreu failover de volta para o local, é necessário que a máquina virtual do Azure esteja em execução. Isso é para que o serviço de mobilidade registre-se no servidor de configuração local e possa iniciar a replicação comunicando-se com o servidor de processo. Se o computador estiver em uma rede incorreta ou não estiver em execução (estado suspenso ou desligado), o servidor de configuração não poderá acessar o serviço de mobilidade na máquina virtual para iniciar a nova proteção. Você pode reiniciar a máquina virtual para que ela possa começar a comunicar-se de volta localmente. Reiniciar o trabalho de nova proteção depois de iniciar a máquina virtual do Azure

### <a name="error-code-8061"></a>Código de erro 8061

*A loja de dados não está acessível no host ESXi.*

Consulte os [pré-requisitos de destino](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) e as [lojas de dados de suporte](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) para failback


## <a name="troubleshoot-errors-when-performing-a-failback-of-an-azure-virtual-machine-back-to-on-premises"></a>Solucionar erros ao executar um failback de uma máquina virtual do Azure para locais
Você poderá receber um dos erros a seguir ao executar failback de uma máquina virtual do Azure para locais. Para solucionar problemas, use as etapas descritas para cada condição de erro.

### <a name="error-code-8038"></a>Código de erro 8038

*Falha ao ativar a máquina virtual local devido ao erro*

Isso acontece quando a máquina virtual local é ativada em um host que não tem memória suficiente provisionada.

Para resolver esse problema

1. Você pode provisionar mais memória no host ESXi.
1. Mova o vMotion da VM para outro host ESXi com memória suficiente para inicializar a máquina virtual.