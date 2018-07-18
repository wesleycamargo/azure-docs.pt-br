---
title: Arquitetura de referência do Integration Services do Azure
description: Arquitetura de referência mostrando como implementar um padrão de integração empresarial com Aplicativos Lógicos, Gerenciamento de API, Barramento de Serviço e Grade de Eventos
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
ms.openlocfilehash: db3350b3c70f6e6f35949e8423334061849b7b37
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231867"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>Integração empresarial com filas e eventos: arquitetura de referência

## <a name="overview"></a>Visão geral

Essa arquitetura de referência mostra um conjunto de práticas comprovadas para um aplicativo de integração que usa o Integration Services do Azure. Essa arquitetura pode servir de base para muitos padrões diferentes de aplicativos e APIs HTTP, fluxo de trabalho e orquestração.

*Há muitos aplicativos possíveis da tecnologia de integração, desde um aplicativo ponto a ponto simples até um barramento de serviço corporativo completo. Esta série de arquitetura define as partes de componentes reutilizáveis para compilar qualquer aplicativo de integração – arquitetos devem considerar os componentes necessários para os aplicativos e a infraestrutura.*

![Diagrama de arquitetura - integração empresarial com filas e eventos](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>Arquitetura

Essa arquitetura baseia-se naquela mostrada em [integração empresarial simples](logic-apps-architectures-simple-enterprise-integration.md). Ela inclui os seguintes componentes:

- Grupo de recursos. Um grupo de recursos é um contêiner lógico para recursos do Azure.
- Gerenciamento de API do Azure. O Gerenciamento de API do Azure é uma plataforma totalmente gerenciada para publicação, proteção e transformação de APIs HTTP.
- Portal do Desenvolvedor de Gerenciamento de API do Azure. Cada instância de Gerenciamento de API do Azure vem com um Portal do Desenvolvedor que fornece acesso à documentação, a exemplos de código e à capacidade de testar uma API.
- Aplicativo Lógico do Azure. Os Aplicativos Lógicos são uma plataforma sem servidor para compilar integração e fluxo de trabalho empresarial.
- Conectores. Os conectores são usados pelos Aplicativos Lógicos para conectarem os serviços normalmente utilizados. Os Aplicativos Lógicos já possuem centenas de conectores diferentes, mas também podem ser criados usando um conector personalizado.
- Barramento de Serviço do Azure. O Barramento de Serviço fornece mensagens seguras e confiáveis. Mensagens podem ser usadas para separar os aplicativos uns dos outros e integrar com outros sistemas baseados em mensagens.
- Grade de Eventos do Azure. A Grade de Eventos é uma plataforma sem servidor para publicar e entregar eventos de aplicativos.
- Endereço IP. O serviço de Gerenciamento de API do Azure tem um nome de domínio e um endereço IP público fixo. O nome de domínio é um subdomínio do azure-api.net, como contoso.azure-api.net. Aplicativos Lógicos e Barramento de Serviço também têm um endereço IP público, no entanto, nessa arquitetura, restringimos o acesso para chamar pontos de extremidades de Aplicativos Lógicos apenas para o endereço IP do Gerenciamento de API (por segurança). As chamadas para o Barramento de Serviço são protegidas por uma assinatura de acesso compartilhado.
- DNS do Azure. O DNS do Azure é um serviço de hospedagem para domínios DNS, fornecendo resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. Para usar um nome de domínio personalizado (como contoso.com), crie registros DNS que mapeiem o nome de domínio personalizado para o endereço IP. Para obter mais informações, consulte Configurar um nome de domínio personalizado no Gerenciamento de API do Azure.
- Azure AD (Azure Active Directory). Use o Azure AD ou outro provedor de identidade para autenticação. O Azure AD fornece autenticação para acessar os pontos de extremidade de API (passando um Token Web JSON para o Gerenciamento de API validar) e pode proteger o acesso ao Portal do Desenvolvedor de Gerenciamento de API (somente camadas Standard e Premium).

Esta arquitetura possui alguns padrões fundamentais para seu funcionamento:

1. As APIs HTTP de back-end existentes são publicadas por meio do Portal do Desenvolvedor de Gerenciamento de API, permitindo que os desenvolvedores (internos à organização, externos ou ambos) integrem chamadas a essas APIs em aplicativos.
2. APIs compostas são compiladas usando aplicativos lógicos, orquestrando chamadas para sistemas SAAS, serviços do Azure e quaisquer APIs publicadas no Gerenciamento de API. Os Aplicativos Lógicos também são publicados por meio do Portal do Desenvolvedor de Gerenciamento de API.
3. Os aplicativos adquirem um token de segurança OAuth 2.0 necessário para obter acesso a uma API usando o Azure Active Directory.
4. O Gerenciamento de API do Azure valida o token de segurança e passa a solicitação ao aplicativo lógico/API de back-end.
5. As filas do Barramento de Serviço são usadas para separar a atividade do aplicativo e suavizar os picos de carga. As mensagens são adicionadas às filas por Aplicativos Lógicos, aplicativos de terceiros ou (não ilustrados), publicando a fila como uma API HTTP por meio do Gerenciamento de API.
6. Quando mensagens são adicionadas a uma fila do Barramento de Serviço, um Evento é acionado. Um Aplicativo Lógico é disparado por esse evento e processa a mensagem.
7. Da mesma forma, outros serviços do Azure (por exemplo, Armazenamento de Blobs, Hub de Eventos) também publicam eventos na Grade de Eventos. Eles disparam Aplicativos Lógicos para receber o evento e realizar ações subsequentes.

## <a name="recommendations"></a>Recomendações

Seus requisitos podem ser diferentes dos requisitos da arquitetura descrita aqui. Use as recomendações nesta seção como um ponto inicial.

### <a name="service-bus-tier"></a>Camada do Barramento de Serviço

Utilize a camada Premium do Barramento de Serviço, pois atualmente dá suporte a notificações de grade de eventos (suporte em todas as camadas é esperado). Consulte [preço do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Preço da Grade de Eventos

A Grade de Eventos funciona usando um modelo sem servidor – a cobrança é calculada com base no número de operações (execução de evento). Consulte [Preço da Grade de Eventos](https://azure.microsoft.com/pricing/details/event-grid/) para obter mais informações. Atualmente, não há considerações de camada para a Grade de Eventos.

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>Usar PeekLock ao consumir mensagens do Barramento de Serviço

Ao criar Aplicativos Lógicos para consumir mensagens do Barramento de Serviço, use [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) no Aplicativo Lógico para acessar um grupo de mensagens. O Aplicativo Lógico pode executar etapas para validar cada mensagem antes de concluir ou abandonar. Essa abordagem protege contra a perda acidental de mensagens.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Verificar vários objetos quando um gatilho da Grade de Eventos é acionado

Gatilhos da Grade de Eventos acionados significa simplesmente que "pelo menos 1 desses eventos aconteceu". Por exemplo, quando a Grade de Eventos dispara um aplicativo lógico em uma mensagem que aparece em uma fila do Barramento de Serviço, o aplicativo lógico deverá sempre assumir que pode haver uma ou mais mensagens disponíveis para processamento.

### <a name="region"></a>Região

Provisione Gerenciamento de API, Aplicativos Lógicos e Barramento de Serviço na mesma região para minimizar latência da rede. Em geral, escolha a região mais próxima aos usuários.

O grupo de recursos também tem uma região, que especifica onde os metadados de implantação são armazenados e de onde o modelo de implantação é executado. Coloque o grupo de recursos e seus recursos na mesma região. Isso pode melhorar a disponibilidade durante a implantação.

## <a name="scalability-considerations"></a>Considerações sobre escalabilidade

A camada Premium do Barramento de Serviço do Azure pode expandir o número de unidades de mensagens para obter maior escalabilidade. A camada Premium pode ter 1, 2 ou 4 unidades de mensagens. Para obter mais orientações sobre como expandir o Barramento de Serviço do Azure, consulte [Melhores práticas para melhorias de desempenho usando o Sistema de Mensagens do Barramento de Serviço](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability-considerations"></a>Considerações sobre disponibilidade

No momento da gravação deste artigo, o SLA (Contrato de Nível de Serviço) para Gerenciamento de API do Azure era de 99,9% para as camadas Básico, Standard e Premium. Configurações de camada Premium com implantação de pelo menos uma unidade em duas ou mais regiões têm um SLA de 99,95%.

No momento da gravação deste artigo, o SLA (Contrato de Nível de Serviço) dos Aplicativos Lógicos do Azure era de 99,9%.

### <a name="disaster-recovery"></a>Recuperação de desastre

Considere implementar a recuperação de desastres geográficos na camada Premium do Barramento de Serviço para habilitar failover no caso de uma interrupção grave. Leia mais sobre [Recuperação de desastre geográfico do Barramento de Serviço do Azure](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability-considerations"></a>Considerações sobre capacidade de gerenciamento

Crie grupos de recursos separados para ambientes de produção, desenvolvimento e teste. Isso facilita o gerenciamento de implantações, a exclusão de implantações de teste e a atribuição de direitos de acesso.
Ao atribuir recursos para grupos de recursos, considere o seguinte:

- Ciclo de vida. Em geral, coloque recursos com o mesmo ciclo de vida no mesmo grupo de recursos.
- Acesso. É possível utilizar RBAC [Controle de Acesso Baseado em Função](../role-based-access-control/overview.md) para aplicar políticas de acesso aos recursos de um grupo.
- Cobrança. Você pode exibir os custos acumulados para o grupo de recursos.
- Tipos de preço para Gerenciamento de API – é recomendável usar o Tipo de Desenvolvedor para ambientes de desenvolvimento e teste. Para pré-produção, é recomendável implantar uma réplica do ambiente de produção, executando testes e, em seguida, desligando para minimizar o custo.

Para obter mais informações, consulte a visão geral do [grupo de recursos](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Implantação

É recomendável utilizar os [modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) para implantar o Gerenciamento de API do Azure, Aplicativos Lógicos, Grade de Eventos e Barramento de Serviço. Os modelos facilitam a automatização das implantações por meio do PowerShell ou da CLI (interface de linha de comando) do Azure.

É recomendável colocar o Gerenciamento de API do Azure, quaisquer Aplicativos Lógicos individuais, tópicos de Grade de Eventos e Namespaces do Barramento de Serviço nos próprios modelos do Resource Manager separados. Isso permitirá armazená-los nos sistemas de controle do código-fonte. Esses modelos podem ser implantados juntos ou individualmente como parte de um processo de CI/CD (integração contínua/implantação contínua).

### <a name="diagnostics-and-monitoring"></a>Diagnóstico e monitoramento

O Barramento de Serviço, como o Gerenciamento de API e Aplicativos Lógicos, pode ser monitorado usando o Azure Monitor. O Azure Monitor é habilitado por padrão e fornecerá informações com base nas diferentes métricas configuradas para cada serviço.

## <a name="security-considerations"></a>Considerações de segurança

Barramento de Serviço Seguro usando uma Assinatura de Acesso Compartilhado. A [autenticação SAS](../service-bus-messaging/service-bus-sas.md) permite que você conceda a um usuário o acesso aos recursos do Barramento de Serviço, com direitos específicos. Leia mais sobre [Autenticação e autorização do Barramento de Serviço](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Além disso, se uma fila do Barramento de Serviço precisar ser exposta como um ponto de extremidade HTTP (para permitir o postagem de novas mensagens), o Gerenciamento de API deverá ser usado para protegê-lo, administrando o ponto de extremidade. Isso pode ser protegido com certificados ou OAuth, conforme apropriado. A maneira mais fácil de fazer isso é usar um Aplicativo Lógico com um gatilho HTTP de Solicitação/Resposta como intermediário.

A Grade de Eventos protege a entrega de eventos por meio de um código de validação. Se você usar LogicApps para consumir o evento, isso será feito automaticamente. Veja mais detalhes sobre [Segurança e autenticação da Grade de Eventos](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Próximas etapas

* [Enterprise Integration Simples](logic-apps-architectures-simple-enterprise-integration.md)
