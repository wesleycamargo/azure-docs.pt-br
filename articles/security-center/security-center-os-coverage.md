---
title: Recursos e plataformas compatíveis com a Central de Segurança do Azure | Microsoft Docs
description: Este documento fornece uma lista de recursos e plataformas compatíveis com a Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/9/2019
ms.author: rkarlin
ms.openlocfilehash: 98d2d29e7822d9ca97ba488fcf67298a0b40efbf
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265457"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Plataformas e recursos compatíveis com a Central de Segurança do Azure

As recomendações e o monitoramento do estado de segurança estão disponíveis para computadores e VMs (máquinas virtuais) criadas com o modelo de implantação do Resource Manager e o modelo clássico.

> [!NOTE]
> Saiba mais sobre os [modelos de implantação clássica e do Gerenciador de Recursos](../azure-classic-rm.md) para recursos do Azure.
>
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Plataformas que dão suporte ao agente de coleta de dados 

Esta seção lista as plataformas em que o agente da Central de Segurança do Azure pode ser executado e das quais ele pode coletar dados.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Plataformas com suporte para computadores com Windows e VMs
Os sistemas operacionais Windows a seguir são compatíveis:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

> [!NOTE]
> A integração com o Windows Defender ATP dá suporte a apenas ao Windows Server 2012 R2 e ao Windows Server 2016.
>
>

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Plataformas com suporte para computadores Linux e VMs
Os sistemas operacionais Linux a seguir são compatíveis:

* Ubuntu versões 12.04 LTS, 14.04 LTS e 16.04 LTS.
* Debian versões 6, 7, 8 e 9.
* CentOS versões 5, 6 e 7.
* RHEL (Red Hat Enterprise Linux) versões 5, 6 e 7.
* SLES (SUSE Linux Enterprise Server) versões 11 e 12.
* Oracle Linux versões 5, 6 e 7.
* Amazon Linux 2012.09 a 2017.
* OpenSSL 1.1.0 só é compatível com plataformas x86_64, 64 bits.

## <a name="vms-and-cloud-services"></a>VMs e Serviços de Nuvem
VMs que são executadas em um serviço de nuvem também são compatíveis. Apenas funções Web e de trabalho de serviços de nuvem que são executadas em slots de produção são monitorados. Para saber mais sobre os serviços de nuvem, confira [Visão geral dos Serviços de Nuvem do Azure](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Recursos de IaaS compatíveis

> [!div class="mx-tableFixed"]
> 

|Servidor| Windows||Linux||
|----|----|----|----|----|
|Ambiente|Azure|Não Azure|Azure|Não Azure|
|Alertas de detecção de ameaças VMBA|✔|✔|✔ (em versões compatíveis)|✔|
|Alertas de detecção de ameaças baseadas em rede|✔|X|✔|X|
|Integração com o Windows Defender ATP|✔ (em versões compatíveis)|✔|X|X|
|Patches ausentes|✔|✔|✔|✔|
|Configurações de segurança|✔|✔|✔|✔|
|Proteção do ponto de extremidade|✔|✔|X|X|
|Acesso à VM JIT|✔|X|✔|X|
|Controles de aplicativo adaptáveis|✔|X|X|X|
|FIM|✔|✔|✔|✔|
|Criptografia do disco|✔|X|✔|X|
|Implantação de terceiros|✔|X|✔|X|
|NSGs|✔|X|✔|X|
|Detecção de ameaças sem arquivo|✔|✔|X|X|
|Mapa de rede|✔|X|✔|X|
|Controles de rede adaptável|✔|X|✔|X|


### <a name="supported-endpoint-protection-solutions"></a>Soluções de proteção de ponto de extremidade com suporte

A tabela a seguir fornece uma matriz de:
 - Se você pode usar a Central de Segurança do Azure para instalar cada solução para você.
 - Quais soluções de proteção de ponto de extremidade a Central de Segurança pode descobrir. Se uma dessas soluções de proteção de ponto de extremidade for descoberta, a Central de Segurança não recomendará a instalação de uma.

| Proteção do ponto de extremidade| Plataformas | Instalação da Central de Segurança | Descoberta da Central de Segurança |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Não, Integrado no SO| SIM |
| System Center Endpoint Protection (antimalware da Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 (consulte a observação abaixo) | Via extensão | SIM |
| Trend Micro – Todas as versões | Família Windows Server  | Não  | SIM |
| Symantec v12.1.1100+| Família Windows Server  | Não  | SIM |
| McAfee v10+ | Família Windows Server  | Não  | SIM |
| Kaspersky| Família Windows Server  | Não  | Não   |
| Sophos| Família Windows Server  | Não  | Não   |

> [!NOTE]
> - A detecção do System Center Endpoint Protection (SCEP) em uma máquina virtual do Windows Server 2008 R2 exige que o SCEP seja instalado após o PowerShell 3.0 (ou uma versão superior).
>
>

## <a name="supported-paas-features"></a>Recursos de PaaS compatíveis 


|Serviço|Recomendações|Detecção de ameaças|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Contas de Armazenamento de Blobs do Azure*|✔| ✔|
|Serviços de aplicativos|✔| ✔|
|Serviços de Nuvem|✔| X|
|VNets|✔| ND|
|Sub-redes|✔| ND|
|NICs|✔| ✔|
|NSGs|✔| ND|
|Assinatura|✔| ✔|

\* Esses recursos são compatíveis atualmente com a versão prévia pública. 



## <a name="next-steps"></a>Próximas etapas

- Saiba como [planejar e entender as considerações de design para adotar a Central de Segurança do Azure](security-center-planning-and-operations-guide.md).
- Saiba mais sobre a [análise comportamental de máquina virtual e análise de memória de despejo de memória na Central de Segurança](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Encontre [perguntas frequentes sobre como usar a Central de Segurança do Azure](security-center-faq.md).
- Encontre [postagens no blog sobre a conformidade e segurança do Azure](https://blogs.msdn.com/b/azuresecurity/).
