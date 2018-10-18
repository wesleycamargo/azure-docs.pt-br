---
title: Blueprint de conformidade e segurança do Azure - Aplicativo Web de PaaS para carga de trabalho OFICIAL DO REINO UNIDO
description: Blueprint de conformidade e segurança do Azure - Aplicativo Web de PaaS para carga de trabalho OFICIAL DO REINO UNIDO
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: 6fe85d7ac527179ab39e89739f5744f3aa1ef8e2
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297548"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Blueprint de conformidade e segurança do Azure - Aplicativo Web de PaaS para carga de trabalho OFICIAL DO REINO UNIDO

## <a name="azure-security-and-compliance-blueprints"></a>Segurança do Azure e Esquemas de Conformidade

Especificações técnicas do Azure consistem em documentos de diretrizes e modelos de automação que implantam arquiteturas baseado em nuvem para oferecer soluções para cenários que têm requisitos de conformidade ou de capacitação. Especificações técnicas do Azure são coleções de modelo de automação e diretrizes que permitem que os clientes do Microsoft Azure aceleram a entrega de suas metas de negócios por meio de uma arquitetura de base que pode ser estendida para atender a quaisquer requisitos adicionais de provisionamento.

## <a name="overview"></a>Visão geral

Este projeto de conformidade e segurança do Azure fornece scripts de automação e diretrizes para entregar uma plataforma do Microsoft Azure [plataforma como serviço (PaaS)](https://azure.microsoft.com/overview/what-is-paas/) hospedado, arquitetura do aplicativo web apropriada para lidar com cargas de trabalho classificadas como [OFICIAL REINO UNIDO](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf). Essa classificação de segurança abrange a maioria das informações criada ou processada pelo setor público. Isso inclui operações de rotina de negócios e serviços, que, se perdidos, roubados ou publicados na mídia, alguns doss quais pode ter consequências graves. O perfil de ameaça típico para a classificação OFICIAL é muito semelhante a uma empresa privada que fornece serviços e informações valiosas. O OFICIAL DO REINO UNIDO prevê a necessidade de se defender contra ameaças ou o comprometimento por invasores, com os serviços ou a dados do governo do Reino Unido limitados, como funções e recursos (mas não está limitado a) hacktivistas, grupos de pressão de edição única, jornalistas investigativos, hackers individuais competentes e a maioria dos indivíduos e grupos criminosos.

Esta especificação técnica foi revisada pela UK NCSC National Cyber Security Centre (NCSC) e se alinha com os princípios de segurança de nuvem do NCSC 14.

A arquitetura usa os componentes da plataforma do Azure [como serviço](https://azure.microsoft.com/overview/what-is-paas/) para oferecer um ambiente que permite que os clientes evitem os gastos e a complexidade de comprar licenças de software de gerenciamento da infraestrutura subjacente do aplicativo e middleware ou as ferramentas de desenvolvimento e outros recursos. Os clientes gerenciam os aplicativos e serviços que desenvolvem, concentrando-se em fornecer valor comercial, enquanto o Microsoft Azure gerencia os recursos do Azure como máquinas virtuais, armazenamento e rede, colocando mais da [divisão da responsabilidade](https://docs.microsoft.com/azure/security/security-paas-deployments#division-of-responsibility) para gerenciamento de infraestrutura para a plataforma do Azure. Os [Serviços de Aplicativos do Azure](https://azure.microsoft.com/services/app-service/) oferecem dimensionamento automático e alta disponibilidade, compatível com Windows e Linux e permite implantações automatizadas do GitHub, Azure DevOps ou qualquer repositório Git como serviços padrão. Por meio de Serviços de Aplicativos, os desenvolvedores podem se concentrar em fornecer valor comercial sem a sobrecarga de gerenciamento de infraestrutura. É possível criar novos aplicativos web de Java, PHP, Node. js, Python, HTML ou C# de greenfield ou também a migração de nuvem existente ou no local de aplicativos web para Serviços de Aplicativos do Azure (embora completa devido cuidado e teste para confirmar se é necessário um desempenho).

Este blueprint enfoca o provisionamento de uma [plataforma de base segura como serviço](https://azure.microsoft.com/overview/what-is-paas/) interface baseada web para o público e também usuários do back office. Nesse cenário de design do plano gráfico considera o uso dos serviços baseados na web hospedados do Azure onde um usuário público pode enviar, visualizar e gerenciar com segurança os dados confidenciais; além de um back office ou operador governamental que podem processar com segurança os dados confidenciais que o usuário público enviou. Casos de uso para esse cenário podem incluir:

- Um usuário que envia um retorno de imposto, com um operador do governo processando o envio;
- Um usuário que está solicitando um serviço por meio de um aplicativo baseado na web, com um usuário de back-office validando e fornecendo o serviço; ou
- Um usuário que busca e exibe o domínio público ajuda as informações relacionadas a um serviço do governo.

Usar modelos do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) e dos scripts da interface Azure Command Line, o blueprint implanta um ambiente que alinha para o UK National Cyber Security Centre (NCSC) 14 [Princípios de Segurança de Nuvem](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) e o Centro para Segurança de Internet (CIS) [Controles de Segurança Crítica](https://www.cisecurity.org/critical-controls.cfm). O NCSC recomenda que seus Princípios de Segurança de Nuvem sejam usados pelos clientes para avaliar as propriedades de segurança do serviço e para ajudar a entender a divisão de responsabilidades entre cliente e fornecedor. A Microsoft forneceu informações em relação a cada um desses princípios para ajudá-lo a entender a divisão das responsabilidades. Essa arquitetura e modelos correspondentes do Azure Resource Manager têm suporte do white paper da Microsoft [14 Cloud Security Controls for UK cloud Using Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Esta arquitetura foi revisada pelo NCSC e alinha com os Princípios de Segurança em Nuvem UK NCSC 14, assim habilitando as organizações do setor público para rastrear rapidamente sua capacidade de encontrar obrigações de conformidade usando os serviços baseados em nuvem globais no Reino Unido na nuvem do Microsoft Azure. Este modelo implanta a infraestrutura para a carga de trabalho. O código do aplicativo e o software de camada de dados e de negócios precisa estar instalado e configurado. As instruções detalhadas de implantação estão disponíveis [aqui](https://aka.ms/ukofficial-paaswa-repo/).

Esta blueprint é uma arquitetura de base. Nossos clientes podem usar esta especificação técnica como uma base para suas cargas de trabalho baseado na web a classificação OFICIAL e expandir os modelos e recursos com seus próprios requisitos. Esta especificação técnica se baseia nos princípios do [blueprint de aplicativos de Web de IaaS de três camadas OFICIAL DO REINO UNIDO](https://aka.ms/ukofficial-iaaswa) para oferecer a nossos clientes [infraestrutura como serviço (IaaS)](https://azure.microsoft.com/overview/what-is-iaas/) e opções de implementação de PaaS para hospedar cargas de trabalho baseada na web.

Para implantar esse blueprint, é necessário uma assinatura do Azure. Se você não tiver uma assinatura do Azure, pode inscrever-se rápida e facilmente - Introdução ao Azure. Clique [aqui](https://aka.ms/ukofficial-paaswa-repo/) para obter instruções de implantação.

## <a name="architecture-and-components"></a>Arquitetura e componentes

Este blueprint fornece um solução em um ambiente de nuvem do Azure que dá suporte a cargas de trabalho do REINO UNIDO OFICIAL de hospedagem de aplicativos de web. A arquitetura oferece um ambiente seguro que aproveita a plataforma do Azure como recursos de um serviço. Dentro do ambiente, dois aplicativos do Serviço de Aplicativo do Azure web são implantados (um para os usuários públicos) e outro para usuários de back-office, com uma camada de API App fornecer os serviços de negócios para o front-end da web. Um banco de dados do SQL Azure é implantado como um repositório de dados relacionados gerenciados para o aplicativo. Conectividade com esses componentes fora da plataforma e entre todos esses componentes é criptografada por meio do protocolo TLS 1.2 para garantir os dados na privacidade de transporte, com acesso autenticado pelo Active Directory do Azure.

![Hospedagem de aplicativos de Web do PaaS para o diagrama de arquitetura de referência de cargas de trabalho OFICIAL do REINO UNIDO](images/ukofficial-paaswa-architecture.png?raw=true "hospedagem de aplicativos de Web do PaaS para o diagrama de arquitetura de referência de cargas de trabalho OFICIAL do REINO UNIDO")

Como parte da arquitetura de implantação, provisionar armazenamento seguro, monitoramento e registro em log, gerenciamento de segurança unificado e proteção avançada contra ameaças e gerenciamento de recursos também são implantados para garantir que os clientes tenham todas as ferramentas necessárias para proteger e monitorar seu ambiente para esta solução.

A solução usa os serviços do Azure a seguir. Há detalhes da arquitetura de implantação na seção [arquitetura de implantação](#deployment-architecture).

- Azure Active Directory
- Managed Service Identity
- Serviço de Aplicativo
- Aplicativo Web
- Aplicativo de API
- DNS do Azure
- Key Vault
- Azure Monitor
- Application Insights
- Log Analytics
- Azure Resource Manager
- Central de Segurança do Azure
- Banco de Dados SQL do Azure
- Armazenamento do Azure

## <a name="deployment-architecture"></a>Arquitetura de implantação

A seção a seguir fornece detalhes sobre os elementos de implantação e implementação.

### <a name="security"></a>Segurança

#### <a name="identity-and-authentication"></a>Identidade e autenticação

Esta especificação técnica garante que o acesso aos recursos seja protegido por meio dos serviços de gerenciamento de identidade e diretório. Essa arquitetura torna o uso total de [identidade como o perímetro de segurança](https://docs.microsoft.com/azure/security/security-paas-deployments#identity-as-the-primary-security-perimeter). 

As tecnologias a seguir fornecem funcionalidades de gerenciamento de identidades no ambiente do Azure:

- O [Azure AD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários da solução foram criados no Azure Active Directory, incluindo os usuários que acessam o Banco de Dados SQL.
- A autenticação para o operador voltada para o aplicativo web e acesso para a administração dos recursos do Azure é executada usando o Microsoft Azure Active Directory. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Além disso, a criptografia de coluna do banco de dados usa o Microsoft Azure Active Directory para autenticar o aplicativo no Banco de Dados SQL do Azure. Para obter mais informações, consulte [Always Encrypted: proteger dados confidenciais no Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- O cidadão voltados para o aplicativo web está configurado para acesso público. Para permitir a criação de conta a autenticação através do diretório ativo ou provedor de identidade de rede social o [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) pode ser integrado, se necessário.
- O [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades e contas arriscadas fornece recomendações para melhorar a postura de segurança de identidades da sua organização, configura respostas automatizadas para detectada ações suspeitas relacionadas a identidades de sua organização e investiga incidentes suspeitos e toma a medida apropriada para resolvê-los.
- O [RBAC (Controle De Acesso Baseado Em Função) do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) permite o gerenciamento de acesso detalhado para o Azure. O acesso à assinatura é limitado ao administrador da assinatura e o acesso ao Azure Key Vault é restrito apenas ao usuários que precisaram do acesso de gerenciamento.
- Para aprover o [Acesso Condicional do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) os clientes podem impor controles de segurança adicionais sobre o acesso a aplicativos ou usuários em seu ambiente com base em condições específicas, como local, dispositivo, estado e entrar em risco.
- [A Proteção contra DDoS do Azure](https://docs.microsoft.com/azure/security/security-paas-deployments#security-advantages-of-a-paas-cloud-service-model) combinada com as práticas recomendadas de design de aplicativo, fornece defesa contra ataques de DDoS, com o monitoramento de tráfego sempre ativo e a mitigação em tempo real de ataques comuns no nível de rede. Com uma arquitetura de PaaS, o nível de plataforma a proteção contra DDoS é transparente para o cliente e incorporado à plataforma, mas é importante observar que a responsabilidade de design de segurança de aplicativo se encontra com o cliente.

#### <a name="data-in-transit"></a>Dados em trânsito

Os dados são trânsito de fora e entre os componentes do Azure são protegidos usando [Transport Layer Security/protocolo SSL (TLS/SSL)](https://www.microsoft.com/TrustCenter/Security/Encryption), que usa a criptografia simétrica com base em um segredo compartilhado para criptografar as comunicações conforme passam pela rede. Por padrão, o tráfego de rede é protegido usando o TLS 1.2.

#### <a name="security-and-malware-protection"></a>Proteção de segurança e malware

A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) permite uma exibição centralizada do estado da segurança de todos os seus recursos do Azure. Você pode verificar rapidamente se os controles de segurança apropriados estão em vigor e configurados de maneira correta, bem como identificar com rapidez os recursos que exigem atenção.

O [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-overview) é um consultor de nuvem personalizado que ajuda a seguir as melhores práticas para otimizar as implantações do Azure. Ele analisa a telemetria de uso e configuração do recurso e, depois, recomenda soluções que podem ajudar você a melhorar a economia, o desempenho, a alta disponibilidade e a segurança de seus recursos do Azure.

O [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. Isso por padrão é instalado na infraestrutura subjacente de máquina virtual de PaaS e é gerenciado pela malha do Azure de forma transparente para o cliente, garantindo

### <a name="paas-services-in-this-blueprint"></a>Serviços de PaaS neste blueprint

#### <a name="azure-app-service"></a>Serviço de aplicativo do Azure

Aplicativos Web do Azure fornecem um ambiente para o aplicativo da web desenvolvido em Java, PHP, Node.js Python, HTML e C# sem a necessidade de gerenciar a infraestrutura de hospedagem da web totalmente gerenciada. Ele oferece dimensionamento automático e alta disponibilidade, compatível com Windows e Linux e permite implantações automatizadas do [Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/) ou qualquer repositório baseado em Git.

O serviço de aplicativo é em conformidade com [ISO, SOC, e PCI](https://www.microsoft.com/TrustCenter/) e pode autenticar usuários com o [Azure Active Directory](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication) ou com o logon social ([Google](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-google-authentication), [Facebook](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-facebook-authentication), [Twitter](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-twitter-authentication), e autenticação da [Microsoft](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-microsoft-authentication).

Os planos Básico, Standard e Premium são para cargas de trabalho de produção e são executados em instâncias dedicadas de Máquina Virtual. Cada instância pode oferecer suporte a vários aplicativos e domínios. Serviços de aplicativos também são compatíveis com [restrições de endereço IP](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions) para proteger o tráfego para endereços IP confiáveis se necessário e também [Identidade de Serviço Gerenciada](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity) para conexão segura para outros serviços de PaaS, como [Cofre de chaves](https://azure.microsoft.com/services/key-vault/) e [banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/). Onde a segurança adicional é necessária, nosso plano isolado hospeda aplicativos em um ambiente particular e dedicado do Azure, ideal para aplicativos que exigem conexões seguras a sua rede local ou desempenho e escala adicionais.

Esse modelo implanta os seguintes recursos do Serviço de Aplicativo do Azure:

- [Standard](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) Camada do Plano de Serviço de Aplicativo
- Vários aplicativos Web [slots de implantação](https://docs.microsoft.com/azure/app-service/web-sites-staged-publishing): Dev, visualização, controle de qualidade, UAT e claro (slot padrão) de produção.
- [Identidade de Serviço Gerenciada](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity) para se conectar ao [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) (isso também pode ser usado para fornecer acesso ao [Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/) 
- Integração com o [Visual Studio Online Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-azure-web-apps) para monitorar o desempenho
- [Logs de Diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 
- Alertas de [métricas](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) 
- [Aplicativos de API do Azure](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure

O Banco de Dados SQL é um serviço gerenciado de banco de dados relacional de uso geral no Microsoft Azure que dá suporte a estruturas como XML, JSON, espacial e dados relacionais. O Banco de Dados SQL oferece bancos de dados SQL únicos gerenciados, bancos de dados SQL gerenciados em um [pool elástico](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) e [Instâncias Gerenciadas](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) do SQL (em versão prévia pública). Ele oferece [desempenho dinamicamente escalonável])https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers) e fornece opções como [índices columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) para análise extrema e geração de relatórios, além de[OLTP in-memory](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) para processamento transacional extremo. A Microsoft trata todos os patches e a atualização da base de código SQL sem interrupções e abstrai todo o gerenciamento da infraestrutura subjacente.

Banco de dados SQL do Azure neste projeto

A instância do Banco de Dados SQL usa as seguintes medidas de segurança de banco de dados:

- [Regras de firewall no nível do servidor e o nível de banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure), ou através dos [pontos de extremidade de serviço de rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usando [regras de Rede Virtual do Microsoft Azure](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- [Criptografia de dados transparentes](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) ajuda a proteger o Banco de Dados SQL do Microsoft Azure e o banco de dados de data warehouse do Azure contra a ameaça de atividade mal-intencionada. Ela realiza a criptografia e a descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo.
- [Com a autenticação do Microsoft Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication), é possível gerenciar centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento central de IDs fornece um único local para gerenciar os usuários do banco de dados e simplifica o gerenciamento de permissões.
- Uso do Azure Active Directory para a administração de banco de dados
- [Logs de auditoria](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) às contas de armazenamento
- Alertas de [métrica](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) para conexões de banco de dados com falha
- [Detecção de ameaças do SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Colunas Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Armazenamento do Azure

Armazenamento do [Transparent data encryption](https://azure.microsoft.com/services/storage/) helps protect Azure SQL Database and Azure Data Warehouse against the threat of malicious activity.Microsoft Azure é um serviço de nuvem gerenciado pela Microsoft que fornece armazenamento altamente disponível, seguro, durável, escalonável e redundante. O Armazenamento do Microsoft Azure consiste no armazenamento de Blobs, armazenamento de Arquivos e armazenamento de Filas.

#### <a name="azure-storage-in-this-blueprint"></a>Armazenamento do Microsoft Azure neste blueprint

Este modelo usa os seguintes componentes de Armazenamento do Microsoft Azure:

- [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Permitir somente o tráfego HTTPS

#### <a name="data-at-rest"></a>Dados em repouso

Através da [Criptografia do Serviço de Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) todos os dados gravados no Armazenamento do Azure são criptografados por meio da criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis. Use chaves de criptografia gerenciadas pela Microsoft com a SSE ou suas [próprias chaves de criptografia](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

As contas de armazenamento podem ser protegidas por meio dos [Pontos de Extremidade de Serviço de Rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usando [regras de rede virtual](https://docs.microsoft.com/azure/storage/common/storage-network-security).

Informações detalhadas sobre como proteger o Armazenamento do Microsoft Azure podem ser encontradas na [guia de segurança](https://docs.microsoft.com/azure/storage/common/storage-security-guide).


### <a name="secrets-management"></a>Gerenciamento de segredos

#### <a name="azure-key-vault"></a>Cofre da Chave do Azure

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) é usado para proteger as chaves de aplicativo e segredos para garantir que eles não estejam acessíveis por terceiros. O Key Vault não deve ser usado como um repositório de senhas de usuário. O Key Vault permite que você crie vários contêineres seguros, chamado cofres. Esses cofres contam com HSMs (Módulos de Segurança de Hardware). Os cofres ajudam a reduzir a possibilidade de perda acidental de informações de segurança pela centralização do armazenamento de segredos do aplicativo. Os Key Vaults também controlam e registram o acesso a todas as coisas armazenadas neles. O Azure Key Vault pode tratar da solicitação e da renovação de certificados TLS, fornecendo os recursos necessários para uma solução de gerenciamento de ciclo de vida de certificados robusta.

#### <a name="azure-key-vault-in-this-blueprint"></a>Azure Key Vault neste blueprint

- Contém a chave de acesso de armazenamento, com acesso de leitura concedido para a [Identidade de Serviço Gerenciada](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity) do aplicativo web voltado para o cliente
- Contém a senha do DBA do SQL Server (em um cofre separado)
- Log de diagnósticos

### <a name="monitoring-logging-and-audit"></a>Monitoramento, registro em log e auditoria

#### <a name="log-analytics"></a>Log Analytics

O [Log Analytics](https://azure.microsoft.com/services/log-analytics/) é um serviço no Azure que ajuda a coletar e analisar dados gerados por recursos nos ambientes locais e de nuvem.

#### <a name="log-analytics-in-this-blueprint"></a>Log Analytics neste blueprint

- Avaliação do SQL
- Diagnóstico do Cofre de Chaves
- Conexão do Application Insights
- Log de Atividades do Azure

#### <a name="application-insights"></a>Application Insights

O [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço APM (Gerenciamento de Desempenho de Aplicativos) extensível para desenvolvedores da Web em várias plataformas. Usado para monitorar aplicativos web em tempo real, ele detectará automaticamente anomalias de desempenho, analisará o desempenho e diagnosticará problemas e entenderá como os usuários interagem com o aplicativo. O Application Insights pode ser desenvolvido nas plataformas incluindo .NET e J2EE, hospedados localmente ou na nuvem. Ele integra-se ao seu processo DevOps e tem pontos de conexão para uma ampla variedade de ferramentas de desenvolvimento.

#### <a name="application-insights-in-this-blueprint"></a>Application Insights neste blueprint

Este modelo usa os seguintes componentes do Application Insights:

- Painel do Application Insights por site (operador, cliente e API)

#### <a name="azure-activity-logs"></a>Logs de atividade do Azure

O [Log de atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs#what-you-can-do-with-the-activity-log) audita os eventos do painel de controle para as suas assinaturas. Usando o Log de Atividades, você pode determinar 'o que, quem e quando' para quaisquer operações de gravação (PUT, POST, DELETE) executadas nos recursos em sua assinatura. Também é possível compreender o status da operação e outras propriedades relevantes.

#### <a name="azure-monitor"></a>Azure Monitor

O [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) habilita o monitoramento principal dos serviços do Azure, permitindo a coleta de métricas, logs de atividades e logs de diagnóstico. O Azure Monitor fornece logs e métricas de infraestrutura de nível básico para a maioria dos serviços do Microsoft Azure.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados dessa arquitetura de referência está disponível para [download](https://aka.ms/ukofficial-paaswa-tm) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entenderem os pontos de risco em potencial na infraestrutura do sistema ao fazer modificações.

![Hospedagem do aplicativo Web PaaS para modelo de ameaça de carga de trabalho OFICIAL DO REINO UNIDO](images/ukofficial-paaswa-threat-model.png?raw=true "Hospedagem do Aplicativo Web PaaS para o modelo de ameaça das cargas de trabalho OFICIAIS DO REINO UNIDO")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Documentação de conformidade com os Princípios de Segurança de Nuvem do NCSC

O Crown Commercial Service (departamento que funciona para melhorar as atividades comerciais e de compras do governo inglês) renovou a classificação dos serviços de nuvem empresariais da Microsoft em escopo como G-Cloud v6, abrangendo todas as suas ofertas no nível OFFICIAL. Os detalhes do Azure e do G-Cloud podem ser encontrados no [Resumo da avaliação da segurança do Azure UK G-Cloud](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud).

Esse projeto está de acordo com os 14 princípios de segurança de nuvem que estão documentados nos [Princípios de Segurança de Nuvem](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) do NCSC para ajudar a garantir um ambiente que dê suporte a cargas de trabalho classificadas como UK-OFFICIAL.

A [Segurança do Azure e Blueprint de Conformidade - OFICIAL DO REINO UNIDO Matriz de Responsabilidades do Cliente](https://aka.ms/ukofficial-crm) (pasta de trabalho do Excel) lista todos os 14 princípios de segurança de nuvem e a matriz indica, para cada entidade de segurança (ou subitem do princípio), se a implementação do princípio é de responsabilidade da Microsoft, do cliente ou compartilhado entre os dois.

A [Segurança do Azure e Blueprint de Conformidade - Aplicativo Web PaaS para Matriz de Implementação de Princípios OFICIAL DO REINO UNIDO](https://aka.ms/ukofficial-paaswa-pim) (pasta de trabalho do Excel) lista todos os 14 princípios de segurança, e a matriz denota, para cada princípio (ou subitem do princípio), que acarreta a responsabilidade do cliente na Matriz de Responsabilidades do Cliente, 1) se a Automação do Diagrama do Azure implementa o princípio e 2) uma descrição de como a implementação se alinha com os requisitos do princípio.  

Além disso, a CSA (Cloud Security Alliance) publicou a Matriz de Controle de Nuvem para ajudar os clientes na avaliação de provedores de nuvem e a identificar perguntas que devem ser respondidas antes da migração para os serviços de nuvem. Em resposta, o Microsoft Azure respondeu ao Questionário de Iniciativa de Avaliação de Consenso da CSA ([CSA CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA)), que descreve como a Microsoft trata os princípios sugeridos.

## <a name="third-party-assessment"></a>Avaliação de produtos de terceiros

Esta especificação técnica foi revisada pela UK NCSC National Cyber Security Centre (NCSC) e se alinha com os princípios de segurança de nuvem do NCSC 14

Os modelos de automação foram testados pela equipe de Arquiteto de Solução de Nuvem do Azure de Unidade de Sucesso do Cliente do Reino Unido e por nosso parceiro da Microsoft, [Ampliphae](http://www.ampliphae.com/).


## <a name="deploy-the-solution"></a>Implantar a solução

Esta Automação de Segurança e Conformidade do Azure Blueprint é composta de arquivos de configuração JSON e scripts do PowerShell tratados pelo serviço de API do Azure Resource Manager para implantar recursos no Azure. As instruções detalhadas de implantação estão disponíveis [aqui](https://aka.ms/ukofficial-paaswa-repo).

Três abordagens foram fornecidas para a implantação; Uma simples "expressa" [CLI do Azure 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) adequada para criar rapidamente um teste de ambiente; um com parâmetros [clI do Azure 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) abordagem fornecendo maior configuração em ambientes de carga de trabalho; e um portal do Azure com base no qual o operador pode especificar os parâmetros de implantação por meio do portal do Azure de implantação. 

1.  Clone ou baixe [este](https://aka.ms/ukofficial-paaswa-repo) repositório GitHub na sua estação de trabalho local.
2.  Revisão [Método 1: CLI do Azure 2 (versão Express)](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) e execute os comandos fornecidos.
3.  Revisão [Método 1a: CLI do Azure 2 (como configurar a implantação por meio de argumentos de script)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) e execute os comandos fornecidos
4.  Revisão [Método 2: processo de implantação do Portal do Azure](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process) e execute os comandos listados

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="api-management"></a>Gerenciamento de API

[Gerenciamento de API do Azure](https://azure.microsoft.com/services/api-management/) poderia ser usado na frente do Serviço de Aplicativo da API e para fornecer camadas adicionais de segurança, limitação e controles para expor o proxy e proteger APIs.

### <a name="azure-b2c"></a>B2C do Azure

[Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) pode ser implementado como um controle para permitir que os usuários se registram, criem uma identidade e habilitem a autorização e controle para o aplicativo web público de acesso.

## <a name="disclaimer"></a>Isenção de responsabilidade

- Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.
- Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.
- Os clientes podem copiar e usar este documento para fins de consulta interna.
- Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.
- Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
- Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
