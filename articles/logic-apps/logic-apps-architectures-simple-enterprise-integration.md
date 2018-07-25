---
title: Arquitetura de referência de integração empresarial simples do Integration Services do Azure
description: Descreve a referência mostrando como implementar um padrão de integração empresarial simples com Aplicativo Lógico do Azure e Gerenciamento de API do Azure.
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
ms.openlocfilehash: 982a5eabf8c6c3012a9b3e8fdbe2ff32ba439972
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113585"
---
# <a name="reference-architecture-simple-enterprise-integration"></a>Arquitetura de referência: integração empresarial simples

Essa arquitetura de referência mostra um conjunto de práticas comprovadas que você pode aplicar a um aplicativo de integração que usa o Integration Services do Azure. A arquitetura pode servir de base para muitos padrões diferentes de aplicativos e APIs HTTP, fluxo de trabalho e orquestração.

![Diagrama de arquitetura - integração empresarial simples](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

*Há muitos aplicativos possíveis para a tecnologia de integração. Variam de um aplicativo ponto a ponto simples a uma empresa completo de aplicativo do barramento de serviço do Microsoft Azure. A série de arquitetura descreve as partes de componente reutilizável que pode ser aplicada para criar um aplicativo de integração genérico. Arquitetos devem considerar quais componentes precisam implementar para seus aplicativos e infraestrutura.*

## <a name="architecture"></a>Arquitetura

A arquitetura tem os seguintes componentes:

- **Grupo de recursos**. Um [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) é um contêiner lógico para recursos do Azure.
- **Gerenciamento de API do Azure**. [Gerenciamento de API](https://docs.microsoft.com/azure/api-management/) é uma plataforma totalmente gerenciada que é usada para publicar, proteger e transformar APIs HTTP.
- **Portal do Desenvolvedor de Gerenciamento de API do Azure**. Cada instância do gerenciamento de API do Azure vem com acesso para o [Portal do desenvolvedor](https://docs.microsoft.com/azure/api-management/api-management-customize-styles). O portal do desenvolvedor do Gerenciamento de API fornece acesso a documentação e exemplos de códigos. Você pode testar as APIs no portal do desenvolvedor.
- **Aplicativo Lógico do Azure**. Os [Aplicativos Lógicos](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) são uma plataforma sem servidor que é usada para compilar integração e fluxo de trabalho empresarial.
- **Conectores**. Os [conectores](https://docs.microsoft.com/azure/connectors/apis-list) são usados pelos Aplicativos Lógicos para conectarem os serviços normalmente utilizados. Os Aplicativos Lógicos já possuem centenas de conectores diferentes, mas também podem ser criados usando um conector personalizado.
- **Endereço IP**. O serviço de Gerenciamento de API do Azure tem um nome de domínio e um [endereço IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) público fixo. O nome de domínio é um subdomínio do azure-api.net, como contoso.azure-api.net. Aplicativos lógicos e o barramento de serviço também têm um endereço IP público. No entanto, nessa arquitetura restringimos o acesso para chamar pontos de extremidade de aplicativos lógicos apenas para o endereço IP do Gerenciamento de API (por segurança). As chamadas para o Barramento de Serviço são protegidas por uma assinatura de acesso compartilhado (SAS).
- **DNS do Azure**. [O DNS do Azure](https://docs.microsoft.com/azure/dns/) é um serviço de hospedagem para domínios DNS. O DNS do Azure fornece resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Microsoft Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. Para usar um nome de domínio personalizado como contoso.com, crie registros DNS que mapeiem o nome de domínio personalizado para o endereço IP. Para obter mais informações, consulte [Configurar um nome de domínio personalizado no Gerenciamento de API](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain).
- **Azure Active Directory (Azure AD)**. Use o [Azure AD](https://docs.microsoft.com/azure/active-directory/) ou outro provedor de identidade para autenticação. O Azure Active Directory fornece autenticação para acessar os pontos de extremidade de API, passando um [JSON Web Token para o gerenciamento de API](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) para validar. O Azure Active Directory pode proteger o acesso ao portal do desenvolvedor do gerenciamento de API (somente em camadas Standard e Premium).

Esta arquitetura possui alguns padrões que são fundamentais para seu funcionamento:

- APIs de composição são criadas usando aplicativos lógicos. Eles orquestram chamadas para o software como sistemas de serviço (SaaS), aos serviços do Azure e para quaisquer APIs que são publicados no gerenciamento de API. [Os Aplicativos Lógicos também são publicados](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) por meio do Portal do Desenvolvedor de Gerenciamento de API.
- Aplicativos usam o Azure AD para [adquirir um token de segurança do OAuth 2.0](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) que é necessário para obter acesso a uma API.
- O Gerenciamento de API do Azure [valida o token](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) de segurança e passa a solicitação ao aplicativo lógico ou API de back-end.

## <a name="recommendations"></a>Recomendações

Seus requisitos específicos podem ser diferentes da arquitetura genérica descrita neste artigo. Use as recomendações nesta seção como um ponto inicial.

### <a name="azure-api-management-tier"></a>Camada de Gerenciamento de API do Azure

Use as camadas de gerenciamento de API básica, Standard ou Premium. As camadas oferecem um acordo de nível de serviço (SLA) de produção e suporte para escalar horizontalmente na região do Azure (o número de unidades varia por camada). A camada Premium também dá suporte para escalar horizontalmente em várias regiões do Azure. Baseie a camada que você escolheu no nível de taxa de transferência necessária e no conjunto de recursos. Para obter mais informações, consulte [preço de Gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).

Você será cobrado por todas as instâncias de Gerenciamento de API quando estiverem em execução. Caso tenha escalado horizontalmente e não precisar desse nível de desempenho o tempo todo, considere aproveitar a cobrança por hora de Gerenciamento de API e reduzir verticalmente.

### <a name="logic-apps-pricing"></a>Preço de Aplicativos Lógicos

Os Aplicativos lógicos usam um modelo [sem servidor](logic-apps-serverless-overview.md). A cobrança é calculada com base na ação e execução do conector. Para obter mais informações, consulte [Preços de Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). Atualmente, não há considerações de camada para Aplicativos Lógicos.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Aplicativos Lógicos para chamadas à API assíncronas

Os aplicativos lógicos funcionam melhor em cenários que não exigem baixa latência. Por exemplo, ela funcionará melhor para chamadas assíncronas ou semi chamadas de API de execução longa. Se for necessária baixa latência (por exemplo, uma chamada que bloqueie uma interface do usuário), é recomendável implementar essa API ou operação usando uma tecnologia. Por exemplo, use o Azure Functions ou uma API Web que você implantar usando o serviço de aplicativo do Azure. Além disso, é recomendável que essa API seja administrada usando o Gerenciamento de API para consumidores de API.

### <a name="region"></a>Região

Provisione Gerenciamento de API e Aplicativos Lógicos na mesma região para minimizar latência da rede. Em geral, escolha a região mais próxima aos usuários.

O grupo de recursos também tem uma região. A região especifica onde os metadados de implantação são armazenados e de onde o modelo de implantação é executado. Coloque o grupo de recursos e seus recursos na mesma região para melhorar a disponibilidade durante a implantação.

## <a name="scalability"></a>Escalabilidade

Os administradores de Gerenciamento de API devem adicionar [políticas de cache](../api-management/api-management-howto-cache.md), quando apropriado, para aumentar a escalabilidade do serviço e reduzir a carga nos serviços back-end. Armazenamento em cache também ajuda a reduzir a carga nos serviços de back-end.

O Gerenciamento de API do Azure Básico, Standard e Premium pode ser escalado horizontalmente em uma região do Azure para oferecer maior capacidade. Os administradores podem usar a **Métrica de Capacidade** no menu **Métricas** para analisar o uso dos serviços e escalar verticalmente ou reduzir verticalmente, conforme apropriado.

Recomendações para colocação em escala de um serviço de Gerenciamento de API:

- Necessidades de dimensionamento para levar em conta padrões de tráfego. Os clientes com padrões de tráfego mais voláteis têm maior necessidade de maior capacidade.
- Capacidade consistente acima de 66% pode indicar a necessidade de escalar verticalmente.
- Capacidade consistente abaixo de 20% pode indicar uma oportunidade para reduzir verticalmente.
- É sempre recomendável o teste de carga do serviço de Gerenciamento de API com uma carga representativa antes de habilitar carregar em produção.

Os serviços da camada Premium podem ser escalados horizontalmente em várias regiões do Azure. Os clientes que implantam, dimensionando serviços em várias regiões do Azure obtém um SLA mais alto (99,95% em comparação com 99,9%) e podem provisionar serviços próximos aos usuários em várias regiões.

Os aplicativos lógicos do modelo sem servidor querem dizer aos administradores que não é necessário planejar a escalabilidade de serviço. O serviço é dimensionado automaticamente para atender à demanda.

## <a name="availability"></a>Disponibilidade

Atualmente, o SLA para o gerenciamento de API do Azure é de 99,9% para camadas Basic, Standard e Premium. Configurações de camada Premium com implantação de pelo menos uma unidade em duas ou mais regiões têm um SLA de 99,95%.

Atualmente, o SLA para aplicativos lógicos do Azure é de 99,9%.

### <a name="backups"></a>Backups

A configuração de gerenciamento de API do Azure deve ser [feita regularmente](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (com base em regularidade de alteração). Arquivos de backup devem ser armazenados em um local ou a região do Azure que é diferente de onde o serviço reside. Os clientes podem escolher uma das duas opções para a estratégia de recuperação de desastre:

- Em um evento de recuperação de desastre, uma nova instância de Gerenciamento de API é provisionada, o backup é restaurado à nova instância, e os registros DNS são reordenados.
- Os clientes mantêm uma cópia passiva do serviço em outra região do Azure (incorrendo em custos adicionais). Os backups são regularmente restaurados para ele. Em um evento de recuperação de desastre, somente os registros DNS precisam ser reordenados para restaurar o serviço.

Como os Aplicativos Lógicos podem ser recriados muito rapidamente e são sem servidor, o backup é feito salvando uma cópia do modelo associado do Azure Resource Manager. Os modelos podem ser salvos no controle do código-fonte e integrados ao processo de integração contínua/implantação contínua CI/CD do cliente.

Se um aplicativo lógico que foi publicado por meio do gerenciamento de API for movido para um datacenter diferente, atualize a localização do aplicativo. Para atualizar a localização do aplicativo, use um script PowerShell para atualizar o**Backend** property da API.

## <a name="manageability"></a>Capacidade de gerenciamento

Crie grupos de recursos separados para ambientes de produção, desenvolvimento e teste. Usar grupos de recursos separados torna mais fácil gerenciar implantações, excluir implantações de teste a atribuir direitos de acesso.

Ao atribuir recursos para grupos de recursos, considere o seguinte:

- **Ciclo de vida**. Em geral, coloque recursos com o mesmo ciclo de vida no mesmo grupo de recursos.
- **Acesso**. É possível utilizar [controle de acesso baseado em função](../role-based-access-control/overview.md) (RBAC) para aplicar políticas de acesso aos recursos de um grupo.
- **Cobrança**. Você pode exibir os custos acumulados para o grupo de recursos.
- **Tipo de preço para o gerenciamento de API**. É recomendável usar o Tipo de Desenvolvedor para ambientes de desenvolvimento e teste. Para pré-produção, é recomendável implantar uma réplica do ambiente de produção, executando testes e, em seguida, desligando para minimizar o custo.

Para saber mais, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Implantação

É recomendável utilizar os [modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) para implantar o Gerenciamento de API e os Aplicativos Lógico do Azure. Os modelos facilitam a automatização das implantações por meio do PowerShell ou da CLI do Azure.

É recomendável colocar o Gerenciamento de API do Azure e qualquer Aplicativo Lógico individual nos próprios modelos separados do Resource Manager. Quando você usa modelos separados, você pode armazenar os recursos em sistemas de controle do código-fonte. Você também pode implantar os recursos juntos ou individualmente como parte de um processo de implantação de CI/CD.

### <a name="versions"></a>Versões

Sempre que você fizer uma alteração no Aplicativo Lógico (ou implantar uma atualização no modelo do Resource Manager), uma cópia dessa versão será mantida para sua conveniência (todas as versões que tiverem uma execução de histórico serão mantidas). É possível usar essas versões para rastrear alterações no histórico e também promover uma versão para ser a configuração atual do aplicativo lógico. Por exemplo, você pode efetivamente reverter um aplicativo lógico.

O Gerenciamento de API tem dois [conceitos de versão](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/) distintos (mas complementares):

- As versões usadas para fornecer aos consumidores de API uma opção da API que eles poderiam consumir com base em suas necessidades (por exemplo, v1, v2 ou beta, produção).
- As revisões dos Administradores de APIs para fazer alterações com segurança a uma API e, então, implantar as alterações aos usuários com comentário opcional.

No contexto da implantação, é uma boa ideia considerar as revisões do Gerenciamento de API como uma forma de fazer alterações com segurança, manter um histórico de alterações e conscientizar os consumidores de API sobre essas alterações. Uma revisão pode ser criada em um ambiente de desenvolvimento e implantada entre outros ambientes usando modelos do Resource Manager.

Embora você possa usar revisões para testar uma API antes de torná-la acessível a usuários e "atual", não recomendamos usar esse mecanismo de carga ou teste de integração. Em vez disso, use ambientes de pré-produção ou teste separado.

### <a name="configuration"></a>Configuração

Nunca verifique senhas, chaves de acesso ou cadeias de conexão no controle do código-fonte. Se isso for necessário, use a técnica apropriada para implantar e proteger esses valores. 

Nos Aplicativos Lógicos, quaisquer valores confidenciais necessários no aplicativo lógico (que não podem ser criados no formato de uma conexão) devem ser armazenados no Azure Key Vault e referenciados a partir de um modelo do Resource Manager. Além disso, é recomendável utilizar parâmetros de modelo de implantação e arquivos de parâmetros para cada ambiente. Para obter mais informações, consulte [Proteger parâmetros e entradas em um fluxo de trabalho](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

No Gerenciamento de API, os segredos são gerenciados usando objetos chamados *valores* ou *propriedades nomeadas*. Os objetos armazenam com segurança valores que podem ser acessados nas políticas de Gerenciamento de API. Para obter mais informações, consulte [Gerenciar segredos em Gerenciamento de API](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnóstico e monitoramento

O [Gerenciamento de API](../api-management/api-management-howto-use-azure-monitor.md) e os [Aplicativos Lógicos](logic-apps-monitor-your-logic-apps.md) dão suporte para monitoramento operacional por meio do [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). O Azure Monitor fornece informações com base nas métricas que são configuradas para cada serviço. O Azure Monitor é habilitado por padrão.

Essas opções também estão disponíveis para cada serviço:

- Logs de Aplicativos Lógicos podem ser enviados para o [Log Analytics do Azure](logic-apps-monitor-your-logic-apps-oms.md) para painéis e análise profunda.
- O Gerenciamento de API dá suporte para configuração do Application Insights do Azure para monitoramento de DevOps.
- O Gerenciamento de API dá suporte para [Modelo de solução do Power BI para API de análise personalizada](http://aka.ms/apimpbi). Os clientes podem usar o modelo de solução para criar sua própria solução de análises personalizadas. Os relatórios estão disponíveis no Power BI para usuários empresariais.

## <a name="security"></a>Segurança

Esta seção lista as considerações de segurança específicas dos serviços do Azure descritas neste artigo e que são implantadas na arquitetura conforme descrito. Esta não é uma lista completa de práticas recomendadas de segurança.

- Use o RBAC (Controle de Acesso Baseado em Função) para garantir níveis apropriados de acesso para usuários.
- Proteger os pontos de extremidade públicos de API no Gerenciamento de API usando OAuth/ Open IDConnect. Para proteger os pontos de extremidade de API públicos, configure um provedor de identidade e adicione uma política de validação de JSON Web Token (JWT).
- Conectar os serviços back-end do Gerenciamento de API usando certificados mútuos
- Proteja os Aplicativos Lógicos baseados em gatilho HTTP criando uma lista de permissões de Endereço IP que aponte para o Endereço IP do Gerenciamento de API. Um endereço IP na lista de permissões evita chamar o aplicativo lógico da Internet pública sem primeiro passar pelo Gerenciamento de API.

## <a name="next-steps"></a>Próximas etapas

- Saiba sobre [Enterprise Integration com Filas e Eventos](logic-apps-architectures-enterprise-integration-with-queues-events.md)
