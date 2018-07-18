---
title: Integration Services do Azure - Integração empresarial simples
description: Arquitetura de referência mostrando como implementar um padrão de integração empresarial simples com Aplicativo Lógico do Azure e Gerenciamento de API do Azure
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
ms.openlocfilehash: 17f591a470bf65d3c9365bca9c5ae2d5a6c9574f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231829"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>Integração empresarial simples - arquitetura de referência

## <a name="overview"></a>Visão geral

Essa arquitetura de referência mostra um conjunto de práticas comprovadas para um aplicativo de integração que usa o Integration Services do Azure. Essa arquitetura pode servir de base para muitos padrões diferentes de aplicativos e APIs HTTP, fluxo de trabalho e orquestração.

*Há muitos aplicativos possíveis da tecnologia de integração, desde um aplicativo ponto a ponto simples até um barramento de serviço corporativo completo. Esta série de arquitetura define as partes de componentes reutilizáveis para compilar qualquer aplicativo de integração – arquitetos devem considerar os componentes necessários para os aplicativos e a infraestrutura.*

   ![Diagrama de arquitetura - integração empresarial simples](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>Arquitetura

A arquitetura tem os seguintes componentes:

- Grupo de recursos. Um grupo de recursos é um contêiner lógico para recursos do Azure.
- Gerenciamento de API do Azure. O Gerenciamento de API do Azure é uma plataforma totalmente gerenciada para publicação, proteção e transformação de APIs HTTP.
- Portal do Desenvolvedor de Gerenciamento de API do Azure. Cada instância de Gerenciamento de API do Azure vem com um Portal do Desenvolvedor que fornece acesso à documentação, a exemplos de código e à capacidade de testar uma API.
- Aplicativo Lógico do Azure. Os Aplicativos Lógicos são uma plataforma sem servidor para compilar integração e fluxo de trabalho empresarial.
- Conectores. Os conectores são usados pelos Aplicativos Lógicos para conectarem os serviços normalmente utilizados. Os Aplicativos Lógicos já possuem centenas de conectores diferentes, mas também podem ser criados usando um conector personalizado.
- Endereço IP. O serviço de Gerenciamento de API do Azure tem um nome de domínio e um endereço IP público fixo. O nome de domínio é um subdomínio do azure-api.net, como contoso. azure-api.net. Os Aplicativos Lógicos do Azure também têm um endereço IP público, no entanto, nessa arquitetura restringimos o acesso para chamar pontos de extremidade de aplicativos lógicos apenas para o endereço IP do Gerenciamento de API (por segurança).
- DNS do Azure. O DNS do Azure é um serviço de hospedagem para domínios DNS, fornecendo resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. Para usar um nome de domínio personalizado (como contoso.com), crie registros DNS que mapeiem o nome de domínio personalizado para o endereço IP. Para obter mais informações, consulte Configurar um nome de domínio personalizado no Gerenciamento de API do Azure.
- Azure AD (Azure Active Directory). Use o Azure AD ou outro provedor de identidade para autenticação. O Azure AD fornece autenticação para acessar os pontos de extremidade de API (passando um Token Web JSON para o Gerenciamento de API validar) e pode proteger o acesso ao Portal do Desenvolvedor de Gerenciamento de API (somente camadas Standard e Premium).

Esta arquitetura possui alguns padrões fundamentais para seu funcionamento:

1. As APIs HTTP de back-end existentes são publicadas por meio do Portal do Desenvolvedor de Gerenciamento de API, permitindo que os desenvolvedores (internos à organização, externos ou ambos) integrem chamadas a essas APIs em aplicativos.
2. APIs compostas são compiladas usando aplicativos lógicos, orquestrando chamadas para sistemas SAAS, serviços do Azure e quaisquer APIs publicadas no Gerenciamento de API. Os Aplicativos Lógicos também são publicados por meio do Portal do Desenvolvedor de Gerenciamento de API.
3. Os aplicativos adquirem um token de segurança OAuth 2.0 necessário para obter acesso a uma API usando o Azure Active Directory.
4. O Gerenciamento de API do Azure valida o token de segurança e passa a solicitação ao aplicativo lógico/API de back-end.

## <a name="recommendations"></a>Recomendações

Seus requisitos podem ser diferentes dos requisitos da arquitetura descrita aqui. Use as recomendações nesta seção como um ponto inicial.

### <a name="azure-api-management-tier"></a>Camada de Gerenciamento de API do Azure

Utilize os níveis Básico, Standard ou Premium porque oferecem SLA de produção e suporte para escalar horizontalmente na região do Azure (o número de unidades varia por camada). A camada Premium também dá suporte para escalar horizontalmente em várias regiões do Azure. Baseie a camada que você escolheu no nível de taxa de transferência necessária e no conjunto de recursos. Para obter mais informações, consulte [preço de Gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).

Você será cobrado por todas as instâncias de Gerenciamento de API quando estiverem em execução. Caso tenha escalado horizontalmente e não precisar desse nível de desempenho o tempo todo, considere aproveitar a cobrança por hora de Gerenciamento de API e reduzir verticalmente.

### <a name="logic-apps-pricing"></a>Preço de Aplicativos Lógicos

Os Aplicativos Lógicos funcionam como um modelo [sem servidor](logic-apps-serverless-overview.md) – a cobrança é calculada com base na ação e execução do conector. Consulte [preços de Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/) para obter mais informações. Atualmente, não há considerações para Aplicativos Lógicos.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Aplicativos Lógicos para chamadas à API assíncronas

Os Aplicativos Lógicos funcionam melhor em cenários que não exigem baixa latência – por exemplo, chamadas à API de execução semi-longa ou assíncronas. Se for necessária baixa latência (por exemplo, uma chamada que bloqueie uma interface do usuário), é recomendável implementar essa API ou operação usando uma tecnologia diferente, por exemplo, API da Web ou Azure Functions implantados usando o Serviço de Aplicativo. Além disso, é recomendável que essa API seja administrada usando o Gerenciamento de API para consumidores de API.

### <a name="region"></a>Região

Provisione Gerenciamento de API e Aplicativos Lógicos na mesma região para minimizar latência da rede. Em geral, escolha a região mais próxima aos usuários.

O grupo de recursos também tem uma região, que especifica onde os metadados de implantação são armazenados e de onde o modelo de implantação é executado. Coloque o grupo de recursos e seus recursos na mesma região. Isso pode melhorar a disponibilidade durante a implantação.

## <a name="scalability-considerations"></a>Considerações sobre escalabilidade

Os administradores de Gerenciamento de API devem adicionar [políticas de cache](../api-management/api-management-howto-cache.md), quando apropriado, para aumentar a escalabilidade do serviço e reduzir a carga nos serviços back-end.

O Gerenciamento de API do Azure Básico, Standard e Premium pode ser escalado horizontalmente em uma região do Azure para oferecer maior capacidade. Os administradores podem usar a Métrica de Capacidade no menu Métricas para analisar o uso dos serviços e escalar verticalmente ou reduzir verticalmente, conforme apropriado.

Recomendações para colocação em escala de um serviço de Gerenciamento de API:

- A colocação em escala precisa considerar os padrões de tráfego – clientes com padrões de tráfego mais voláteis terão maior necessidade de aumento de capacidade.
- Capacidade consistente acima de 66% pode indicar a necessidade de escalar verticalmente.
- Uma capacidade consistente abaixo de 20% pode indicar uma oportunidade para reduzir verticalmente.
- É sempre recomendável o teste de carga do serviço de Gerenciamento de API com uma carga representativa antes de habilitar em produção.

Os serviços da camada Premium podem ser escalados horizontalmente em várias regiões do Azure. Os clientes que implantarem dessa maneira obterão um SLA superior (99,95% ao contrário de 99,9%) e poderão provisionar serviços próximos a usuários em várias regiões.

O modelo sem servidor dos Aplicativos Lógicos significa que os administradores não precisam fazer considerações extras para escalabilidade de serviço, pois o serviço escala automaticamente para atender à demanda.

## <a name="availability-considerations"></a>Considerações sobre disponibilidade

No momento da gravação deste artigo, o SLA (Contrato de Nível de Serviço) para Gerenciamento de API do Azure era de 99,9% para as camadas Básico, Standard e Premium. Configurações de camada Premium com implantação de pelo menos uma unidade em duas ou mais regiões têm um SLA de 99,95%.

No momento da gravação deste artigo, o SLA (Contrato de Nível de Serviço) dos Aplicativos Lógicos do Azure era de 99,9%.

### <a name="backups"></a>Backups

A configuração do Gerenciamento de API do Azure deve ser [copiada em backup regularmente](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (com base na regularidade da alteração) e os arquivos de backup armazenados em um local ou Região do Azure diferentes de onde o serviço reside. Os clientes podem escolher uma das duas opções para a estratégia de RD:

1. Em um evento de RD, uma nova instância de Gerenciamento de API é provisionada, o backup é restaurado e os registros DNS são reordenados.
2. Os clientes mantêm uma cópia passiva do serviço em outra região do Azure (incorrendo em custos adicionais). Os backups são regularmente restaurados para ele. Em um evento de RD, somente os registros DNS precisam ser reordenados para restaurar o serviço.

Como os Aplicativos Lógicos podem ser recriados muito rapidamente e são sem servidor, o backup é feito salvando uma cópia do modelo associado do Azure Resource Manager. Estes podem ser salvos no controle do código-fonte/integrados ao processo de CI/CD (integração contínua/implantação contínua) do cliente.

Os Aplicativos Lógicos que foram publicados por meio do Gerenciamento de API precisarão dos locais atualizados, caso migrem para um data center diferente. Isso pode ser feito por meio de um script do PowerShell simples para atualizar a propriedade de back-end da API.

## <a name="manageability-considerations"></a>Considerações sobre capacidade de gerenciamento

Crie grupos de recursos separados para ambientes de produção, desenvolvimento e teste. Isso facilita o gerenciamento de implantações, a exclusão de implantações de teste e a atribuição de direitos de acesso.
Ao atribuir recursos para grupos de recursos, considere o seguinte:

- Ciclo de vida. Em geral, coloque recursos com o mesmo ciclo de vida no mesmo grupo de recursos.
- Acesso. É possível utilizar RBAC [Controle de Acesso Baseado em Função](../role-based-access-control/overview.md) para aplicar políticas de acesso aos recursos de um grupo.
- Cobrança. Você pode exibir os custos acumulados para o grupo de recursos.
- Tipos de preço para Gerenciamento de API – é recomendável usar o Tipo de Desenvolvedor para ambientes de desenvolvimento e teste. Para pré-produção, é recomendável implantar uma réplica do ambiente de produção, executando testes e, em seguida, desligando para minimizar o custo.

Para obter mais informações, consulte a visão geral do [grupo de recursos](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Implantação

É recomendável utilizar os [modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) para implantar o Gerenciamento de API do Azure e o Aplicativo Lógico do Azure. Os modelos facilitam a automatização das implantações por meio do PowerShell ou da CLI (interface de linha de comando) do Azure.

É recomendável colocar o Gerenciamento de API do Azure e qualquer Aplicativo Lógico individual nos próprios modelos separados do Resource Manager. Isso permitirá armazená-los nos sistemas de controle do código-fonte. Esses modelos podem ser implantados juntos ou individualmente como parte de um processo de CI/CD (integração contínua/implantação contínua).

### <a name="versions"></a>Versões

Sempre que você fizer uma alteração no Aplicativo Lógico (ou implantar uma atualização no modelo do Resource Manager), uma cópia dessa versão será mantida para sua conveniência (todas as versões que tiverem uma execução de histórico serão mantidas). É possível usar essas versões para rastrear alterações no histórico e também promover uma versão para ser a configuração atual do aplicativo lógico e, isso significa que você pode efetivamente reverter para o Aplicativo Lógico, por exemplo.

O Gerenciamento de API tem dois [conceitos de versão](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/) distintos (mas complementares):

- Versões usadas para fornecer aos consumidores de API uma opção da API que eles poderiam consumir com base em suas necessidades (por exemplo, v1, v2 ou beta, produção).
- As revisões dos Administradores de APIs fazem alterações em uma API e as implantam em usuários com comentários opcionais.

No contexto da implantação – As revisões do Gerenciamento de API devem ser consideradas como uma forma de fazer alterações com segurança, manter um histórico de alterações e conscientizar os consumidores de API sobre essas alterações. Uma revisão pode ser criada em um ambiente de desenvolvimento e implantada entre outros ambientes usando modelos do Resource Manager.

Embora as revisões possam ser utilizadas para testar a API antes que ela se torne 'atual' e tornada acessível aos usuários, não é recomendável o uso desse mecanismo para testes de integração ou carga – em vez disso, devem ser utilizados ambientes de pré-produção ou teste separados.

### <a name="configuration"></a>Configuração

Nunca verifique senhas, chaves de acesso ou cadeias de conexão no controle do código-fonte. Se isso for necessário, use a técnica apropriada para implantar e proteger esses valores. 

Nos Aplicativos Lógicos, quaisquer valores confidenciais necessários no aplicativo lógico (que não podem ser criados no formato de uma conexão) devem ser armazenados no Azure Key Vault e referenciados a partir de um modelo do Resource Manager. Além disso, é recomendável utilizar parâmetros de modelo de implantação em conjunto com arquivos de parâmetros para cada ambiente. Mais diretrizes sobre [proteger parâmetros e entradas em um fluxo de trabalho](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

No Gerenciamento de API, os segredos são gerenciados usando objetos chamados Propriedades/ Valores Nomeados. Esses armazenam com segurança valores que podem ser acessados nas políticas de Gerenciamento de API. Saiba como [ gerenciar segredos no Gerenciamento de API](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnóstico e monitoramento

Tanto o [Gerenciamento de API](../api-management/api-management-howto-use-azure-monitor.md) como os [Aplicativos Lógicos](logic-apps-monitor-your-logic-apps.md) dão suporte para monitoramento operacional por meio do [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). O Azure Monitor é habilitado por padrão e fornecerá informações com base nas diferentes métricas configuradas para cada serviço.

Além disso, há outras opções para cada serviço:

- Logs de Aplicativos Lógicos podem ser enviados para o [Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) para painéis e análise profunda.
- O Gerenciamento de API dá suporte para configuração do Application Insights para monitoramento de DevOps.
- O Gerenciamento de API dá suporte para [Modelo de solução do Power BI para API de análise personalizada](http://aka.ms/apimpbi). Esse modelo de solução permite que os clientes criem sua própria solução de análise personalizada, com relatórios disponíveis no Power BI para usuários empresariais.

## <a name="security-considerations"></a>Considerações de segurança

Esta seção lista as considerações de segurança específicas dos serviços do Azure descritas neste artigo, implantadas na arquitetura conforme descrito. Esta não é uma lista completa de práticas recomendadas de segurança.

- Use o RBAC (Controle de Acesso Baseado em Função) para garantir níveis apropriados de acesso para usuários.
- Proteger os pontos de extremidade públicos de API no Gerenciamento de API usando OAuth/ Open IDConnect. Faça isso configurando um provedor de identidade e adicionando uma política de validação do JWT.
- Conectar os serviços back-end do Gerenciamento de API usando certificados mútuos
- Proteja os Aplicativos Lógicos baseados em gatilho HTTP criando uma lista de permissões de Endereço IP que aponte para o Endereço IP do Gerenciamento de API. Isso evita chamar o aplicativo lógico da Internet pública sem primeiro passar pelo Gerenciamento de API.

Essa arquitetura de referência mostrou como criar uma plataforma de integração empresarial simples usando o Integration Services do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Enterprise Integration com Filas e Eventos](logic-apps-architectures-enterprise-integration-with-queues-events.md)
