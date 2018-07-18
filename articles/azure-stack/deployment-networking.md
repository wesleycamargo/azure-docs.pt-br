---
title: Tráfego de rede de implantação de pilha do Azure | Microsoft Docs
description: Este artigo descreve o que esperar sobre processos de implantação de rede de pilha do Azure.
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
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: b808875e66e867b84e2971c6a5bd031d108d003b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656136"
---
# <a name="about-deployment-network-traffic"></a>Sobre o tráfego de rede de implantação
Entender como os fluxos de tráfego de rede durante a pilha do Azure implantação é essencial para garantir uma implantação bem-sucedida. Este artigo o orienta por meio de tráfego de rede esperado durante o processo de implantação para fornecer uma compreensão do que esperar.

Esta ilustração mostra todas as conexões e os componentes envolvidos no processo de implantação:

![Topologia de rede de implantação de pilha do Azure](media/deployment-networking/figure1.png)

> [!NOTE]
> Este artigo descreve os requisitos para uma implantação de conectado, para saber mais sobre outros métodos de implantação, consulte [modelos de conexão de implantação do Azure pilha](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>A implantação de VM
A solução de pilha do Azure inclui um grupo de servidores que são usados para hospedar os componentes da pilha do Azure e um servidor adicional chamado de Host de ciclo de vida de Hardware (HLH). Este servidor é usado para implantar e gerenciar o ciclo de vida de sua solução e hospeda a VM de implantação (DVM) durante a implantação.

## <a name="deployment-requirements"></a>Requisitos de implantação
Antes do início da implantação, há alguns requisitos mínimos que podem ser validados pelo seu OEM para garantir a implantação for concluída com êxito. Compreender que esses requisitos ajudarão você a preparar o ambiente e verifique se a validação for bem-sucedida, estes são:

-   [Certificados](azure-stack-pki-certs.md)
-   [Assinatura do Azure](https://azure.microsoft.com/free/?b=17.06)
-   Acesso à Internet
-   DNS
-   NTP

> [!NOTE]
> Este artigo se concentra nos últimos três requisitos. Para obter mais informações sobre os dois primeiros, consulte os links acima.

## <a name="deployment-network-traffic"></a>Tráfego de rede de implantação
O DVM está configurado com um IP de rede BMC e requer acesso à rede para a internet. Embora nem todos os componentes de rede BMC exigem acesso à Internet ou roteamento externo, alguns componentes específicos de OEM utilizando IPs da rede também podem exigir que ele.

Durante a implantação, o DVM se autentica no Azure Active Directory (AD do Azure) usando uma conta do Azure de sua assinatura. Para fazer isso, o DVM requer acesso à internet para uma lista de URLs e porta específica. Você pode encontrar a lista completa do [publicar pontos de extremidade](azure-stack-integrate-endpoints.md) documentação. O DVM utilizará um servidor DNS para encaminhar solicitações DNS feitas por componentes internos para URLs externas. O DNS interno encaminha essas solicitações para o endereço do encaminhador DNS que você fornecer ao OEM antes da implantação. O mesmo é verdadeiro para o servidor NTP, um servidor de horário confiável é necessário para manter a consistência e a hora de sincronização para todos os componentes da pilha do Azure.

O acesso à Internet exigido pelo DVM durante a implantação é saído somente, sem chamadas de entrada são feitas durante a implantação. Tenha em mente que usa seu IP como fonte e a pilha do Azure não dá suporte a configurações de proxy. Portanto, se necessário, você precisa fornecer um proxy transparente ou NAT para acessar a internet. Após a conclusão da implantação, toda a comunicação entre o Azure e a pilha do Azure são feitas por meio da rede externa usando VIPs públicos.

Configurações de rede em comutadores de pilha do Azure contêm controle listas de acesso (ACLs) que restringem o tráfego entre certas fontes de rede e destinos. O DVM é o único componente com acesso irrestrito; até mesmo o HLH é muito restrito. Você pode pedir o OEM sobre as opções de personalização para facilitar o gerenciamento e acesso de suas redes. Devido a essas ACLs, é importante evitar alterar os endereços de servidor DNS e NTP no momento da implantação. Se você fizer isso, você precisará reconfigurar todas as opções para a solução.

Depois de concluída a implantação, os endereços de servidor NTP e o DNS fornecidos continuará a ser usada diretamente por componentes do sistema. Por exemplo, se você verificar as solicitações de DNS após a implantação, a fonte será alterado do DVM IP para um endereço do intervalo de rede externa.

Depois de pilha do Azure é implantada com êxito, seu parceiro OEM pode usar o DVM para tarefas pós-implantação adicionais. No entanto, quando todas as tarefas de implantação e pós-implantação configurações forem concluídas, o OEM deve remover e excluir o DVM do HLH.

## <a name="next-steps"></a>Próximas etapas
[Validar o registro do Azure](azure-stack-validate-registration.md)
