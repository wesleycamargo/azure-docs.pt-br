---
title: Introdução aos ambientes de equipe com base em Máquina Virtual de Ciência de Dados – Azure | Microsoft Docs
description: Padrões para implantar a VM de Ciência de Dados em um ambiente de equipe corporativo.
keywords: aprendizado profundo, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados da equipe
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 2e17ab5cfe51f3772148cc730c982671d602a79a
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56340462"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Ambiente de IA e análise de equipe com base em Máquina Virtual de Ciência de Dados 
A [DSVM](overview.md) (Máquina Virtual de Ciência de Dados) fornece um ambiente avançado na plataforma Azure com o software predefinido de IA (inteligência artificial) e análise de dados. 

Tradicionalmente, a DSVM era usada como uma área de trabalho de análise individual. Os cientistas de dados individuais obtêm produtividade com essa noção compartilhada de seu ambiente de análise pré-compilado. À medida que as equipes de análise de grande porte planejam seus ambientes de análise para seus cientistas de dados e desenvolvedores de IA, um dos temas recorrentes é uma infraestrutura compartilhada de análise para experimentação e desenvolvimento. Essa infraestrutura é gerenciada de acordo com as políticas de TI da empresa, que também facilita a colaboração e a consistência entre as equipes de ciência de dados/análise. 

Uma infraestrutura compartilhada também permite que a TI utilize melhor o ambiente de análise. Algumas organizações chamam a infraestrutura de ciência/análise de dados com base em equipe de "área restrita de análise". Isso permite que cientistas de dados acessem vários ativos de dados para compreender os dados, executar experiências rapidamente, validar hipóteses e criar modelos de previsão sem afetar o ambiente de produção. 

Como a DSVM opera no nível de infraestrutura do Azure, os administradores de TI podem configurar prontamente a DSVM para operar em conformidade com as políticas de TI da empresa. O DSVM oferece total flexibilidade na implementação de várias arquiteturas de compartilhamento com acesso a ativos de dados corporativos de uma forma controlada. 

Esta seção discute alguns padrões e diretrizes que você pode usar para implantar a DSVM como uma infraestrutura de ciência de dados baseada em equipe. Os blocos de construção desses padrões são provenientes da infraestrutura do Azure como um serviço (IaaS). Portanto, eles se aplicam a qualquer VM do Azure. O foco desta série de artigos é sobre como aplicar esses recursos de infraestrutura padrão do Azure na VM de Ciência de Dados. 

Alguns dos principais blocos de construção de um ambiente corporativo de análise de equipe são:

* [Pool de dimensionamento automático de Máquinas Virtuais de Ciência de Dados](dsvm-pools.md)
* [Identidade e acesso comum ao workspace de qualquer uma das DSVMs no pool](dsvm-common-identity.md)
* [Acesso seguro a fontes de dados](dsvm-secure-access-keys.md)


Esta série de artigos fornece diretrizes e indicações para cada um dos itens anteriores. Isso não abrange todas as considerações e necessidades de implantação de DSVM em configurações de grande porte. Aqui está a documentação do Azure que você pode usar durante a implementação de instâncias DSVM em sua empresa: 

* [Segurança de rede](https://docs.microsoft.com/azure/security/azure-network-security)
* [Monitoramento](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) e [gerenciamento](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Log e auditoria](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Configuração da política e execução](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Criptografia](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Descoberta de dados e governança](https://docs.microsoft.com/azure/data-catalog/)

O [Centro de Arquitetura do Azure](https://docs.microsoft.com/azure/architecture/) fornece padrões e arquitetura detalhada de ponta a ponta para criar e gerenciar sua infraestrutura de análise baseado em nuvem. 
