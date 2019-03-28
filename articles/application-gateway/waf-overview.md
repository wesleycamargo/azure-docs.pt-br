---
title: Introdução ao firewall do aplicativo web para o Gateway de aplicativo do Azure
description: Este artigo fornece uma visão geral do WAF (Firewall do Aplicativo Web) para Gateway de Aplicativo
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 830513a03bd65ca14cb0938ae599a676f1bb3bca
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518177"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Firewall do aplicativo Web para o Gateway de aplicativo do Azure

O Gateway de aplicativo do Azure oferece um firewall do aplicativo web (WAF) que fornece proteção centralizada de seus aplicativos web contra vulnerabilidades e explorações comuns. Aplicativos Web cada vez mais são alvo de ataques mal-intencionados que exploram vulnerabilidades conhecidas com frequência. Injeção de SQL e scripts entre sites estão entre os ataques mais comuns.

Impedir esses ataques no código do aplicativo é um desafio. Ele pode exigir manutenção rigorosa, aplicação de patches e monitoramento em várias camadas da topologia do aplicativo. Firewall do aplicativo web centralizado ajuda a tornar o gerenciamento de segurança muito mais simples. Um WAF também oferece aos administradores de aplicativo mais garantia de proteção contra ameaças e invasões.

Uma solução WAF pode reagir a ameaças de segurança mais rapidamente ao aplicar um patch centralmente uma vulnerabilidade conhecida, em vez de proteger todos os aplicativos web individuais. Gateways de aplicativo existente poderá ser facilmente convertidos em gateways de aplicativo habilitado para parede de incêndio.

O WAF do Gateway de aplicativo se baseia [Core regra CRS (conjunto)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9 do Open Web OWASP Application Security Project (). O WAF é atualizado automaticamente para incluir a proteção contra novas vulnerabilidades, sem nenhuma configuração adicional necessária.

![Diagrama do WAF do Gateway de aplicativo](./media/waf-overview/WAF1.png)

O Gateway de aplicativo funciona como um controlador de entrega de aplicativos (ADC). Ele oferece a terminação de Secure Sockets Layer (SSL), afinidade de sessão baseada em cookies, distribuição de carga de round-robin, roteamento baseado em conteúdo, capacidade de hospedar vários sites e os aprimoramentos de segurança.

Aprimoramentos de segurança do Gateway de aplicativo incluem gerenciamento de política de SSL e dar suporte a SSL de ponta a ponta. Segurança de aplicativo é reforçada pela integração do WAF no Gateway de aplicativo. A combinação protege seus aplicativos web contra vulnerabilidades comuns. E ele fornece um local central-configurar para gerenciar.

## <a name="benefits"></a>Benefícios

Esta seção descreve os principais benefícios que fornecem o Gateway de aplicativo e seu WAF.

### <a name="protection"></a>Proteção

* Protege seus aplicativos web contra vulnerabilidades da web e de ataques sem modificação no código de back-end.

* Protege vários aplicativos web ao mesmo tempo. Uma instância do Gateway de aplicativo pode hospedar até 20 sites que são protegidos pelo firewall do aplicativo web.

### <a name="monitoring"></a>Monitoramento

* Monitorar ataques contra seus aplicativos web usando um log WAF em tempo real. O log é integrado ao [do Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) para controlar os alertas de WAF e monitorar facilmente as tendências.

* O WAF do Gateway de aplicativo é integrado com a Central de segurança do Azure. Central de segurança fornece uma visão central do estado de segurança de todos os seus recursos do Azure.

### <a name="customization"></a>Personalização

* Você pode personalizar as regras WAF e grupos de regras para atender às suas necessidades de aplicativo e eliminar falsos positivos.

## <a name="features"></a>Recursos

- Proteção de injeção de SQL.
- Proteção contra scripts entre sites.
- Proteção contra outros ataques da web comuns, como injeção de comando, as solicitações HTTP indesejadas, divisão de resposta HTTP e inclusão de arquivo remoto.
- Proteção contra violações de protocolo HTTP.
- Proteção contra anomalias de protocolo HTTP, como ausência de host de agente do usuário e cabeçalhos de aceitação.
- Proteção contra bots, rastreadores e scanners.
- Detecção de problemas de configuração de aplicativo comuns (por exemplo, o Apache e o IIS).
- Limita o tamanho da solicitação configurável com os limites inferior e superior.
- Listas de exclusão permitem omitir determinados atributos de solicitação de uma avaliação de WAF. Um exemplo comum é tokens inseridos pelo Active Directory que são usados para autenticação ou campos de senha.

### <a name="core-rule-sets"></a>Conjuntos de regras principais

O Gateway de aplicativo dá suporte a dois conjuntos de regras, CRS 3.0 e CRS 2.2.9. Essas regras protegem seus aplicativos web contra atividades maliciosas.

O Gateway de aplicativo WAF vem pré-configurado com o CRS 3.0 por padrão. Mas você pode optar por usar o CRS 2.2.9 em vez disso. O CRS 3.0 oferece menos falsos positivos em comparação com o CRS 2.2.9. Você também pode [personalize regras para atender às suas necessidades](application-gateway-customize-waf-rules-portal.md).

O WAF protege contra vulnerabilidades da web a seguir:

- Ataques de injeção de SQL
- Ataques de script entre sites
- Solicitação de outros ataques comuns, como injeção de comandos HTTP indesejada, separação de resposta HTTP e inclusão de arquivo remoto
- Violações de protocolo HTTP
- Anomalias de protocolo HTTP, como ausência de hospedam de agente do usuário e cabeçalhos de aceitação
- Bots, rastreadores e scanners
- Aplicativo configurações incorretas comuns (por exemplo, o Apache e o IIS)

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0 inclui 13 grupos de regras, conforme mostrado na tabela a seguir. Cada grupo contém várias regras, que podem ser desabilitadas.

|Grupo de regras|DESCRIÇÃO|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Bloquear métodos (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|Proteção contra scanners de porta e ambiente|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|Proteger contra o protocolo e problemas de codificação|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|Proteger contra injeção de cabeçalho, solicitações indesejadas e divisão de resposta|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Proteger contra ataques de arquivo e caminho|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Proteger contra ataques RFI (inclusão) de arquivo remoto|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Proteger novamente os ataques de execução remota de código|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|Proteger contra ataques de injeção de PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Proteger contra ataques de script entre sites|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Proteger contra ataques de injeção de SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Proteger contra ataques de fixação da sessão|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

O CRS 2.2.9 inclui 10 grupos de regras, conforme mostrado na tabela a seguir. Cada grupo contém várias regras, que podem ser desabilitadas.

|Grupo de regras|DESCRIÇÃO|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|Proteção contra violações de protocolo (como caracteres inválidos ou um GET com um corpo de solicitação)|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Proteção contra informações de cabeçalho incorretas|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Proteção contra argumentos ou arquivos que excedem os limites|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|Proteção contra métodos restritos, cabeçalhos e tipos de arquivo|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Proteção contra scanners e rastreadores da web|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Proteção contra ataques genéricos (como fixação da sessão, inclusão de arquivo remoto e injeção de PHP)|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Proteger contra ataques de injeção de SQL|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Proteger contra ataques de script entre sites|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Proteger contra ataques de percurso de caminho|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Proteção contra cavalos de Troia de backdoor|

### <a name="waf-modes"></a>Modos de WAF

O WAF do Gateway de aplicativo pode ser configurado para ser executado em dois modos a seguir:

* **Modo de detecção**: Monitora e registra todos os alertas de ameaça. Ativar o log de diagnóstico do Gateway de aplicativo na **diagnóstico** seção. Você também deve verificar se que o log do WAF é selecionado e ativado. Firewall do aplicativo Web não bloqueia solicitações de entrada quando ele está operando no modo de detecção.
* **Modo de prevenção**: Invasões de blocos e ataques que as regras de detecção. O invasor recebe uma exceção de "403 acesso não autorizado", e a conexão será encerrada. Modo de prevenção registra tais ataques nos logs do WAF.

### <a name="anomaly-scoring-mode"></a>Modo de pontuação de anomalias
 
OWASP tem dois modos para decidir se deseja bloquear o tráfego: Modo tradicional e modo de pontuação de anomalias.

No modo tradicional, o tráfego que corresponde a qualquer regra é considerado independentemente de quaisquer outras correspondências de regra. Esse modo é fácil de entender. Mas a falta de informações sobre quantas regras correspondem a uma solicitação específica é uma limitação. Portanto, o modo de pontuação de anomalias foi introduzido. É o padrão para 3 OWASP. *x*.

No modo de pontuação de anomalias, o tráfego que corresponde a qualquer regra não é bloqueado imediatamente quando o firewall está em modo de prevenção. As regras têm uma severidade específica: *Críticos*, *erro*, *aviso*, ou *aviso*. Essa severidade afeta um valor numérico para a solicitação, o que é chamado de pontuação de anomalias. Por exemplo, uma *aviso* regra de correspondência contribui 3 para a pontuação. Uma *crítico* regra de correspondência contribui 5.

Há um limite de 5 para a pontuação de anomalias para bloquear o tráfego. Portanto, um único *crítico* correspondência de regra é suficiente para o Gateway de aplicativo WAF bloquear uma solicitação, mesmo no modo de prevenção. Mas uma *aviso* regra de correspondência só aumenta a anomalia pontuação por 3, não é suficiente por si só para bloquear o tráfego.

> [!NOTE]
> A mensagem que é registrada quando o tráfego corresponde a uma regra WAF inclui o valor da ação "Bloqueado". Mas o tráfego é, na verdade, apenas bloqueado para uma pontuação de anomalias de 5 ou superior.  

### <a name="waf-monitoring"></a>Monitoramento de WAF

É importante monitorar a integridade de seu gateway de aplicativo. Monitorando a integridade do seu WAF e dos aplicativos que ele protege é suportado pela integração com a Central de segurança do Azure, Azure Monitor, e registra em log do Azure Monitor.

![Diagrama de diagnóstico de Gateway de aplicativo WAF](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Logs de Gateway de aplicativo são integrados com [do Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md). Isso permite que você acompanhe informações de diagnóstico, incluindo logs e alertas de WAF. Você pode acessar esse recurso nas **diagnóstico** guia no recurso do Gateway de aplicativo no portal ou diretamente por meio do Azure Monitor. Para saber mais sobre como habilitar os logs, consulte [diagnóstico do Gateway de aplicativo](application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Central de Segurança do Azure

[A Central de segurança](../security-center/security-center-intro.md) ajuda a impedir, detectar e responder a ameaças. Ele fornece maior visibilidade e controle sobre a segurança dos recursos do Azure. É o Gateway de aplicativo [integrado com a Central de segurança](application-gateway-integration-security-center.md). Central de segurança examinará seu ambiente para detectar aplicativos web desprotegidos. Ele pode recomendar o WAF do Gateway de aplicativo para proteger esses recursos vulneráveis. Você pode criar os firewalls diretamente da Central de segurança. Essas instâncias WAF são integradas com a Central de segurança. Eles enviarem alertas e informações de integridade para a Central de segurança para relatórios.

![Janela de visão geral da Central de segurança](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Registro em log

O WAF do Gateway de aplicativo fornece relatórios detalhados sobre cada ameaça detectado. Registro em log é integrado aos logs de diagnóstico do Azure. Alertas são registrados no formato. JSON. Esses logs podem ser integrados aos [logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![Logs de diagnóstico de Gateway de aplicativo windows](./media/waf-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Preços da SKU do WAF do Gateway de Aplicativo

O WAF do Gateway de aplicativo está disponível em um novo um SKU. Este SKU está disponível apenas no modelo de provisionamento do Azure Resource Manager, não no modelo de implantação clássico. Além disso, o SKU do WAF vem apenas em médios e grandes tamanhos de instância de Gateway de aplicativo. Todos os limites do Gateway de aplicativo também se aplicam ao SKU do WAF.

Preço é baseado em um encargo de instância de gateway por hora e uma taxa de processamento de dados. [Preços do Gateway de aplicativo](https://azure.microsoft.com/pricing/details/application-gateway/) para o SKU do WAF é diferente de encargos SKU padrão. Encargos de processamento de dados são os mesmos. Não há encargos por regra ou grupo de regras. Você pode proteger vários aplicativos web atrás do mesmo firewall de aplicativo web. Não é cobrado que dão suporte a vários aplicativos.

## <a name="next-steps"></a>Próximas etapas

Ver [como configurar o firewall do aplicativo web no Gateway de aplicativo](tutorial-restrict-web-traffic-powershell.md).
