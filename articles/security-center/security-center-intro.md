---
title: "Introdução à Central de Segurança do Azure | Microsoft Docs"
description: "Saiba mais sobre a Central de Segurança do Azure, seus principais recursos e como ela funciona."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 8951167213da6ab5341c1ca420353ec625ef5424
ms.contentlocale: pt-br
ms.lasthandoff: 06/17/2017


---
# <a name="introduction-to-azure-security-center"></a>Introdução à Central de Segurança do Azure
Saiba mais sobre a Central de Segurança do Azure, seus principais recursos e como ela funciona.

> [!NOTE]
> A partir do início de junho de 2017, a Central de Segurança usará o Microsoft Monitoring Agent para coletar e armazenar dados. Veja [Migração da Plataforma Central de Segurança do Azure](security-center-platform-migration.md) para saber mais. As informações deste artigo representam a funcionalidade da Central de Segurança após a transição para o Microsoft Monitoring Agent.
>
>

## <a name="what-is-azure-security-center"></a>O que é a Central de Segurança do Azure?
 A Central de Segurança ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

## <a name="key-capabilities"></a>Principais recursos
 A Central de Segurança oferece uma prevenção contra ameaças eficiente e fácil de usar, além de recursos de detecção e resposta que são internos no Azure. Os principais recursos são:

| Estágio | Recurso |
| --- | --- |
| Evitar |Monitora o estado de segurança de seus recursos do Azure |
| Evitar | Define políticas para suas assinaturas do Azure com base nos requisitos de segurança da sua empresa, nos tipos de aplicativos que você usa e na confidencialidade dos seus dados |
| Evitar | Usa recomendações de segurança orientadas por políticas para guiar seus proprietários através do processo de implementação dos controles necessários |
| Evitar | Implanta rapidamente aplicativos da Microsoft e de parceiros e serviços de segurança |
| Detectar |Coleta e analisa dados de segurança automaticamente de seus recursos do Azure, da rede e de soluções de parceiros, como programas antimalware e firewalls |
| Detectar | Usa as ameaças globais de inteligência dos produtos e serviços Microsoft, a unidade de Crimes digitais da Microsoft (DCU), o Microsoft Security Response Center (MSRC) e feeds externos |
| Detectar | Aplica a análise avançada, incluindo aprendizado de máquina e análise comportamental |
| Responder |Fornece alertas/incidentes de segurança priorizados |
| Responder | Oferece informações sobre a origem do ataque e dos recursos afetados |
| Responder | Sugere maneiras para interromper o ataque atual e ajudar a evitar ataques futuros |

## <a name="introductory-walkthrough"></a>Passo a passo introdutório

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo. Este documento não é um guia passo a passo.
>
>

 Você acessar a Central de Segurança pelo [portal do Azure](https://azure.microsoft.com/features/azure-portal/). [Entre no Portal](https://portal.azure.com). No menu principal do portal, role até a opção **Central de Segurança** ou escolha o bloco **Central de Segurança** que você fixou anteriormente ao painel do Portal.

![Bloco de segurança no portal do Azure][1]

Na Central de Segurança, defina as políticas de segurança, monitore as configurações de segurança e exiba alertas de segurança.

### <a name="security-policies"></a>Diretivas de segurança
Você pode definir políticas para suas assinaturas do Azure de acordo com os requisitos de segurança da sua empresa. Você também pode personalizar os tipos de aplicativos que está usando ou a confidencialidade dos dados em cada assinatura. Por exemplo, os recursos usados para desenvolvimento ou teste podem ter requisitos de segurança diferentes daqueles usados para aplicativos de produção. Da mesma forma, os aplicativos com dados regulamentados, como PII, podem exigir um nível mais alto de segurança.

> [!NOTE]
> Para modificar uma política de segurança, você deve ser um Administrador de Segurança ou o Proprietário ou Colaborador da assinatura. Confira [Permissões na Central de Segurança do Azure](security-center-permissions.md) para saber mais sobre as funções e as ações permitidas na Central de Segurança.
>
>

Na folha **Central de segurança**, selecione o bloco **Política** para obter uma lista de suas assinaturas e grupos de recursos.   

![Folha da Central de segurança][2]

Na folha **Política de segurança** , selecione uma assinatura para exibir os detalhes da política.

**Coleta de dados** habilita a coleta de dados para uma política de segurança. A habilitação fornece:

* Verificação diária de todas as máquinas virtuais com suporte para monitoramento de segurança e recomendações.
* Coleção de eventos de segurança para a análise e detecção de ameaças.

> [!NOTE]
> A coleta de dados é configurada no nível de assinatura.
>
>

Selecione **Política de prevenção** para abrir a folha **Política de prevenção**. **Mostrar recomendações para** permite que você escolha os controles de segurança que deseja monitorar e as recomendações que você deseja ver, com base nas necessidades de segurança dos recursos na assinatura.

### <a name="security-recommendations"></a>Recomendações de segurança
 A Central de Segurança analisa o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades na segurança. Uma lista de recomendações orienta você no processo de configuração dos controles necessários. Os exemplos incluem:

* Provisionamento de antimalware para ajudar a identificar e remover software mal-intencionado
* Configurando os grupos de segurança de rede e as regras para controlar o tráfego para máquinas virtuais
* Provisionamento de firewalls do aplicativo Web para ajudar a proteger contra ataques direcionados aos seus aplicativos Web
* Como implantar atualizações de sistema ausentes
* Endereçamento de configurações do sistema operacional que não coincidem com as linhas de base recomendadas

Clique no bloco **Recomendações** para obter uma lista de recomendações. Clique em cada recomendação para exibir informações adicionais ou tomar uma atitude para resolver o problema.

![Recomendações de segurança na Central de Segurança do Azure][5]

### <a name="security-state-of-azure-resources"></a>Estado da segurança dos recursos do Azure
A seção **Prevenção** do painel mostra a postura geral de segurança do ambiente por tipo de recurso, incluindo VMs, aplicativos Web e outros recursos.   

Selecione um tipo de recurso em **Prevenção** para exibir mais informações, incluindo uma lista de possíveis vulnerabilidades de segurança que foram identificadas. (**Computação** é selecionado no exemplo a seguir.)

![Bloco de integridade de recursos][6]

### <a name="security-alerts"></a>Alertas de segurança
 A Central de segurança automaticamente coleta, analisa e integra os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros como programas antimalware e firewalls. Quando forem detectadas ameaças, é criado um alerta de segurança. Os exemplos abrangem a detecção de:

* As máquinas virtuais comprometidas se comunicam com os endereços IP mal-intencionados conhecidos
* Malware avançado detectado usando o relatório de erros do Windows
* Ataques por força bruta contra máquinas virtuais
* Alertas de segurança dos firewalls e programas antimalware integrados

Clicar no bloco **alertas de segurança** exibe uma lista de alertas prioritários.

![Alertas de segurança][7]

Selecionar um alerta mostra mais informações sobre o ataque e sugestões para corrigi-lo.

![Detalhes do alerta de segurança][8]

### <a name="partner-solutions"></a>Soluções de parceiros
O bloco **Soluções de parceiros** permite monitorar rapidamente o estado da segurança de suas soluções de parceiros integradas com sua assinatura do Azure. A Central de Segurança exibe alertas das soluções.

Selecione o bloco **Soluções de parceiros** . Uma folha será aberta exibindo uma lista de todas as soluções de parceiro conectadas.

![Soluções de parceiros][9]

## <a name="get-started"></a>Introdução
Para começar a usar a Central de Segurança, você precisa ter uma assinatura do Microsoft Azure. A Central de segurança é habilitada com sua assinatura do Azure. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

 Você acessar a Central de Segurança pelo [portal do Azure](https://azure.microsoft.com/features/azure-portal/). Consulte a [documentação do portal](https://azure.microsoft.com/documentation/services/azure-portal/) para saber mais.

[Introdução à Central de Segurança do Azure](security-center-get-started.md) o orienta rapidamente quanto ao monitoramento de segurança e aos componentes de gerenciamento da política da Central de Segurança.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você foi apresentado à Central de Segurança, seus principais recursos e como começar. Para saber mais, consulte os seguintes recursos:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) : saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e responder aos alertas de segurança.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Segurança de dados da Central de Segurança do Azure](security-center-data-security.md) – saiba como os dados são gerenciados e protegidos na Central de Segurança do Azure.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenha as últimas notícias de segurança e informações do Azure.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png

