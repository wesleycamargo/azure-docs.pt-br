---
title: "Linha de base de solução de Segurança e Auditoria do Operations Management Suite | Microsoft Docs"
description: "Este documento explica como usar a solução de Segurança e Auditoria do OMS para realizar uma avaliação de linha de base de Web de todos os servidores Web para fins de conformidade e segurança."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ROBOTS: NOINDEX
redirect_url: https://www.microsoft.com/cloud-platform/security-and-compliance
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0d4707dc0c0ffbf40d0d10a6d12b9709a9655258
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Avaliação de linha de base de Web na Solução de Segurança e Auditoria do Operations Management Suite
Este documento o ajuda a usar os recursos de avaliação da linha de base da Web da [Solução de Segurança e Auditoria do Operations Management Suite (OMS)](operations-management-suite-overview.md) para acessar o estado de segurança de seus recursos monitorados.

## <a name="what-is-web-baseline-assessment"></a>O que é a Avaliação de Linha de Base da Web?
Atualmente a Segurança do OMS oferece uma avaliação de linha de base para os sistemas operacionais. Ele examina as configurações de sistema operacional dos servidores a cada 24 horas e fornece uma exibição para configurações potencialmente vulneráveis. Leia [Avaliação de Linha de Base na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-baseline.md) para saber mais informações sobre esta opção.

O objetivo da avaliação de linha de base da Web é localizar as configurações de servidor Web vulneráveis. As três fontes principais para as configurações de linha de base da Web são: configuração do .NET, ASP.NET e IIS.  Assim como a avaliação de linha de base do sistema operacional, a Segurança do OMS vai verificar os seus servidores Web a cada 24 horas e fornecer uma exibição do estado de segurança deles.  No Serviço de Informações da Internet (IIS), as configurações são altamente personalizáveis, o que permite que vários níveis de aplicativo e site sejam substituídos. O scanner verifica as configurações em cada nível de site/aplicativo além do nível de raiz padrão. Isso ajuda a identificar os locais das potenciais vulnerabilidades das configurações e corrigi-las rapidamente.


## <a name="web-security-baseline-assessment"></a>Avaliação de Linha de Base de Segurança da Web
Para essa visualização esse recurso está prestes a ser acessado usando a opção de Pesquisa do OMS. Siga as etapas abaixo para executar a consulta adequada:

1. No painel principal do **Microsoft Operations Management Suite**, clique no bloco **Segurança e Auditoria**.
2. No painel **Segurança e Auditoria**, clique no botão **Pesquisa de Log**.
3. A primeira consulta que você pode usar é o **Resumo da avaliação de linha de base da Web**. No campo **Iniciar pesquisa aqui**, digite esta consulta: Type*=SecurityBaselineSummary BaselineType=web*. A tela a seguir tem um exemplo de saída:

![Avaliação de linha de base da Web](./media/oms-security-web-baseline/oms-security-web-baseline-fig1-new.png)

> [!NOTE]
> Nesta consulta, cada registro indica o resumo da avaliação em um único servidor.

Quando você estiver na **Pesquisa de Log**, você pode digitar diferentes consultas para obter mais informações sobre a avaliação de linha de base da web. Além da consulta anterior, você também pode usar as seguintes nesta exibição.

**Avaliação da regra de linha de base de Web**: Cada registro representa uma avaliação da regra de linha de base única Web em um único servidor. Ele inclui todos os dados para a regra, local, o resultado esperado e o resultado real.

**Consulta**: Type*=SecurityBaseline BaselineType=web*

![Avaliação de regra de linha de base da Web](./media/oms-security-web-baseline/oms-security-web-baseline-fig2.png)

**Mostrar todos os resultados para um servidor específico**: Esta consulta mostra como ver os resultados de um servidor específico.

**Consulta**: *Type=SecurityBaseline BaselineType=web Computer=BaselineTestVM1*

![Todos os resultados](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

Você também pode usar esses registros/consultas para criar seus próprios painéis, relatórios ou alertas. A tela a seguir tem um controle de interface do usuário de exemplo que você pode adicionar ao seu painel. Você pode aprender como visualizar os seus dados usando o Designer de Exibição do OMS [aqui](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/). A tela a seguir é um exemplo de como o bloco parecerá após fazer essa personalização.

![Interface de usuário de exemplo](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

> [!NOTE]
> Se você gostaria de saber as configurações que são verificadas para a avaliação de linha de base, você pode baixar [essa planilha do Excel](https://gallery.technet.microsoft.com/OMS-Web-Baseline-1e811690) que contém essas configurações.

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu sobre a avaliação de linha de base de Web da Segurança e Auditoria do OMS. Para saber mais sobre a Segurança do OMS, veja os seguintes artigos:

* [Operations Management Suite (OMS) overview](operations-management-suite-overview.md)
* [Monitorando e respondendo a alertas de segurança na solução de Segurança e Auditoria do Operations Management Suite](oms-security-responding-alerts.md)
* [Monitorando recursos na solução de Segurança e Auditoria do Operations Management Suite](oms-security-monitoring-resources.md)


