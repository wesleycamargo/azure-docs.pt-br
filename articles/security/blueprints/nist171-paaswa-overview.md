---
title: Blueprint de segurança e conformidade do Azure - Aplicativo Web de PaaS para NIST SP 800-171
description: Blueprint de segurança e conformidade do Azure - Aplicativo Web de PaaS do NIST SP 800-171
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f9773c3b372ab22cbcd99828e147d23c185c4eb6
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244614"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Blueprint de segurança e conformidade do Azure - Aplicativo Web de PaaS para Publicação Especial do NIST 800-171

## <a name="overview"></a>Visão Geral
A [Publicação Especial do NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornece diretrizes para proteção de CUI (Controlled Unclassified Information) que reside em organizações e sistemas de informações não federais. O NIST SP 800-171 estabelece 14 famílias de requisitos de segurança para proteger a confidencialidade de CUI.

Esse blueprint de segurança e conformidade do Azure fornece diretrizes para ajudar os clientes a implantar um aplicativo Web de PaaS (plataforma como serviço) no Azure que implementa um subconjunto de controles do NIST SP 800-171. Esta solução demonstra maneiras em que os clientes podem atender a requisitos de conformidade e segurança específicos. Ela também funciona como base para os clientes criarem e configurarem seus próprios aplicativos Web no Azure.

Essa arquitetura de referência, o guia de implementação associado e o modelo de risco devem funcionar como uma base que clientes adaptarão às suas necessidades específicas. Eles não devem ser usados no estado em que se encontram em um ambiente de produção. A implantação dessa arquitetura sem modificação é insuficiente para atender completamente aos requisitos do NIST SP 800-171. Os clientes são responsáveis por realizar avaliações de conformidade e segurança apropriadas de qualquer solução criada usando essa arquitetura. Os requisitos podem variar com base nas especificações da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura

Este Blueprint de Segurança e Conformidade do Azure fornece uma arquitetura de referência para um aplicativo Web de PaaS com um back-end do Banco de Dados SQL do Azure. O aplicativo Web é hospedado em um Ambiente do Serviço de Aplicativo isolado, que é um ambiente privado e dedicado em um datacenter do Azure. A ambiente balanceia a carga de tráfego para o aplicativo Web nas VMs (máquinas virtuais) gerenciadas pelo Azure. Essa arquitetura também inclui NSGs (grupos de segurança de rede), um gateway de aplicativo do Azure, o DNS do Azure e o Azure Load Balancer.

Para análises e relatórios aprimorados, os bancos de dados SQL do Azure podem ser configurados com índices columnstore. Os bancos de dados SQL do Azure podem ser ampliados ou reduzidos ou desligados completamente em resposta ao uso do cliente. Todo o tráfego SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como melhor prática, o Azure recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.

A solução usa contas de Armazenamento do Azure, que os clientes podem configurar para usar a Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O Azure armazena três cópias dos dados dentro de um data center selecionado do cliente para garantir a resiliência. O armazenamento com redundância geográfica garante que os dados sejam replicados para um data center secundário a centenas de milhas de distância e armazenados novamente como três cópias dentro desse data center. Essa disposição impede que um evento prejudicial no data center primário do cliente resulte em perda de dados.

Para maior segurança, todos os recursos nessa solução são gerenciados como um grupo de recursos por meio do Azure Resource Manager. O RBAC (controle de acesso baseado em função) do Azure AD (Azure Active Directory) é usado para controlar o acesso aos recursos implantados. Esses recursos incluem as chaves do cliente no Azure Key Vault. A integridade do sistema é monitorada por meio do Azure Monitor. Os clientes configuram esse serviço de monitoramento para capturar logs. A integridade do sistema é exibida em um único painel fácil de usar.

O Banco de Dados SQL geralmente é gerenciado por meio do SQL Server Management Studio. Ele é executado de um computador local configurado para acessar o Banco de Dados SQL por meio de uma conexão segura VPN ou do Azure ExpressRoute.

O Application Insights fornece gerenciamento de desempenho do aplicativo em tempo real e análise de logs do Azure Monitor *Microsoft recomenda que você configure uma conexão VPN ou ExpressRoute para importação de dados e gerenciamento para a referência sub-rede de arquitetura.*

![Aplicativo Web de PaaS para diagrama de arquitetura de referência do NIST SP 800-171](images/nist171-paaswa-architecture.png "Aplicativo Web de PaaS para diagrama de arquitetura de referência do NIST SP 800-171")

A solução usa os serviços do Azure a seguir. Para obter mais informações, confira a seção [Arquitetura de implantação](#deployment-architecture).

- Máquinas Virtuais do Azure
    - (1) gerenciamento/bastião (Windows Server 2016 Datacenter)
- Rede Virtual do Azure
    - (1) /16 rede
    - (4) /24 redes
    - (4) Grupos de segurança de rede
- Gateway de Aplicativo do Azure
    - Firewall de Aplicativo Web
        - Modo de firewall: prevenção
        - Conjunto de regras: OWASP
        - Porta do ouvinte: 443
- Application Insights
- Active Directory do Azure
- Ambiente do Serviço de Aplicativo v2
- Automação do Azure
- DNS do Azure
- Cofre de Chaves Azure
- Azure Load Balancer
- O Azure Monitor (logs)
- Azure Resource Manager
- Central de Segurança do Azure
- Banco de Dados SQL do Azure
- Armazenamento do Azure
- Automação do Azure
- Aplicativos Web do Azure

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir fornece detalhes sobre os elementos de implantação e implementação.

**Azure Resource Manager**: [O Gerenciador de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pode ser usado pelos clientes para trabalhar com os recursos da solução como um grupo. Os clientes podem implantar, atualizar ou excluir todos os recursos da solução em uma única operação coordenada. Os clientes usam um modelo para implantação. O modelo pode ser útil para diferentes ambientes, como teste, preparação e produção. O Resource Manager fornece recursos de segurança, auditoria e marcação para ajudar os clientes a gerenciar seus recursos após a implantação.

**Host bastião**: O host de bastiões é o único ponto de entrada que os usuários podem usar para acessar os recursos implantados nesse ambiente. O host bastião fornece uma conexão segura aos recursos implantados, permitindo apenas o tráfego remoto de endereços IP públicos em uma lista segura. Para permitir o tráfego de área de trabalho remota, a fonte do tráfego precisa ser definida no NSG.

Essa solução cria uma VM como um host bastião ingressado no domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Extensão de Diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usando o Key Vault.
-   Uma [política de desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos da VM quando eles não estão em uso.
-   O [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) é habilitado para que as credenciais e outros segredos sejam executados em um ambiente protegido e isolado do sistema operacional em execução.

**Aplicativos Web**: [Aplicativos Web](https://docs.microsoft.com/azure/app-service/) é um recurso do serviço de aplicativo do Azure. Os clientes podem usá-lo para criar e hospedar aplicativos Web na linguagem de programação de sua escolha, sem gerenciar infraestrutura. Ele oferece dimensionamento automático e alta disponibilidade. Ele dá suporte ao Windows e ao Linux e permite implantações automatizadas do GitHub, do Azure DevOps ou de repositórios Git.

**Ambiente do Serviço de Aplicativo**: [Ambiente do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/environment/intro) é um recurso do serviço de aplicativo. Ele fornece um ambiente totalmente isolado e dedicado para executar com segurança os aplicativos do Serviço de Aplicativo em grande escala.

O Ambiente do Serviço de Aplicativo é isolado para executar apenas um único aplicativo. Ele é sempre implantado em uma rede virtual. Devido ao recurso de isolamento, a arquitetura de referência oferece o isolamento completo do locatário e é removida do ambiente multilocatário do Azure. Os clientes têm um controle refinado sobre o tráfego de rede de entrada e saída do aplicativo. Os aplicativos também podem estabelecer conexões seguras de alta velocidade por redes virtuais para recursos empresariais locais. Os clientes podem realizar o "dimensionamento automático" com o Ambiente do Serviço de Aplicativo com base em métricas de carga, no orçamento disponível ou em um agendamento definido.

O uso do Ambiente do Serviço de Aplicativo para essa arquitetura oferece os seguintes controles e configurações:

- Hospedar dentro de uma rede virtual do Azure protegida e com regras de segurança de rede.
- Certificado do balanceador de carga interno autoassinado para comunicação HTTPS. Como melhor prática, a Microsoft recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.
- [Modo de balanceamento de carga interno](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (modo 3).
- Desabilitar o [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Alterar a [criptografia TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Controlar [as portas N/W do tráfego de entrada](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic).
- [Firewall do aplicativo Web – restringir dados](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall).
- Permitir [tráfego do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview).

### <a name="virtual-network"></a>Rede virtual
A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: [Os NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm listas de controle de acesso que permitem ou negam o tráfego em uma rede virtual. Os NSGs podem ser usados para proteger o tráfego em nível individual de VM ou sub-rede. Os seguintes NSGs existem:
- Um NSG para o Gateway de Aplicativo
- Um NSG para o Ambiente do Serviço de Aplicativo
- Um NSG para o Banco de Dados SQL
- Um NSG para host bastião

Cada NSG tem portas e protocolos específicos abertos para que a solução possa funcionar corretamente e com segurança. Além disso, as seguintes configurações estão habilitadas para cada NSG:
  - Os [eventos e logs de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) estão habilitados e são armazenados em uma conta de armazenamento.
  - Os logs do Azure Monitor está conectado para o [diagnóstico do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**sub-redes**: Cada sub-rede está associada ao seu NSG correspondente.

**DNS do Azure**: O sistema de nome de domínio (DNS) é responsável por converter (ou seja, resolver) um nome de site ou serviço para seu endereço IP. O [DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de hospedagem para domínios DNS que fornece resolução de nomes usando a infraestrutura do Azure. Ao hospedar domínios no Azure, os usuários podem gerenciar registros DNS usando as mesmas credenciais, APIs, ferramentas e cobranças que outros serviços do Azure. O DNS do Azure também dá suporte a domínios DNS privados.

**Azure Load Balancer**: [Balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) pode ser usado por clientes para dimensionar seus aplicativos e criar alta disponibilidade para serviços. O Load Balancer oferece suporte a cenários de entrada e saídas. Ele oferece baixa latência e alta produtividade e pode ser escalado verticalmente a milhões de fluxos para todos os aplicativos TCP e UDP.

### <a name="data-in-transit"></a>Dados em trânsito
O Azure criptografa todas as comunicações entre os data centers do Azure por padrão. Todas as transações para o Armazenamento do Azure por meio do portal do Azure ocorrem por HTTPS.

### <a name="data-at-rest"></a>Dados em repouso

A arquitetura protege dados em repouso usando criptografia, auditoria de banco de dados e outras medidas.

**Armazenamento do Azure**: Para atender aos requisitos de dados em repouso criptografados, todo o [Armazenamento](https://azure.microsoft.com/services/storage/) usa [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption). Esse recurso ajuda a proteger e preservar os dados em suporte aos compromissos da segurança organizacional e aos requisitos de conformidade definidos pelo NIST SP 800-171.

**Azure Disk Encryption**: O [Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usa o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução integra-se ao Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de Dados SQL do Azure**: A instância do Banco de Dados SQL usa as seguintes medidas de segurança do banco de dados:
-   A [autenticação e a autorização do Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) permitem o gerenciamento de identidade dos usuários de banco de dados e de outros serviços da Microsoft em uma única localização central.
-   A [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) controla os eventos de banco de dados e grava-os em um log de auditoria em uma conta de armazenamento do Azure.
-   O Banco de Dados SQL é configurado para usar a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Ela executa criptografia e descriptografia em tempo real do banco de dados, dos backups associados e dos arquivos de log de transações para proteger as informações em repouso. A Transparent Data Encryption oferece a garantia de que os dados armazenados não estejam sujeitos a acesso não autorizado.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem o acesso aos servidores do banco de dados até que as permissões apropriadas sejam concedidas. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.
-   A [Detecção de Ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) habilita a detecção e a resposta a possíveis ameaças conforme elas ocorrem. Ela fornece alertas de segurança sobre atividades suspeitas do banco de dados, possíveis vulnerabilidades, ataques de injeção de SQL e padrões anormais de acesso ao banco de dados.
-   As [colunas criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca sejam exibidos como texto sem formatação dentro do sistema de banco de dados. Depois que a criptografia de dados estiver habilitada, somente os aplicativos clientes ou servidores de aplicativos com acesso às chaves poderão acessar dados de texto sem formatação.
- A [Máscara de Dados Dinâmicos](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos sem privilégios. Ela pode descobrir automaticamente dados possivelmente confidenciais e sugerir as máscaras apropriadas a serem aplicadas. A Máscara de Dados Dinâmicos ajuda a reduzir o acesso para que os dados confidenciais não saiam do banco de dados por meio de acesso não autorizado. *Os clientes são responsáveis por ajustar as configurações para seguir seu próprio esquema de banco de dados.*

### <a name="identity-management"></a>Gerenciamento de identidades
As seguintes tecnologias oferecem funcionalidades para gerenciar o acesso a dados no ambiente do Azure:
-   O [Azure AD](https://azure.microsoft.com/services/active-directory/) é o serviço multilocatário de gerenciamento de identidade e de diretório baseado em nuvem da Microsoft. Todos os usuários dessa solução são criados no Azure AD e incluem os usuários que acessam o Banco de Dados SQL.
-   A autenticação para o aplicativo é executada por meio do Azure AD. Para obter mais informações, confira como [integrar aplicativos ao Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). A criptografia de coluna do banco de dados também usa o Azure AD para autenticar o aplicativo no Banco de Dados SQL. Para obter mais informações, veja como [proteger dados confidenciais no Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   O [RBAC do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) pode ser usado por administradores para definir permissões de acesso refinadas. Com ele, eles podem conceder somente a quantidade de acesso que os usuários precisam para realizar seus trabalhos. Em vez de conceder a todos os usuários acesso irrestrito aos recursos do Azure, os administradores podem permitir apenas determinadas ações para acessar recursos e dados. O acesso à assinatura é limitado ao administrador da assinatura.
- O [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) pode ser usado pelos clientes para minimizar o número de usuários com acesso a determinadas informações. Os administradores podem usar o Azure AD Privileged Identity Management para descobrir, restringir e monitorar identidades com privilégios e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor o acesso administrativo Just-In-Time sob demanda, quando necessário.
- O [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades da organização. Ele configura respostas automatizadas para detectar ações suspeitas relacionadas às identidades da organização. Ele também investiga incidentes suspeitos para tomar as devidas providências para resolvê-los.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: A solução usa [Key Vault](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves e segredos. O Key Vault ajuda a proteger chaves de criptografia e segredos usados por aplicativos e serviços de nuvem. As seguintes funcionalidades do Key Vault ajudam os clientes a proteger os dados:
- Políticas de acesso avançadas são configuradas com base na necessidade.
- As políticas de acesso do Key Vault são definidas com o mínimo de permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault têm datas de validade.
- Todas as chaves no Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma chave RSA de 2048 bits protegida pelo módulo de segurança de hardware.
- Todos os usuários e identidades recebem as permissões mínimas necessárias quando o RBAC é usado.
- Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
- As operações criptográficas permitidas para chaves são restritas às necessárias.

**Central de Segurança do Azure**: Com a [Central de Segurança](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar políticas de segurança em cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques de forma centralizada. A Central de Segurança também acessa as configurações existentes dos serviços do Azure para fornecer recomendações de configuração e de serviço para ajudar a melhorar a situação de segurança e proteger os dados.

A Central de Segurança usa uma variedade de funcionalidades de detecção para alertar os clientes sobre possíveis ataques direcionados a seus ambientes. Esses alertas contêm informações valiosas sobre o que disparou o alerta, os recursos de destino e a origem do ataque. A Central de Segurança tem um conjunto de [alertas de segurança predefinidos](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que é disparado em caso de ameaça ou de atividade suspeita. Os clientes podem usar as [regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) para definir novos alertas de segurança com base nos dados que já foram coletados do ambiente.

A Central de Segurança fornece alertas e incidentes de segurança priorizados. A Central de Segurança facilita a descoberta e a resolução de possíveis problemas de segurança para os clientes. Um [relatório de inteligência de ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada. As equipes de resposta a incidentes podem usar os relatórios ao investigarem e corrigirem ameaças.

**Gateway de Aplicativo do Azure**: A arquitetura reduz o risco de vulnerabilidades de segurança por meio de um gateway de aplicativo com o firewall do aplicativo web configurado e o conjunto de regras OWASP habilitado. Dentre outros recursos estão:

- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Habilitar o [descarregamento SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Desabilitar o [TLS v1.0 e v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Firewall do aplicativo Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo de prevenção).
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com o conjunto de regras OWASP 3.0.
- Habilitar o [log de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Investigações de integridade personalizadas](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- A [Central de Segurança](https://azure.microsoft.com/services/security-center) e o [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecem proteção e notificações adicionais. A Central de segurança também fornece um sistema de reputação.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

Os serviços do Azure registram em log de forma extensiva as atividades do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividades**: Os [logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem insights sobre as operações executadas em recursos em uma assinatura. Os logs de atividade podem ajudar a determinar o iniciador, o horário da ocorrência e o status de uma operação.
- **Logs de diagnóstico**: Os [logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os logs emitidos por cada um dos recursos. Esses logs incluem os logs do sistema de eventos do Windows, os logs de Armazenamento, os logs de auditoria do Key Vault e os logs de acesso e do firewall do Gateway de Aplicativo. Todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. Os usuários podem configurar um período de retenção de até 730 dias para atender às suas necessidades específicas.

**Logs do Azure Monitor**: Os logs são consolidados [registra em log do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e emissão de relatórios do painel. Depois que os dados são coletados, eles são organizados em tabelas separadas para cada tipo de dados nos espaços de trabalho do Log Analytics. Dessa forma, todos os dados podem ser analisados juntos, independentemente de sua fonte original. A Central de segurança se integra com os logs do Azure Monitor. Os clientes podem usar consultas Kusto para acessar seus dados de evento de segurança e combiná-lo com dados de outros serviços.

O Azure a seguir [soluções de monitoramento](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) são incluídos como parte dessa arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): A solução de Verificação de Integridade do Active Directory avalia o risco e a integridade dos ambientes de servidor em um intervalo regular. Ela fornece uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): A solução de Verificação de Integridade do SQL avalia o risco e a integridade dos ambientes do servidor em um intervalo regular. Ela fornece aos clientes uma lista priorizada de recomendações específicas para a infraestrutura de servidor implantada.
- [Integridade do Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): A solução de Integridade do Agente informa quantos agentes estão implantados e sua distribuição geográfica. Ela também relata quantos agentes não estão respondendo e o número de agentes que enviam dados operacionais.
-   [Análise do Log de Atividades](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): A solução de Análise do Log de Atividades auxilia na análise dos logs de atividades do Azure em todas as assinaturas do Azure para um cliente.

**Automação do Azure**: A [Automação](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nessa solução, os runbooks ajudam a coletar logs do Banco de Dados SQL. Os clientes podem usar a solução de [Controle de Alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) da Automação para identificar facilmente as alterações no ambiente.

**Azure Monitor**: O [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências. As organizações podem usá-lo para auditar, criar alertas e arquivar dados. Elas também podem acompanhar as chamadas à API em seus recursos do Azure.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço de gerenciamento de desempenho de aplicativo extensível para desenvolvedores da web em várias plataformas. O Application Insights detecta anomalias de desempenho. Os clientes podem usá-lo para monitorar o aplicativo Web em tempo real. O Application Insights inclui ferramentas de análise avançadas para ajudar os clientes a diagnosticar problemas e entender o que os usuários fazem com seu aplicativo. Ele foi projetado para ajudar os clientes a aprimorar continuamente o desempenho e a usabilidade.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados dessa arquitetura de referência está disponível para [download](https://aka.ms/nist171-paaswa-tm) ou pode ser encontrado aqui. Esse modelo pode ajudar os clientes a entenderem os possíveis pontos de risco na infraestrutura do sistema ao fazer modificações.

![Aplicativo Web de PaaS para modelo de risco do NIST SP 800-171](images/nist171-paaswa-threat-model.png "Aplicativo Web de PaaS para modelo de risco do NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Blueprint de segurança e conformidade do Azure - Matriz de responsabilidade do cliente para NIST SP 800-171](https://aka.ms/nist171-crm) lista todos os controles de segurança exigidos pelo NIST SP 800-171. Essa matriz detalha se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou compartilhada entre os dois.

O [Blueprint de Segurança e Conformidade do Azure – Matriz de Implementação de Controle de Aplicativo Web de PaaS do NIST SP 800-171](https://aka.ms/nist171-paaswa-cim) fornece informações sobre quais controles do NIST SP 800-171 são cobertos pela arquitetura de aplicativo Web de PaaS. Ele inclui descrições detalhadas de como a implementação atende aos requisitos de cada controle coberto.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel VPN seguro ou um [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer uma conexão com os recursos implantados como parte dessa arquitetura de referência de aplicativo Web de PaaS de forma segura. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Implementando um túnel de VPN seguro com o Azure, é possível criar uma conexão privada virtual entre uma rede local e uma rede virtual do Azure. Essa conexão ocorre pela Internet e permite que os clientes enviem informações por "túnel” com segurança usando um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. O [modo de túnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego do túnel de VPN passa pela Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O ExpressRoute é um link de WAN dedicado entre o Azure e uma localização local ou um provedor de hospedagem do Exchange. As conexões do ExpressRoute conectam-se diretamente ao provedor de telecomunicações do cliente. Como resultado, os dados não passam pela Internet e não são expostos a ela. Essas conexões oferecem mais confiabilidade, velocidades mais rápidas, latências mais baixas e segurança maior do que as conexões típicas.

Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) práticas recomendadas para a implementação de uma rede híbrida segura que estende uma rede local para o Azure.

## <a name="disclaimer"></a>Aviso de isenção de responsabilidade

 - Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de consulta interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.
 - Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
 - Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
