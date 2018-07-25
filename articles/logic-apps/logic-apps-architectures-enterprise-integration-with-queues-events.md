---
title: Arquitetura de referência de integração empresarial do Integration Services do Azure
description: Descreve a arquitetura de referência mostrando como implementar um padrão de integração empresarial com Aplicativos Lógicos, Gerenciamento de API, Barramento de Serviço e Grade de Eventos.
author: mattfarm
manager: jonfan
editor: ''
services: logic-apps api-management
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/15/2018
ms.author: LADocs; estfan
ms.openlocfilehash: a86c4c4227795a712dd51ace1fbefe9d2b96518a
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116105"
---
# <a name="reference-architecture-enterprise-integration-with-queues-and-events"></a>Arquitetura de referência: integração empresarial com filas e eventos

Essa arquitetura de referência mostra um conjunto de práticas comprovadas que você pode aplicar a um aplicativo de integração que usa o Integration Services do Azure. A arquitetura pode servir de base para muitos padrões diferentes de aplicativos e APIs HTTP, fluxo de trabalho e orquestração.

![Diagrama de arquitetura - integração empresarial com filas e eventos](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

*Há muitos aplicativos possíveis para a tecnologia de integração. Variam de um aplicativo ponto a ponto simples a uma empresa completo de aplicativo do barramento de serviço do Microsoft Azure. A série de arquitetura descreve as partes de componente reutilizável que pode ser aplicada para criar um aplicativo de integração genérico. Arquitetos devem considerar quais componentes precisam implementar para seus aplicativos e infraestrutura.*

## <a name="architecture"></a>Arquitetura

Essa arquitetura baseia-se *na* [arquitetura de integração](logic-apps-architectures-simple-enterprise-integration.md) empresarial. [Recomendações para arquitetura corporativa simples](logic-apps-architectures-simple-enterprise-integration.md#recommendations) se aplicam aqui. São omitidos das [recomendações](#recommendations) neste artigo para fins de brevidade. 

A arquitetura tem os seguintes componentes:

- **Grupo de recursos**. Um [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) é um contêiner lógico para recursos do Azure.
- **Gerenciamento de API do Azure**. [Gerenciamento de API](https://docs.microsoft.com/azure/api-management/) é uma plataforma totalmente gerenciada que é usada para publicar, proteger e transformar APIs HTTP.
- **Portal do Desenvolvedor de Gerenciamento de API do Azure**. Cada instância do gerenciamento de API do Azure vem com acesso para o [Portal do desenvolvedor](https://docs.microsoft.com/azure/api-management/api-management-customize-styles). O portal do desenvolvedor do Gerenciamento de API fornece acesso a documentação e exemplos de códigos. Você pode testar as APIs no portal do desenvolvedor.
- **Aplicativo Lógico do Azure**. Os [Aplicativos Lógicos](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) são uma plataforma sem servidor que é usada para compilar integração e fluxo de trabalho empresarial.
- **Conectores**. Os [conectores](https://docs.microsoft.com/azure/connectors/apis-list) são usados pelos Aplicativos Lógicos para conectarem os serviços normalmente utilizados. Os Aplicativos Lógicos já possuem centenas de conectores diferentes, mas também podem ser criados usando um conector personalizado.
- **Barramento de Serviço do Azure**. O [Barramento de Serviço](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) fornece mensagens seguras e confiáveis. As mensagens podem ser usadas para separar os aplicativos uns dos outros e integrar com outros sistemas baseados em mensagens.
- **Grade de Eventos do Azure**. A [Grade de Eventos](https://docs.microsoft.com/azure/event-grid/overview) é uma plataforma sem servidor para publicar e entregar eventos de aplicativos.
- **Endereço IP**. O serviço de Gerenciamento de API do Azure tem um nome de domínio e um [endereço IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) público fixo. O nome de domínio é um subdomínio do azure-api.net, como contoso.azure-api.net. Aplicativos lógicos e o barramento de serviço também têm um endereço IP público. No entanto, nessa arquitetura restringimos o acesso para chamar pontos de extremidade de aplicativos lógicos apenas para o endereço IP do Gerenciamento de API (por segurança). As chamadas para o Barramento de Serviço são protegidas por uma assinatura de acesso compartilhado (SAS).
- **DNS do Azure**. [O DNS do Azure](https://docs.microsoft.com/azure/dns/) é um serviço de hospedagem para domínios DNS. O DNS do Azure fornece resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Microsoft Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. Para usar um nome de domínio personalizado como contoso.com, crie registros DNS que mapeiem o nome de domínio personalizado para o endereço IP. Para obter mais informações, consulte [Configurar um nome de domínio personalizado no Gerenciamento de API](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain).
- **Azure Active Directory (Azure AD)**. Use o [Azure AD](https://docs.microsoft.com/azure/active-directory/) ou outro provedor de identidade para autenticação. O Azure Active Directory fornece autenticação para acessar os pontos de extremidade de API, passando um [JSON Web Token para o gerenciamento de API](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) para validar. O Azure Active Directory pode proteger o acesso ao portal do desenvolvedor do gerenciamento de API (somente em camadas Standard e Premium).

Esta arquitetura possui alguns padrões que são fundamentais para seu funcionamento:

* As APIs HTTP de back-end existentes por meio do Portal do Desenvolvedor de Gerenciamento de API. No portal, os desenvolvedores (ambos internos de sua organização, externo ou ambos) podem integrar as chamadas para essas APIs em aplicativos.
* APIs compostas são compiladas usando aplicativos lógicos, orquestrando chamadas para sistemas de software como serviço (SaaS), serviços do Azure e quaisquer APIs publicadas no Gerenciamento de API. [Os Aplicativos Lógicos também são publicados](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) por meio do Portal do Desenvolvedor de Gerenciamento de API.
- Aplicativos usam o Azure AD para [adquirir um token de segurança do OAuth 2.0](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) que é necessário para obter acesso a uma API.
- O Gerenciamento de API [valida o token](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) de segurança e passa a solicitação ao aplicativo lógico ou API de back-end.
- As filas do Barramento de Serviço são usadas para [separar](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) a atividade do aplicativo e [suavizar os picos de carga](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). As mensagens são adicionadas às filas por Aplicativos Lógicos, aplicativos de terceiros ou (não ilustrados), publicando a fila como uma API HTTP por meio do Gerenciamento de API.
- Quando mensagens são adicionadas a uma fila do Barramento de Serviço, um evento é acionado. Um aplicativo lógico é disparado pelo evento. O aplicativo lógico, em seguida, processa a mensagem.
- Outros serviços do Azure (por exemplo, Armazenamento de Blobs, Hub de Eventos) também publicam eventos na Grade de Eventos. Esses serviços disparam Aplicativos Lógicos para receber o evento e realizar ações subsequentes.

## <a name="recommendations"></a>Recomendações

Seus requisitos específicos podem ser diferentes da arquitetura genérica descrita neste artigo. Use as recomendações nesta seção como um ponto inicial.

### <a name="service-bus-tier"></a>Camada do Barramento de Serviço

Use a camada Premium do barramento de serviço. A camada Premier dá suporte a notificações de grade de eventos. Para obter mais informações, consulte [Preços de Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Preço da Grade de Eventos

A Grade de Eventos usam um modelo sem servidor. A cobrança é calculada com base no número de operações (execução do evento). Consulte mais informações, consulte [Preço da Grade de Eventos](https://azure.microsoft.com/pricing/details/event-grid/). Atualmente, não há considerações de camada para a Grade de Eventos.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>Usar PeekLock ao consumir mensagens do Barramento de Serviço

Ao criar um aplicativo lógico para consumir mensagens do Barramento de Serviço, use [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) no Aplicativo Lógico para acessar um grupo de mensagens. Ao usar o PeekLock pode executar etapas para validar cada mensagem antes de concluir ou abandonar a mensagem. Essa abordagem protege contra a perda acidental de mensagens.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Verificar vários objetos quando um gatilho da Grade de Eventos é acionado

Quando um gatilho de grade de eventos é acionado, simplesmente significa que "pelo menos uma dessas coisas aconteceu." Por exemplo, quando a Grade de Eventos dispara um aplicativo lógico em uma mensagem que aparece em uma fila do Barramento de Serviço, o aplicativo lógico deverá sempre assumir que pode haver uma ou mais mensagens disponíveis para processamento.

### <a name="region"></a>Região

Provisione Gerenciamento de API, Aplicativos Lógicos e Barramento de Serviço na mesma região para minimizar latência da rede. Em geral, escolha a região mais próxima aos usuários.

O grupo de recursos também tem uma região. A região especifica onde os metadados de implantação são armazenados e de onde o modelo de implantação é executado. Coloque o grupo de recursos e seus recursos na mesma região para melhorar a disponibilidade durante a implantação.

## <a name="scalability"></a>Escalabilidade

A camada Premium do Barramento de Serviço pode expandir o número de unidades de mensagens para obter maior escalabilidade. Configurações de camada Premium podem ter um, dois ou quatro unidades de mensagens. Para saber mais informações sobre escalonar Barramento de Serviço, consulte [Práticas recomendadas para melhorias de desempenho usando o Sistema de Mensagens do Barramento de Serviço](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability"></a>Disponibilidade

Atualmente, o contrato de nível de serviço (SLA) para Gerenciamento de API do Azure era de 99,9% para as camadas Básico, Standard e Premium. Configurações de camada Premium com implantação de pelo menos uma unidade em duas ou mais regiões têm um SLA de 99,95%.

Atualmente, o SLA para aplicativos lógicos do Azure é de 99,9%.

### <a name="disaster-recovery"></a>Recuperação de desastre

Considere implementar a recuperação de desastres geográficos na camada Premium do Barramento de Serviço para habilitar failover se uma interrupção grave ocorrer. Para mais informações consulte [Recuperação de desastre em área geográfica do Barramento de Serviço do Azure](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability"></a>Capacidade de gerenciamento

Crie grupos de recursos separados para ambientes de produção, desenvolvimento e teste. Grupos de recursos separados torna mais fácil gerenciar implantações, excluir implantações de teste a atribuir direitos de acesso.

Ao atribuir recursos para grupos de recursos, considere o seguinte:

- **Ciclo de vida**. Em geral, coloque recursos com o mesmo ciclo de vida no mesmo grupo de recursos.
- **Acesso**. É possível utilizar [controle de acesso baseado em função](../role-based-access-control/overview.md) (RBAC) para aplicar políticas de acesso aos recursos de um grupo.
- **Cobrança**. Você pode exibir os custos acumulados para o grupo de recursos.
- **Tipo de preço para o gerenciamento de API**. É recomendável usar o Tipo de Desenvolvedor para ambientes de desenvolvimento e teste. Para pré-produção, é recomendável implantar uma réplica do ambiente de produção, executando testes e, em seguida, desligando para minimizar o custo.

Para saber mais, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Implantação

É recomendável utilizar os [modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) para implantar o Gerenciamento de API do Azure, Aplicativos Lógicos, Grade de Eventos e Barramento de Serviço. Os modelos facilitam a automatização das implantações por meio do PowerShell ou da CLI do Azure.

É recomendável colocar o Gerenciamento de API do Azure, quaisquer Aplicativos Lógicos individuais, tópicos de Grade de Eventos e Namespaces do Barramento de Serviço nos próprios modelos do Resource Manager separados. Quando você usa modelos separados, você pode armazenar os recursos em sistemas de controle do código-fonte. Você pode implantar esses modelos juntos ou individualmente como parte de um processo de CI/CD (integração contínua/implantação contínua).

### <a name="diagnostics-and-monitoring"></a>Diagnóstico e monitoramento

Como gerenciamento de API e Aplicativos Lógicos, pode ser monitorado usando o Azure Monitor. O Azure Monitor fornece informações com base nas métricas que são configuradas para cada serviço. O Azure Monitor é habilitado por padrão.

## <a name="security"></a>Segurança

Barramento de Serviço Seguro usando uma SAS. Você pode usar a [autenticação SAS](../service-bus-messaging/service-bus-sas.md) para conceder a um usuário o acesso aos recursos do Barramento de Serviço, com direitos específicos. Para saber mais, confira [Autenticação e autorização do barramento de Serviço](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Se uma fila do Barramento de Serviço precisar ser exposta como um ponto de extremidade HTTP (para permitir o postagem de novas mensagens), você deve usar o Gerenciamento de API para protegê-lo administrando o ponto de extremidade. O ponto de extremidade pode ser protegido com certificados ou OAuth, conforme apropriado. A maneira mais fácil de garantir um ponto de extremidade é usar um aplicativo lógico com uma solicitação8resposta de gatilhh HTTP como intermediário.

A Grade de Eventos protege a entrega de eventos por meio de um código de validação. Se você usar Aplicativos Lógicos para consumir o evento, isso será feito automaticamente. Para saber mais, confira [Event Grid security and authentication](../event-grid/security-authentication.md) (Segurança e autenticação da Grade de Eventos).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [integração corporativa simples](logic-apps-architectures-simple-enterprise-integration.md).