---
title: "Perguntas frequentes sobre problemas de rede e conectividade para Serviços de Nuvem do Microsoft Azure | Microsoft Docs"
description: "Este artigo lista as perguntas frequentes sobre conectividade e rede para Serviços de Nuvem do Microsoft Azure."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 3fc0d34fdb617ebb1af9c9f33e018d5fe6ec9a7d
ms.contentlocale: pt-br
ms.lasthandoff: 06/15/2017


---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de rede e conectividade para Serviços de Nuvem do Azure: perguntas frequentes

Este artigo inclui perguntas frequentes sobre problemas de conectividade e rede para [Serviços de Nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Você também pode consultar a [página de tamanho de VM de Serviços de Nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Eu não consigo reservar um IP em um serviço de nuvem de vários VIPs
Primeiro, certifique-se de que a instância de máquina virtual que você está tentando reservar o IP esteja ativada. Segundo, certifique-se de usar IPs Reservados para as implantações de preparo e produção. **Não** altere as configurações enquanto a implantação é atualizada.

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>Como acesso a área de trabalho remota quando tenho um NSG?
Adicione regras para o NSG que permitem o tráfego nas portas **3389** e **20000**.  A Área de Trabalho Remota usa a porta **3389**.  Instâncias de serviço de nuvem têm a carga balanceada, de modo que você não pode controlar diretamente a escolha da instância à qual se conectar.  Os agentes *RemoteForwarder* e *RemoteAccess* gerenciam o tráfego RDP e permitem que o cliente envie um cookie RDP e especifique uma instância individual à qual se conectar.  Os agentes *RemoteForwarder* e *RemoteAccess* exigem que a porta **20000** esteja aberta, que poderá ser bloqueada se você tiver um NSG.

## <a name="can-i-ping-a-cloud-service"></a>Posso executar ping de um serviço de nuvem?

Não, não usando o protocolo ICMP/"ping" normal. O protocolo ICMP não é permitido por meio do Azure Load Balancer.

Para testar a conectividade, é recomendável fazer um ping de porta. Enquanto Ping.exe utiliza ICMP, outras ferramentas, como PSPing, Nmap e telnet, permitem testar a conectividade com uma porta TCP específica.

Para obter mais informações, consulte [Usar pings de porta em vez de ICMP para testar a conectividade de VM do Azure](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>Como fazer para impedir o recebimento de milhares de visitas de endereços IP desconhecidos que indiquem algum tipo de ataque mal-intencionado ao serviço de nuvem?
O Azure implementa um segurança de rede de várias camadas para proteger seus serviços de plataforma contra ataques de DDoS (negação de serviço distribuída). O sistema de proteção contra DDoS do Azure faz parte do processo de monitoramento contínuo do Azure, que é aprimorado continuamente por meio de teste de penetração. Esse sistema de proteção DDoS foi projetado para resistir não só a ataques externos, como também a de outros locatários do Azure. Para obter mais detalhes, consulte [Segurança de Rede do Microsoft Azure](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Você também pode criar uma tarefa de inicialização para bloquear seletivamente alguns endereços IP específicos. Para obter mais informações, consulte [Bloquear um endereço IP específico](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Quando tento fazer RDP para minha instância de serviço de nuvem, recebo a mensagem: "A conta do usuário expirou".
Você pode receber a mensagem de erro "Esta conta de usuário expirou" quando você ignora a data de validade configurada nas configurações de RDP. Você pode alterar a data de validade no portal seguindo estas etapas:
1. Faça logon no Console de Gerenciamento do Azure (https://manage.windowsazure.com), navegue até seu serviço de nuvem e selecione a guia **Configurar**.
2. Selecione **Remoto**.
3. Altere a data "Expira em" e, em seguida, salve a configuração.

Agora você deve ser capaz de fazer RDP para seu computador.

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>Por que o Balanceador de Carga não está balanceamento o tráfego uniformemente?
Para obter informações sobre como funciona o balanceador de carga interno, consulte [Novo modo de distribuição do Azure Load Balancer](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

O algoritmo de distribuição usado é um hash de 5 tuplas (IP de origem, porta de origem, IP de destino, porta de destino, tipo de protocolo) para mapear o tráfego para os servidores disponíveis. Ele fornece permanência somente dentro de uma sessão de transporte. Os pacotes na mesma sessão TCP ou UDP serão direcionados para a mesma instância do DIP (IP de Datacenter), atrás do ponto de extremidade de balanceamento de carga. Quando o cliente fecha e reabre a conexão ou inicia uma nova sessão por meio do mesmo IP de origem, a porta de origem é alterada e faz com que o tráfego vá para um ponto de extremidade DIP diferente.

