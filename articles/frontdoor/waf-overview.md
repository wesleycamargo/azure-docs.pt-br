---
title: O que é o firewall do aplicativo web do Azure para frente do Azure? (Visualização)
description: Saiba como o Azure firewall do aplicativo web para o serviço de porta da frente do Azure protege seus aplicativos web contra ataques mal-intencionados.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: 17cf6629aca6c73bc96e4cf0c172a2e87a7aafb8
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58910069"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door-preview"></a>O que é o firewall do aplicativo web do Azure para frente do Azure? (Visualização)

Firewall do aplicativo web do Azure (WAF) (versão prévia) fornece proteção centralizada de seus aplicativos web que são entregues globalmente usando a porta de entrada do Azure. Ele é projetado e operado em defender seus serviços web contra vulnerabilidades e explorações comuns e manter o serviço altamente disponível para seus usuários, além de ajudar você a atender aos requisitos de conformidade.

> [!IMPORTANT]
> O firewall do aplicativo web do Azure (WAF) de frente para o Azure está atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aplicativos Web são cada vez mais os alvos de ataques mal-intencionados, como negação de ataques de script entre sites, ataques de injeção de SQL e inundações de serviço. Esses ataques mal-intencionados podem causar perda de dados e de interrupção de serviço, representem uma ameaça significativa para os proprietários do aplicativo web.

Pode ser difícil impedir esses ataques no código do aplicativo e pode exigir manutenção rigorosa, aplicação de patches e monitoramento em várias camadas da topologia do aplicativo. Um firewall de aplicativo Web centralizado ajuda a simplificar bastante o gerenciamento de segurança e oferece mais garantia ao administrador do aplicativo contra ameaças ou invasões. Além disso, uma solução WAF pode reagir a ameaças de segurança mais rapidamente ao aplicar um patch uma vulnerabilidade conhecida em um local central, em vez de proteção de cada um dos aplicativos web individuais.

WAF para frente é uma solução global e centralizada. Ele é implantado em locais de borda de rede do Azure em todo o mundo e cada solicitação de entrada para um aplicativo web do WAF habilitado fornecido pela porta da frente é inspecionada na borda da rede. Isso permite que o WAF impedir ataques mal-intencionados perto de fontes de ataque, antes de entrar na rede virtual e oferece proteção global em escala sem sacrificar o desempenho. Uma política de WAF pode ser facilmente vinculada a qualquer perfil de porta de entrada em sua assinatura e novas regras podem ser implantadas em minutos, o que permite responder rapidamente a padrões de ameaças em constante mudança.

![Firewall do aplicativo web do Azure](./media/waf-overview/web-application-firewall-overview.png)

O WAF do Azure também pode ser habilitado com o Gateway de aplicativo. Para obter mais informações, consulte [firewall do aplicativo Web](../application-gateway/waf-overview.md).

## <a name="waf-policy-and-rules"></a>Regras e políticas de WAF

Você pode configurar uma política de WAF e associar a essa política para um ou mais da frente front-ends para proteção. Uma política de WAF consiste em dois tipos de regras de segurança:
- regras personalizadas que são criadas pelo cliente.
- conjuntos de regras gerenciados que são uma coleção de gerenciado do Azure previamente configurado o conjunto de regras. Quando ambos estiverem presentes, regras personalizadas são executadas antes de executar regras em um conjunto de regras gerenciado. Uma regra é composta por uma condição de correspondência, uma prioridade e uma ação. Tipos de ação com suporte são: PERMITIR, bloquear, LOG e REDIRECIONAMENTO. Você pode criar uma política totalmente personalizada que atenda às suas necessidades de proteção de aplicativo específico combinando as regras personalizadas e não gerenciados.

Regras dentro de uma política são executadas uma ordem de prioridade em que a prioridade é um inteiro exclusivo que define a ordem de execução da regra. Valor de inteiro menor denota uma prioridade mais alta e aqueles são avaliadas antes das regras com um valor inteiro mais alto. Depois que uma regra é correspondida, a ação correspondente que foi definida na regra é aplicada à solicitação. Depois que essa correspondência é processada, as regras com prioridades inferiores não são processadas ainda mais.

Um aplicativo web fornecido pela porta de entrada pode ter apenas uma política de WAF associada a ele por vez. No entanto, você pode ter uma configuração de porta da frente sem quaisquer políticas de WAF associadas a ele. Se uma política de WAF estiver presente, ele é replicado para todos os nossos locais de borda para garantir a consistência nas políticas de segurança em todo o mundo.

## <a name="waf-modes"></a>Modos de WAF

Política de WAF pode ser configurada para ser executado em dois modos a seguir:

- **Modo de detecção:** Quando executado no modo de detecção, o WAF não faz quaisquer outras ações que não seja de monitores e registra a solicitação e sua regra WAF correspondente aos logs de WAF. Você pode ativar o log de diagnóstico para frente (ao usar o portal, isso pode ser conseguido indo para o **diagnóstico** seção no portal do Azure).

- **Modo de prevenção:** Quando configurado para ser executado no modo de prevenção, o WAF usa a ação especificada se uma solicitação corresponde a uma regra e se uma correspondência for encontrada, nenhuma regra adicional com prioridade mais baixa é avaliada. Todas as solicitações correspondentes também são registradas nos logs de WAF.

## <a name="waf-actions"></a>Ações de WAF

Os clientes do WAF podem optar por executar uma das ações quando uma solicitação corresponder às condições de uma regra:
- **Permita:**  Solicitação passa por WAF e é encaminhada para o back-end. Nenhuma outra regras de prioridade mais baixos podem bloquear essa solicitação.
- **Bloco:** A solicitação será bloqueada e WAF envia uma resposta ao cliente sem encaminhar a solicitação para o back-end.
- **Log:**  Solicitação é registrada nos logs de WAF e WAF continua a avaliar as regras de prioridade mais baixos.
- **Redirecione:** WAF redireciona a solicitação para o URI especificado. O URI especificado é uma configuração de nível de política. Uma vez configurado, todas as solicitações que correspondem a **redirecionar** ação será enviada para esse URI.


## <a name="waf-rules"></a>Regras de WAF

Uma política de WAF pode consistir em dois tipos de regras de segurança - regras personalizadas, criados por clientes e gerenciado rulesets, gerenciados do Azure previamente configurado o conjunto de regras.

### <a name="custom-authored-rules"></a>Regras criadas personalizadas
Você pode configurar regras personalizadas WAF da seguinte maneira:

- **Permissões de IP lista e a lista de bloqueios:** Você pode configurar regras personalizadas para controlar o acesso aos seus aplicativos web com base em uma lista de endereços IP do cliente ou intervalos de endereços IP. Há suporte para tipos de endereço IPv4 e IPv6. Essa lista pode ser configurada para bloquear ou permitir que essas solicitações onde o IP de origem corresponde a um IP na lista.

- **Controle de acesso com base geográfica:** Você pode configurar regras personalizadas para controlar o acesso aos seus aplicativos web com base no código de país associado ao endereço IP do cliente.

- **Controle de acesso com base em parâmetros HTTP:** Você pode configurar regras personalizadas com base em parâmetros de solicitação HTTP/HTTPS, como cadeias de caracteres de consulta, POSTAGEM args, URI de solicitação, o cabeçalho de solicitação e corpo da solicitação de correspondência de cadeia de caracteres.

- **Controle de acesso com base no método da solicitação:** Você pode configurar regras personalizadas com base no método de solicitação HTTP da solicitação como GET, PUT ou HEAD.

- **Restrição de tamanho:** Você pode configurar regras personalizadas com base em comprimentos de partes específicas de uma solicitação, como a cadeia de caracteres de consulta, Uri, ou o corpo da solicitação.

- **Limitação da taxa de regras:** Uma regra de controle de taxa tem a finalidade de limitar tráfego anormalmente alto proveniente de qualquer IP de cliente. Você pode configurar um limite no número de solicitações da web permitidas de um IP de cliente durante uma duração de um minuto. Isso é diferente de uma regra de personalizada baseada em lista permitir/bloquear IP que permita que todos ou blocos de todas as solicitações de um IP do cliente. A limitação de taxa pode ser combinada com condições de correspondência adicionais, como parâmetros HTTP (S) correspondente para o controle granular de taxa.


### <a name="azure-managed-rule-sets"></a>Conjuntos de regras gerenciados do Azure

Conjuntos de regras gerenciados do Azure fornecem uma maneira fácil de implantar a proteção contra um conjunto comum de ameaças à segurança. Uma vez que esses conjuntos de regras são gerenciados pelo Azure, as regras são atualizadas conforme necessário para proteger contra novas assinaturas de ataques. Na visualização pública, gerenciados do Azure padrão conjunto de regras inclui regras em relação as seguintes categorias de ameaça:

- Scripts entre sites
- Ataques de Java
- Inclusão de arquivo local
- Ataques de injeção de PHP
- Execução de comando remoto
- Inclusão de arquivo remoto
- Fixação da sessão
- Proteção contra injeção de SQL

O número de versão do conjunto de regra padrão será incrementado quando novas assinaturas de ataques são adicionadas ao conjunto de regras.
Conjunto de regras padrão é habilitado por padrão no modo de detecção em suas políticas de WAF. Você pode desabilitar ou habilitar regras individuais dentro do padrão conjunto de regras para atender aos requisitos de aplicativo. Você também pode definir a ações específicas (permitir/bloquear/REDIRECIONAMENTO/LOG) por regra. Ação padrão é bloquear. Além disso, as regras personalizadas podem ser configuradas na mesma política WAF se você quiser ignorar qualquer uma das regras pré-configuradas em que o conjunto de regra padrão.
Regras personalizadas são sempre aplicadas antes que as regras em que o conjunto de regras padrão são avaliadas. Se uma solicitação corresponde a uma regra personalizada, ação de regra correspondente é aplicada, e a solicitação está bloqueada ou passada para o back-end, sem a invocação de todas as regras personalizadas adicionais ou as regras em que o conjunto de regra padrão. Além disso, você terá a opção de remover o conjunto de regra padrão de suas políticas de WAF.


## <a name="configuration"></a>Configuração
Durante a visualização pública:
- Configurando e implantando a todos os tipos de regra do WAF é totalmente suportado usando APIs REST, modelos do Azure Resource Manager e do Azure PowerShell.
- Usando o portal do Azure, você pode configurar ou exibir apenas gerenciado do Azure padrão conjunto de regras.

## <a name="monitoring"></a>Monitoramento

Monitoramento de WAF na frente é integrado com o Azure Monitor para controlar os alertas e monitorar facilmente as tendências de tráfego.

## <a name="pricing"></a>Preços

Durante a visualização pública, qualquer uso associado WAF para frente é gratuito e não será cobrado.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).

