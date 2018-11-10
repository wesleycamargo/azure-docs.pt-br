---
title: 'Blueprint de segurança e conformidade do Azure: aplicativo Web de IaaS para GDPR'
description: 'Blueprint de segurança e conformidade do Azure: aplicativo Web de IaaS para GDPR'
services: security
author: jomolesk
ms.assetid: 04d5239c-fff0-4c2d-9379-1fa79ddbec78
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: f5f04fdaa12c716b21bafcc0abccc719428fe791
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242068"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-gdpr"></a>Blueprint de segurança e conformidade do Azure: aplicativo Web de IaaS para GDPR

## <a name="overview"></a>Visão geral
O GDPR (Regulamento Geral sobre a Proteção de Dados) contém muitos requisitos sobre coletar, armazenar e usar informações pessoais, incluindo como as organizações identificam e protegem os dados pessoais, acomodam requisitos de transparência, detectam e relatam violações de dados pessoais e treinam o pessoal de privacidade e outros funcionários. O GDPR fornece às pessoas um controle maior sobre seus dados pessoais e impõe muitas obrigações novas às organizações que coletam, manipulam ou analisam dados pessoais. O GDPR impõe novas regras às organizações que oferecem bens e serviços para pessoas da UE (União Europeia) ou que coletam e analisam dados vinculados a residentes da UE. O GDPR se aplica independentemente de onde uma organização está localizada.

A Microsoft projetou o Azure com medidas de segurança e políticas de privacidade líderes do setor para proteger os dados na nuvem, incluindo as categorias de dados pessoais identificadas pelo GDPR. Os [termos contratuais](https://aka.ms/Online-Services-Terms) da Microsoft submetem a Microsoft aos requisitos dos processadores.

Esta Blueprint de segurança e conformidade do Azure fornece diretrizes para implantar um ambiente de IaaS (infraestrutura como serviço) adequada para um único aplicativo Web voltado para a Internet. Esta solução demonstra maneiras em que os clientes podem atender a requisitos de segurança e conformidade específicos do GDPR e servir como uma base para os clientes criarem e configurarem suas próprias soluções de aplicativo Web de IaaS no Azure. Os clientes podem utilizar esta arquitetura de referência e seguir o [processo de quatro etapas](https://aka.ms/gdprebook) da Microsoft em sua jornada para conformidade com o GDPR:
1. Descobrir: identificar quais dados pessoais existem e onde eles residem.
2. Gerenciar: controlar como os dados pessoais são usados e acessados.
3. Proteger: estabelecer controles de segurança para evitar, detectar e responder a violações de dados e vulnerabilidades.
4. Relatar: manter a documentação necessária e gerenciar solicitações de dados e notificações de violação.

Essa arquitetura de referência, o guia de implementação associado e os modelos de ameaça são destinados a servir como uma base que os clientes podem adaptar a seus requisitos específicos e não devem ser usados no estado em que se encontra em um ambiente de produção. Observe o seguinte:
- A arquitetura fornece uma linha de base para ajudar os clientes implantarem cargas de trabalho no Azure em conformidade com o GDPR.
- Os clientes são responsáveis por realizar as devidas avaliações de segurança e conformidade de qualquer solução criada usando essa arquitetura, uma vez que os requisitos podem variar com base nas particularidades da implementação de cada cliente.

## <a name="architecture-diagram-and-components"></a>Diagrama e componentes da arquitetura
A solução implanta uma arquitetura de referência para um aplicativo Web de IaaS com um back-end do SQL Server. A arquitetura inclui uma camada da Web, uma camada de dados, a infraestrutura do Active Directory, o Gateway de Aplicativo e o Load Balancer. As máquinas virtuais implantadas nas camadas da Web e de dados são configuradas em um conjunto de disponibilidade, e as instâncias do SQL Server são configuradas em um grupo de disponibilidade AlwaysOn para alta disponibilidade. As máquinas virtuais são associadas ao domínio e as políticas de grupo do Active Directory são usadas para aplicar configurações de conformidade e segurança no nível do sistema operacional. Um bastião de gerenciamento fornece conexão segura para os administradores acessarem recursos implantados. **O Azure recomenda configurar uma conexão VPN ou do ExpressRoute para gerenciamento e importação de dados para a sub-rede na arquitetura de referência.**

![Diagrama de arquitetura de referência do aplicativo Web de IaaS para GDPR](images/gdpr-iaaswa-architecture.png?raw=true "Diagrama de arquitetura de referência do aplicativo Web de IaaS para GDPR")

A solução usa os serviços do Azure a seguir. Os detalhes da arquitetura de implantação estão localizados na seção [Arquitetura de implantação](#deployment-architecture).

- Máquinas Virtuais do Azure
    - (1) gerenciamento/bastião (Windows Server 2016 Datacenter)
    - (2) controlador de domínio do Active Directory (Windows Server 2016 Datacenter)
    - (2) nó de cluster do SQL Server (SQL Server 2017 no Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Conjuntos de Disponibilidade
    - (1) controladores de domínio do Active Directory
    - (1) nós de cluster do SQL
    - (1) Web/IIS
- Rede Virtual do Azure
    - (1)/16 redes virtuais
    - (5)/24 sub-redes
    - As configurações de DNS são definidas em ambos os controladores de domínio
- Azure Load Balancer
- Gateway de Aplicativo do Azure
    - (1) Gateway de Aplicativo WAF habilitado
        - modo de firewall: prevenção
        - conjunto de regras: OWASP 3.0
        - ouvinte: porta 443
- Armazenamento do Azure
    - (7) contas de armazenamento com redundância geográfica
- Testemunha de nuvem
- Cofre dos Serviços de Recuperação
- Cofre da Chave do Azure
- Azure Active Directory (AAD)
- Azure Resource Manager
- Log Analytics
- Central de Segurança do Azure

## <a name="deployment-architecture"></a>Arquitetura de implantação
A seção a seguir fornece detalhes sobre os elementos de implantação e implementação.

**Host bastião**: o host bastião é o único ponto de entrada que permite que os usuários acessem os recursos implantados nesse ambiente. O host bastião fornece uma conexão segura com os recursos implantados, permitindo apenas tráfego remoto de endereços IP públicos em uma lista de segurança. Para permitir o tráfego RDP (área de trabalho remota), a origem do tráfego precisa ser definida no NSG (Grupo de Segurança de Rede).

Essa solução cria uma máquina virtual como um host bastião ingressado em domínio com as seguintes configurações:
-   [Extensão de antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Extensão do Log Analytics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extensão de Diagnóstico do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) usando o Azure Key Vault
-   Uma [política de desligamento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reduzir o consumo de recursos de máquina virtual quando eles não estiverem em uso
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) habilitado, para que as credenciais e outras informações secretas sejam executadas em um ambiente protegido isolado do sistema operacional em execução

### <a name="virtual-network"></a>Rede virtual
A arquitetura define uma rede virtual privada com um espaço de endereço de 10.200.0.0/16.

**Grupos de segurança de rede**: essa solução implanta recursos em uma arquitetura com uma sub-rede da Web, uma sub-rede do banco de dados, uma sub-rede do Active Directory e uma sub-rede de gerenciamento separadas em uma rede virtual. Sub-redes são separadas logicamente pelas regras do grupo de segurança de rede aplicadas a sub-redes individuais para restringir o tráfego entre as sub-redes para somente o que é necessário para a funcionalidade de gerenciamento e de sistema.

Consulte a configuração dos [grupos de segurança de rede](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implantada com esta solução. As organizações podem configurar Grupos de Segurança de Rede editando o arquivo acima usando [esta documentação](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) como guia.

Cada uma das camadas de rede tem um NSG (grupo de segurança de rede dedicado):
- 1 NSG para o Gateway de Aplicativo (LBNSG)
- 1 NSG para o host bastião (MGTNSG)
- 1 NSG para controladores de domínio primário e de backup (ADNSG)
- 1 NSG para SQL Servers e testemunha de nuvem (SQLNSG)
- 1 NSG para a camada da Web (WEBNSG)

### <a name="data-in-transit"></a>Dados em trânsito
O Azure criptografa todas as comunicações entre datacenters do Azure por padrão. Além disso, todas as transações para o Armazenamento do Azure por meio do portal do Azure ocorrem por HTTPS.

### <a name="data-at-rest"></a>Dados em repouso
A arquitetura protege dados em repouso com diversas medidas, incluindo criptografia e auditoria de banco de dados.

**Armazenamento do Azure**: para atender aos requisitos de criptografia de dados em repouso, todo o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) usa [Criptografia do Serviço de Armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption). Isso ajuda a proteger e defender dados pessoais para dar suporte a compromissos de segurança organizacional e aos requisitos de conformidade definidos pelo GDPR.

**Azure Disk Encryption**: o Azure Disk Encryption é usado para criptografar discos de máquina virtual de IaaS do Windows. O [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita o recurso BitLocker do Windows para fornecer criptografia de volume para o sistema operacional e os discos de dados. A solução é integrada ao Azure Key Vault para ajudar a controlar e a gerenciar as chaves de criptografia de disco.

**SQL Server**: a instância do SQL Server usa as seguintes medidas de segurança de banco de dados:
-   A [autenticação e a autorização do AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permitem o gerenciamento de identidades dos usuários de banco de dados e de outros serviços da Microsoft em uma única localização central.
-   A [auditoria do banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) controla os eventos de banco de dados e grava-os em um log de auditoria em uma conta de armazenamento do Azure.
-   O Banco de Dados SQL está configurado para usar [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que executa criptografia e descriptografia do banco de dados, dos backups associados e dos arquivos de log de transações para proteger as informações em repouso. O TDE fornece a garantia de que os dados pessoais armazenados não tenham ficado sujeitos a acesso não autorizado.
-   [Regras de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impedem o acesso aos servidores do banco de dados até que as permissões apropriadas sejam concedidas. O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.
-   A [Detecção de Ameaças SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite detectar e responder a possíveis ameaças conforme elas ocorrem, fornecendo alertas de segurança sobre atividades suspeitas no banco de dados, vulnerabilidades potenciais, ataques de injeção de SQL e padrões anormais de acesso ao banco de dados.
-   As [colunas Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantem que dados pessoais confidenciais nunca sejam exibidos como texto não criptografado dentro do sistema do banco de dados. Após a habilitação da criptografia de dados, somente aplicativos cliente ou servidores de aplicativo com acesso às chaves poderão acessar dados de texto não criptografado.
- O recurso [Propriedades Estendidas](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) pode ser usado para interromper o processamento de entidades de dados, pois permite aos usuários adicionar propriedades personalizadas a objetos de banco de dados e marcar os dados como "Descontinuado" para dar suporte à lógica de aplicativo para evitar o processamento de dados pessoais associados.
- A [Segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite aos usuários definir políticas para restringir o acesso aos dados para interromper o processamento.
- A [DDM (Máscara de Dados Dinâmicos do Banco de Dados) SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados pessoais confidenciais mascarando os dados para usuários ou aplicativos não privilegiados. A DDM pode descobrir automaticamente dados potencialmente confidenciais e sugerir as máscaras apropriadas a serem aplicadas. Isso ajuda com a identificação dos dados pessoais que se qualificam para a proteção de GDPR e para reduzir o acesso, de modo que ele não saia do banco de dados por meio de acesso não autorizado. **Observação: os clientes precisarão ajustar as configurações da DDM para seguir o esquema de banco de dados que usam.**

### <a name="identity-management"></a>Gerenciamento de identidades
As tecnologias a seguir oferecem recursos para gerenciar o acesso a dados pessoais no ambiente do Azure:
-   O [AAD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) é o serviço de gerenciamento de identidades e diretório multilocatário baseado em nuvem da Microsoft. Todos os usuários para esta solução são criados no AAD, incluindo os usuários que acessam o SQL Server.
-   A autenticação para o aplicativo é executada usando o AAD. Para saber mais, confira [Integrando aplicativos com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a criptografia de coluna do banco de dados usa o AAD para autenticar o aplicativo no SQL Server. Para obter mais informações, veja como [proteger dados confidenciais no Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   O [RBAC (Controle de Acesso Baseado em Função) do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite que os administradores definam permissões de acesso refinadas para conceder apenas a quantidade de acesso de que os usuários precisam para realizar seus trabalhos. Em vez de fornecer permissões irrestritas aos recursos do Azure para todos os usuários, os administradores podem permitir apenas determinadas ações para acessar dados pessoais. O acesso à assinatura é limitado ao administrador da assinatura.
- O [Privileged Identity Management do AAD](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) ajuda os administradores do Azure a minimizar o número de usuários que têm acesso a determinados recursos.  Os administradores podem usar o Privileged Identity Management do AAD para descobrir, restringir e monitorar identidades privilegiadas e seu acesso aos recursos. Essa funcionalidade também pode ser usada para impor o acesso administrativo sob demanda Just-In-Time quando necessário.
- O [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta possíveis vulnerabilidades que afetam as identidades da organização, configura as respostas automatizadas para detectar ações suspeitas relacionadas às identidades da organização e investiga incidentes suspeitos e toma a medida apropriada para resolvê-las.
- Uma instância do Active Directory IaaS implantada fornece gerenciamento de identidade no nível do sistema operacional para máquinas virtuais IaaS implantadas.

### <a name="security"></a>Segurança
**Gerenciamento de segredos**: a solução usa o [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para gerenciar chaves e segredos. O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Os seguintes recursos do Azure Key Vault ajudam os clientes a proteger dados pessoais e o acesso a esses dados:
- Políticas de acesso avançadas são configuradas com base na necessidade.
- As políticas de acesso do Key Vault são definidas com o mínimo de permissões necessárias para chaves e segredos.
- Todas as chaves e segredos no Key Vault têm datas de validade.
- Todas as chaves no Key Vault são protegidas pelos HSMs (módulos de segurança de hardware) especializados. O tipo de chave é uma Chave RSA de 2048 bits protegida por HSM.
- Todos os usuários e identidades recebem as permissões mínimas requeridas usando o RBAC.
- Os Logs de diagnóstico para Key Vault são habilitados com um período de retenção de pelo menos 365 dias.
- As operações criptográficas permitidas para chaves são restritas às necessárias.
- A solução é integrada ao Azure Key Vault para gerenciar as chaves de criptografia de disco e segredos da máquina virtual IaaS.

**Gerenciamento de patches**: máquinas virtuais do Windows implantadas como parte dessa arquitetura de referência são configuradas por padrão para receber atualizações automáticas do serviço Windows Update. Essa solução também inclui o serviço [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) por meio do qual as implantações atualizadas podem ser criadas para aplicar patch em máquinas virtuais quando necessário.

**Proteção contra malware**: o [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Máquinas Virtuais oferece funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando ocorrem tentativas de instalar ou executar softwares mal-intencionados ou indesejados conhecidos em máquinas virtuais protegidas.

**Alertas de segurança**: a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) permite que os clientes monitorem o tráfego, coletem logs e analisem as fontes de dados para as ameaças. Além disso, a Central de Segurança do Azure acessa a configuração existente de serviços do Azure para fornecer recomendações de serviço e configuração para ajudar a melhorar a postura de segurança e a proteger dados pessoais. A Central de Segurança do Azure inclui um [relatório de inteligência de ameaças](https://docs.microsoft.com/azure/security-center/security-center-threat-report) para cada ameaça detectada para ajudar as equipes de resposta a incidente a investigar e corrigir as ameaças.

**Gateway de Aplicativo**: a arquitetura reduz o risco de vulnerabilidades de segurança usando o Gateway de Aplicativo com o WAF (firewall do aplicativo Web) e o conjunto de regras OWASP habilitados. Dentre outros recursos estão:

- [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Habilitar [Descarregamento SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Desabilitar [TLS versões 1.0 e 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall do aplicativo Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo WAF)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com conjunto de regras OWASP 3.0
- [Habilitar o registro em log de diagnóstico](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Investigações de integridade personalizadas](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- A [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center) e o [Assistente do Azure](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fornecer proteção e notificações adicionais. A Central de Segurança do Azure também fornece um sistema de reputação.

### <a name="business-continuity"></a>Continuidade dos negócios

**Alta disponibilidade**: a solução implementa todas as máquinas virtuais em um [Conjunto de Disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Os conjuntos de disponibilidade fazem com que as máquinas virtuais sejam distribuídas entre vários clusters de hardware isolados para melhorar a disponibilidade. Pelo menos uma máquina virtual fica disponível durante um evento de manutenção planejada ou não planejada, atendendo ao SLA do Azure de 99,95%.

**Cofre dos Serviços de Recuperação**: o [Cofre dos Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) armazena dados de backup e protege todas as configurações das Máquinas Virtuais do Azure nesta arquitetura. Com um Cofre dos Serviços de Recuperação, os clientes podem restaurar arquivos e pastas de uma VM de IaaS sem restaurar a VM inteira, acelerando o tempo de restauração.

**Testemunha de Nuvem**: a [Testemunha de Nuvem](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) é um tipo de testemunha de quorum do cluster de failover no Windows Server 2016 que aproveita o Azure como o ponto de arbitragem. A Testemunha de Nuvem, como qualquer outra testemunha de quorum, obtém um voto e pode participar dos cálculos de quorum, mas usa o Armazenamento de Blobs do Azure padrão, disponível ao público. Isso elimina a sobrecarga adicional de manutenção de VMs hospedadas em uma nuvem pública.

### <a name="logging-and-auditing"></a>Registro em log e auditoria

O Log Analytics fornece registro extensivo de atividades do sistema e do usuário, bem como integridade do sistema. A solução [Log Analytics](https://azure.microsoft.com/services/log-analytics/) coleta e analisa dados gerados por recursos no Azure e em ambientes locais.
- **Logs de atividades:** os [Logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem insights sobre as operações executadas nos recursos de uma assinatura. Os logs de atividade podem ajudar a determinar o iniciador, o horário da ocorrência e o status de uma operação.
- **Logs de diagnóstico**: os [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluem todos os logs emitidos por todos os recursos. Esses logs são logs do sistema de eventos do Windows, logs de Armazenamento do Azure, logs de auditoria do Key Vault e logs de acesso e firewall do Gateway de Aplicativo.
- **Arquivamento de logs**: todos os logs de diagnóstico são gravados em uma conta de armazenamento do Azure centralizada e criptografada para arquivamento. A retenção é configurável pelo usuário, de até 730 dias, para atender aos requisitos de retenção específicos da organização. Esses logs são conectados ao Azure Log Analytics para processamento, armazenamento e criação de relatórios de painéis.

Além disso, as seguintes soluções de monitoramento são incluídas como parte dessa arquitetura:
-   [Avaliação do AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): a solução de Verificação de Integridade do Active Directory avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece uma lista priorizada de recomendações específicas da infraestrutura do servidor implantado.
-   [Avaliação de Antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): a solução Antimalware fornece informações sobre malware, ameaças e status de proteção.
-   [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): a solução de Automação do Azure armazena, executa e gerencia runbooks.
-   [Segurança e Auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): o painel Segurança e Auditoria fornece insights de alto nível sobre o estado de segurança dos recursos, fornecendo métricas sobre domínios de segurança, problemas importantes, detecções, inteligência contra ameaças e consultas de segurança comuns.
-   [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): a solução de Verificação de Integridade do SQL avalia o risco e a integridade dos ambientes de servidor em intervalos regulares e fornece aos clientes uma lista priorizada de recomendações específicas da infraestrutura do servidor implantado.
-   [Gerenciamento de Atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): a solução de Gerenciamento de Atualizações permite que os clientes gerenciem as atualizações de segurança do sistema operacional, incluindo o status das atualizações disponíveis e o processo de instalação das atualizações necessárias.
-   [Integridade do Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): a solução de Integridade do Agente informa quantos agentes estão implantados e sua distribuição geográfica, bem como quantos agentes não estão respondendo e o número de agentes que estão enviando dados operacionais.
-   [Logs de Atividades do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): a solução de Análise do Log de Atividades ajuda a analisar os logs de atividades do Azure em todas as assinaturas do Azure de um cliente.
-   [Controle de Alterações](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): a solução de Controle de Alterações permite que os clientes identifiquem alterações no ambiente facilmente.

## <a name="threat-model"></a>Modelo de ameaça

O DFD (diagrama de fluxo de dados) dessa arquitetura de referência está disponível para [download](https://aka.ms/gdprIaaSdfd) ou pode ser encontrado abaixo. Esse modelo pode ajudar os clientes a entenderem os pontos de risco em potencial na infraestrutura do sistema ao fazer modificações.

![Modelo de ameaça de aplicativo Web de IaaS para GDPR](images/gdpr-iaaswa-threat-model.png?raw=true "Modelo de ameaça de aplicativo Web de IaaS para GDPR")

## <a name="compliance-documentation"></a>Documentação de conformidade

O [Blueprint de Segurança e Conformidade do Azure – Matriz de responsabilidades do cliente do GDPR](https://aka.ms/gdprCRM) lista as responsabilidades do processador e do controlador para todos os artigos do GDPR. Observe que, para os serviços do Azure, um cliente é geralmente o controlador e Microsoft atua como o processador.

O [Blueprint de Segurança e Conformidade do Azure – Matriz de implementação do aplicativo Web de IaaS do GDPR](https://aka.ms/gdprIaaSweb) fornece informações sobre quais artigos do GDPR são abordados pela arquitetura de aplicativo Web de IaaS, incluindo descrições detalhadas de como a implementação atende aos requisitos de cada artigo abordado.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações
### <a name="vpn-and-expressroute"></a>VPN e ExpressRoute
Um túnel de VPN seguro ou o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) precisa ser configurado para estabelecer uma conexão segura com os recursos implantados como parte dessa arquitetura de referência de aplicativo Web de IaaS. Configurando adequadamente o ExpressRoute ou uma VPN, os clientes podem adicionar uma camada de proteção para os dados em trânsito.

Implementando um túnel de VPN seguro com o Azure, é possível criar uma conexão privada virtual entre uma rede local e uma Rede Virtual do Azure. Essa conexão ocorre pela Internet e permite que os clientes enviem informações por "túnel” com segurança usando um link criptografado entre a rede do cliente e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. O [modo de túnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) é usado nessa opção como um mecanismo de criptografia.

Como o tráfego do túnel de VPN passa pela Internet com uma VPN site a site, a Microsoft oferece outra opção de conexão ainda mais segura. O Azure ExpressRoute é um link de WAN dedicado entre o Azure e a instalação local ou um provedor de hospedagem do Exchange. Como as conexões do ExpressRoute não ocorrem pela Internet, elas oferecem mais confiabilidade, mais velocidade, latências mais baixas e maior segurança que as conexões típicas pela Internet. Além disso, como essa é uma conexão direta do provedor de telecomunicações do cliente, os dados não passam pela Internet e, portanto, não estão expostos a ela.

Estão [disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) práticas recomendadas para a implementação de uma rede híbrida segura que estende uma rede local para o Azure.

## <a name="disclaimer"></a>Isenção de responsabilidade

- Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.  
- Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.  
- Os clientes podem copiar e usar este documento para fins de consulta interna.  
- Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.  
- Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
- Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
