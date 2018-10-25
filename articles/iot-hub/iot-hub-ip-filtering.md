---
title: Filtros IP de conexão do Hub IoT do Azure | Microsoft Docs
description: Como usar a filtragem de IP para bloquear conexões de endereços IP específicos para seu Hub IoT do Azure. Você pode bloquear conexões de endereços IP individuais ou de intervalos de endereços IP.
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: rezas
ms.openlocfilehash: 903f8284327d3d5b9ef386305a436ce44a8a11b2
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378095"
---
# <a name="use-ip-filters"></a>Usar filtros IP

A segurança é um aspecto importante de qualquer solução de IoT com base no Hub IoT do Azure. Às vezes você precisa especificar explicitamente os endereços IP dos quais o dispositivo pode se conectar como parte da sua configuração de segurança. O recurso *filtro IP* permite que você configure regras para rejeitar ou aceitar tráfego de endereços IPv4 específicos.

## <a name="when-to-use"></a>Quando usar

Há dois casos de uso específicos em que é útil bloquear os pontos de extremidade do Hub IoT para determinados endereços IP:

* O Hub IoT deve receber tráfego somente de um intervalo específico de endereços IP e rejeitar todo o resto. Por exemplo, caso você esteja usando o Hub IoT com o [Azure ExpressRoute](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) para criar conexões privadas entre um Hub IoT e sua infraestrutura local.

* Você precisa rejeitar tráfego de endereços IP que foram identificados como suspeitos pelo administrador do Hub IoT.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro IP são aplicadas no nível do serviço Hub IoT. Portanto, as regras de filtro IP se aplicam a todas as conexões de dispositivos e aplicativos de back-end que usam qualquer protocolo com suporte.

Todas as tentativas de conexão de um endereço IP que corresponde a uma regra IP de rejeição em seu Hub IoT recebem um código de status 401 não autorizado e uma descrição. A mensagem de resposta não menciona a regra IP.

## <a name="default-setting"></a>Configuração padrão

Por padrão, a grade **Filtro IP** no portal de Hub IoT fica vazia. Essa configuração padrão significa que seu hub aceita conexões de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

![Configurações de filtro IP padrão do Hub IoT](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Adicionar ou editar uma regra de filtro IP

Quando você adiciona uma regra de filtro IP, os seguintes valores são solicitados:

* Um **nome de regra de filtro IP** que deve ser uma cadeia de caracteres alfanumérica exclusiva com até 128 caracteres que diferencia maiúsculas de minúsculas. Somente são aceitos caracteres alfanuméricos ASCII de 7 bits mais `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Selecione **rejeitar** ou **aceitar** como a **ação** para a regra de filtro IP.

* Forneça um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, uma notação CIDR 192.168.100.0/22 representa os 1024 endereços IPv4 de 192.168.100.0 a 192.168.103.255.

![Adicionar uma regra de filtro IP a um Hub IoT](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

Depois de salvar a regra, você verá um alerta informando que a atualização está em andamento.

![Notificação sobre como salvar uma regra de filtro IP](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

A opção **Adicionar** é desabilitada quando você atinge o máximo de dez regras de filtro IP.

Você pode editar uma regra existente clicando duas vezes na linha que contém a regra.

> [!NOTE]
> Rejeitar endereços IP pode impedir que outros serviços do Azure (como Stream Analytics do Azure, Máquinas Virtuais do Azure ou o Explorer de dispositivos no portal) interajam com o hub IoT.

> [!WARNING]
> Se você usar o ASA (Azure Stream Analytics) para ler mensagens de um hub IoT com a filtragem de IP habilitada, use o nome compatível com o Hub-de Eventos e o ponto de extremidade do Hub IoT na cadeia de conexão ASA.

## <a name="delete-an-ip-filter-rule"></a>Excluir uma regra de filtro IP

Para excluir uma regra de filtro IP, selecione uma ou mais regras na grade e clique em **Excluir**.

![Excluir uma regra de filtro IP de Hub IoT](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Por exemplo, se você quiser aceitar endereços no intervalo 192.168.100.0/22 e rejeitar todo o resto, a primeira regra na grade deverá aceitar o intervalo de endereços 192.168.100.0/22. A próxima regra deve rejeitar todos os endereços usando o intervalo 0.0.0.0/0.

Você pode alterar a ordem de suas regras de filtro IP na grade clicando nos três pontos verticais no início de uma linha e usando arrastar e soltar.

Para salvar a nova ordem das regras de filtro IP, clique em **Salvar**.

![Alterar a ordem de suas regras de filtro IP de Hub IoT](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Próximas etapas

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Monitoramento de operações](iot-hub-operations-monitoring.md)
* [Métricas do IoT Hub](iot-hub-metrics.md)