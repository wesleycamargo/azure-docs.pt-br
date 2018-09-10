---
title: Blueprint de segurança e conformidade do Azure - Data Analytics para NIST SP 800-171
description: Blueprint de segurança e conformidade do Azure - Data Analytics para NIST SP 800-171
services: security
author: jomolesk
ms.assetid: ca75d2a8-4e20-489b-9a9f-3a61e74b032d
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: b2c96509b2b8fd61c1814d5b4a015048eeb1807d
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392633"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Blueprint de segurança e conformidade do Azure - Data Analytics para NIST SP 800-171

## <a name="overview"></a>Visão geral
A [Publicação Especial do NIST 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fornece diretrizes para proteção de CUI (Controlled Unclassified Information) que reside em organizações e sistemas de informações não federais. O NIST SP 800-171 estabelece 14 famílias de requisitos de segurança para proteger a confidencialidade de CUI.

Este blueprint de segurança e conformidade do Azure fornece diretrizes para ajudar os clientes a implantar uma arquitetura de análise de dados no Azure que implementa um subconjunto de controles do NIST SP 800-171. Esta solução demonstra maneiras em que os clientes podem atender a requisitos de segurança e conformidade específicos e servir como uma base para os clientes criarem e configurarem suas próprias soluções de análise de dados no Azure.

Essa arquitetura de referência, o guia de implementação associado e o modelo de risco servem como uma base para os clientes adaptarem-se aos requisitos específicos e não devem ser usados como estão em um ambiente de produção. A implantação dessa arquitetura sem modificação é insuficiente para atender completamente aos requisitos do NIST SP 800-171. Os clientes são responsáveis por realizar as devidas avaliações de segurança e conformidade de qualquer solução criada usando essa arquitetura, uma vez que os requisitos podem variar com base nas particularidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
Esta solução fornece uma plataforma de análise na qual os clientes podem criar suas próprias ferramentas de análise. A arquitetura de referência descreve um caso de uso genérico em que os clientes inserem dados por meio de importações em massa de dados pelo Administrador de dados/SQL ou por meio de atualizações de dados operacionais de um Usuário operacional. Ambos os fluxos de trabalho incorporam o Azure Functions para importar dados para o Banco de Dados SQL do Azure. O Azure Functions precisa ser configurado pelo cliente por meio do Portal do Azure para lidar com as tarefas de importação exclusivas para os requisitos de análise do cliente.

O Azure oferece uma variedade de serviços de relatórios e de análise para o cliente. No entanto, essa solução incorpora os serviços do Azure Machine Learning em conjunto com o Banco de Dados SQL do Azure para procurar dados rapidamente e entregar resultados com maior velocidade por meio da modelagem mais inteligente dos dados. O Azure Machine Learning é uma forma de aprendizado de máquina cuja finalidade é aumentar a velocidade de consulta descobrindo novas relações entre conjuntos de dados. Após os dados terem sido treinados usando várias funções estatísticas, até sete pools de consulta adicionais (total de oito incluindo o servidor do cliente) podem ser sincronizados com os mesmos modelos de tabela para distribuir a carga de trabalho de consulta e reduzir os tempos de resposta.

Para criação de relatórios e análise avançadas, o Banco de Dados SQL do Azure pode ser configurado com índices de repositório de coluna. O Azure Machine Learning e os Bancos de Dados SQL do Azure podem ser escalados ou reduzidos verticalmente ou desligados completamente em resposta ao uso do cliente. Todo o tráfego SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como melhor prática, o Azure recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.

Após serem carregados no Banco de Dados SQL do Azure e treinados pelo Azure Machine Learning, os dados são resumidos pelo Usuário Operacional e pelo Administrador de Sados/SQL com o Power BI. O Power BI exibe dados intuitivamente e reúne informações em vários conjuntos de dados para gerar insights mais amplos. Seu alto nível de adaptabilidade e fácil integração com o Banco de Dados SQL do Azure garantem que os clientes possam configurá-lo para lidar com uma ampla gama de cenários, conforme exigido por suas necessidades de negócio.

A solução integral é criada com base no Armazenamento do Microsoft Azure que os clientes configuram no portal do Azure. O Armazenamento do Azure criptografa todos os dados com a Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O armazenamento com redundância geográfica garante que um evento adverso no data center primário do cliente não cause perda de dados, pois uma segunda cópia será armazenada em um local separado a centenas de quilômetros de distância.

Para maior segurança, todos os recursos nessa solução são gerenciados como um grupo de recursos por meio do Azure Resource Manager. O controle de acesso baseado em função do Azure Active Directory é usado para controlar o acesso aos recursos implantados, incluindo as chaves no Azure Key Vault. A integridade do sistema é monitorada por meio da Central de Segurança do Azure e do Azure Monitor. Os clientes configuram os dois serviços de monitoramento para capturar logs e exibir a integridade do sistema em um único painel facilmente navegável.

O Banco de Dados SQL do Azure normalmente é gerenciado por meio do SQL Server Management Studio, que é executado de um computador local configurado para acessar o Banco de Dados SQL do Azure usando uma conexão segura VPN ou do ExpressRoute. **A Microsoft recomenda configurar uma conexão VPN ou do ExpressRoute para o gerenciamento e a importação de dados para o grupo de recursos**.

![Data Analytics para diagrama de arquitetura de referência do NIST SP 800-171](images/nist171-analytics-architecture.png "Data Analytics para diagrama de arquitetura de referência do NIST SP 800-171")

A solução usa os serviços do Azure a seguir. Mais detalhes estão na seção [arquitetura de implantação](#deployment-architecture).

- Application Insights
- Azure Active Directory
- Catálogo de Dados do Azure
- Azure Disk Encryption
- Grade de Eventos do Azure
- Funções do Azure
- Cofre da Chave do Azure
- Azure Machine Learning
- Azure Monitor
- Central de Segurança do Azure
- Banco de Dados SQL do Azure
- Armazenamento do Azure
- Azure Log Analytics
- Rede Virtual do Azure
    - (1) /16 rede
    - (2) /24 redes
    - (2) Grupos de segurança de rede
- Painel do Power BI

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir fornece detalhes sobre os elementos de implantação e implementação.

**Grade de Eventos do Azure**: [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) permite que os clientes criem aplicativos facilmente com arquiteturas baseadas em evento. Os usuários selecionam o recurso do Azure que desejam assinar e fornecem ao manipulador de eventos ou ao webhook um ponto de extremidade ao qual o evento deverá ser enviado. Os clientes podem proteger os pontos de extremidade do webhook adicionando parâmetros de consulta à URL do webhook ao criar uma Assinatura de Evento. A Grade de Eventos do Azure dá suporte apenas a pontos de extremidade do webhook HTTPS. A Grade de Eventos do Azure permite que os clientes controlem o nível de acesso concedido a usuários diferentes para execução de várias operações de gerenciamento, como listar assinaturas de evento, criar novos e gerar chaves. A Grade de Eventos utiliza o controle de acesso baseado em função do Azure.

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) é um serviço de computação sem servidor que permite que os usuários executem o código sob demanda sem precisar provisionar ou gerenciar explicitamente a infraestrutura. Use o Azure Functions para executar um script ou parte do código em resposta a uma variedade de eventos.

**Azure Machine Learning**: [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) é uma técnica da ciência de dados que permite que os computadores usem os dados existentes para prever tendências, resultados e comportamentos futuros.

**Catálogo de Dados do Azure**: o [Catálogo de Dados](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) torna fontes de dados facilmente identificáveis e compreensíveis para os usuários que gerenciam os dados. Fontes de dados comuns podem ser registradas, marcadas e pesquisadas por dados. Os dados permanecem no local existente, mas uma cópia de seus metadados é adicionada ao Catálogo de Dados, juntamente com uma referência ao local da fonte de dados. Os metadados também são indexados para tornar cada fonte de dados fácil de descobrir por meio de pesquisa e compreensível para os usuários que os descobrirem.

### <a name="virtual-network"></a>Rede virtual
Esta arquitetura de referência define uma rede virtual privada com um espaço de endereço de 10.0.0.0/16.

**Grupos de segurança de rede**: [os grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm listas de controle de acesso que permitem ou negam tráfego dentro de uma Rede Virtual. Os grupos de segurança de rede podem ser usados para proteger o tráfego no nível da sub-rede ou da máquina virtual individual. Existem os seguintes grupos de segurança de rede:
  - Um grupo de segurança de rede para o Active Directory
  - Um grupo de segurança de rede para a carga de trabalho

Cada um dos grupos de segurança de rede têm portas e protocolos específicos abertos para que a solução possa funcionar corretamente e com segurança. Além disso, as configurações a seguir são habilitadas para cada grupo de segurança de rede:
  - [Eventos e logs de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
  - O Log Analytics está conectado aos [diagnósticos do grupo de segurança de rede](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sub-redes**: cada sub-rede está associada a seu grupo de segurança de rede correspondente.

### <a name="data-in-transit"></a>Dados em trânsito
O Azure criptografa todas as comunicações entre datacenters do Azure por padrão. Todas as transações para o Armazenamento do Azure por meio do portal do Azure ocorrem por HTTPS.

### <a name="data-at-rest"></a>Dados em repouso

A arquitetura protege dados em repouso usando criptografia, auditoria de banco de dados e outras medidas.

**Armazenamento do Azure**: para atender aos requisitos de criptografia de dados em repouso, todo o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption).  Isso ajuda a proteger e preservar os dados em suporte aos compromissos de segurança organizacional e aos requisitos de conformidade definidos pelo NIST SP 800-171.

**Azure Disk Encryption**: o [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução é integrada ao Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de Dados SQL do Azure**: a instância do Banco de Dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:
-   A [autenticação e a autorização do AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades dos usuários de banco de dados e de outros serviços da Microsoft em uma única localização central.
-   A [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) controla os eventos de banco de dados e grava-os em um log de auditoria em uma conta de armazenamento do Azure.
-   O Banco de Dados SQL do Azure está configurado para usar a [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia em tempo real do banco de dados, dos backups associados e dos arquivos de log de transações para proteger as informações em repouso. Transparent Data Encryption oferece a garantia de que os dados armazenados não fiquem sujeitos a acesso não autorizado.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem o acesso aos servidores do banco de dados até que as permissões apropriadas sejam concedidas. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.
-   A [Detecção de Ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite detectar e responder a possíveis ameaças conforme elas ocorrem, fornecendo alertas de segurança sobre atividades suspeitas no banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões anormais de acesso ao banco de dados.
-   [Colunas Criptografadas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que os dados confidenciais nunca sejam exibidos como texto não criptografado no sistema do banco de dados. Após a habilitação da criptografia de dados, somente aplicativos cliente ou servidores de aplicativo com acesso às chaves poderão acessar dados de texto não criptografado.
- A [Máscara de Dados Dinâmicos do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais mascarando os dados para usuários ou aplicativos não privilegiados. A Máscara de Dados Dinâmicos pode descobrir automaticamente dados possivelmente confidenciais e sugerir as máscaras apropriadas a serem aplicadas. Isso ajuda a reduzir o acesso de forma que os dados confidenciais não saiam do banco de dados por meio de acesso não autorizado. Os clientes são responsáveis por ajustar as configurações da Máscara de Dados Dinâmicos para seguir seus respectivos esquemas de banco de dados.

### <a name="identity-management"></a>Gerenciamento de identidades
As seguintes tecnologias oferecem funcionalidades para gerenciar o acesso a dados no ambiente do Azure:
-   O [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidades e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários dessa solução são criados no Azure Active Directory, incluindo os usuários que acessam o Banco de Dados SQL do Azure.
-   A autenticação no aplicativo é executada usando o Azure Active Directory. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a criptografia da coluna do banco de dados utiliza o Azure Active Directory para autenticar o aplicativo no Banco de Dados SQL do Microsoft Azure. Para obter mais informações, veja como [proteger dados confidenciais no Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   O [controle de acesso baseado em função do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite que os administradores definam permissões de acesso refinadas para conceder apenas o limite de acesso que os usuários precisam para realizar seus trabalhos. Em vez de conceder permissões irrestritas aos recursos do Azure a todos os usuários, os administradores podem permitir apenas determinadas ações para acesso a dados. O acesso à assinatura é limitado ao administrador da assinatura.
- O [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permite que os clientes minimizem o número de usuários que tenham acesso a determinadas informações, como dados.  Os administradores podem usar o Azure Active Directory Privileged Identity Management para descobrir, restringir e monitorar identidades privilegiadas e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor o acesso administrativo sob demanda Just-In-Time quando necessário.
-   O [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades da organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas a essas identidades e investiga incidentes suspeitos e toma a medida apropriada para resolvê-los.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: a solução usa o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para gerenciar chaves e segredos. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Os recursos do Azure Key Vault a seguir ajudam os clientes a proteger os dados:
- Políticas de acesso avançadas são configuradas com base na necessidade.
- As políticas de acesso do Key Vault são definidas com o mínimo de permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault têm datas de validade.
- Todas as chaves no Key Vault são protegidas por módulos de segurança de hardware especializados. O tipo de chave é uma chave RSA de 2048 bits protegida por módulos de segurança de hardware.
- Todos os usuários e identidades recebem permissões mínimas necessárias usando o controle de acesso baseado em função.
- Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
- As operações criptográficas permitidas para chaves são restritas às necessárias.

**Central de Segurança do Azure**: com [ela](https://docs.microsoft.com/azure/security-center/security-center-intro), os clientes podem aplicar e gerenciar políticas de segurança de maneira centralizada nas cargas de trabalho, limitar a exposição a ameaças, além de detectar ataques e responder a eles. Além disso, a Central de Segurança do Azure acessa as configurações existentes de serviços do Azure para fornecer recomendações de serviço e configuração, a fim de ajudar a melhorar a postura de segurança e proteger os dados.

A Central de Segurança do Azure usa uma variedade de funcionalidades de detecção para alertar os clientes de ataques potenciais direcionados a seus ambientes. Esses alertas contêm informações valiosas sobre o que disparou o alerta, os recursos de destino e a origem do ataque. A Central de Segurança do Azure tem um conjunto de [alertas de segurança predefinidos](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que são disparados em caso de ameaça ou atividade suspeita. As [regras de alerta personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) na Central de Segurança do Azure permitem que os clientes definam novos alertas de segurança com base nos dados já coletados do ambiente.

A Central de Segurança do Azure fornece alertas e incidentes de segurança priorizados, simplificando a descoberta e a resolução por parte dos clientes de possíveis problemas de segurança. Um [relatório de inteligência contra ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) é gerado para cada ameaça detectada, a fim de ajudar as equipes de resposta a incidentes a investigar e corrigir as ameaças.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

Os serviços do Azure registram em log de forma extensiva as atividades do sistema e do usuário, bem como a integridade do sistema:
- **Logs de atividades:** os [Logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem insights sobre as operações executadas nos recursos de uma assinatura. Os logs de atividade podem ajudar a determinar o iniciador, o horário da ocorrência e o status de uma operação.
- **Logs de diagnóstico**: os [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os logs emitidos por todos os recursos. Esses logs são logs do sistema de eventos do Windows, logs de Armazenamento do Azure, logs de auditoria do Key Vault e logs de acesso e firewall do Gateway de Aplicativo. Todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, de até 730 dias, para atender aos requisitos de retenção específicos da organização.

**Log Analytics**: esses logs são consolidados no [Log Analytics](https://azure.microsoft.com/services/log-analytics/) para processamento, armazenamento e relatórios de painéis. Depois de coletados, os dados são organizados em tabelas separadas para cada tipo de dados nos espaços de trabalho do Operations Management Suite, o que permite que todos os dados sejam analisados em conjunto, independentemente da fonte original. Além disso, a Central de Segurança do Azure é integrada ao Log Analytics, permitindo que os clientes usem as consultas do Log Analytics para acessar seus dados de eventos de segurança e combiná-los com os dados de outros serviços.

As seguintes [soluções de gerenciamento](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) do Log Analytics são incluídas como parte dessa arquitetura:
-   [Avaliação do Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): a solução Verificação de Integridade do Active Directory avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece uma lista priorizada de recomendações específicas da infraestrutura de servidor implantada.
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): a solução de Verificação de Integridade do SQL avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece aos clientes uma lista priorizada de recomendações específicas da infraestrutura do servidor implantado.
- [Integridade do Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): a solução de Integridade do Agente informa quantos agentes estão implantados e sua distribuição geográfica, bem como quantos agentes não estão respondendo e o número de agentes que estão enviando dados operacionais.
-   [Análise do Log de Atividades](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): a solução Análise do Log de Atividades ajuda com a análise dos logs de atividades do Azure em todas as assinaturas do Azure de um cliente.

**Automação do Azure**: a solução [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) armazena, executa e gerencia runbooks. Nessa solução, os runbooks ajudam a coletar logs do Banco de Dados SQL do Azure. A solução [Controle de Alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking) da Automação permite que os clientes identifiquem com facilidade as alterações no ambiente.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os usuários a acompanhar o desempenho, manter a segurança e identificar tendências, permitindo que as organizações auditem, criem alertas e arquivem dados, incluindo o acompanhamento de chamadas à API em seus recursos do Azure.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/) é um serviço de gerenciamento de desempenho de aplicativos extensível indicado a desenvolvedores para Web em várias plataformas. Ele detecta anomalias de desempenho e inclui ferramentas de análise avançadas para ajudar a diagnosticar problemas e compreender o que os usuários realmente fazem com o aplicativo. Ele foi projetado para ajudar os usuários a aprimorar continuamente o desempenho e a usabilidade.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados dessa arquitetura de referência está disponível para [download](https://aka.ms/nist171-analytics-tm) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entenderem os pontos de risco em potencial na infraestrutura do sistema ao fazer modificações.

![Data Analytics para modelo de risco do NIST SP 800-171](images/nist171-analytics-threat-model.png "Data Analytics para modelo de risco do NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Blueprint de segurança e conformidade do Azure - Matriz de responsabilidade do cliente para NIST SP 800-171](https://aka.ms/nist171-crm) lista todos os controles de segurança exigidos pelo NIST SP 800-171. Essa matriz detalha se a implementação de cada controle é de responsabilidade da Microsoft, do cliente ou compartilhada entre os dois.

O [Blueprint de segurança e conformidade do Azure – Matriz de implementação de controle do Data Analytics para NIST SP 800-171](https://aka.ms/nist171-analytics-cim) fornece informações sobre quais controles do NIST SP 800-171 são abordados pela arquitetura de análise de dados, incluindo descrições detalhadas sobre como a implementação atende aos requisitos de cada controle coberto.


## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel VPN ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) seguro precisa ser configurado para estabelecer uma conexão de forma segura com os recursos implantados como parte dessa arquitetura de referência de análise de dados. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Implementando um túnel de VPN seguro com o Azure, é possível criar uma conexão privada virtual entre uma rede local e uma Rede Virtual do Azure. Essa conexão ocorre pela Internet e permite que os clientes enviem informações por "túnel” com segurança usando um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. O [modo de túnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego do túnel de VPN passa pela Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link de WAN dedicado entre o Azure e a instalação local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não ocorrem pela Internet, elas oferecem mais confiabilidade, mais velocidade, latências mais baixas e maior segurança que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicações do cliente, os dados não passam pela Internet e, portanto, não estão expostos a ela.

Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) práticas recomendadas para a implementação de uma rede híbrida segura que estende uma rede local para o Azure.

### <a name="extract-transform-load-process"></a>Processo de extração, transformação e carregamento
O [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) pode carregar dados no Banco de Dados SQL do Azure sem precisar de uma ferramenta separada de extração, transformação e carregamento ou de importação. O PolyBase permite acessar dados por meio de consultas T-SQL. É possível usar a pilha de análise de business intelligence da Microsoft com o PolyBase, bem como ferramentas de terceiros compatíveis com o SQL Server.

### <a name="azure-active-directory-setup"></a>Configuração do Azure Active Directory
O [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para gerenciar a implantação e provisionar acesso para pessoas que interagem com o ambiente. Um Windows Server Active Directory existente pode ser integrado ao Azure Active Directory com [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Os clientes também podem associar a infraestrutura implantada (os controladores de domínio) do Active Directory a um Azure Active Directory existente fazendo da infraestrutura do Active Directory implantada um subdomínio de uma floresta do Azure Active Directory.

## <a name="disclaimer"></a>Isenção de responsabilidade

 - Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de consulta interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.
 - Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
 - Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
