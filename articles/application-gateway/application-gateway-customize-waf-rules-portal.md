---
title: Personalizar regras de firewall de aplicativo web no Gateway de aplicativo do Azure - portal do Azure
description: Este artigo fornece informações sobre como personalizar regras de firewall de aplicativo Web no Gateway de Aplicativo com o portal do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: b18c9666e58925746a3b61740db6fb5118c2010b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733709"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>Personalizar regras de firewall de aplicativo Web por do portal do Azure

O WAF (firewall de aplicativo Web) do Gateway de Aplicativo do Azure fornece proteção para aplicativos Web. Essas proteções são fornecidas pelo CRS (conjunto de regras principais) do OWASP (Open Web Application Security Project). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esse motivo, o Gateway de Aplicativo possibilita que a capacidade personalize regras e grupos de regras. Para obter mais informações sobre os grupos de regras e as regras específicas, consulte a [Lista de regras e grupos de regras de CRS do firewall de aplicativo Web](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Se o gateway de aplicativo não estiver usando a camada WAF, será exibida no painel direito a opção de atualizar o gateway de aplicativo para a camada WAF. 

![Habilitar WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Exibir grupos de regras e regras

**Para exibir grupos de regras e regras**
   1. Navegue até o gateway de aplicativo e selecione **Firewall do aplicativo Web**.  
   2. Selecione **Configuração de regra avançada**.  
   Essa exibição mostra uma tabela na página com todos os grupos de regras fornecidos com o conjunto de regras escolhido. Todas as caixas de seleção de regra são selecionadas.

![Configurar regras desabilitadas][1]

## <a name="search-for-rules-to-disable"></a>Pesquisar pelas regras a desabilitar

A folha **Configurações de firewall do aplicativo Web** possibilita que a capacidade filtre as regras usando uma pesquisa de texto. O resultado exibe apenas grupos de regras e regras que contêm o texto que você pesquisou.

![Pesquisar por regras][2]

## <a name="disable-rule-groups-and-rules"></a>Desabilitar regras e grupos de regras

Quando você está desabilitando regras, você pode desabilitar um grupo de regras inteiro ou regras específicas em um ou mais grupos de regras. 

**Para desabilitar regras específicas ou grupos de regras**

   1. Pesquise pelas regras ou os grupos de regras que você deseja desabilitar.
   2. Desmarque as caixas de seleção das regras que deseja desabilitar. 
   2. Clique em **Salvar**. 

![Salvar alterações][3]

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista a seguir contém condições que causam o WAF bloquear a solicitação no modo de prevenção (no modo de detecção são registradas como exceções). Eles não podem ser configurados ou desabilitados:

* Falha ao analisar o corpo da solicitação resulta na solicitação que está sendo bloqueada, a menos que a inspeção de corpo está desativada (XML, JSON, dados de formulário)
* Comprimento de dados do corpo (e não há arquivos) de solicitação é maior do que o limite configurado
* Corpo (incluindo arquivos) é maior que o limite de solicitação
* Ocorreu um erro interno no mecanismo de WAF

Específico do CRS 3. x:

* Limite excedida de pontuação de anomalias de entrada

## <a name="next-steps"></a>Próximas etapas

Depois de configurar as regras desabilitadas, você pode aprender como exibir os logs de WAF. Para obter mais informações, consulte [Diagnósticos do Gateway de Aplicativo](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
