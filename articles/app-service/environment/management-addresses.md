---
title: "Endereços de gerenciamento de Ambiente do Serviço de Aplicativo do Azure"
description: "Lista os endereços de gerenciamento usados para comandar um Ambiente de Serviço de Aplicativo"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 949b445793768f593af205321bf3ab5a7aa150c0
ms.contentlocale: pt-br
ms.lasthandoff: 07/14/2017

---
# <a name="app-service-environment-management-addresses"></a>Endereços de gerenciamento de Ambiente de Serviço de Aplicativo

O ASE (Ambiente do Serviço de Aplicativo) é uma implantação do Serviço de Aplicativo do Azure em uma sub-rede da sua VNet (Rede Virtual) do Azure.  O ASE deve estar acessível do Serviço de Aplicativo do Azure para que possa ser gerenciado.  Esse tráfego de gerenciamento do ASE atravessa a rede controlada pelo usuário.  Isso vem dos servidores de gerenciamento do Serviço de Aplicativo do Azure para o VIP público associado ao ASE.  Para obter detalhes sobre as dependências de rede do ASE, leia [Considerações de rede e o Ambiente de Serviço de Aplicativo][networking].  Para obter informações gerais sobre o ASE, você pode iniciar com [Introdução ao Ambiente do Serviço de Aplicativo][intro].

Este documento lista os IPs de origem para o tráfego de gerenciamento para o ASE. Você pode usar esses endereços para criar Grupos de Segurança de Rede para bloquear o tráfego de entrada ou usá-lo em Tabelas de Rotas, conforme necessário.  Para usar essas informações, você precisa usar:

* os endereços IP listados para Todas as regiões
* os endereços IP que correspondem à região na qual seu ASE está implantado.

O tráfego de gerenciamento de entrada chega desses endereços IP para as portas 454 e 455.

| Região | Endereços |
|--------|-----------|
| Todas as regiões | 70.37.57.58, 157.55.208.185, 52.174.22.21,13.94.149.179,13.94.143.126,13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141 |
| Centro-Sul dos EUA e Centro-Norte dos EUA | 23.102.188.65, 191.236.154.88 |
| Sudeste da Austrália e Leste da Austrália | 23.101.234.41, 104.210.90.65 |
| Oeste dos EUA e Leste dos EUA | 104.45.227.37, 191.236.60.72 |
| Europa Ocidental e Europa Setentrional | 191.233.94.45, 191.237.222.191 |
| Centro-Oeste dos EUA e Oeste dos EUA 2 | 13.78.148.75, 13.66.225.188 |
| EUA Central e Leste dos EUA 2 | 104.43.165.73, 104.46.108.135 |
| Ásia Oriental e Sudeste Asiático | 23.99.115.5, 104.215.158.33 |
| Leste do Japão e Oeste do Japão | 104.41.185.116, 191.239.104.48 |
| Centro do Canadá e Leste do Canadá | 40.85.230.101, 40.86.229.100 |
| Oeste do Reino Unido e Sul do Reino Unido | 51.141.8.34, 51.140.185.75 |
| Sul da Coreia e Centro da Coreia | 52.231.200.177, 52.231.32.117 |
| Sul do Brasil e Centro-Sul dos EUA| 104.41.46.178, 23.102.188.65 |
| Índia Central e Sul da Índia | 104.211.98.24, 104.211.225.66 |
| Índia Ocidental e Sul da Índia | 104.211.160.229, 104.211.225.66 |


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md

