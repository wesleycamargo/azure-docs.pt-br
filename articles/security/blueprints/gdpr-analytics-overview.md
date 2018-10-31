---
title: 'Blueprint de segurança e conformidade do Azure: Análise para GDPR'
description: 'Blueprint de segurança e conformidade do Azure: Análise para GDPR'
services: security
author: jomolesk
ms.assetid: fe97b5e5-72d8-4930-bbe9-ead2b6ef3542
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: f744a1126e12766980727e31d5c50ce4aa17934c
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408771"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-gdpr"></a>Blueprint de segurança e conformidade do Azure – Análise para GDPR

## <a name="overview"></a>Visão geral
O GDPR (Regulamento Geral sobre a Proteção de Dados) contém muitos requisitos sobre coletar, armazenar e usar informações pessoais, incluindo como as organizações identificam e protegem os dados pessoais, acomodam requisitos de transparência, detectam e relatam violações de dados pessoais e treinam o pessoal de privacidade e outros funcionários. O GDPR fornece às pessoas um controle maior sobre seus dados pessoais e impõe muitas obrigações novas às organizações que coletam, manipulam ou analisam dados pessoais. O GDPR impõe novas regras às organizações que oferecem bens e serviços para pessoas da UE (União Europeia) ou que coletam e analisam dados vinculados a residentes da UE. O GDPR se aplica independentemente de onde uma organização está localizada.

A Microsoft projetou o Azure com medidas de segurança e políticas de privacidade líderes do setor para proteger os dados na nuvem, incluindo as categorias de dados pessoais identificadas pelo GDPR. Os [termos contratuais](http://aka.ms/Online-Services-Terms) da Microsoft submetem a Microsoft aos requisitos dos processadores.

Esse Blueprint de Segurança e Conformidade do Azure fornece diretrizes para implantar uma arquitetura de análise de dados no Azure que ajuda com os requisitos do GDPR. Esta solução demonstra maneiras em que os clientes podem atender a requisitos de segurança e conformidade específicos e servir como uma base para os clientes criarem e configurarem suas próprias soluções de análise de dados no Azure. Os clientes podem utilizar esta arquitetura de referência e seguir o [processo de quatro etapas](https://aka.ms/gdprebook) da Microsoft em sua jornada para conformidade com o GDPR:
1. Descobrir: identificar quais dados pessoais existem e onde eles residem.
2. Gerenciar: controlar como os dados pessoais são usados e acessados.
3. Proteger: estabelecer controles de segurança para evitar, detectar e responder a violações de dados e vulnerabilidades.
4. Relatar: manter a documentação necessária e gerenciar solicitações de dados e notificações de violação.

Essa arquitetura de referência, o guia de implementação associado e os modelos de ameaça são destinados a servir como uma base que os clientes podem adaptar a seus requisitos específicos e não devem ser usados no estado em que se encontra em um ambiente de produção. Observe o seguinte:
- A arquitetura fornece uma linha de base para ajudar os clientes implantarem cargas de trabalho no Azure em conformidade com o GDPR.
- Os clientes são responsáveis por realizar as devidas avaliações de segurança e conformidade de qualquer solução criada usando essa arquitetura, uma vez que os requisitos podem variar com base nas particularidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
Esta solução fornece uma plataforma de análise na qual os clientes podem criar suas próprias ferramentas de análise. A arquitetura de referência descreve um caso de uso genérico em que os clientes inserem dados por meio de importações em massa de dados pelo Administrador de dados/SQL ou por meio de atualizações de dados operacionais de um Usuário operacional. Os dois fluxos de trabalho incorporam o Azure Functions para importar dados para o Banco de Dados SQL do Azure. O Azure Functions precisa ser configurado pelo cliente por meio do portal do Azure para lidar com as tarefas de importação exclusivas aos requisitos de análise de cada cliente.

O Azure oferece uma variedade de serviços de relatórios e de análise para o cliente. No entanto, essa solução incorpora os serviços do Azure Machine Learning em conjunto com o Banco de Dados SQL do Azure para procurar dados rapidamente e fornecer resultados com maior velocidade por meio da modelagem mais inteligente dos dados do cliente. O Azure Machine Learning é uma forma de aprendizado de máquina cuja finalidade é aumentar a velocidade de consulta descobrindo novas relações entre conjuntos de dados. Após os dados terem sido treinados usando várias funções estatísticas, até sete pools de consulta adicionais (total de oito incluindo o servidor do cliente) podem ser sincronizados com os mesmos modelos de tabela para distribuir a carga de trabalho de consulta e reduzir os tempos de resposta.

Para análises e relatórios aprimorados, os bancos de dados SQL do Azure podem ser configurados com índices columnstore. Os bancos de dados do Azure Machine Learning e do Azure SQL podem ser escalados ou reduzidos verticalmente ou desligados completamente em resposta ao uso do cliente. Todo o tráfego SQL é criptografado com SSL por meio da inclusão de certificados autoassinados. Como melhor prática, o Azure recomenda o uso de uma autoridade de certificação confiável para aumentar a segurança.

Após serem carregador no Banco de Dados SQL do Azure e treinados pelo Azure Machine Learning, os dados são ingeridos pelo Usuário operacional e pelo Administrador de dados/SQL com o Power BI. O Power BI exibe dados intuitivamente e reúne informações em vários conjuntos de dados para gerar insights mais amplos. Seu alto nível de adaptabilidade e fácil integração com o Banco de Dados SQL do Azure garantem que os clientes possam configurá-lo para lidar com uma ampla gama de cenários, conforme exigido por suas necessidades de negócio.

Toda a solução é baseada em um Armazenamento do Azure que os clientes configuram no portal do Azure. O Armazenamento do Azure criptografa todos os dados com a Criptografia do Serviço de Armazenamento para manter a confidencialidade dos dados em repouso. O GRS (armazenamento com redundância geográfica) garante que eventos adversos no data center primário do cliente não causem perda de dados, pois uma segunda cópia será armazenada em um local separado a centenas de quilômetros de distância.

Para maior segurança, essa arquitetura gerencia recursos usando o Azure Active Directory e o Azure Key Vault. A integridade do sistema é monitorada por meio do Log Analytics e do Azure Monitor. Os clientes configuram os dois serviços de monitoramento para capturar logs e exibir a integridade do sistema em um único painel facilmente navegável.

O Banco de Dados SQL do Azure normalmente é gerenciado por meio do SSMS (SQL Server Management Studio), que é executado de um computador local configurado para acessar o Banco de Dados SQL do Azure usando uma conexão segura do ExpressRoute ou VPN. **O Azure recomenda configurar uma conexão VPN ou do ExpressRoute para gerenciamento e importação de dados para o grupo de recursos da arquitetura de referência**.

![Diagrama de arquitetura de referência de análise para GDPR](images/gdpr-analytics-architecture.png?raw=true "Diagrama de arquitetura de referência de análise para GDPR")

A solução usa os serviços do Azure a seguir. Há detalhes da arquitetura de implantação na seção [Arquitetura de implantação](#deployment-architecture).

- Funções do Azure
- Banco de Dados SQL do Azure
- Azure Machine Learning
- Azure Active Directory
- Cofre da Chave do Azure
- Log Analytics
- Azure Monitor
- Armazenamento do Azure
- Painel do Power BI
- Catálogo de Dados do Azure
- Central de Segurança do Azure
- Application Insights
- Grade de Eventos do Azure
- grupos de segurança de rede

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir fornece detalhes sobre os elementos de implantação e implementação.

**Grade de Eventos do Azure**
[A Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview) permite que os clientes compilem facilmente aplicativos com as arquiteturas baseadas em evento. Os usuários selecionam o recurso do Azure que desejam assinar e fornecem ao manipulador de eventos ou ao webhook um ponto de extremidade ao qual enviar o evento. Os clientes podem proteger os pontos de extremidade do webhook adicionando parâmetros de consulta à URL do webhook ao criar uma Assinatura de Evento. A Grade de Eventos do Azure dá suporte apenas a pontos de extremidade do webhook HTTPS. A Grade de Eventos do Azure permite que os clientes controlem o nível de acesso concedido a usuários diferentes para execução de várias operações de gerenciamento, como listar assinaturas de evento, criar novos e gerar chaves. A Grade de Eventos utiliza o RBAC (Controle de Acesso Baseado em Função) do Azure.

**Azure Functions**
[O Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) é um serviço de computação sem servidor que permite que os usuários executem o código sob demanda sem precisar provisionar explicitamente ou gerenciar a infraestrutura. Use o Azure Functions para executar um script ou parte do código em resposta a uma variedade de eventos.

**Azure Machine Learning**
[O Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) é uma técnica da ciência de dados que permite que os computadores usem os dados existentes para prever tendências, resultados e comportamentos futuros.

**Catálogo de Dados do Azure**: o [Catálogo de Dados](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) torna fontes de dados facilmente identificáveis e compreensíveis para os usuários que gerenciam os dados. Fontes de dados comum podem ser registradas, marcadas e pesquisadas em busca de dados pessoais. Os dados permanecem no local existente, mas uma cópia de seus metadados é adicionada ao Catálogo de Dados, juntamente com uma referência ao local da fonte de dados. Os metadados também são indexados para tornar cada fonte de dados fácil de descobrir por meio de pesquisa e compreensível para os usuários que os descobrirem.

### <a name="virtual-network"></a>Rede virtual
Esta arquitetura de referência define uma rede virtual com um espaço de endereço de 10.0.0.0/16.

**Grupos de segurança de rede**: os [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm ACLs (listas de controle de acesso) que permitem ou negam o tráfego dentro de uma rede virtual. Os NSGs podem ser usados para proteger o tráfego em nível individual de VM ou sub-rede. Os seguintes NSGs existem:
  - Um NSG para o Active Directory
  - Um NSG para a carga de trabalho

Cada NSG tem portas e protocolos específicos abertos para que a solução possa funcionar segura e corretamente. Além disso, as seguintes configurações estão habilitadas para cada NSG:
  - [Eventos e logs de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) são habilitados e armazenados em uma conta de armazenamento
  - O Log Analytics está conectado ao [diagnóstico do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sub-redes**: cada sub-rede é associada ao seu NSG correspondente.

### <a name="data-in-transit"></a>Dados em trânsito
O Azure criptografa todas as comunicações entre datacenters do Azure por padrão. Todas as transações para o Armazenamento do Azure por meio do portal do Azure ocorrem por HTTPS.

### <a name="data-at-rest"></a>Dados em repouso

A arquitetura protege dados em repouso usando criptografia, auditoria de banco de dados e outras medidas.

**Armazenamento do Azure** Para atender aos requisitos de criptografia de dados em repouso, todo o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption). Isso ajuda a proteger e defender dados pessoais para dar suporte a compromissos de segurança organizacional e aos requisitos de conformidade definidos pelo GDPR.

**Azure Disk Encryption**
[O Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para discos de dados. A solução é integrada ao Azure Key Vault para ajudar a controlar e gerenciar as chaves de criptografia de disco.

**Banco de Dados SQL do Azure**: a instância do Banco de Dados SQL do Azure usa as seguintes medidas de segurança de banco de dados:
-   A [autenticação e a autorização do AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades dos usuários de banco de dados e de outros serviços da Microsoft em uma única localização central.
-   A [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) controla os eventos de banco de dados e grava-os em um log de auditoria em uma conta de armazenamento do Azure.
-   O Banco de Dados SQL do Azure está configurado para usar [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia do banco de dados, dos backups associados e dos arquivos de log de transações para proteger as informações em repouso. O TDE fornece a garantia de que os dados pessoais armazenados não tenham ficado sujeitos a acesso não autorizado.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem o acesso aos servidores do banco de dados até que as permissões apropriadas sejam concedidas. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.
-   A [Detecção de Ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite detectar e responder a possíveis ameaças conforme elas ocorrem, fornecendo alertas de segurança sobre atividades suspeitas no banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões anormais de acesso ao banco de dados.
-   As [colunas Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que dados pessoais confidenciais nunca sejam exibidos como texto não criptografado dentro do sistema do banco de dados. Após a habilitação da criptografia de dados, somente aplicativos cliente ou servidores de aplicativo com acesso às chaves poderão acessar dados de texto não criptografado.
- [Propriedades estendidas](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) podem ser usadas para interromper o processamento de entidades de dados, pois permitem aos usuários adicionar propriedades personalizadas para objetos de banco de dados e marcar os dados como "Descontinuado" para dar suporte à lógica de aplicativo para evitar o processamento de dados pessoais associados.
- A [Segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite aos usuários definir políticas para restringir o acesso aos dados para interromper o processamento.
- A [DDM (Máscara de Dados Dinâmicos) do SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados pessoais confidenciais mascarando os dados para usuários ou aplicativos não privilegiados. A DDM pode descobrir automaticamente dados potencialmente confidenciais e sugerir as máscaras apropriadas a serem aplicadas. Isso ajuda com a identificação dos dados pessoais que se qualificam para a proteção de GDPR e para reduzir o acesso, de modo que ele não saia do banco de dados por meio de acesso não autorizado. **Observação: os clientes precisarão ajustar as configurações da DDM para seguir o esquema de banco de dados que usam.**

### <a name="identity-management"></a>Gerenciamento de identidades
As tecnologias a seguir oferecem recursos para gerenciar o acesso a dados pessoais no ambiente do Azure:
-   O [AAD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidades e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários para esta solução são criados no AAD, incluindo os usuários que acessam o Banco de Dados do SQL Azure.
-   A autenticação para o aplicativo é executada usando o AAD. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a criptografia de coluna do banco de dados usa o AAD para autenticar o aplicativo no Banco de Dados SQL do Azure. Para obter mais informações, veja como [proteger dados confidenciais no Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   O [RBAC (Controle de Acesso Baseado em Função) do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso de que os usuários precisam para realizar seus trabalhos. Em vez de fornecer permissões irrestritas aos recursos do Azure para todos os usuários, os administradores podem permitir apenas determinadas ações para acessar dados pessoais. O acesso à assinatura é limitado ao administrador da assinatura.
- O [PIM (Privileged Identity Management) do AAD](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ajuda os administradores do Azure a minimizar o número de usuários que têm acesso a determinadas informações como dados pessoais.  Os administradores podem usar o Privileged Identity Management do AAD para descobrir, restringir e monitorar identidades privilegiadas e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor o acesso administrativo sob demanda Just-In-Time quando necessário.
-   O [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades da organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas às identidades da organização e investiga incidentes suspeitos e toma a medida apropriada para resolvê-las.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: a solução usa o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para gerenciar chaves e segredos. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Os seguintes recursos do Azure Key Vault ajudam os clientes a proteger dados pessoais e o acesso a esses dados:
- Políticas de acesso avançadas são configuradas com base na necessidade.
- As políticas de acesso do Key Vault são definidas com o mínimo de permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault têm datas de validade.
- Todas as chaves no Key Vault são protegidas pelos HSMs (módulos de segurança de hardware) especializados. O tipo de chave é uma Chave RSA de 2048 bits protegida por HSM.
- Todos os usuários e identidades recebem as permissões mínimas requeridas usando o RBAC.
- Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
- As operações criptográficas permitidas para chaves são restritas às necessárias.

**Alertas de segurança**: a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) permite que os clientes monitorem o tráfego, coletem logs e analisem as fontes de dados para as ameaças. Além disso, a Central de Segurança do Azure acessa a configuração existente de serviços do Azure para fornecer recomendações de serviço e configuração para ajudar a melhorar a postura de segurança e a proteger dados pessoais. A Central de Segurança do Azure inclui um [relatório de inteligência de ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) para cada ameaça detectada para ajudar as equipes de resposta a incidente a investigar e corrigir as ameaças.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

O [Log Analytics](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) fornece registro em log extensivo de atividades do sistema e de usuário, bem como integridade do sistema. A solução [Log Analytics](https://azure.microsoft.com/services/log-analytics/) coleta e analisa dados gerados por recursos no Azure e em ambientes locais.
- **Logs de atividades:** os [Logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem insights sobre as operações executadas nos recursos de uma assinatura. Os logs de atividade podem ajudar a determinar o iniciador, o horário da ocorrência e o status de uma operação.
- **Logs de diagnóstico**: os [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os logs emitidos por todos os recursos. Eles incluem logs do sistema de eventos do Windows e logs de fila, de tabelas e do Armazenamento de Blobs do Azure.
- **Arquivamento de logs**: todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento, com um período de retenção de 2 dias definido. Esses logs são conectados ao Azure Log Analytics para processamento, armazenamento e criação de relatórios de painéis.

Além disso, as seguintes soluções de monitoramento são incluídas como parte dessa arquitetura:
-   [Avaliação do AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): a solução de Verificação de Integridade do Active Directory avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece uma lista priorizada de recomendações específicas da infraestrutura do servidor implantado.
-   [Avaliação de Antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): a solução Antimalware fornece informações sobre malware, ameaças e status de proteção.
-   [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): a solução de Automação do Azure armazena, executa e gerencia runbooks.
-   [Segurança e Auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): o painel Segurança e Auditoria fornece insights de alto nível sobre o estado de segurança dos recursos, fornecendo métricas sobre domínios de segurança, problemas importantes, detecções, inteligência contra ameaças e consultas de segurança comuns.
-   [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): a solução de Verificação de Integridade do SQL avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece aos clientes uma lista priorizada de recomendações específicas da infraestrutura do servidor implantado.
-   [Gerenciamento de Atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): a solução de Gerenciamento de Atualizações permite que os clientes gerenciem as atualizações de segurança do sistema operacional, incluindo o status das atualizações disponíveis e o processo de instalação das atualizações necessárias.
-   [Integridade do Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): a solução de Integridade do Agente informa quantos agentes estão implantados e sua distribuição geográfica, bem como quantos agentes não estão respondendo e o número de agentes que estão enviando dados operacionais.
-   [Logs de Atividades do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): a solução de Análise do Log de Atividades ajuda a analisar os logs de atividades do Azure em todas as assinaturas do Azure de um cliente.
-   [Controle de Alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking): a solução de Controle de Alterações permite que os clientes identifiquem alterações no ambiente facilmente.

**Azure Monitor**
[O Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ajuda os clientes a rastrear o desempenho, manter a segurança e identificar tendências, permitindo que as organizações auditem, criem alertas e arquivem dados, incluindo o rastreamento de chamadas à API nos recursos do Azure dos clientes.

**Application Insights**
[O Application Insights](https://docs.microsoft.com/azure/application-insights/) é um serviço de APM (Gerenciamento de Desempenho de Aplicativos) extensível para desenvolvedores da Web em várias plataformas. Use-o para monitorar o aplicativo Web online. Ele detecta anomalias de desempenho e inclui ferramentas de análise avançadas para ajudar a diagnosticar problemas e entender o que os usuários realmente fazem com o aplicativo. Ele foi projetado para ajudar os usuários a aprimorar continuamente o desempenho e a usabilidade.

## <a name="threat-model"></a>Modelo de ameaça

O diagrama de fluxo de dados dessa arquitetura de referência está disponível para [download](https://aka.ms/gdprAnalyticsdfd) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entenderem os pontos de risco em potencial na infraestrutura do sistema ao fazer modificações.

![Modelo de ameaça de análise para GDPR](images/gdpr-analytics-threat-model.png?raw=true "Modelo de ameaça de análise para GDPR")

## <a name="compliance-documentation"></a>Documentação de conformidade
O [Blueprint de Segurança e Conformidade do Azure – Matriz de responsabilidades do cliente do GDPR](https://aka.ms/gdprCRM) lista as responsabilidades do processador e do controlador para todos os artigos do GDPR. Observe que, para os serviços do Azure, um cliente é geralmente o controlador e Microsoft atua como o processador.

O [Blueprint de Segurança e Conformidade do Azure – Matriz de implementação da análise de dados do GDPR](https://aka.ms/gdprAnalytics) fornece informações sobre quais artigos do GDPR são abordados pela arquitetura de análise de dados, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada artigo abordado.


## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel VPN ou [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) seguro precisa ser configurado para estabelecer uma conexão de forma segura com os recursos implantados como parte dessa arquitetura de referência de análise de dados. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Implementando um túnel de VPN seguro com o Azure, é possível criar uma conexão privada virtual entre uma rede local e uma Rede Virtual do Azure. Essa conexão ocorre pela Internet e permite que os clientes enviem informações por "túnel” com segurança usando um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. O [modo de túnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego do túnel de VPN passa pela Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link de WAN dedicado entre o Azure e a instalação local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não ocorrem pela Internet, elas oferecem mais confiabilidade, mais velocidade, latências mais baixas e maior segurança que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicações do cliente, os dados não passam pela Internet e, portanto, não estão expostos a ela.

Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) práticas recomendadas para a implementação de uma rede híbrida segura que estende uma rede local para o Azure.

### <a name="extract-transform-load-etl-process"></a>Processo ETL (extração, transformação e carregamento)
O [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) pode carregar dados no Banco de Dados SQL do Azure sem a necessidade uma ferramenta separada de ETL ou de importação. O PolyBase permite acessar dados por meio de consultas T-SQL. É possível usar a pilha de análise de business intelligence da Microsoft com o PolyBase, bem como ferramentas de terceiros compatíveis com o SQL Server.

### <a name="azure-active-directory-setup"></a>Configuração do Azure Active Directory
O [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é essencial para gerenciar a implantação e provisionar acesso para pessoas que interagem com o ambiente. Um Active Directory existente do Windows Server pode ser integrado ao AAD em [quatro cliques](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Os clientes também podem associar a infraestrutura (controladores de domínio) do Active Directory implantado a um AAD existente fazendo da infraestrutura do Active Directory implantado um subdomínio de uma floresta do AAD.

## <a name="disclaimer"></a>Isenção de responsabilidade

 - Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de consulta interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.
 - Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
 - Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
