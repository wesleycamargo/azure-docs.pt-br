---
title: O tráfego de rede de implantação do Azure Stack | Microsoft Docs
description: Este artigo descreve o que esperar sobre processos de implantação de sistema de rede do Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 5f4f76f87718ddcc81f8fae8b043b73a4dbd6b0a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189269"
---
# <a name="about-deployment-network-traffic"></a>Sobre o tráfego de rede de implantação
Noções básicas sobre como os fluxos de tráfego de rede durante o Azure Stack implantação é vital para garantir uma implantação bem-sucedida. Este artigo orienta esperada tráfego de rede durante o processo de implantação para fornecer uma compreensão do que esperar.

Esta ilustração mostra todas as conexões e os componentes envolvidos no processo de implantação:

![Topologia de rede de implantação do Azure Stack](media/deployment-networking/figure1.png)

> [!NOTE]
> Este artigo descreve os requisitos para uma implantação conectado, para saber mais sobre outros métodos de implantação, consulte [modelos de conexão de implantação do Azure Stack](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>A implantação de VM
A solução do Azure Stack inclui um grupo de servidores que são usados para hospedar os componentes de pilha do Azure e um servidor adicional chamado o Host de ciclo de vida de Hardware (HLH). Esse servidor é usado para implantar e gerenciar o ciclo de vida de sua solução e hospeda a VM de implantação (DVM) durante a implantação.

## <a name="deployment-requirements"></a>Requisitos de implantação
Antes de iniciar a implantação, há alguns requisitos mínimos que podem ser validados pelo seu OEM para garantir a implantação for concluída com êxito. Noções básicas sobre que esses requisitos ajudam a preparar o ambiente e verifique se a validação for bem-sucedida, estes são:

-   [Certificados](azure-stack-pki-certs.md)
-   [Assinatura do Azure](https://azure.microsoft.com/free/?b=17.06)
-   Acesso à Internet
-   DNS
-   NTP

> [!NOTE]
> Este artigo se concentra nos últimos três requisitos. Para obter mais informações sobre as duas primeiras, consulte os links acima.

## <a name="deployment-network-traffic"></a>Tráfego de rede de implantação
O DVM está configurado com um IP da rede BMC e requer acesso à rede para a internet. Embora nem todos os componentes de rede BMC exijam roteamento externo ou acesso à Internet, alguns componentes específicos de OEM utilizando IPs da rede também podem exigir que ele.

Durante a implantação, o DVM autentica no Azure Active Directory (Azure AD) usando uma conta do Azure de sua assinatura. Para fazer isso, o DVM requer acesso à internet para obter uma lista de URLs e porta específica. Você pode encontrar a lista completa na [publicar pontos de extremidade](azure-stack-integrate-endpoints.md) documentação. O DVM utilizará um servidor DNS para encaminhar solicitações DNS feitas por componentes internos para URLs externas. O DNS interno encaminha essas solicitações para o endereço do encaminhador DNS que você fornecer ao OEM antes da implantação. O mesmo é verdadeiro para o servidor NTP, um servidor de horário confiável é necessário para manter a consistência e a hora de sincronização para todos os componentes do Azure Stack.

O acesso à internet exigido pelo DVM durante a implantação é apenas de saída, não há chamadas de entrada são feitas durante a implantação. Tenha em mente que ele usa seu endereço IP como fonte e a pilha do Azure não oferece suporte a configurações de proxy. Portanto, se necessário, você precisa fornecer um proxy transparente ou o NAT para acessar a internet. Durante a implantação, alguns componentes internos serão iniciado ao acessar a internet por meio da rede externa usando os VIPs públicos. Após a conclusão da implantação, toda a comunicação entre o Azure e o Azure Stack é feita por meio da rede externa usando os VIPs públicos.

Configurações de rede no Azure Stack comutadores contêm Access Control Lists (ACLs) que restringem o tráfego entre determinadas fontes de rede e os destinos. O DVM é o único componente com acesso irrestrito; até mesmo o HLH é muito restrito. Você pode pedir ao OEM sobre as opções de personalização para facilitar o gerenciamento e acesso de suas redes. Devido a essas ACLs, é importante evitar alterar os endereços de servidor DNS e NTP no momento da implantação. Se você fizer isso, você precisará reconfigurar todos os comutadores para a solução.

Após a implantação for concluída, os endereços de servidor DNS e NTP fornecidos continuará a ser usado por componentes do sistema diretamente. Por exemplo, se você verificar as solicitações de DNS após a implantação for concluída, a fonte será alterado do DVM IP para um endereço do intervalo de rede externa.

Após a implantação for concluída, os endereços de servidor DNS e NTP fornecidos continuará a ser usado por componentes do sistema por meio de SDN usando a rede externa. Por exemplo, se você verificar as solicitações de DNS após a implantação for concluída, a fonte será alterado do DVM IP a um VIP público.

## <a name="next-steps"></a>Próximas etapas
[Validar o registro do Azure](azure-stack-validate-registration.md)
