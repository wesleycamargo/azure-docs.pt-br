---
title: Sobre o Monitoramento de Rede no Log Analytics | Microsoft Docs
description: "Visão geral de soluções de monitoramento de rede, incluindo NPM, para gerenciar redes em nuvens, locais e ambientes híbridos."
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 6d93821b59e1f69a48c3d5eeda96dad2edddb188
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="network-monitoring-solutions"></a>Soluções de monitoramento de rede 

Azure oferece diversas soluções para monitorar seus ativos de rede. Azure tem soluções e utilidades para monitorar a conectividade de rede, a integridade de circuitos ExpressRoute e analisar o tráfego de rede na nuvem.

## <a name="network-performance-monitor-npm"></a>Monitor de Desempenho de Rede (NPM)

Monitor de Desempenho de Rede (NPM) é um conjunto de recursos, cada um deles é voltado para o monitoramento da integridade de sua rede, a conectividade de rede para seus aplicativos e fornece informações sobre o desempenho da rede. NPM é baseado em nuvem e fornece uma solução de monitoramento de rede híbrida que monitora a conectividade entre:
 
* Implantações na nuvem e pontos locais
* Vários data centers e filiais
* Microsserviços/aplicativos multiníveis críticos à missão
* Aplicativos baseado em web e locais de usuários (HTTP/HTTPs) 

## <a name="performance-monitor"></a>Monitorar Desempenho

Monitor de Desempenho é parte do NPM e está monitorando a rede para ambientes locais, híbridos e na nuvem. Você pode monitorar a conectividade de rede em escritórios e filiais remotas, locais de armazenamento, data centers e nuvens. Você pode detectar problemas de rede antes de seus usuários reclamarem. As principais vantagens são:

* Monitorar perda e latência entre várias sub-redes e definir alertas
* Monitorar todos os caminhos (incluindo caminhos redundantes) na rede
* Solucionar problemas de rede transitórios e pontuais, que são difíceis de replicar
* Determinar um segmento específico na rede, que é responsável pelo desempenho degradado
* Monitorar a integridade da rede, sem a necessidade de SNMP

![Mapa de topologia NPM](./media/network-monitoring-overview/npm-topology-map.png) 

Para obter mais informações, consulte os seguintes artigos:

* [Configurar uma Solução Monitor de Desempenho de Rede no Log Analytics](../log-analytics/log-analytics-network-performance-monitor.md) 
* [Casos de uso](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  Atualizações de produto: [fevereiro de 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [agosto de 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>ExpressRoute Monitor

NPM para o ExpressRoute oferece monitoramento abrangente de ExpressRoute para conexões de emparelhamento privadas. Você pode monitorar desempenho e conectividade E2E entre as filiais e o Azure por meio do ExpressRoute. Os principais recursos são:

* Detecção automática de circuitos ER associados à sua assinatura
* Detecção da topologia de rede do local para seus aplicativos de nuvem
* Planejamento de capacidade, análise de utilização
* Monitoramento e alertas em caminhos primários e secundários
* Detectar degradação de conectividade para VNets

Para obter mais informações, consulte os seguintes artigos:

* [Configurar o Monitor de Desempenho de Rede para ExpressRoute](../expressroute/how-to-npm.md)
* [Postagem no blog](https://aka.ms/NPMExRmonitorGA)

## <a name="service-endpoint-monitor"></a>Monitor de Ponto de Extremidade de Serviço

Com o monitoramento de ponto de extremidade de serviço, você pode testar a acessibilidade de aplicativos e detectar gargalos de desempenho entre locais, redes de transporte e data centers de nuvem/privada.

* Monitorar a conectividade de rede de ponta a ponta para aplicativos
* Correlacionar a entrega de aplicativos com o desempenho da rede, detectar o local exato de degradação ao longo do caminho entre o usuário e o aplicativo
* Testar a acessibilidade de aplicativo de vários locais do usuário em todo o mundo
* Determinar a rede latência e perda de pacotes para a linha de negócios e aplicativos SaaS
* Determinar os pontos de acesso na rede, que podem estar causando o desempenho insatisfatório do aplicativo
* Monitorar o alcance a aplicativos do Office 365, usando testes internos para o Microsoft Office 365, Dynamics 365, Skype for Business e outros serviços da Microsoft

Para obter mais informações, consulte os seguintes artigos:

* [Configurar o Monitor de Desempenho de Rede para monitorar pontos de extremidade do serviço](https://aka.ms/applicationconnectivitymonitorguide)
* [Postagem no blog](https://aka.ms/svcendptmonitor)

## <a name="next-steps"></a>Próximas etapas

* [Configurar o Monitor de Desempenho de Rede](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)
* [Configurar o Monitor de Desempenho de Rede para ExpressRoute](../expressroute/how-to-npm.md)
