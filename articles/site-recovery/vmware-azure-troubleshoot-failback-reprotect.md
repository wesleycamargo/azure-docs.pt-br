---
title: Solucionar problemas de falhas durante failback de VMs do Azure para VMware local com Azure Site Recovery | Microsoft Docs
description: "Este artigo descreve maneiras de solucionar problemas comuns de failback e erros de nova proteção durante failback para VMware do Azure, usando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/27/2017
ms.author: rajanaki
ms.openlocfilehash: 9b1156884a78eb7d68dc9680765b3c1436c0606a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>Solucionar problemas de failback do Azure para VMware

Este artigo descreve como solucionar problemas que podem ser encontrados ao fazer failback nas VMs do Azure na sua infraestrutura VMware local, após o failover para o Azure usando o [Azure Site Recovery](site-recovery-overview.md).

O failback envolve essencialmente duas etapas principais. Após o failover, é necessário fazer nova proteção das VMs do Azure para local para que iniciem a replicação. A segunda etapa é executar um failover do Azure, para fazer failback ao site local. 

## <a name="troubleshoot-reprotection-errors"></a>Solucionar problemas de erros de nova proteção

Esta seção detalha os erros de nova proteção comuns e como corrigi-los.

### <a name="error-code-95226"></a>Código de erro 95226

**A nova proteção falhou porque a máquina virtual do Azure não foi capaz de alcançar o servidor de configuração local.**

Esse erro ocorre quando:

1. A VM do Azure não pode alcançar o servidor de configuração local. A VM não pode ser descoberta e registrada no servidor de configuração. 
2. O serviço InMage Scout Application não está executando na VM Azure após o failover. O serviço é necessário para comunicações com o servidor de configuração local.

Para resolver esse problema:

1. Verifique se a rede de VMs do Azure permite que a VM do Azure comunique-se com o servidor de configuração local. Para fazer isso, configure uma VPN site a site no datacenter local ou configure uma conexão ExpressRoute com o emparelhamento privado na rede virtual do VM do Azure. 
2. Se a VM puder comunicar-se com o servidor de configuração local, faça logon na VM e verifique o 'InMage Scout Application Service'. Se você observar que não está em execução, inicie o serviço manualmente e verifique se o tipo de início do serviço está definido como Automático.

### <a name="error-code-78052"></a>Código de erro 78052

***Não foi possível concluir a proteção da máquina virtual.**

Isso pode ocorrer se já houver uma VM com o mesmo nome no servidor de destino mestre para qual o failback está falhando.

Para resolver esse problema, faça o seguinte:
1. Selecione um servidor de destino mestre diferente em um host diferente, de modo que a nova proteção crie a máquina em um host diferente, onde os nomes não entrem em conflito. 
2. Você também pode implementar o destino mestre em um host diferente no qual não ocorra conflito entre os nomes. Se a VM existente for um computador perdido, renomeie-a para que a nova VM possa ser criada no mesmo host ESXi.

### <a name="error-code-78093"></a>Código de erro 78093

**A VM não está em execução, está em um estado suspenso ou não está acessível.**

Para fazer a nova proteção de failover da VM, a VM do Azure deve estar em execução. Isso é para que o serviço de Mobilidade registre-se no servidor de configuração local e possa iniciar a replicação comunicando-se com o servidor de processo. Se o computador estiver em uma rede incorreta ou não estiver em execução (estado suspenso ou desligamento), o servidor de configuração não poderá alcançar o serviço de Mobilidade na VM, para iniciar a nova proteção. 

1. Reinicie a VM para que possa começar a comunicar-se de volta no local.
2. Reiniciar o trabalho de nova proteção depois de iniciar a máquina virtual do Azure

### <a name="error-code-8061"></a>Código de erro 8061

**A loja de dados não está acessível no host ESXi.**

Verifique os [pré-requisitos de destino](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) e os [armazenamentos de dados com suporte](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) para failback.


## <a name="troubleshoot-failback-errors"></a>Solucionar problemas de erros de failback

Esta seção descreve erros comuns que podem ser encontrados durante o failback.

### <a name="error-code-8038"></a>Código de erro 8038

**Falha ao ativar a máquina virtual local devido ao erro**

Isso ocorre quando a VM virtual é criada em um host que não possui memória suficiente. Para resolver esse problema:

1. Provisione mais memória no host ESXi.
2. Além disso, você pode mover o vMotion da VM para outro host ESXi que tenha memória suficiente para inicializar a VM.
