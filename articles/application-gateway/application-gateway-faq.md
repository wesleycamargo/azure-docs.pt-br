---
title: Perguntas frequentes sobre o Gateway de Aplicativo do Azure
description: Esta página fornece respostas às perguntas frequentes sobre o Gateway de Aplicativo do Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/20/2019
ms.author: victorh
ms.openlocfilehash: f549f9c612797c1c956d6921fe4898a5f8bee9e6
ms.sourcegitcommit: 5e4ca656baf3c7d370ab3c0fbad0278aa2c9f1e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319407"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Perguntas frequentes sobre o Gateway de Aplicativo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>Geral

### <a name="what-is-application-gateway"></a>O que é o Gateway de Aplicativo?

O Gateway de Aplicativo do Azure é um ADC (Controlador de Entrega de Aplicativos) como serviço que oferece diversas funcionalidades de balanceamento de carga da camada 7 para seus aplicativos. Ele oferece um serviço altamente disponível e escalonável e totalmente gerenciado pelo Azure.

### <a name="what-features-does-application-gateway-support"></a>Quais recursos o Gateway de Aplicativo suporta?

O Gateway de Aplicativo dá suporte para dimensionamento automático, descarregamento de SSL e SSL de ponta a ponta, firewall do aplicativo Web, afinidade de sessão baseada em cookies, roteamento baseado em caminho de URL, hospedagem em vários sites e outros. Para obter uma lista completa dos recursos com suporte, confira [Introdução ao Gateway de Aplicativo](application-gateway-introduction.md).

### <a name="what-is-the-difference-between-application-gateway-and-azure-load-balancer"></a>O que é a diferença entre o Gateway de aplicativo e balanceador de carga do Azure?

Gateway de aplicativo é um balanceador de carga de camada 7, que significa que ele funciona com apenas tráfego da web (HTTP/HTTPS/WebSocket/HTTP/2). Ele oferece suporte a recursos como terminação SSL, a afinidade de sessão baseada em cookie e round robin para tráfego de balanceamento de carga. O Load Balancer balanceia o tráfego na camada 4 (TCP/UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quais protocolos o Gateway de Aplicativo suporta?

O Gateway de Aplicativo fornece suporte HTTP, HTTPS, HTTP/2 e WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Como o Gateway de aplicativo dá suporte a HTTP/2?

Ver [suporte HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support) para saber como o gateway de aplicativo dá suporte ao protocolo HTTP/2.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Quais recursos têm suporte atualmente como parte do pool de back-end?

Ver [suporte para recursos de back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool) para saber quais recursos têm suporte pelo gateway de aplicativo.

### <a name="what-regions-is-the-service-available-in"></a>Em quais regiões o serviço está disponível?

O Gateway de Aplicativo está disponível em todas as regiões do Azure global. Ele também está disponível no [Azure China 21Vianet](https://www.azure.cn/) e no [Azure Governamental](https://azure.microsoft.com/overview/clouds/government/)

### <a name="is-this-a-dedicated-deployment-for-my-subscription-or-is-it-shared-across-customers"></a>Esta é uma implantação dedicada para minha assinatura ou é compartilhada entre os clientes?

O Gateway de Aplicativo é uma implementação dedicada em sua rede virtual.

### <a name="is-http-https-redirection-supported"></a>O redirecionamento HTTP-> HTTPS é suportado?

Há suporte para redirecionamento. Confira [Visão geral do redirecionamento do Gateway de Aplicativo](application-gateway-redirect-overview.md) para saber mais.

### <a name="in-what-order-are-listeners-processed"></a>Em que ordem os ouvintes são processados?

Ver [ordem de processamento de ouvintes de](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-application-gateways-ip-and-dns"></a>Onde encontro o IP e o DNS do Application Gateway?

Ao usar um endereço IP público como um ponto de extremidade, encontre essas informações no recurso do endereço IP público ou na página Visão geral do Gateway de Aplicativo no portal. Para endereços IP internos, isso pode ser encontrado na página Visão geral.

### <a name="what-is-keep-alive-timeout-and-tcp-idle-timeout-setting-on-application-gateway"></a>O que é o tempo limite de Keep-Alive e configuração de tempo limite de ociosidade de TCP no Gateway de aplicativo?

Tempo limite Keep-Alive no SKU v1 é 120 s Keep-Alive timeout no SKU v2 é 75 SEC. tempo limite de ociosidade de TCP é o padrão de 4 minutos em que o VIP do Gateway de aplicativo de front-end.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>O nome IP ou DNS muda durante a vida útil do Gateway de Aplicativo?

O VIP poderá ser alterado se o Gateway de Aplicativo for interrompido e iniciado. O nome DNS associado ao Gateway de Aplicativo não é alterado ao longo do ciclo de vida do gateway. Por esse motivo, é recomendado usar um alias do CNAME e apontá-lo para o endereço DNS do Gateway de Aplicativo.

### <a name="does-application-gateway-support-static-ip"></a>O Gateway de Aplicativo suporta IP estático?

Sim, o SKU do Application Gateway v2 suporta endereços IP públicos estáticos. O v1 SKU suporta IPs internos estáticos.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>O Gateway de Aplicativo suporta vários IPs públicos no gateway?

Há suporte apenas para um único endereço IP público em um Gateway de Aplicativo.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Quão grande devo criar minha sub-rede para o Application Gateway?

Ver [considerações de tamanho de sub-rede de Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet) para entender o tamanho de sub-rede necessário para sua implantação.

### <a name="q-can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>P. Posso implantar mais de um recurso de Gateway de Aplicativo em uma única sub-rede?

Sim, além de ter várias instâncias de uma determinada implantação do Application Gateway, você pode provisionar outro recurso exclusivo do Application Gateway para uma sub-rede existente que contém um recurso diferente do Application Gateway.

Combinar Standard_v2 e o Gateway de Aplicativo Standard na mesma sub-rede não tem suporte.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>O Gateway de aplicativo dá suporte a cabeçalhos x-forwarded-for?

Sim. Ver [modificações para solicitar](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request) para saber mais sobre os cabeçalhos x-forwarded-for com suporte pelo Gateway de aplicativo.

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-does-my-application-gateway-still-work-when-being-updated"></a>Quanto tempo demora para implantar um Gateway de Aplicativo? O meu Application Gateway ainda funciona quando está sendo atualizado?

As novas implantações de SKU do Application Gateway v1 podem levar até 20 minutos para provisionar. Alterações de tamanho/contagem de instâncias não são interrompidas, e o gateway permanece ativo durante esse tempo.

As implantações do SKU v2 podem levar cerca de cinco a seis minutos para serem provisionadas.

### <a name="can-exchange-server-be-used-as-backend-with-application-gateway"></a>Exchange server pode ser usado como back-end com o Gateway de aplicativo?

Não, o Gateway de aplicativo não oferece suporte a protocolos de email como SMTP, IMAP e POP3. 

## <a name="performance"></a>Desempenho

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Como o Application Gateway suporta alta disponibilidade e escalabilidade?

O SKU do Application Gateway v1 oferece suporte a cenários de alta disponibilidade quando você tem duas ou mais instâncias implantadas. O Azure distribui essas instâncias entre domínios de atualização e de falha para garantir que todas as instâncias não falham ao mesmo tempo. O v1 SKU suporta escalabilidade adicionando várias instâncias do mesmo gateway para compartilhar a carga.

A SKU v2 garante automaticamente que novas instâncias sejam distribuídas entre domínios de falha e domínios de atualização. Se a redundância de zona for escolhida, as instâncias mais recentes também serão distribuídas entre as zonas de disponibilidade para oferecer resiliência zonal em caso de falha.

### <a name="how-do-i-achieve-dr-scenario-across-data-centers-with-application-gateway"></a>Como obter o cenário de recuperação de desastres em data centers com o Gateway de aplicativo?

Os clientes podem usar o Gerenciador de Tráfego para distribuir o tráfego entre vários Gateways de Aplicativo em data centers diferentes.

### <a name="is-autoscaling-supported"></a>O auto escalonamento é suportado?

Sim, o SKU Application Gateway v2 suporta escalonamento automático. Para obter mais informações, confira [Dimensionamento automático e o Gateway de Aplicativo com redundância de zona (versão prévia pública)](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-updown-cause-downtime"></a>A escala manual aumenta/diminui o tempo de inatividade?

Não há tempo de inatividade. As instâncias são distribuídas entre domínios de atualização e domínios de falha.

### <a name="does-application-gateway-support-connection-draining"></a>O Gateway de Aplicativo suporta a drenagem de conexão?

Sim. Você pode configurar o descarregamento de conexão para alterar os membros dentro de um pool de back-end sem interrupções. Isso permite que as conexões existentes continuem sendo enviadas ao seu destino anterior até essa conexão ser fechada ou um tempo limite configurável expirar. A drenagem de conexão apenas aguarda a conclusão das conexões atuais em andamento. O Gateway de Aplicativo não está ciente do estado de sessão do aplicativo.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Posso alterar o tamanho da instância de médio para grande sem interrupção?

Sim, o Azure distribui essas instâncias entre domínios de atualização e de falha para garantir que todas as instâncias não falham ao mesmo tempo. O Gateway de Aplicativo dá suporte à escalabilidade adicionando várias instâncias do mesmo gateway para compartilhar a carga.

## <a name="configuration"></a>Configuração

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>O Application Gateway está sempre implantado em uma rede virtual?

Sim, o Gateway de Aplicativo é sempre implantado em uma sub-rede de rede virtual. Essa sub-rede só pode conter Gateways de Aplicativos. Ver [requisitos de rede e sub-rede virtual](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet) para entender as considerações de sub-rede do Gateway de aplicativo.

### <a name="can-application-gateway-communicate-with-instances-outside-of-the-virtual-network-it-is-in-or-outside-of-the-subscription-it-is-in"></a>O Gateway de aplicativo pode se comunicar com instâncias fora da rede virtual está em ou fora da assinatura está em?

O Gateway de aplicativo pode se comunicar com instâncias fora da rede virtual que está na ou fora da assinatura é, desde que exista conectividade IP. Se você planeja usar IPs internos como membros de pool de back-end, será necessário usar o [Emparelhamento VNET](../virtual-network/virtual-network-peering-overview.md) ou o [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Posso implantar coisa na sub-rede do gateway de aplicativo?

Não, mas você pode implantar outros gateways de aplicativo na sub-rede.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Os grupos de segurança de rede são suportados na sub-rede do gateway de aplicativos?

Ver [restrições de grupos de segurança de rede na sub-rede do Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-supported-on-the-application-gateway-subnet) Saiba mais sobre os grupos de segurança de rede com suporte na sub-rede do gateway de aplicativo.

### <a name="are-user-defined-routes-supported-on-the-application-gateway-subnet"></a>As rotas definidas pelo usuário são suportadas na sub-rede do gateway de aplicativo?

Ver [restrições de rotas definidas pelo usuário](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet) para saber mais sobre as rotas definidas pelo usuário com suporte na sub-rede do gateway de aplicativo.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quais são os limites no Gateway de Aplicativo? Posso aumentar esses limites?

Confira [Limites do Gateway de Aplicativo](../azure-subscription-service-limits.md#application-gateway-limits) para saber quais são os limites.

### <a name="can-i-use-application-gateway-for-both-external-and-internal-traffic-simultaneously"></a>Posso usar o Gateway de Aplicativo para tráfego externo e interno simultaneamente?

Sim, o Gateway de Aplicativo dá suporte a um endereço IP interno e a um IP externo por Gateway de Aplicativo.

### <a name="is-vnet-peering-supported"></a>É o emparelhamento VNet tem suporte?

Sim, o emparelhamento VNet tem suporte e é útil para o balanceamento de carga de tráfego em outras redes virtuais.

### <a name="can-i-talk-to-on-premises-servers-when-they-are-connected-by-expressroute-or-vpn-tunnels"></a>Posso falar com servidores locais quando eles estão conectados por túneis de rota expressa ou VPN?

Sim, contanto que o tráfego seja permitido.

### <a name="can-i-have-one-backend-pool-serving-many-applications-on-different-ports"></a>Posso ter um pool de back-end que atende muitos aplicativos em portas diferentes?

Há suporte para arquitetura de microsserviço. Você precisaria de várias configurações http definidas para investigação em portas diferentes.

### <a name="do-custom-probes-support-wildcardsregex-on-response-data"></a>Os testes personalizados suportam curingas / regex nos dados de resposta?

As investigações personalizadas não têm suporte para curingas/regex nos dados de resposta.

### <a name="how-are-rules-processed"></a>Como as regras são processadas?

Ver [ordem das regras de processamento](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules) para entender como regras de roteamento são processos no Gateway de aplicativo.

### <a name="what-does-the-host-field-for-custom-probes-signify"></a>O que significa o campo Host para investigações personalizadas?

O campo Host especifica o nome ao qual enviar a investigação. Aplicável somente quando vários sites são configurados no Gateway de Aplicativo; do contrário, use '127.0.0.1'. Esse valor é diferente do nome de host da VM e está no formato \<protocolo\>://\<host\>:\<porta\>\<caminho\>.

### <a name="can-i-whitelist-application-gateway-access-to-a-few-source-ips"></a>Posso colocar na lista de permissões o acesso do Application Gateway a alguns IPs de origem?

Sim. Ver [restringir o acesso a IPs de origem específico](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips) para entender como garantir que apenas na lista de permissões IPs de origem pode acessar o Gateway de aplicativo.

### <a name="can-the-same-port-be-used-for-both-public-and-private-facing-listeners"></a>A mesma porta pode ser usada tanto para ouvintes voltados para o público quanto para o privado?

Não, isso não tem suporte.

## <a name="configuration---ssl"></a>Configuração - SSL

### <a name="what-certificates-are-supported-on-application-gateway"></a>Quais certificados são suportados no Application Gateway?

Há suporte para certificados autoassinados, certificados de autoridade de certificação, certificados EV e certificados curinga.

### <a name="what-are-the-current-cipher-suites-supported-by-application-gateway"></a>Quais são os conjuntos de criptografia atuais suportados pelo Application Gateway?

Estes são os conjuntos de criptografia atuais com suporte no Gateway de Aplicativo. Confira [Configurar versões de política SSL e conjuntos de codificação no Gateway de Aplicativo](application-gateway-configure-ssl-policy-powershell.md) para aprender a personalizar as opções de SSL.

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

### <a name="does-application-gateway-also-support-re-encryption-of-traffic-to-the-backend"></a>O Application Gateway também suporta a re-criptografia do tráfego para o back-end?

Sim, o Gateway de Aplicativo oferece suporte ao descarregamento SSL, SSL de ponta a ponta, que criptografa novamente o tráfego para o back-end.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Posso configurar a política SSL para controlar as versões do Protocolo SSL?

Sim, você pode configurar o Gateway de Aplicativo para negar TLS1.0, TLS1.1 e TLS1.2. SSL 2.0 e 3.0 já estão desabilitados por padrão e não são configuráveis.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Posso configurar conjuntos de criptografia e ordem de política?

Sim, há suporte para [configuração de conjuntos de criptografia](application-gateway-ssl-policy-overview.md). Ao definir uma política personalizada, pelo menos um dos seguintes pacotes de codificação deve ser habilitado. O Gateway de Aplicativo usa SHA256 para gerenciamento de back-end.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

### <a name="how-many-ssl-certificates-are-supported"></a>Quantos certificados SSL são suportados?

Há suporte para até 100 certificados SSL.

### <a name="how-many-authentication-certificates-for-backend-re-encryption-are-supported"></a>Quantos certificados de autenticação para re-criptografia de back-end são suportados?

Há suporte para até 10 certificados de autenticação, com um padrão de cinco.

### <a name="does-application-gateway-integrate-with-azure-key-vault-natively"></a>O Gateway de aplicativo se integra ao Azure Key Vault nativamente?

Não, ele não é integrado ao Azure Key Vault.

### <a name="how-to-configure-https-listeners-for-com-and-net-sites"></a>Como configurar os ouvintes HTTPS para sites. com e .net? 

Para vários baseado em domínio (baseado em host) roteamento, você pode criar ouvintes multissite, escolha HTTPS como o protocolo na configuração de ouvinte e associar os ouvintes de com as regras de roteamento. Para obter mais detalhes, consulte [hospedar vários sites com o Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Configuração - Firewall do aplicativo Web (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-with-the-standard-sku"></a>O WAU SKU oferece todos os recursos disponíveis com o SKU padrão?

Sim, o WAF oferece suporte a todos os recursos no SKU Standard.

### <a name="what-is-the-crs-version-application-gateway-supports"></a>Qual é a versão do CRS que o Application Gateway suporta?

O Gateway de Aplicativo dá suporte a CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e a CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Como monitorar o WAF?

O WAF é monitorado por meio do log de diagnóstico. Encontre mais informações sobre o log de diagnóstico em [Log de diagnósticos e métricas para o Gateway de Aplicativo](application-gateway-diagnostics.md)

### <a name="does-detection-mode-block-traffic"></a>Modo de detecção bloqueia o tráfego?

Não, o modo de detecção apenas registra o tráfego em log, o que dispara uma regra WAF.

### <a name="can-i-customize-waf-rules"></a>Como posso personalizar regras WAF?

Sim, as regras WAF são personalizáveis. Para saber mais, veja [Personalizar regras e grupos de regras de WAF](application-gateway-customize-waf-rules-portal.md)

### <a name="what-rules-are-currently-available"></a>Quais regras estão disponíveis no momento

No momento, o WAF oferece suporte a CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), que fornece segurança de linha de base contra a maioria das dez principais vulnerabilidades identificadas pelo OWASP (Open Web Application Security Project) encontradas aqui [Dez principais vulnerabilidades do OWASP](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Proteção contra injeção de SQL

* Proteção contra scripts entre sites

* Proteção Contra Ataques Comuns da Web, como a injeção de comandos, as solicitações HTTP indesejadas, a divisão de resposta HTTP e o ataque de inclusão de arquivo remoto

* Proteção contra violações de protocolo HTTP

* Proteção contra anomalias de protocolo HTTP, como ausência de host de agente do usuário e de cabeçalhos de aceitação

* Prevenção contra bots, rastreadores e scanners

* Detecção de problemas de configuração de aplicativo comuns (ou seja, Apache, IIS etc.)

### <a name="does-waf-also-support-ddos-prevention"></a>O WAF também oferece suporte à prevenção de DDoS?

Sim. Você pode habilitar a Proteção contra DDoS na rede virtual em que o gateway de aplicativo é implantado. Isso garante que o VIP do gateway de aplicativo também seja protegido usando o serviço de Proteção contra DDoS do Azure.

## <a name="diagnostics-and-logging"></a>Diagnóstico e registro em log

### <a name="what-types-of-logs-are-available-with-application-gateway"></a>Quais tipos de logs estão disponíveis com o Application Gateway?

Há três logs disponíveis para o Gateway de Aplicativo. Para saber mais sobre esses logs e outros recursos de diagnóstico, confira [Integridade de back-end, logs de diagnóstico e métricas do Gateway de Aplicativo](application-gateway-diagnostics.md).

* **ApplicationGatewayAccessLog** – o log de acesso contém cada solicitação enviada ao front-end do Gateway de Aplicativo. Os dados incluem o IP do chamador, a URL solicitada, latência da resposta, o código de retorno, bytes de entrada e saída. O log de acesso é coletado a cada 300 segundos. Esse log contém um registro por instância de um Gateway de Aplicativo.
* **ApplicationGatewayPerformanceLog** - O log de desempenho captura informações sobre o desempenho por instância, incluindo a solicitação total atendida, a taxa de transferência em bytes, o total de solicitações atendidas, a contagem de solicitações com falha, a contagem de instâncias de back-end íntegras ou não.
* **ApplicationGatewayFirewallLog** - O log de firewall contém solicitações registradas por meio do modo de detecção ou prevenção de um gateway de aplicativo configurado com o firewall do aplicativo Web.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Como sei se meus membros do pool de back-end são saudáveis?

Use o cmdlet do PowerShell `Get-AzApplicationGatewayBackendHealth` ou verifique a integridade por meio do portal visitando [Diagnóstico do Gateway de Aplicativo](application-gateway-diagnostics.md)

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>O que é a política de retenção nos logs de diagnóstico?

Os logs de diagnóstico fluem para a conta de armazenamento de clientes, e os clientes podem definir a política de retenção com base em suas preferências. Os logs de diagnóstico também podem ser enviados para o Hub de Eventos ou os logs do Azure Monitor. Confira [Diagnóstico do Gateway de Aplicativo](application-gateway-diagnostics.md) para obter mais detalhes.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Como posso obter os logs de auditoria para o Gateway de aplicativo?

Os logs de auditoria estão disponíveis para o Gateway de Aplicativo. No portal, clique em **Log de atividades** na folha do menu de um gateway de aplicativo para acessar o log de auditoria. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Configurar alertas com o Gateway de aplicativo?

Sim, o Gateway de Aplicativo dá suporte a alertas. Os alertas são configurados nas métricas. Confira [Métricas do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) para saber mais sobre as métricas do Gateway de Aplicativo. Para saber mais sobre alertas, confira [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Como faço para analisar as estatísticas de tráfego do Application Gateway?

É possível exibir e analisar logs do Access por meio de vários mecanismos, como logs do Azure Monitor, Excel, Power BI, etc.

Publicamos também um modelo do Resource Manager que instala e executa o popular analisador de logs [GoAccess](https://goaccess.io/) para logs de acesso do Gateway de Aplicativo. O GoAccess fornece valiosas estatísticas de tráfego HTTP, tais como visitantes exclusivos, arquivos solicitados, hosts, sistemas operacionais, navegadores, códigos de status HTTP e muito mais. Para obter mais detalhes, consulte o [arquivo Leiame na pasta de modelo do Resource Manager no GitHub](https://aka.ms/appgwgoaccessreadme).

### <a name="backend-health-returns-unknown-status-what-could-be-causing-this-status"></a>A integridade do back-end retorna status desconhecido, o que pode estar causando esse status?

O motivo mais comum é o acesso para o back-end está bloqueado por um NSG, o DNS personalizado, ou você tem uma UDR na sub-rede do gateway de aplicativo. Confira [Integridade do back-end, registro em log de diagnóstico e métricas do Gateway de Aplicativo](application-gateway-diagnostics.md) para saber mais.

## <a name="next-steps"></a>Próximas Etapas

Para saber mais sobre o Gateway de Aplicativo, confira [O que é o Gateway de Aplicativo do Azure?](overview.md)
