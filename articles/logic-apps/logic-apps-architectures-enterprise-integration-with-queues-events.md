---
title: Padrão de arquitetura de integração empresarial – Azure Integration Services
description: Esta referência de arquitetura mostra como implementar um padrão de integração empresarial simples com o Aplicativo Lógico do Azure, o Gerenciamento de API do Azure, o Barramento de Serviço do Azure e a Grade de Eventos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 2ffb1f7edef0cf92cbbf7adc4314967858bcfeb1
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128636"
---
# <a name="enterprise-integration-architecture-with-queues-and-events"></a>Arquitetura de integração empresarial com filas e eventos

Este artigo descreve uma arquitetura de integração empresarial que usa práticas comprovadas que podem ser aplicadas a um aplicativo de integração ao usar o Azure Integration Services. Você pode usar essa arquitetura como base para muitos padrões diferentes de aplicativos que exigem APIs HTTP, fluxo de trabalho e orquestração.

![Diagrama de arquitetura - integração empresarial com filas e eventos](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

A série de arquitetura descreve os blocos componentes reutilizáveis que podem ser aplicados para compilar um aplicativo de integração genérico. Como tecnologia de integração tem muitos aplicativos possíveis, variando de aplicativos ponto a ponto simples até os aplicativos empresariais completos do Barramento de Serviço do Azure, considere quais componentes você precisa implementar para seus aplicativos e infraestrutura.

## <a name="architecture-components"></a>Componentes da arquitetura

Essa arquitetura baseia-se naquela descrita no artigo [Referência de arquitetura: integração empresarial simples](../logic-apps/logic-apps-architectures-simple-enterprise-integration.md). As [recomendações](../logic-apps/logic-apps-architectures-simple-enterprise-integration.md#recommendations) dessa arquitetura também se aplicam aqui, mas para ser mais breve, este artigo omite essas recomendações da seção [Recomendações](#recommendations). Essa arquitetura de integração empresarial inclui os seguintes componentes:

- **Grupo de recursos**: um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) é um contêiner lógico para os recursos do Azure.

- **Gerenciamento de API do Azure**: o serviço [Gerenciamento de API](https://docs.microsoft.com/azure/api-management/) é uma plataforma totalmente gerenciada para publicação, proteção e transformação de APIs HTTP.

- **Portal do Desenvolvedor do Gerenciamento de API do Azure**: cada instância do Gerenciamento de API do Azure fornece acesso ao [Portal do Desenvolvedor](../api-management/api-management-customize-styles.md). Esse portal fornece acesso à documentação e a exemplo de código. Também é possível testar as APIs no Portal do Desenvolvedor.

- **Aplicativo Lógico do Azure**: os [Aplicativos Lógicos](../logic-apps/logic-apps-overview.md) são uma plataforma sem servidor para compilar fluxos de trabalho e integrações empresariais.

- **Conectores**: os Aplicativos Lógicos usam [conectores](../connectors/apis-list.md) para conectarem aos serviços utilizados com frequência. Os Aplicativos Lógicos já oferecem centenas de conectores, mas também é possível criar um conector personalizado.

- **Barramento de Serviço do Azure**: o [Barramento de Serviço](../service-bus-messaging/service-bus-messaging-overview.md) fornece mensagens seguras e confiáveis. As mensagens podem ser usadas para separar os aplicativos uns dos outros e integrá-los a outros sistemas baseados em mensagens.

- **Grade de Eventos do Azure**: a [Grade de Eventos](../event-grid/overview.md) é uma plataforma sem servidor para publicar e entregar eventos de aplicativos.

- **Endereço IP**: o serviço de Gerenciamento de API do Azure tem um nome de domínio e um [endereço IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) público fixo. O nome de domínio padrão é um subdomínio de azure-api.net, como contoso.azure-api.net, mas também é possível configurar [domínios personalizados](../api-management/configure-custom-domain.md). Aplicativos lógicos e o barramento de serviço também têm um endereço IP público. No entanto, por questão de segurança, essa arquitetura restringe o acesso para chamada de pontos de extremidade de Aplicativos Lógicos a apenas para o endereço IP do Gerenciamento de API. As chamadas para o Barramento de Serviço são protegidas por uma assinatura de acesso compartilhado (SAS).

- **DNS do Azure**: o [DNS do Azure](https://docs.microsoft.com/azure/dns/) é um serviço de hospedagem para domínios DNS. O DNS do Azure fornece resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Microsoft Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. Para usar um nome de domínio personalizado, como contoso.com, crie registros DNS que mapeiem o nome de domínio personalizado para o endereço IP. Para obter mais informações, consulte [Configurar um nome de domínio personalizado no Gerenciamento de API](../api-management/configure-custom-domain.md).

- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/): é possível usar o **Azure AD** ou outro provedor de identidade para autenticação. O Azure Active Directory fornece autenticação para acessar os pontos de extremidade de API, passando um [JSON Web Token para o gerenciamento de API](../api-management/policies/authorize-request-based-on-jwt-claims.md) para validar. Para camadas Standard e Premium, o Azure AD pode proteger o acesso ao Portal do Desenvolvedor do Gerenciamento de API.

## <a name="patterns"></a>Padrões 

Esta arquitetura tem alguns padrões que são fundamentais para sua operação:

* As APIs HTTP de back-end existentes por meio do Portal do Desenvolvedor de Gerenciamento de API. No portal, os desenvolvedores, que são internos da sua organização, externos ou ambos,  
podem integrar as chamadas a essas APIs em aplicativos.

* As APIs compostas são compiladas usando aplicativos lógicos, que orquestram chamadas para sistemas SaaS (software como serviço), serviços do Azure e quaisquer APIs publicadas no Gerenciamento de API. Os aplicativos lógicos também são [publicados por meio do Portal do Desenvolvedor de Gerenciamento de API](../api-management/import-logic-app-as-api.md).

* Aplicativos usam o Azure AD para [adquirir um token de segurança do OAuth 2.0](../api-management/api-management-howto-protect-backend-with-aad.md) que é necessário para obter acesso a uma API.

* O Gerenciamento de API do Azure [valida o token](../api-management/api-management-howto-protect-backend-with-aad.md) de segurança e passa a solicitação ao aplicativo lógico ou API de back-end.

* As filas do Barramento de Serviço do Azure são usadas para [separar](../service-bus-messaging/service-bus-messaging-overview.md) a atividade do aplicativo e [suavizar os picos de carga](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). As mensagens são adicionadas às filas por aplicativos lógicos, aplicativos de terceiros ou (não mostrado) publicando a fila como uma API HTTP por meio do Gerenciamento de API.

* Quando mensagens são adicionadas a uma fila do Barramento de Serviço, um evento é acionado. O evento dispara um aplicativo lógico, que processa a mensagem.

* Outros serviços do Azure, como o Armazenamento de Blobs e o Hub de Eventos do Azure, também publicam eventos na Grade de Eventos. Esses serviços disparam Aplicativos Lógicos para receber o evento e realizar ações subsequentes.

## <a name="recommendations"></a>Recomendações

Seus requisitos específicos podem ser diferentes da arquitetura genérica descrita neste artigo. Use as recomendações nesta seção como um ponto inicial.

### <a name="service-bus-tier"></a>Camada do Barramento de Serviço

Use a camada Premium do Barramento de Serviço, que é compatível com notificações da Grade de Eventos. Para obter mais informações, consulte [Preços de Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Preço da Grade de Eventos

A Grade de Eventos usam um modelo sem servidor. A cobrança é calculada com base no número de operações (execuções do evento). Consulte mais informações, consulte [Preço da Grade de Eventos](https://azure.microsoft.com/pricing/details/event-grid/). Atualmente, não há considerações de camada para a Grade de Eventos.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>Usar PeekLock ao consumir mensagens do Barramento de Serviço

Ao criar um aplicativo lógico para consumir mensagens do Barramento de Serviço, use [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) no aplicativo lógico para acessar um grupo de mensagens. Ao usar o PeekLock pode executar etapas para validar cada mensagem antes de concluir ou abandonar a mensagem. Essa abordagem protege contra a perda acidental de mensagens.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Verificar vários objetos quando um gatilho da Grade de Eventos é acionado

Quando um gatilho da Grade de Eventos é acionado, essa ação significa apenas que “pelo menos uma dessas coisas aconteceu”. Por exemplo, quando a Grade de Eventos dispara um aplicativo lógico em uma mensagem que aparece em uma fila do Barramento de Serviço, o aplicativo lógico deverá sempre assumir que pode haver uma ou mais mensagens disponíveis para processamento.

### <a name="region"></a>Região

Para minimizar a latência de rede, escolha a mesma região para o Gerenciamento de API, os Aplicativos Lógicos e o Barramento de Serviço. Em geral, escolha a região mais próxima dos usuários.

O grupo de recursos também tem uma região. Essa região especifica onde os metadados de implantação são armazenados e onde o modelo de implantação é executado. Para melhorar a disponibilidade durante a implantação, coloque o grupo de recursos e os respectivos recursos na mesma região.

## <a name="scalability"></a>Escalabilidade

Para obter maior escalabilidade, a camada Premium do Barramento de Serviço pode aumentar o número de unidades do sistema de mensagens. Configurações de camada Premium podem ter um, dois ou quatro unidades de mensagens. Para saber mais informações sobre escalonar Barramento de Serviço, consulte [Práticas recomendadas para melhorias de desempenho usando o Sistema de Mensagens do Barramento de Serviço](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability"></a>Disponibilidade

* Para as camadas Básica, Standard e Premium, o SLA (Contrato de Nível de Serviço) para o Gerenciamento de API do Azure é atualmente 99,9%. Para configurações da camada Premium com implantação de pelo menos uma unidade em duas ou mais regiões, o SLA é de 99,95%.

* Atualmente, o SLA para Aplicativo Lógico do Azure é de 99,9%.

### <a name="disaster-recovery"></a>Recuperação de desastre

Para habilitar o failover em caso de uma interrupção grave, considere implementar a recuperação de desastre geográfico no Barramento de Serviço Premium. Para mais informações consulte [Recuperação de desastre em área geográfica do Barramento de Serviço do Azure](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability"></a>Capacidade de gerenciamento

Crie grupos de recursos separados para ambientes de produção, desenvolvimento e teste. Ter grupos de recursos separados facilita o gerenciamento de implantações, a exclusão de implantações de teste a atribuição de direitos de acesso.

Ao atribuir recursos a grupos de recursos, considere os seguintes fatores:

* **Ciclo de vida**: em geral, coloque recursos com o mesmo ciclo de vida no mesmo grupo de recursos.

* **Acesso**: para aplicar as políticas de acesso aos recursos de um grupo, utilize o [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md).

* **Cobrança**: é possível exibir os custos de rollup para o grupo de recursos.

* **Tipos de preço para Gerenciamento de API**: use o tipo de Desenvolvedor para ambientes de desenvolvimento e teste. Para minimizar os custos durante a pré-produção, implante uma réplica do seu ambiente de produção, execute os testes e desligue.

Para saber mais, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="deployment"></a>Implantação

* Para implantar o Gerenciamento de API, Aplicativos Lógicos, Grade de Eventos e Barramento de Serviço, use os [modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Os modelos facilitam a automatização das implantações por meio do PowerShell ou da CLI do Azure.

* Coloque o Gerenciamento de API, quaisquer aplicativos lógicos individuais, tópicos da Grade de Eventos e namespaces do Barramento de Serviço em seus próprios modelos do Resource Manager separados. Usando modelos separados, é possível armazenar os recursos em sistemas de controle do código-fonte. Você pode implantar esses modelos juntos ou individualmente como parte de um processo de CI/CD (integração contínua/implantação contínua).

## <a name="diagnostics-and-monitoring"></a>Diagnóstico e monitoramento

Assim como no Gerenciamento de API e nos Aplicativos Lógicos, é possível monitorar o Barramento de Serviço usando o Azure Monitor, que é habilitado por padrão. O Azure Monitor fornece informações com base nas métricas que são configuradas para cada serviço. 

## <a name="security"></a>Segurança

Para proteger o Barramento de Serviço, use a SAS (Assinatura de Acesso Compartilhado). Por exemplo, é possível conceder a um usuário o acesso aos recursos do Barramento de Serviço com direitos específicos usando a [autenticação SAS](../service-bus-messaging/service-bus-sas.md). Para saber mais, confira [Autenticação e autorização do barramento de Serviço](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Se for necessário expor uma fila do Barramento de Serviço como um ponto de extremidade HTTP, por exemplo, para postar novas mensagens, use o Gerenciamento de API para proteger a fila administrando o ponto de extremidade. Em seguida, proteja o ponto de extremidade com certificados ou autenticação OAuth, conforme apropriado. A maneira mais fácil de proteger um ponto de extremidade é usar um aplicativo lógico com um gatilho de solicitação/resposta HTTP como intermediário.

O serviço da Grade de Eventos protege a entrega de eventos por meio de um código de validação. Se você usar os Aplicativos Lógicos para consumir o evento, a validação será realizada automaticamente. Para saber mais, confira [Event Grid security and authentication](../event-grid/security-authentication.md) (Segurança e autenticação da Grade de Eventos).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [integração empresarial simples](logic-apps-architectures-simple-enterprise-integration.md)
