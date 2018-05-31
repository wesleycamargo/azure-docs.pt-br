---
title: Introdução aos ambientes de equipe com base em Máquina Virtual de Ciência de Dados – Azure | Microsoft Docs
description: Padrões para a implantação de VM de Ciência de Dados como ambiente de equipes corporativas.
keywords: aprendizado profundo, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados da equipe
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 6a755ef4d933046377a6a25be76655b44f4bf508
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361362"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Ambiente de IA e Análise de Equipe com base em Máquina Virtual de Ciência de Dados 
A DSVM ([Máquina Virtual de Ciência de Dados](overview.md)) fornece um ambiente rico no Azure Cloud com o software pré-integrado para análise de dados de IA. Tradicionalmente, a DSVM foi usado como área de trabalho de análise individual, e os cientistas de dados individuais ganham produtividade com essa noção compartilhada de seu ambiente de análise pré-integrado. À medida que as equipes de análise de grande porte planejam seus ambientes de análise para seus cientistas de dados e desenvolvedores de IA, um dos temas recorrentes é uma infraestrutura compartilhada de experimentação e desenvolvimento de análise, gerenciada de acordo com as políticas de TI da empresa, que também facilita a colaboração e a consistência entre as equipes de ciência de dados/análise. Uma infraestrutura compartilhada também permite que a TI utilize melhor o ambiente de análise. A infraestrutura de ciência de dados/análise baseada em equipes também é conhecida por algumas organizações como uma "Área restrita de análise", que permite aos cientistas de dados compreender rapidamente os dados, realizar experiências, validar hipóteses, criar modelos de previsão de uma maneira segura sem afetar o ambiente de produção, contando com acesso a vários ativos de dados. 

Como a DSVM opera no nível de infraestrutura do Azure, os administradores de TI podem configurar prontamente a DSVM para operar em conformidade com as políticas de TI da empresa, e oferece total flexibilidade na implementação de várias arquiteturas de compartilhamento com acesso ao ativos de dados corporativos de forma controlada. 

Esta seção discute alguns padrões e diretrizes que podem ser usadas para implantar a DSVM como uma infraestrutura de ciência de dados baseada em equipe.  Os blocos de construção para esses padrões são usados diretamente no Azure IaaS (infraestrutura como serviço) e, por isso, são aplicáveis a todas as VMs do Azure. O foco específico desta série de artigos é sobre como aplicar esses recursos de infraestrutura padrão do Azure na VM de Ciência de Dados. 

Alguns dos principais blocos de construção de um ambiente corporativo de análise de equipe são:

* [Pool de dimensionamento automático de máquinas de virtuais de ciência de dados](dsvm-pools.md)
* [Identidade e acesso comum ao espaço de trabalho de qualquer uma das DSVMs no pool](dsvm-common-identity.md)
* [Acesso seguro ao catálogo de dados](dsvm-secure-access-keys.md)


Nesta série de artigos, forneceremos orientação e indicadores sobre cada um desses aspectos. Obviamente, há várias considerações adicionais e necessidades ao implantar DSVM em configurações de grande porte que não são ainda diretamente abordadas nesta série de artigos. Aqui estão algumas das outras considerações e ponteiros à documentação do Azure geral que podem ser usadas imediatamente ao implementá-la em instâncias DSVM em sua empresa. 

* [Segurança de rede](https://docs.microsoft.com/azure/security/azure-network-security)
* [Monitoramento](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) e [Gerenciamento](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Registro em log e auditoria](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Configuração da política e execução](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [Anti-malware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Criptografia](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Descoberta de dados e governança](https://docs.microsoft.com/azure/data-catalog/)

O [centro da arquitetura do Microsoft Azure](https://docs.microsoft.com/en-us/azure/architecture/) também é um ótimo recurso que fornece padrões e arquitetura detalhada de ponta a ponta para criar e gerenciar sua infraestrutura de análise baseado em nuvem. 
