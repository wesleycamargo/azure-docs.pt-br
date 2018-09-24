---
title: Padrão de arquitetura de integração empresarial simples – Azure Integration Services
description: Essa referência de arquitetura mostra como implementar um padrão de integração empresarial simples usando o Aplicativo Lógico do Azure e o Gerenciamento de API do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 5ed6fa9f514bae3ea651edba6702714e2680091f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955938"
---
# <a name="simple-enterprise-integration-architecture"></a>Arquitetura de integração empresarial simples

Este artigo descreve uma arquitetura de integração empresarial que usa práticas comprovadas que podem ser aplicadas a um aplicativo de integração ao usar o Azure Integration Services. Você pode usar essa arquitetura como base para muitos padrões diferentes de aplicativos que exigem APIs HTTP, fluxo de trabalho e orquestração.

![Diagrama de arquitetura - integração empresarial simples](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

A série de arquitetura descreve os blocos componentes reutilizáveis que podem ser aplicados para compilar um aplicativo de integração genérico. Como tecnologia de integração tem muitos aplicativos possíveis, variando de aplicativos ponto a ponto simples até os aplicativos empresariais completos do Barramento de Serviço do Azure, considere quais componentes você precisa implementar para seus aplicativos e infraestrutura.

## <a name="architecture-components"></a>Componentes da arquitetura

Essa arquitetura de integração empresarial inclui os seguintes componentes:

- **Grupo de recursos**: um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) é um contêiner lógico para os recursos do Azure.

- **Gerenciamento de API do Azure**: o serviço [Gerenciamento de API](https://docs.microsoft.com/azure/api-management/) é uma plataforma totalmente gerenciada para publicação, proteção e transformação de APIs HTTP.

- **Portal do Desenvolvedor do Gerenciamento de API do Azure**: cada instância do Gerenciamento de API do Azure fornece acesso ao [Portal do Desenvolvedor](../api-management/api-management-customize-styles.md). Esse portal fornece acesso à documentação e a exemplo de código. Também é possível testar as APIs no Portal do Desenvolvedor.

- **Aplicativo Lógico do Azure**: os [Aplicativos Lógicos](../logic-apps/logic-apps-overview.md) são uma plataforma sem servidor para compilar fluxos de trabalho e integrações empresariais.

- **Conectores**: os Aplicativos Lógicos usam [conectores](../connectors/apis-list.md) para conectarem aos serviços utilizados com frequência. Os Aplicativos Lógicos já oferecem centenas de conectores, mas também é possível criar um conector personalizado.

- **Endereço IP**: o serviço de Gerenciamento de API do Azure tem um nome de domínio e um [endereço IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) público fixo. O nome de domínio padrão é um subdomínio de azure-api.net, como contoso.azure-api.net, mas também é possível configurar [domínios personalizados](../api-management/configure-custom-domain.md). Aplicativos lógicos e o barramento de serviço também têm um endereço IP público. No entanto, por questão de segurança, essa arquitetura restringe o acesso para chamada de pontos de extremidade de Aplicativos Lógicos a apenas para o endereço IP do Gerenciamento de API. As chamadas para o Barramento de Serviço são protegidas por uma assinatura de acesso compartilhado (SAS).

- **DNS do Azure**: o [DNS do Azure](https://docs.microsoft.com/azure/dns/) é um serviço de hospedagem para domínios DNS. O DNS do Azure fornece resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Microsoft Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure. Para usar um nome de domínio personalizado, como contoso.com, crie registros DNS que mapeiem o nome de domínio personalizado para o endereço IP. Para obter mais informações, consulte [Configurar um nome de domínio personalizado no Gerenciamento de API](../api-management/configure-custom-domain.md).

- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/): é possível usar o **Azure AD** ou outro provedor de identidade para autenticação. O Azure Active Directory fornece autenticação para acessar os pontos de extremidade de API, passando um [JSON Web Token para o gerenciamento de API](../api-management/policies/authorize-request-based-on-jwt-claims.md) para validar. Para camadas Standard e Premium, o Azure AD pode proteger o acesso ao Portal do Desenvolvedor do Gerenciamento de API.

## <a name="patterns"></a>Padrões 

Esta arquitetura tem alguns padrões que são fundamentais para sua operação:

* As APIs compostas são compiladas usando aplicativos lógicos, que orquestram chamadas para sistemas SaaS (software como serviço), serviços do Azure e quaisquer APIs publicadas no Gerenciamento de API. Os aplicativos lógicos também são [publicados por meio do Portal do Desenvolvedor de Gerenciamento de API](../api-management/import-logic-app-as-api.md).

* Aplicativos usam o Azure AD para [adquirir um token de segurança do OAuth 2.0](../api-management/api-management-howto-protect-backend-with-aad.md) que é necessário para obter acesso a uma API.

* O Gerenciamento de API do Azure [valida o token](../api-management/api-management-howto-protect-backend-with-aad.md) de segurança e passa a solicitação ao aplicativo lógico ou API de back-end.

## <a name="recommendations"></a>Recomendações

Seus requisitos específicos podem ser diferentes da arquitetura genérica descrita neste artigo. Use as recomendações nesta seção como um ponto inicial.

### <a name="azure-api-management-tier"></a>Camada de Gerenciamento de API do Azure

Use as camadas de gerenciamento de API básica, Standard ou Premium. Essas camadas oferecem um SLA (Contrato de Nível de Serviço) de produção e são compatíveis com escala horizontal dentro da região do Azure. O número de unidades varia por camada. A camada Premium também é compatível com escala horizontal entre várias regiões do Azure. Escolha sua camada com base no conjunto de recursos e o nível de taxa de transferência necessário. Para obter mais informações, consulte [preço de Gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).

Você será cobrado por todas as instâncias de Gerenciamento de API quando estiverem em execução. Caso tenha expandido e não precise desse nível de desempenho o tempo todo, aproveite a cobrança por hora do Gerenciamento de API e reduza.

### <a name="logic-apps-pricing"></a>Preço de Aplicativos Lógicos

Os Aplicativos lógicos usam um modelo [sem servidor](../logic-apps/logic-apps-serverless-overview.md). A cobrança é calculada com base na ação e execução do conector. Para obter mais informações, consulte [Preços de Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). Atualmente, não há considerações de camada para Aplicativos Lógicos.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Aplicativos Lógicos para chamadas à API assíncronas

Os Aplicativos Lógicos funcionam melhor em cenários que não exigem baixa latência. Por exemplo, os Aplicativos Lógicos funcionarão melhor para chamadas à API assíncronas ou de duração razoavelmente longa. Se for necessário ter baixa latência, por exemplo, uma chamada que bloqueia uma interface do usuário, é recomendável implementar sua API ou operação usando uma tecnologia diferente. Por exemplo, use o Azure Functions ou uma API Web que você implantar usando o serviço de aplicativo do Azure. Use o Gerenciamento de API para administrar a API para seus consumidores de API.

### <a name="region"></a>Região

Para minimizar a latência de rede, escolha a mesma região para o Gerenciamento de API, os Aplicativos Lógicos e o Barramento de Serviço. Em geral, escolha a região mais próxima dos usuários.

O grupo de recursos também tem uma região. Essa região especifica onde os metadados de implantação são armazenados e onde o modelo de implantação é executado. Para melhorar a disponibilidade durante a implantação, coloque o grupo de recursos e os respectivos recursos na mesma região.

## <a name="scalability"></a>Escalabilidade

Para elevar a escalabilidade ao administrar um serviço de Gerenciamento de API, adicione [políticas de cache](../api-management/api-management-howto-cache.md) conforme apropriado. Armazenamento em cache também ajuda a reduzir a carga nos serviços de back-end.

Para oferecer maior capacidade, aumente as camadas Básica, Standard e Premium do Gerenciamento de API em uma região do Azure. Para analisar o uso para seu serviço, no menu **Métrica**, selecione a opção **Métricas de Capacidade** e aumente ou reduza, conforme apropriado.

Recomendações para colocação em escala de um serviço de Gerenciamento de API:

- Considere os padrões de tráfego ao dimensionar. Os clientes com padrões de tráfego mais voláteis precisam de mais capacidade.

- Capacidade consistente acima de 66% pode indicar a necessidade de expansão.

- Capacidade consistente abaixo de 20% pode indicar uma oportunidade para reduzir verticalmente.

- Antes de habilitar a carga em produção, sempre realize teste de carga no seu serviço do Gerenciamento de API com uma carga representativa.

É possível expandir os serviços da camada Premium entre diversas regiões do Azure. Se você implantar por meio de colocação em escala em várias regiões do Azure, será possível obter um SLA mais alto (99,95% em comparação com 99,9%) e provisionar serviços próximos aos usuários em várias regiões.

Em modelos sem servidor dos Aplicativos Lógicos, os administradores não precisam planejar a escalabilidade do serviço. O serviço é dimensionado automaticamente para atender à demanda.

## <a name="availability"></a>Disponibilidade

* Para as camadas Básica, Standard e Premium, o SLA (Contrato de Nível de Serviço) para o Gerenciamento de API do Azure é atualmente 99,9%. Para configurações da camada Premium com implantação de pelo menos uma unidade em duas ou mais regiões, o SLA é de 99,95%.

* Atualmente, o SLA para Aplicativo Lógico do Azure é de 99,9%.

### <a name="backups"></a>Backups

Com base na regularidade da alteração, [faça backups regularmente](../api-management/api-management-howto-disaster-recovery-backup-restore.md) da sua configuração do Gerenciamento de API do Azure. Armazene seus arquivos de backup em um local ou a região do Azure que seja distinto de onde o serviço reside. Escolha uma dessas opções como a estratégia de recuperação de desastre:

* No caso de um evento de recuperação de desastre, provisione uma nova instância de Gerenciamento de API, restaure o backup para a nova instância e reordene os registros de DNS.

* Mantenha uma cópia passiva do serviço em outra região do Azure, o que incorre em custos adicionais. Restaure backups regularmente para essa cópia. Para restaurar o serviço durante um evento de recuperação de desastre, será preciso apenas reordenar os registros DNS.

Como é possível recriar rapidamente os aplicativos lógicos, que são sem servidor, faça backup deles salvando uma cópia do modelo do Azure Resource Manager associado. É possível salvar modelos no controle do código-fonte, bem como integrar os modelos ao seu processo de CI/CD (integração/ implantação contínua).

Se um aplicativo lógico foi publicado por meio do Gerenciamento de API do Azure, e o aplicativo lógico é mudado para outro data center, atualize a localização do aplicativo. É possível atualizar a propriedade de **back-end** da API usando um script básico do PowerShell.

## <a name="manageability"></a>Capacidade de gerenciamento

Crie grupos de recursos separados para ambientes de produção, desenvolvimento e teste. Ter grupos de recursos separados facilita o gerenciamento de implantações, a exclusão de implantações de teste a atribuição de direitos de acesso.

Ao atribuir recursos a grupos de recursos, considere os seguintes fatores:

* **Ciclo de vida**: em geral, coloque recursos com o mesmo ciclo de vida no mesmo grupo de recursos.

* **Acesso**: para aplicar as políticas de acesso aos recursos de um grupo, utilize o [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md).

* **Cobrança**: é possível exibir os custos de rollup para o grupo de recursos.

* **Tipos de preço para Gerenciamento de API**: use o tipo de Desenvolvedor para ambientes de desenvolvimento e teste. Para minimizar os custos durante a pré-produção, implante uma réplica do seu ambiente de produção, execute os testes e desligue.

Para saber mais, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="deployment"></a>Implantação

* Para implantar o Gerenciamento de API e os Aplicativos Lógicos, use os [modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Os modelos facilitam a automatização das implantações por meio do PowerShell ou da CLI do Azure.

* Coloque o Gerenciamento de API e os aplicativos lógicos individuais em seus próprios modelos do Resource Manager separados. Usando modelos separados, é possível armazenar os recursos em sistemas de controle do código-fonte. Você pode implantar esses modelos juntos ou individualmente como parte de um processo de CI/CD (integração contínua/implantação contínua).

### <a name="versions"></a>Versões

Sempre que você fizer uma alteração na configuração do aplicativo lógico ou implantar uma atualização por meio do modelo do Resource Manager, o Azure manterá uma cópia dessa versão para sua conveniência, e todas as versões que tiverem uma execução de histórico serão mantidas. Você pode usar essas versões para controlar as alterações históricas ou promover uma versão como a configuração atual do aplicativo lógico. Por exemplo, você pode efetivamente reverter um aplicativo lógico.

O Gerenciamento de API do Azure tem esses [conceitos de versão](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/) distintos, mas complementares:

* Versões que fornecem aos consumidores da API a capacidade de escolher uma versão de API com base nas necessidades, por exemplo, v1, v2, beta ou produção

* Revisões que permitem aos administradores de API fazerem alterações com segurança em uma API e, então, implantar as alterações nos usuários com comentário opcional

Para a implantação, considere as revisões do Gerenciamento de API como uma maneira de fazer as alterações com segurança, manter um histórico de alterações e comunicar essas alterações para os consumidores da sua API. É possível fazer uma revisão em um ambiente de desenvolvimento e implantar essa alteração em outros ambientes usando os modelos do Resource Manager.

Embora seja possível usar revisões para testar uma API antes de tornar essas alterações “atuais” e acessíveis para os usuários, esse método não é o recomendado para testes de carga e integração. Em vez disso, use ambientes de pré-produção ou teste separado.

### <a name="configuration-and-sensitive-information"></a>Configuração e informações confidenciais

Nunca verifique senhas, chaves de acesso ou cadeias de conexão no controle do código-fonte. Se tais valores forem necessários, proteja e implante-os usando as técnicas apropriadas. 

Nos Aplicativos Lógicos, se um aplicativo lógico exigir qualquer valor confidencial que não é possível ser criado dentro de uma conexão, armazene tais valores no Azure Key Vault e faça referência a eles de um modelo do Resource Manager. Use os parâmetros de modelo de implantação e arquivos de parâmetros para cada ambiente. Para obter mais informações, consulte [Proteger parâmetros e entradas em um fluxo de trabalho](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

O Gerenciamento de API administra os segredos usando objetos chamados *valores* ou *propriedades nomeadas*. Esses objetos armazenam com segurança valores que podem ser acessados por meio das políticas de Gerenciamento de API. Para obter mais informações, consulte [Gerenciar segredos em Gerenciamento de API](../api-management/api-management-howto-properties.md).

## <a name="diagnostics-and-monitoring"></a>Diagnóstico e monitoramento

É possível usar o [Azure Monitor](../azure-monitor/overview.md) paras monitoramento operacional tanto no [Gerenciamento de API](../api-management/api-management-howto-use-azure-monitor.md) quanto nos [Aplicativos Lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md). O Azure Monitor fornece informações com base nas métricas que configuradas para cada serviço e é habilitado por padrão.

Cada serviço também tem essas opções:

* Para análise e painéis mais aprofundados, é possível enviar os logs dos Aplicativos Lógicos para o [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md).

* Para monitoramento de DevOps, é possível configurar o Azure Application Insights para o Gerenciamento de API.

* O Gerenciamento de API dá suporte para [Modelo de solução do Power BI para API de análise personalizada](http://aka.ms/apimpbi). Use esse modelo de solução para criar sua própria solução de análise. Para usuários empresariais, o Power BI disponibiliza relatórios.

## <a name="security"></a>Segurança

Embora essa lista não descreva completamente todas as práticas recomendadas de segurança, vejas essas considerações de segurança que se aplicam especificamente aos serviços do Azure implantados na arquitetura descrita neste artigo:

* Para garantir que os usuários tenham níveis de acesso apropriados, use o RBAC (controle de acesso baseado em função).

* Proteja os pontos de extremidade de API públicos no Gerenciamento de API usando OAuth ou OpenID Connect. Para proteger os pontos de extremidade de API públicos, configure um provedor de identidade e adicione uma política de validação de JWT (JSON Web Token).

* Conectar os serviços back-end do Gerenciamento de API usando certificados mútuos

* Proteja os Aplicativos Lógicos baseados em gatilho HTTP criando uma lista de permissões de Endereço IP que aponte para o Endereço IP do Gerenciamento de API. Um endereço IP na lista de permissões evita chamar o aplicativo lógico da Internet pública sem primeiro passar pelo Gerenciamento de API.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Integração empresarial com filas e eventos](../logic-apps/logic-apps-architectures-enterprise-integration-with-queues-events.md)
