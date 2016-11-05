---
title: Introdução à criação de balanceador de carga para a Internet no modelo de implantação clássico usando o Portal do Azure | Microsoft Docs
description: Saiba como criar um balanceador de carga para a Internet no modelo de implantação clássico usando o portal do Azure
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''
tags: azure-service-management

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/17/2016
ms.author: sewhee

---
# Introdução à criação de um balanceador de carga para a Internet (clássico) no Portal do Azure
[!INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação clássico. Também é possível [Saber como criar um balanceador de carga para a Internet usando o Gerenciador de Recursos do Azure](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## Introdução à criação de um ponto de extremidade do balanceador de carga usando o Portal do Azure
Para criar um modelo de implantação (clássico) de balanceador de carga para a Internet no Portal do Azure, siga as etapas abaixo.

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Vá até a folha de máquinas virtuais (clássico) > selecione uma máquina virtual.
3. Na folha "conceitos básicos" das máquinas virtuais > selecione "todas as configurações"
4. Clique em “conjuntos com balanceamento de carga”.
5. Para criar um novo balanceador de carga, clique no ícone "ingressar" na parte superior da folha de conjuntos com balanceamento de carga.
6. Selecione o público "tipo de conjunto com balanceamento de carga" para balanceador de carga para a Internet.
7. Clique em "definir configurações necessárias" para abrir "escolher um conjunto com balanceamento de carga" e clique em "criar um conjunto com balanceamento de carga".
8. Na folha "criar um conjunto com balanceamento de carga", crie um nome para o conjunto de balanceadores de carga. Preencha o nome, porta pública, protocolo de investigação e porta de investigação.
9. Se necessário, altere o intervalo de investigações e novas tentativas.
10. (opcional) se desejar, você pode configurar regras de controle de acesso da folha de criação de conjunto de balanceadores de carga.
11. Clique em ok para voltar à folha "ingressar conjunto com balanceamento de carga".
12. Clique em ok e aguarde o novo recurso de Balanceador de carga ser exibido na folha "conjuntos de balanceadores de carga".

## Próximas etapas
[Introdução à configuração de um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0824_2016-->