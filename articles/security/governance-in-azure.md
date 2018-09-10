---
title: Governança no Azure | Microsoft Docs
description: Saiba mais sobre os serviços de computação baseados em nuvem que podem ser escalados ou reduzidos verticalmente para atender às necessidades de seu aplicativo ou sua empresa.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 579e900ee6616af8fd197e501364acd8e18d3e37
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970516"
---
# <a name="governance-in-azure"></a>Governança no Azure

O Azure fornece muitas opções de segurança e a habilidade de controlá-las, de modo que você possa atender aos requisitos exclusivos das implantações de sua organização.

A governança de nuvem do Azure refere-se aos processos de tomada de decisão, critérios e políticas envolvidos no planejamento, na arquitetura, na aquisição, na implantação, na operação e no gerenciamento da computação em nuvem. A governança de nuvem do Azure fornece uma abordagem de auditoria e consultoria integradas para examinar e fornecer consultoria às organizações sobre seu uso da plataforma Azure. 

Para criar um plano para a governança de nuvem do Azure, você precisa fazer um exame minucioso sobre as pessoas, os processos e as tecnologias agora em vigor. Em seguida, você pode criar estruturas que facilitam para a TI dar suporte consistente às necessidades de negócios, ao mesmo tempo em que proporciona aos usuários a flexibilidade para usar os recursos do Azure.

## <a name="implementation-of-policies-processes-and-standards"></a>Implementação de políticas, processos e padrões 

A gerência estabeleceu funções e responsabilidades para supervisionar a implementação das políticas de segurança da informação e de continuidade operacional no Azure. O gerenciamento do Azure é responsável por supervisionar as práticas de segurança e de continuidade em suas respectivas equipes (incluindo terceiros). Ele também facilita a conformidade com políticas de segurança, processos e padrões.

### <a name="account-provisioning"></a>Provisionamento de conta

A definição da hierarquia de contas é uma etapa importante para usar e estruturar os serviços do Azure em uma empresa. É a estrutura de governança principal. Os clientes que têm um EA (Contrato Enterprise) podem subdividir o ambiente em departamentos, contas e assinaturas.

![Provisionamento de conta](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Caso não tenha um Contrato Enterprise, considere o uso de [marcas do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) no nível da assinatura para definir a hierarquia. Uma assinatura do Azure é a unidade básica que contém todos os recursos. Ela também define vários limites no Azure, como o número de núcleos e recursos. As assinaturas podem conter [grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), que podem conter recursos. Os princípios do [RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/overview) se aplicam nesses três níveis.

Cada empresa é diferente. Para empresas não corporativas, a hierarquia do uso de marcas do Azure permite a flexibilidade em como o Azure é organizado. Antes de implantar os recursos no Azure, você deve modelar uma hierarquia e entender o impacto na cobrança, o acesso a recursos e a complexidade.

### <a name="subscription-controls"></a>Controles de assinatura

As assinaturas determinam como o uso de recursos é relatado e cobrado. Você pode configurar assinaturas para cobrança e pagamento separados. Uma conta do Azure pode ter várias assinaturas. As assinaturas podem ser usadas para determinar o uso de recursos do Azure de vários departamentos em uma empresa.

Por exemplo, uma empresa tem departamentos de TI, RH e Marketing, e esses departamentos estão executando diferentes projetos. A empresa pode basear sua cobrança no uso de cada departamento dos recursos do Azure, como máquinas virtuais. A empresa pode então controlar as finanças de cada departamento.

As assinaturas do Azure estabelecem três parâmetros:

- ID exclusiva do assinante

- Local de cobrança

- Conjunto de recursos disponíveis

Para um indivíduo, esses parâmetros incluem uma ID de conta Microsoft, um número de cartão de crédito e o pacote completo de serviços do Azure. A Microsoft impõe limites de consumo, dependendo do tipo de assinatura.

Hierarquias de registro do Azure definem como os serviços são estruturados dentro de um Contrato Empresarial. O portal do Contrato Enterprise permite aos clientes dividir o acesso aos recursos do Azure associados a um Contrato Enterprise com base em hierarquias flexíveis que são personalizáveis de acordo com as necessidades de uma organização. O padrão de hierarquia deve corresponder à estrutura geográfica e de gerenciamento de uma organização para levar em conta o acesso de recursos e de cobrança associados.

Os três padrões de hierarquia de alto nível são funcional, unidade de negócios e geográfico. Os departamentos são um constructo administrativo para agrupamentos de contas. Em cada departamento, as contas podem receber assinaturas, as quais criam silos de cobrança e vários limites de chave no Azure (por exemplo, número de VMs e contas de armazenamento).

![Controles de assinatura](./media/governance-in-azure/security-governance-in-azure-fig2.png)


Para organizações com um Acordo Empresarial, as assinaturas do Azure seguem uma hierarquia de quatro níveis:

1. Administrador de registro corporativo

2. Administrador de departamento

3. Proprietário da conta

4. Administrador de serviço

Essa hierarquia rege o seguinte:

- Relação de cobrança.

- Administração de conta.

- Acesso aos recursos por meio do RBAC.

- Limites:

  - Uso e cobrança (cartão de taxa baseada nos números de oferta)

  - limites

  - Rede virtual

- Anexo ao Azure AD (Azure Active Directory). Uma instância do Azure AD pode ser associada a muitas assinaturas.

- Associação a uma conta com Registro Enterprise.

### <a name="role-based-access-control"></a>Controle de acesso baseado em função

O [RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) permite o gerenciamento de acesso detalhado de recursos no Azure. Usando o RBAC, você pode conceder apenas a quantidade de acesso de que os usuários precisam para realizar seus trabalhos. As empresas devem se concentrar em fornecer aos funcionários as permissões exatas necessárias. Muitas permissões expõem uma conta a ataques. Permissões insuficientes fazem com que os funcionários não consigam trabalhar com eficiência. 

Em vez de apresentar todos irrestrito permissões em sua assinatura do Azure ou recursos, você pode permitir apenas determinadas ações. Por exemplo, use o RBAC para permitir que um funcionário gerencie as máquinas virtuais em uma assinatura, enquanto outro pode gerenciar bancos de dados SQL na mesma assinatura.

Para conceder acesso, você pode atribuir funções a usuários, grupos ou aplicativos em determinado escopo. O escopo de uma atribuição de função pode ser uma assinatura, um grupo de recursos ou um único recurso. Uma função atribuída a um escopo pai também concede acesso aos filhos contidos nele. Por exemplo, um usuário com acesso a um grupo de recursos pode gerenciar todos os recursos que ele contém, como sites, máquinas virtuais e sub-redes. Em cada assinatura, você pode criar até 2.000 atribuições de função.

Uma função é uma coleção de permissões, e o RBAC tem várias funções internas. As seguintes funções internas se aplicam a todos os tipos de recurso:

- O **Proprietário** tem acesso completo a todos os recursos, incluindo o direito de delegar acesso a outros usuários.

- **Colaborador** pode criar e gerenciar todos os tipos de recursos do Azure, mas não pode conceder acesso a outras pessoas.

- A permissão **Leitor** pode exibir todos os recursos do Azure.

As demais funções internas do Azure permitem o gerenciamento de recursos específicos do Azure. Por exemplo, a função Colaborador de Máquina Virtual permite que o usuário crie e gerencie máquinas virtuais. Para obter uma lista de todas as funções internas e suas operações, consulte [Funções RBAC internas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

O RBAC dá suporte às operações de gerenciamento dos recursos do Azure no portal do Azure e nas APIs do Azure Resource Manager. Na maioria dos casos, o RBAC não pode autorizar operações no nível dos dados para recursos do Azure. Para obter informações sobre como o RBAC está sendo estendido para operações de dados, confira [Noções básicas sobre definições de função](https://docs.microsoft.com/azure/role-based-access-control/role-definitions).

Se as funções internas não atenderem às suas necessidades específicas de acesso, [crie uma função personalizada](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Assim como as funções internas, as funções personalizadas podem ser atribuídas a usuários, grupos e aplicativos na assinatura, no grupo de recursos e no escopo do recurso. Crie funções personalizadas usando o [Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), a [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) e a [API REST](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

### <a name="resource-management"></a>Gerenciamento de recursos

O Azure fornece dois modelos de implantação: [clássico](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) e o Azure Resource Manager.

No modelo clássico, cada recurso existe de maneira independente. Não há nenhuma maneira de agrupar recursos relacionados. Você precisa acompanhar manualmente quais recursos estavam incluídos na solução ou no aplicativo e se lembrar de gerenciá-los em uma abordagem coordenada. A unidade básica de gerenciamento é a assinatura. É difícil dividir os recursos em uma assinatura, o que leva à criação de um grande número de assinaturas.

O modelo de implantação do Resource Manager inclui o conceito de um [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Um grupo de recursos é um contêiner de recursos que compartilham um ciclo de vida comum. Ele pode incluir todos os recursos da solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização.

O modelo de implantação do Gerenciador de Recursos fornece vários benefícios:

- Você pode implantar, gerenciar e monitorar todos os serviços da sua solução como um grupo, em vez de tratá-los individualmente.

- Você pode implantar a solução repetidamente em todo o seu ciclo de vida e com a certeza de que seus recursos serão implantados em um estado consistente.

- Você pode aplicar o controle de acesso a todos os recursos do grupo de recursos. Essas políticas são aplicadas automaticamente quando novos recursos são adicionados ao grupo de recursos.

- Você pode aplicar marcas aos recursos para organizar de modo lógico todos os recursos em sua assinatura.

- Você pode usar a notação JSON (JavaScript Object Notation) para definir a infraestrutura de sua solução. O arquivo JSON é conhecido como um modelo do Resource Manager.

- Você pode definir as dependências entre os recursos para que eles sejam implantados na ordem correta.

![Gerenciador de Recursos](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Para recomendações sobre modelos, consulte [Práticas recomendadas para criar modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

O Azure Resource Manager analisa as dependências para ajudar a garantir que os recursos sejam criados na ordem correta. Se um recurso depender de um valor de outro recurso (como uma máquina virtual que precisa de uma conta de armazenamento para discos), [defina uma dependência](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies) no modelo.

Você também pode usar o modelo para atualizações de infraestrutura. Por exemplo, você pode adicionar um recurso à sua solução e adicionar regras de configuração para os recursos que já foram implantados. Se o modelo especificar a criação de um recurso, mas esse recurso já existir, o Resource Manager fará uma atualização em vez de criar um ativo. O Resource Manager atualiza o ativo existente para o mesmo estado de um novo.

O Resource Manager fornece extensões para cenários em que você precisa de mais operações, como a instalação de um software que não está incluído na instalação.

### <a name="resource-tracking"></a>Acompanhamento de recursos

Conforme os usuários na organização adicionam recursos à assinatura, torna-se cada vez mais importante associar recursos ao departamento, ao cliente e ao ambiente apropriados. Você pode anexar metadados a recursos por meio de [marcações](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags). Você pode usar marcações para fornecer informações sobre o recurso ou o proprietário. As marcas permitem que você não somente agregue e agrupe recursos de diversas maneiras, mas também use esses dados com o propósito de estorno.

Use marcações quando você tiver uma coleção complexa de recursos e de grupos de recursos e precisar visualizar os ativos da maneira que fizer mais sentido para você. Por exemplo, marque os recursos que desempenham uma função semelhante em sua organização ou que pertencem ao mesmo departamento.

Sem marcas, os usuários de sua organização podem criar vários recursos que podem ser difíceis de serem identificados e gerenciados mais tarde. Por exemplo, você pode desejar excluir todos os recursos de um projeto. Se esses recursos não estão marcados para o projeto, você precisa encontrá-los manualmente. A marcação pode ser uma maneira importante de reduzir custos desnecessários em sua assinatura.

Os recursos não precisam residir no mesmo grupo de recursos para compartilharem uma marcação. Você pode criar sua própria taxonomia de marcação para garantir que todos os usuários de sua organização usem marcas comuns, em vez de aplicarem marcações ligeiramente diferentes inadvertidamente (como "dept" em vez de "departamento").

Políticas de recursos permitem que você crie regras padrão para sua organização. Você pode criar políticas que garantem que os recursos sejam marcados com os valores apropriados.

Você também pode exibir os recursos marcados por meio do portal do Azure. O [relatório de uso](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) de sua assinatura inclui nomes de marcações e valores, o que possibilita o detalhamento dos custos por marcações.

Para obter mais informações sobre marcas, confira [Iniciativa de política de marcas de cobrança](../azure-policy/scripts/billing-tags-policy-init.md).

As seguintes limitações se aplicam a marcas:

- Cada recurso ou grupo de recursos pode ter um máximo de 15 pares chave/valor de marca. Essa limitação se aplica somente a marcas aplicadas diretamente ao grupo de recursos ou recurso. Um grupo de recursos pode conter muitos recursos que possuem 15 pares de chave/valor de marca.

- O nome da marca é limitado a 512 caracteres.

- O valor da marca é limitado a 256 caracteres.

- Marcas aplicadas ao grupo de recursos não são herdadas pelos recursos desse grupo de recursos.

Se você tiver mais de 15 valores que você precisa associar a um recurso, use uma cadeia de caracteres JSON para o valor da marca. A cadeia de caracteres JSON pode conter vários valores que são aplicados a uma única chave de marca.

#### <a name="tags-for-billing"></a>Marcas para cobrança

Marcas permitem que você agrupe os dados de cobrança. Por exemplo, se estiver executando várias VMs para organizações diferentes, use marcas para agrupar o uso por centro de custo. Você também pode usar marcas para categorizar os custos pelo ambiente de tempo de execução, como por exemplo, o uso de cobrança para VMs em execução no ambiente de produção.

Você pode recuperar as informações sobre as marcações por meio das [APIs RateCard e Uso de Recursos do Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) ou do arquivo de uso CSV (com valores separados por vírgula). Baixe o arquivo de uso no [portal de contas do Azure](https://account.windowsazure.com/) ou no [portal de EA](https://ea.azure.com/).

Para saber mais sobre o acesso programático às informações de cobrança, confira [Obter informações sobre o consumo de recursos do Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). Para operações de API REST, confira [Referência da API REST de cobrança do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Quando você baixa o CSV de uso para serviços que dão suporte a marcações com cobrança, as marcações aparecerão na coluna Marcações.

### <a name="critical-resource-controls"></a>Controle de recursos críticos

À medida que sua organização adiciona serviços essenciais à assinatura, torna-se cada vez mais importante garantir que esses serviços estejam disponíveis para evitar a interrupção dos negócios. Os [Bloqueios de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) permitem restringir operações nos recursos de alto valor, onde sua modificação ou exclusão teria um impacto significativo nos aplicativos ou na infraestrutura da nuvem. Você pode aplicar bloqueios a uma assinatura, um grupo de recursos ou um recurso. Normalmente, você aplica bloqueios a recursos fundamentais, como redes virtuais, gateways e contas de armazenamento.

Atualmente, os bloqueios de recursos dão suporte a dois valores: **CanNotDelete** e **ReadOnly**. **CanNotDelete** significa que os usuários (com os direitos apropriados) ainda podem ler ou modificar um recurso, mas não podem excluí-lo. **ReadOnly** significa que usuários autorizados não podem excluir nem modificar um recurso.

Os bloqueios de recurso se aplicam apenas às operações que ocorrem no plano de gerenciamento, que consistem em operações enviadas para <https://management.azure.com>. Os bloqueios não restringem a maneira como os recursos executam suas próprias funções. Alterações de recursos são restritas, mas as operações de recursos não são restritas. Por exemplo, um bloqueio **ReadOnly** em um Banco de Dados SQL impede que você exclua ou modifique o banco de dados, mas não impede que você crie, atualize ou exclua dados no banco de dados.

A aplicação de **ReadOnly** pode gerar resultados inesperados, pois algumas operações que parecem operações de leitura exigem ações adicionais. Por exemplo, aplicar um bloqueio **ReadOnly** em uma conta de armazenamento impedirá que todos os usuários listem as chaves. A operação de listagem de chaves é tratada por meio de uma solicitação POST, pois as chaves retornadas estão disponíveis para operações de gravação.

![Controle de recursos críticos](./media/governance-in-azure/security-governance-in-azure-fig5.png)

Para outro exemplo, a aplicação de um bloqueio **ReadOnly** em um recurso do Serviço de Aplicativo do Azure impede que o Gerenciador de Servidores do Visual Studio Server Explorer exiba os arquivos para o recurso, pois essa interação exige o acesso de gravação.

Ao contrário do controle de acesso baseado em função, é possível usar bloqueios de gerenciamento para aplicar uma restrição a todos os usuários e a todas as funções. Para saber mais sobre como configurar permissões, confira [Gerenciar o acesso usando o RBAC e o portal do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Quando você aplica um bloqueio a um escopo pai, todos os recursos filho herdam o mesmo bloqueio. Até mesmo os recursos que você adiciona posteriormente herdam o bloqueio do pai. O bloqueio mais restritivo na herança terá precedência.

Para criar ou excluir bloqueios de gerenciamento, é necessário ter acesso às ações Microsoft.Authorization/ ou Microsoft.Authorization/locks/. Das funções internas, somente Proprietário e Administrador do Acesso de Usuário recebem essas ações.

### <a name="api-access-to-billing-information"></a>API de Acesso a Informações De Cobrança

Use APIs de cobrança do Azure para efetuar pull de dados de uso e de recurso em suas ferramentas de análise de dados preferidas. As APIs RateCard e de Uso de Recursos do Azure e podem ajudá-lo a prever e gerenciar seus custos com precisão. As APIs são implementadas como um provedor de recursos e como parte da família de APIs expostas pelo Azure Resource Manager.

#### <a name="resource-usage-api-preview"></a>API de Uso de Recursos (versão prévia)

Use as [API de Uso de Recursos](https://msdn.microsoft.com/library/azure/mt219003) do Azure para obter seus dados de consumo estimado do Azure. A API inclui:

- **RBAC**: configure as políticas de acesso no [portal do Azure](https://portal.azure.com/) ou por meio dos [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem obter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor de Fatura, Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura específica do Azure.

- **Agregações diárias ou por hora**: os chamadores podem especificar se desejam obter seus dados de uso do Azure em incrementos diários ou por hora. O padrão é diário.

- **Metadados de instância (inclui marcas de recurso)**: obtenha detalhes no nível da instância como o URI de recurso totalmente qualificado (/subscriptions/{subscription-id} /..), as informações do grupo de recursos e marcas de recurso. Esses metadados ajudam você a alocar de forma determinista e programática o uso pelas marcas, para casos de uso como cobrança cruzada.

- **Metadados de recurso**: detalhes de recurso como nome do medidor, categoria do medidor, subcategoria do medidor, unidade e região proporcionam ao chamador um melhor entendimento do que foi consumido. Também estamos trabalhando para alinhar a terminologia de metadados de recurso entre o portal do Azure, o CSV de uso do Azure, o CSV de cobrança do EA e outras experiências públicas, para ajudar você a correlacionar os dados entre as experiências.

- **Uso para todos os tipos de oferta**: os dados de uso estão disponíveis para todos os tipos de oferta, incluindo Pagamento Conforme o Uso, MSDN, Compromisso Monetário, Crédito Monetário e EA.

#### <a name="resource-ratecard-api"></a>API de RateCard de Recursos

Use a API RateCard de Recursos do Azure para obter a lista de recursos do Azure disponíveis, juntamente com as informações estimadas de preço para cada um deles. A API inclui:

- **RBAC**: configure as políticas de acesso no portal do Azure ou por meio dos cmdlets do Azure PowerShell para especificar quais usuários ou aplicativos podem obter acesso aos dados do RateCard. Os chamadores devem usar tokens padrão do Azure Active Directory para autenticação. Adicione o chamador à função Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma assinatura específica do Azure.

- **Suporte para ofertas de Pagamento Conforme o Uso, MSDN, Compromisso Monetário e Crédito Monetário (exceto EA)**: essa API fornece informações de taxa no nível da oferta do Azure. O chamador dessa API deve passar as informações de oferta para obter taxas e detalhes do recurso. Atualmente, não há suporte para o EA porque as ofertas do EA têm taxas personalizadas por registro. 

#### <a name="scenarios"></a>Cenários

A combinação das APIs de Uso e de RateCard possibilita estes cenários:

- **Entender os gastos com o Azure durante o mês**: use a combinação das APIs de Uso e de RateCard para obter melhores insights sobre seus gastos com a nuvem durante o mês. Analise as estimativas de uso e encargo diário e por hora.

- **Configurar alertas**: use as APIs de Uso e de RateCard para obter estimativas de consumo e encargos de nuvem e configurar alertas baseados em recursos ou em crédito monetário.

- **Previsão de fatura**: obtenha a estimativa de consumo e de gastos com a nuvem e aplique algoritmos de aprendizado de máquina para prever qual será sua fatura ao final do ciclo de cobrança.

- **Realizar uma análise de custo de pré-consumo**: use a API de RateCard para prever o valor da fatura para seu uso esperado ao mover suas cargas de trabalho para o Azure. Se você tem cargas de trabalho existentes em outras nuvens ou nuvens privadas, você também pode mapear seu uso com as taxas do Azure para obter uma melhor estimativa do seu gasto com o Azure. Essa estimativa fornece a você a capacidade de dinamizar a oferta e comparar entre os diferentes tipos de oferta, além do Pagamento Conforme o Uso, como o Compromisso Monetário e o Crédito Monetário.

- **Realizar uma análise de hipóteses**: determine se é mais econômico executar cargas de trabalho em outra região ou em outra configuração do recurso do Azure. Os custos dos recursos do Azure podem variar de acordo com a região do Azure que você está usando. Você também pode determinar se outro tipo de oferta do Azure oferece uma melhor taxa em um recurso do Azure.

### <a name="networking-controls"></a>Controles de rede

O acesso a recursos pode ser interno (dentro da rede da empresa) ou externo (por meio da Internet). É fácil para os usuários em sua organização colocar recursos inadvertidamente no lugar errado e, potencialmente, abri-los para o acesso mal-intencionado. Assim como acontece com dispositivos locais, as empresas devem adicionar os controles adequados para garantir que os usuários do Azure tomam as decisões certas.

![Controles de rede](./media/governance-in-azure/security-governance-in-azure-fig6.png)

Para a governança da assinatura, os recursos principais a seguir fornecem um controle básico de acesso.

#### <a name="network-connectivity"></a>Conectividade de rede

[Redes virtuais](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) são objetos de contêiner para sub-redes. Embora não seja estritamente necessária, uma rede virtual geralmente é usada ao conectar aplicativos aos recursos corporativos internos. O serviço Rede Virtual do Azure permite que você conecte com segurança os recursos do Azure com redes virtuais.

Uma rede virtual é uma representação da sua própria rede na nuvem. Uma rede virtual é um isolamento lógico da nuvem do Azure dedicada à sua assinatura. Você também pode conectar redes virtuais à sua rede local.

Estas são as funcionalidades para as redes virtuais do Azure:

- **Isolamento**: as redes virtuais são isoladas entre si. Crie redes virtuais separadas para desenvolvimento, teste e produção que usam os mesmos blocos de endereço CIDR. Por outro lado, crie várias redes virtuais que usam blocos de endereço CIDR diferentes e conectam as redes. É possível segmentar uma rede virtual em várias sub-redes. O Azure fornece uma resolução de nomes interna para VMs e instâncias de função dos Serviços de Nuvem do Azure conectadas a uma rede virtual. Outra opção é configurar uma rede virtual para usar seus próprios servidores DNS, em vez de usar a resolução de nome interna do Azure.

- **Conectividade com a Internet**: todas as máquinas virtuais do Azure e instâncias de função dos Serviços de Nuvem conectadas a uma rede virtual têm acesso à Internet, por padrão. Você também pode habilitar o acesso de entrada a recursos específicos, conforme o necessário.

- **Conectividade de recursos do Azure**: conecte recursos do Azure, como Serviços de Nuvem e VMs, à mesma rede virtual. Os recursos podem se conectar usando endereços IP privados, mesmo se estão em sub-redes diferentes. O Azure fornece roteamento padrão entre sub-redes, redes virtuais e redes locais e, portanto, você não precisa configurar e gerenciar rotas.

- **Conectividade de rede virtual**: conecte redes virtuais entre si. Os recursos que estão conectados a qualquer rede virtual podem, em seguida, se comunicar com recursos de qualquer outra rede virtual.

- **Conectividade local**: conecte redes virtuais a redes locais por meio de conexões de rede privada entre a rede e o Azure, ou por uma conexão VPN (rede virtual privada) site a site na Internet.

- **Filtragem de tráfego**: filtre o tráfego de rede (de entrada e saída) de máquinas virtuais e de Serviços de Nuvem por porta e endereço IP de origem, porta e endereço IP de destino e protocolo.

- **Roteamento**: opcionalmente, você pode substituir o roteamento padrão do Azure configurando suas próprias rotas ou usando rotas BGP por meio de um gateway de rede.

#### <a name="network-access-controls"></a>Controles de acesso à rede

Os [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (grupos de segurança de rede) são semelhantes a um firewall e fornecem regras de como um recurso pode "se comunicar" pela rede. Eles fornecem controle sobre como uma sub-rede (ou uma máquina virtual) pode se conectar à Internet ou a outras sub-redes na mesma rede virtual.

Um grupo de segurança de rede contém uma lista das regras de segurança que permitem ou negam o tráfego de rede aos recursos conectados às redes virtuais do Azure. Os NSGs podem ser associados a sub-redes, VMs individuais (clássicas) ou NICs (adaptadores de rede) individuais anexadas a VMs (Resource Manager).

Quando um NSG é associado a uma sub-rede, as regras se aplicam a todos os recursos conectados à sub-rede. Você pode restringir ainda mais o tráfego associando um NSG a uma VM ou uma NIC.

## <a name="security-and-compliance-with-organizational-standards"></a>Segurança e conformidade com padrões organizacionais

Cada empresa tem diferentes necessidades e obterá benefícios distintos das soluções de nuvem. Ainda assim, clientes de todos os tipos têm as mesmas preocupações básicas sobre como mover para a nuvem. O que os clientes desejam de provedores de nuvem é:

- **Proteger nossos dados**: os líderes de TI reconhecem que a nuvem pode fornecer uma maior segurança de dados e controle administrativo. Mas eles ainda estão preocupados que a migração para a nuvem os deixará mais vulneráveis a hackers do que suas soluções internas atuais.

- **Manter particulares nossos dados**: os serviços de nuvem geram desafios de privacidade únicos. Como as empresas olham para a nuvem para economizar nos custos de infraestrutura e melhorar sua flexibilidade, elas também se preocupar sobre perder o controle de onde seus dados estão armazenados, quem os está acessando e como é usado.

- **Fornecer controle**: mesmo que as empresas aproveitem a nuvem para implantar soluções mais inovadoras, elas estão preocupadas em perder o controle de seus dados. As recentes divulgações de agências governamentais acessando os dados dos clientes, por meios legais e extralegais, fez alguns CIOs ficarem atentos sobre o armazenamento de seus dados na nuvem.

- **Promover a transparência**: os tomadores de decisões de negócios entendem a importância da segurança, da privacidade e do controle. Mas eles também desejam ter a capacidade de verificar de forma independente como seus dados são armazenados, acessados e protegidos.

- **Manter a conformidade**: conforme as empresas expandem seu uso de tecnologias de nuvem, a complexidade e o escopo dos padrões e dos regulamentos continuam evoluindo. As empresas precisam saber que seus padrões de conformidade serão atendidos.

## <a name="security-configuration-for-monitoring-logging-and-auditing"></a>Configuração de segurança para monitoramento, log e auditoria

Os assinantes do Azure podem gerenciar seus ambientes de nuvem em vários dispositivos. Esses dispositivos podem incluir estações de trabalho de gerenciamento, computadores de desenvolvedores e até mesmo dispositivos de usuário final com privilégios que tenham permissões específicas da tarefa. 

Em alguns casos, as funções administrativas são executadas por meio de consoles baseado na Web, como o Portal do Azure. Em outros casos, pode haver conexões diretas com o Azure de sistemas locais por meio de VPNs, Serviços de Terminal, protocolos de aplicativos cliente ou (de forma programática) a SMAPI (API de Gerenciamento de Serviços) do Azure. Além disso, os pontos de extremidade do cliente podem ser ingressados no domínio ou isolados e não gerenciados, como tablets ou smartphones.

Essa variação pode adicionar um risco significativo para uma implantação de nuvem. Pode ser difícil gerenciar, acompanhar e auditar ações administrativas. Essa variação também pode introduzir ameaças à segurança por meio do acesso não regulado aos pontos de extremidade do cliente que são usados para gerenciar os serviços de nuvem. O uso de estações de trabalho pessoais ou gerais para desenvolvimento e gerenciamento de infraestrutura abre vetores de ameaça imprevisíveis, como navegação na Web (por exemplo, ataques do tipo "watering hole") ou email (por exemplo, engenharia social e phishing).

O monitoramento, o log e a auditoria fornecem uma base para acompanhar e entender as atividades administrativas. A auditoria de todas as ações em detalhes completos nem sempre pode ser viável devido à quantidade de dados gerados. No entanto, a auditoria da eficácia das políticas de gerenciamento é uma melhor prática.

A governança de segurança do Azure dos GPOs do AD DS (Azure Active Directory Domain Services) pode ajudar você a controlar as interfaces do Windows de todos os administradores, como compartilhamento de arquivos. Inclua estações de trabalho de gerenciamento em processos de monitoramento, log e auditoria. Acompanhe todo o acesso e uso de administradores e desenvolvedores.

### <a name="azure-security-center"></a>Central de Segurança do Azure

A [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) fornece uma exibição central do status de segurança dos recursos nas assinaturas. Ela fornece recomendações que ajudam a prevenir o comprometimento de recursos. Ela pode permitir políticas mais detalhadas – por exemplo, aplicar políticas a grupos de recursos específicos que permitem à empresa adaptar sua postura ao risco que está enfrentando.

![Central de Segurança do Azure](./media/governance-in-azure/security-governance-in-azure-fig7.png)

O Centro de Segurança permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança. Depois de habilitar as [políticas de segurança](https://docs.microsoft.com/azure/security-center/security-center-policies) para os recursos de uma assinatura, a Central de Segurança analisa a segurança de seus recursos para identificar as possíveis vulnerabilidades. Informações sobre a configuração de rede estão disponíveis imediatamente.

Central de Segurança do Azure representa uma combinação de análise de melhor prática e gerenciamento de política de segurança para todos os recursos dentro de uma assinatura do Azure. Ela permite às equipes de segurança e aos diretores de risco prevenir, detectar e responder a ameaças de segurança, pois coleta e analisa automaticamente dados de segurança dos recursos do Azure, da rede e de soluções de parceiros como firewalls e programas antimalware.

Além disso, a Central de Segurança do Azure aplica a análise avançada, incluindo aprendizado de máquina e análise comportamental. Usa as ameaças globais de inteligência de produtos e serviços da Microsoft, a DCU (Unidade de Crimes Digitais da Microsoft), o MSRC (Microsoft Security Response Center) e feeds externos. Aplique a [governança de segurança](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) amplamente no nível da assinatura. Se preferir, você pode restringi-la a requisitos específicos e aplicá-los a recursos individuais por meio da definição de política.

Por fim, a Central de Segurança do Azure analisa a integridade da segurança dos recursos com base nessas políticas e usa essas informações para fornecer painéis e alertas repletos de insights para eventos como detecção de malware ou tentativas de conexão de IP mal-intencionada. Para obter mais informações sobre como aplicar recomendações, confira [Implementando as recomendações de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

A Central de Segurança do Azure monitora os seguintes recursos do Azure:

- VMs (máquinas virtuais) (incluindo serviços de nuvem)

- Redes virtuais

- Bancos de dados SQL

- Soluções de parceiros integradas à sua assinatura do Azure, como um Firewall do Aplicativo Web em VMs e no [Ambiente do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)

Quando você acessa pela primeira vez a Central de Segurança, a coleta de dados é habilitada em todas as máquinas virtuais em sua assinatura. Recomendamos que você mantenha a coleta de dados habilitada, mas você pode [desabilitá-la](https://docs.microsoft.com/azure/security-center/security-center-faq) na política da Central de Segurança.

### <a name="log-analytics"></a>Log Analytics

O [programa de governança](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) e segurança de informações da equipe de serviço e desenvolvimento de software do Azure Log Analytics dá suporte a seus requisitos de negócios. Ele cumpre leis e regulamentos, conforme descrito em [Central de Confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/) e [Conformidade da Central de Confiabilidade da Microsoft](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Como o Log Analytics estabelece os requisitos de segurança, identifica os controles de segurança, gerencia e monitora os riscos também são descritos. Anualmente, a equipe examina políticas, padrões, procedimentos e diretrizes.

Cada membro de equipe de desenvolvimento do Log Analytics recebe treinamento formal sobre a segurança de aplicativo. Um sistema de controle de versão ajuda a proteger cada projeto de software em desenvolvimento.

A Microsoft tem uma equipe de segurança e conformidade que monitora e analisa todos os serviços na Microsoft. Os diretores de segurança da informação compõem a equipe e não estão associados aos departamentos de engenharia que desenvolvem o Log Analytics. Os diretores de segurança têm sua própria cadeia de gerenciamento. Eles conduziram avaliações independentes de produtos e serviços para ajudar a garantir a segurança e a conformidade.

A principal funcionalidade do Log Analytics é oferecida por um conjunto de serviços que são executados no Azure. Cada serviço fornece uma função de gerenciamento específico, e você pode combinar serviços para obter cenários de gerenciamento diferentes.

![Serviços do Azure para gerenciamento](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Esses serviços de gerenciamento foram criados na nuvem. Eles são totalmente hospedados no Azure, para que não envolvam a implantação e o gerenciamento de recursos locais. A configuração é mínima e você pode ter tudo funcionando em questão de minutos.

Coloque um agente em qualquer computador Windows ou Linux em seu datacenter, e ele enviará dados para o Log Analytics. Nele, eles podem ser analisados junto com todos os outros dados coletados de serviços de nuvem ou locais. Use o Backup do Azure e o Azure Site Recovery para aproveitar a nuvem para backup e alta disponibilidade de recursos locais.

![Serviços de gerenciamento no painel do Azure](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Os runbooks na nuvem normalmente não podem acessar os recursos locais, mas você pode instalar um agente em um ou mais computadores que hospedarão os runbooks no datacenter. Ao iniciar um runbook, especifique se deseja que ele seja executado na nuvem ou em um trabalho local.

Diferentes soluções estão disponíveis na Microsoft e em parceiros que você pode adicionar à sua assinatura do Azure para aumentar o valor de seu investimento no Log Analytics. Por exemplo, o Azure oferece [soluções de gerenciamento](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) – conjuntos de lógica pré-empacotados que implementam um cenário de gerenciamento usando um ou mais serviços de gerenciamento.

![Galeria de soluções de gerenciamento no Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Como parceiro, você pode criar suas próprias soluções para dar suporte a seus aplicativos e serviços e oferecê-las aos usuários por meio do Azure Marketplace ou como modelos de Início Rápido.

## <a name="performance-alerting-and-monitoring"></a>Desempenho do monitoramento e alertas

### <a name="alerting"></a>Alertas

Os alertas são um método de monitoramento de logs, eventos ou métricas de recursos do Azure. Eles notificam você quando uma condição que você especificou é atendida.

Os alertas estão disponíveis em vários serviços, incluindo:

- **Azure Application Insights**: permite alertas de teste na Web e de métrica. Para obter mais informações, confira [Definir alertas no Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) e [Monitorar a disponibilidade e a capacidade de resposta de qualquer site](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- **Log Analytics**: permite o roteamento de logs de atividades e de diagnóstico para o Log Analytics. Permite métrica, log e outros tipos de alerta. Para obter mais informações, consulte [Alertas no Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- **Azure Monitor**: permite alertas com base nos valores das métricas e nos eventos do log de atividades. Você pode usar a [API REST do Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx) para gerenciar alertas. Para obter mais informações, consulte [Using the Azure portal, PowerShell, or the command-line interface to create alerts](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Monitoramento

Problemas de desempenho em seu aplicativo de nuvem podem afetar seus negócios. Com diversos componentes interconectados e frequentes lançamentos, degradações podem ocorrer a qualquer momento. Além disso, se você estiver desenvolvendo um aplicativo, seus usuários normalmente descobrirão problemas que você não encontrou durante os testes. Você precisa tomar conhecimento desses problemas imediatamente e ter ferramentas para diagnosticar e corrigi-los.

Há uma variedade de ferramentas para monitorar serviços e aplicativos do Azure. Alguns de seus recursos se sobrepõem. Isso se deve em parte à fusão entre o desenvolvimento e a operação de um aplicativo.

Estas são as ferramentas de entidade de serviço:

- O **Azure Monitor** é uma ferramenta básica para monitorar os serviços em execução no Azure. Ele fornece dados de nível da infraestrutura sobre a taxa de transferência de um serviço e o ambiente em redor. Se você está gerenciando todos os seus aplicativos no Azure e está decidindo se deseja escalar ou reduzir verticalmente os recursos, o Azure Monitor pode ajudá-lo a começar.

- O **Application Insights** pode ser usado para desenvolvimento e como uma solução de monitoramento de produção. Ele funciona pela instalação de um pacote no aplicativo e, portanto, fornecer uma exibição mais interna do que está acontecendo. Seus dados incluem tempos de resposta de dependências, rastreamentos de exceção, instantâneos de depuração e perfis de execução. Ele fornece ferramentas para analisar toda essa telemetria, para ajudá-lo a depurar um aplicativo e a entender o que os usuários estão fazendo com ele. Você pode discernir se um pico nos tempos de resposta se deve a algo em um aplicativo ou a algum problema externo de alocação de recursos. Se você usar o Visual Studio e o aplicativo apresentar falhas, vá diretamente para a linha de código com o problema para corrigi-la.

- O **Log Analytics** é voltado para pessoas que precisam ajustar o desempenho e planejar a manutenção de aplicativos em execução na produção. Ele coleta e agrega dados de muitas fontes, com um atraso de 10 a 15 minutos. Ele fornece uma solução holística de gerenciamento de TI para a infraestrutura baseada em nuvem de terceiros (como o Amazon Web Services), do Azure e local. Ele fornece ferramentas para analisar os dados entre fontes, permite consultas complexas em todos os logs e pode alertar de forma proativa sobre condições especificadas. Você pode até mesmo coletar dados personalizados em seu repositório central e, em seguida, consultar e visualizar esses dados.

- O **System Center Operations Manager** é voltado ao gerenciamento e monitoramento de instalações de nuvem de grande porte. Você pode já estar familiarizado com ele como uma ferramenta de gerenciamento para nuvens baseadas no Hyper-V e no Windows Server local, mas ele também pode ser integrado aos aplicativos do Azure e gerenciá-los. Entre outras coisas, ele pode instalar o Application Insights em aplicativos ativos existentes. Se um aplicativo ficar inativo, o Operations Manager informará você em poucos segundos.


## <a name="next-steps"></a>Próximas etapas

- [Práticas recomendadas para criação de modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [Exemplos de implementação da governança de assinatura do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples)

- [Microsoft Azure Governamental](https://docs.microsoft.com/azure/azure-government/)
