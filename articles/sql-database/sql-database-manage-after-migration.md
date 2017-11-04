---
title: "Gerenciar após a migração - Banco de Dados SQL do Azure | Microsoft Docs"
description: "Saiba como gerenciar o banco de dados após a migração para o Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: joesackmsft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 10/14/2016
ms.author: Joe.Sack
ms.suite: SQL
ms.prod_service: sql-database
ms.component: management
ms.openlocfilehash: 663ab4aaf229f8a88d1116b34ccb74450aa66c9d
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="how-should-i-manage-my-azure-sql-database-after-migration"></a>Como posso gerenciar meu Banco de Dados SQL do Azure após a migração?

*Perguntas frequentes em relação ao gerenciamento de seus investimentos de Banco de Dados SQL*

Você recentemente moveu bancos de dados SQL Server para o Banco de Dados SQL do Azure, ou talvez planeja mover muito em breve. Depois de movido, o que vem a seguir? Considerando que o Banco de Dados SQL é uma *Plataforma como Serviço*, a Microsoft lida com várias áreas em seu nome. Mas como isso muda as práticas da sua empresa em relação a áreas principais, como segurança, continuidade de negócios, manutenção de banco de dados, ajuste de desempenho, monitoramento e outras? 

A finalidade deste artigo é organizar, de forma sucinta, os recursos e orientações que você vai precisar para fazer a mudança a fim de gerenciar de seus investimentos de Banco de Dados SQL. As principais áreas neste artigo abrangem a continuidade dos negócios, a segurança, a manutenção do banco de dados e a movimentação de dados de desempenho e monitoramento. Vamos abordar as principais áreas que são diferentes entre o SQL Server e o Banco de Dados SQL e indicar as melhores práticas operacionais que ajudarão a maximizar benefícios e a minimizar o risco. 

## <a name="manage-business-continuity-after-migration"></a>Gerenciar a continuidade dos negócios após a migração

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Como fazer para criar e gerenciar backups no Banco de Dados SQL? 
O Banco de Dados SQL faz backup de bancos de dados para você automaticamente e permite fazer a restauração para algum ponto no período de retenção. O período de retenção é de 35 dias para bancos de dados Standard e Premium e de 7 dias para bancos de dados Básico. Além disso, o recurso Retenção de Longo Prazo permite que você mantenha os arquivos de backup por um período mais longo (até 10 anos) e faça a restauração desses backups em qualquer ponto. Além disso, os backups de banco de dados são replicados geograficamente para garantir a capacidade de restauração em qualquer região geográfica em caso de desastre ou de catástrofe regional. Confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-a-regional-catastrophe"></a>Como fazer para garantir a continuidade no caso de um desastre no nível do datacenter ou uma catástrofe regional? 

Os backups de Banco de Dados são replicados geograficamente para garantir a capacidade de restauração em qualquer região geográfica em caso de desastre ou catástrofe regional. Confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md). Além disso, o Banco de Dados SQL permite manter os bancos de dados secundários com replicação geográfica ativa em outra região. Configurá-los em um grupo de failover automático fará com que os bancos de dados façam failover automaticamente para o secundário em caso de desastre. Se um grupo de failover automático não estiver configurado, seu aplicativo precisará monitorar um desastre e iniciar um failover para o secundário ativamente. 
### <a name="sql-server-provided-me-readable-secondary-replicas-can-i-access-the-secondaries-on-sql-database"></a>O SQL Server me forneceu Réplicas Secundárias Legíveis; posso acessar secundários no Banco de Dados SQL? 

Sim, o recurso 'Replicação Geográfica Ativa' é usado para criar Réplicas Secundárias Legíveis. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premise-to-sql-database"></a>Como o plano de recuperação de desastres muda do local para o Banco de Dados SQL? 
As implementações do SQL Server exigia que você gerenciasse backups ativamente usando recursos como o Clustering de Failover, o Espelhamento de Banco de Dados, a Replicação, o Envio de Logs ou simplesmente backups BACPAC. No entanto, no Banco de Dados SQL, os backups são totalmente gerenciados pela Microsoft e só é possível ter planos de recuperação de desastre e backup configurados e funcionando com apenas alguns cliques no portal do Azure (ou alguns comandos do PowerShell). ‌
### <a name="in-the-event-of-disaster-how-do-i-recover-my-databases"></a>No caso de desastre, como faço para recuperar meus bancos de dados? 
O Banco de Dados SQL permite que você restaure os bancos de dados para qualquer ponto no tempo dos últimos 35 dias automaticamente. Isso é uma opção quando você perde dados ou enfrenta um desastre relacionado ao aplicativo. 

Em caso de desastre regional, se os bancos de dados com replicação geográfica estiverem configurados, você poderá recuperar de seus bancos de dados de replicação geográfica secundária em outra região. Para obter acesso em tempo real aos seus aplicativos, é possível realizar failover para o secundário de replicação geográfica na outra região manualmente. Como alternativa, se você tiver um grupo de failover automático configurado, esse failover para o secundário de replicação geográfica ocorrerá automaticamente em um cenário de desastre. Se você não tiver configurado o banco de dados secundário com replicação geográfica, ainda poderá recuperar seus bancos de dados de arquivos de backup replicados automaticamente (funcionalidade interna; não é necessário configurar), com tempo de recuperação relativamente mais longo (RTO de 12 horas) e até uma hora de perda de dados. 

### <a name="are-the-failovers-to-secondary-transparent-how-does-my-application-handle-database-failovers"></a>Os failovers para o secundário são transparentes? Como o meu aplicativo lida com failovers de banco de dados? 
Se você tiver configurado grupos de failover automático, o failover para o secundário será transparente. No entanto, se não for o caso, seu aplicativo deverá incorporar a lógica para monitorar a disponibilidade da réplica primária e, em seguida, executar failover manualmente para o secundário. 
 
## <a name="manage-security-after-migration"></a>Gerenciar a segurança após a migração

### <a name="how-can-i-restrict-access-to-my-sql-database"></a>Como posso restringir acesso ao meu Banco de Dados SQL? 
 
Existem algumas maneiras de bloquear o acesso de conectividade para bancos de dados SQL. 
1. A limitação do tráfego pela Rota Expressa da Internet fornece fibra dedicada à rede do Azure para que seus dados não passem pela Internet. Também é possível configurar conectividade entre regiões usando a Rota expressa. Os links abaixo descrevem a Rota Expressa em mais detalhes: 
 - [Introdução à Rota Expressa](../expressroute/expressroute-introduction.md)
 - [Pré-requisitos](../expressroute/expressroute-prerequisites.md) 
 - [Fluxos de trabalho](../expressroute/expressroute-workflows.md) 
 
2. Selecione os recursos que se conectam ao Banco de Dados SQL: 

   Por padrão, o Banco de Dados SQL está configurado para "Permitir todos os serviços do Azure", o que significa que todas as VMs no Azure podem tentar se conectar ao banco de dados.  A autenticação de todos os logons ainda precisa ocorrer. Se você não quiser que seu banco de dados fique acessível a todos os IPs do Azure, poderá desabilitar a opção "Permitir todos os serviços do Azure" e usar [Pontos de extremidade de serviço de VNET](sql-database-vnet-service-endpoint-rule-overview.md) para restringir o acesso de entrada ao banco de dados somente vindo de recursos do Azure que estão dentro de determinada sub-rede de VNET do Azure. 

   ![Pontos de extremidade de serviço de VNET](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

   Uma opção alternativa é provisionar [IPs reservadas](../virtual-network/virtual-networks-reserved-public-ip.md) para suas máquinas virtuais e a colocar na lista de permissões os endereços IP dessas VMs nas configurações de firewall do servidor. (Consulte a captura de tela abaixo como exemplo no portal do Azure.) Ao atribuir IPs reservados, você evita a necessidade de ter que atualizar as regras de firewall com a alteração dos endereços IP. 

3. Evitar a exposição da porta 1433 fora do Azure

   Execute o SSMS no Azure usando o [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Isso não exige que você abra conexões de saída para a porta 1433; o IP é estático para que o banco de dados só possa ser aberto para o RemoteApp. Ele dá suporte à MFA (Autenticação Multifator) e aceita vários usuários. 

### <a name="what-authentication-methods-are-offered-in-sql-database"></a>Quais métodos de autenticação são oferecidos no Banco de Dados SQL?

Os principais métodos de autenticação oferecidos no Banco de Dados SQL e no SQL Data Warehouse são a autenticação do Azure Active Directory e a autenticação do SQL. O Azure AD (Active Directory) é um serviço de gerenciamento de identidade e acesso centralizado, e o SQL é apenas um dos muitos serviços do Azure que estão integrados ao Azure AD. O benefício de um serviço gerenciado centralizado é que as credenciais do usuário são compartilhadas entre todos os serviços do Azure que você usa para facilitar a autenticação. Isso também permite que o Banco de Dados SQL e o SQL Data Warehouse ofereçam autenticação multifator e contas de usuário convidado em um domínio do Azure AD. 

Se você já tiver um Active Directory local, poderá federar o diretório com o Azure Active Directory para estender seu diretório do Azure. 


|||
|---|---|
| Se você...|Banco de Dados SQL do Azure/SQL Data Warehouse do Azure|
| Preferir não usar o Azure AD (Active Directory) no Azure|Use a [autenticação do SQL](sql-database-security-overview.md)|
| Usou AD no SQL Server local|[Federe o AD com o Azure AD](../active-directory/connect/active-directory-aadconnect.md)e use a autenticação do Azure AD. Com isso, você pode usar o logon único.|
| Precisar impor MFA (autenticação multifator)|Exija MFA como uma política por meio de [Acesso Condicional da Microsoft](sql-database-conditional-access.md)e use a [Autenticação universal do Azure AD com suporte a MFA](sql-database-ssms-mfa-authentication.md).|
| Tiver contas de convidado de contas da Microsoft (live.com, outlook.com) ou outros domínios (gmail.com)|Use a [Autenticação universal do Azure AD](sql-database-ssms-mfa-authentication.md) no Banco de Dados SQL/Data Warehouse, que aproveita a [colaboração B2B do Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
| Estiver conectado ao Windows usando suas credenciais do Azure AD de um domínio federado|Use a [autenticação integrada do Azure AD](sql-database-aad-authentication-configure.md).|
| Estiver conectado ao Windows usando credenciais de um domínio não federado com o Azure|Use a [autenticação de senha do Azure AD](sql-database-aad-authentication-configure.md).|
| Tiver serviços de camada intermediária que precisam se conectar ao Banco de Dados SQL do Azure ou ao Data Warehouse|Use a [autenticação de token do Azure AD](sql-database-aad-authentication-configure.md).
|||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-databases-from-the-application-side"></a>Como posso limitar o acesso a dados confidenciais em meus bancos de dados no lado do aplicativo? 

Para impedir que usuários não autorizados possam exibir dados confidenciais, há algumas opções disponíveis no Banco de Dados SQL: 

- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) é uma forma de criptografia no lado do cliente que criptografa as colunas confidenciais no banco de dados (para que fiquem em texto cifrado para administradores de banco de dados e usuários não autorizados). A chave para o Always Encrypted é armazenada no lado do cliente, para que somente clientes autorizados possam descriptografar as colunas confidenciais. O Always Encrypted dá suporte a comparações de igualdade atualmente, para que os DBAs possam continuar a consultar colunas criptografadas como parte dos comandos SQL. O Always Encrypted pode ser usado com uma variedade de opções de armazenamento de chaves, como o [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), o repositório de certificados do Windows e os módulos de segurança de hardware locais.
- A [Máscara de Dados Dinâmicos](sql-database-dynamic-data-masking-get-started.md) é um recurso de mascaramento de dados que limita a exposição de dados confidenciais mascarando-os para usuários sem privilégios na camada de aplicativo. Defina uma regra de mascaramento que pode criar um padrão de mascaramento (por exemplo, para mostrar apenas os últimos quatro dígitos de números de identificação nacional e marcar o restante como X) e identificar quais usuários podem ser excluídos da regra de mascaramento.
- A [Segurança em Nível de Linha](/sql/relational-databases/security/row-level-security) permite a você controlar o acesso às linhas em uma tabela de banco de dados com base o usuário que executa a consulta (uma associação de grupo ou um contexto de execução). A restrição de acesso é feita no nível do banco de dados em vez de em uma camada de aplicativo, para simplificar a lógica do aplicativo. 

### <a name="what-encryption-options-do-i-have-in-sql-database-and-what-actors-does-the-encryption-protect-from"></a>Quais opções de criptografia estão disponíveis no Banco de Dados SQL, e ela protege contra quais atores?
Há três tecnologias de criptografia principais disponíveis no Banco de Dados SQL: 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) (que é mencionado na pergunta acima): criptografa colunas confidenciais na tabela de ponta a ponta, de clientes não autorizados para o disco físico. O servidor e os administradores de dados não podem ver os dados confidenciais, já que as chaves de criptografia ficam armazenadas no cliente. 
- [TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (Transparent Data Encryption): criptografia em repouso, que criptografa no nível do banco de dados e protege os arquivos de dados, os arquivos de log e os backups associados contra roubo de mídia física. A TDE é habilitada por padrão para todos os bancos de dados recém-criados.
 
  O diagrama a seguir mostra uma visão geral das opções de tecnologia de criptografia.

   ![Visão geral de criptografia](./media/sql-database-manage-after-migration/overview-encryption.png)

### <a name="how-should-i-manage-encryption-keys-in-the-cloud"></a>Como devo gerenciar chaves de criptografia na nuvem? 
Há opções de gerenciamento de chaves para Always Encrypted (criptografia no lado do cliente) e Transparent Data Encryption (criptografia em repouso). Recomenda-se a rotação de chaves de criptografia regularmente e em uma frequência que atenda aos regulamentos internos e aos requisitos de conformidade.

- **Always Encrypted**: há uma [hierarquia de duas teclas](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) no Always Encrypted; uma coluna de dados confidenciais é criptografada pela CEK (chave de criptografia de coluna) AES 256 que, por sua vez, é criptografada pela CMK (chave mestra da coluna). Os drivers de cliente fornecidos para Always Encrypted não têm nenhuma limitação de tamanho das CMKs.

  O valor criptografado da CEK é armazenado no banco de dados, e a CMK é armazenada em um repositório de chaves confiável, como o repositório de certificados do Windows, o Azure Key Vault ou um módulo de segurança de hardware. 
  
  Tanto a [CEK quanto a CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) podem ser giradas. A rotação da CEK pode ser demorada, dependendo do tamanho das tabelas contendo colunas criptografadas. Portanto, planeje cuidadosamente as rotações de CEK. No entanto, a rotação da CMK não interfere no desempenho do banco de dados e pode ser feita com funções separadas.

  O diagrama a seguir mostra as opções de armazenamento de chaves para as chaves mestras de coluna no Always Encrypted 

   ![Provedores de repositório CMK Always Encrypted](./media/sql-database-manage-after-migration/always-encrypted.png)

- **TDE (Transparent Data Encryption)**: há uma hierarquia de duas teclas na TDE; os dados em cada banco de dados do usuário são criptografados por uma DEK (chave de criptografia de banco de dados) exclusiva do banco de dados AES-256 que, por sua vez, é criptografada por uma chave mestra assimétrica RSA 2048 exclusiva do servidor. 

  Por padrão, a chave mestra para Transparent Data Encryption é gerenciada pelo serviço Banco de Dados SQL para sua conveniência. Se sua organização deseja ter controle sobre a chave mestra, há uma opção para usar o [Azure Key Vault](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql) como o repositório de chaves. 

  Usando o Azure Key Vault, sua organização assume o controle sobre o provisionamento, a rotação e a permissão das chaves. [Girar ou alternar o tipo de uma chave mestra de TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) é rápido, pois isso só criptografa novamente a DEK. 

  Para organizações com separação de funções entre segurança e gerenciamento de dados, um administrador de segurança pode provisionar o material da chave para a chave mestra de TDE no Azure Key Vault e fornecer um identificador de chave do Azure Key Vault para o administrador de banco de dados para uso na criptografia em repouso em um servidor. 

## <a name="monitoring-and-compliance-after-migration"></a>Monitoramento e conformidade após a migração

### <a name="how-do-i-monitor-database-activities-in-sql-database"></a>Como fazer para monitorar atividades de banco de dados no Banco de Dados SQL?
Existem alguns recursos de monitoramento incorporados ao Banco de Dados SQL que rastreiam a segurança e outros eventos no banco de dados:
- A [Auditoria do SQL](sql-database-auditing.md) permite que você colete os logs de auditoria de eventos de banco de dados em sua própria conta do Armazenamento do Azure.
- A [Detecção de Ameaças SQL](sql-database-threat-detection.md) permite detectar atividades suspeitas que indicam possíveis atos mal-intencionados de acessar, violar ou explorar dados no banco de dados. A Detecção de Ameaças do Banco de Dados SQL é executada em vários conjuntos de algoritmos que detectam possíveis vulnerabilidades e ataques de injeção de SQL, bem como padrões de acesso anormais do banco de dados (como o acesso de um local fora do comum ou por uma entidade desconhecida). Os agentes de segurança ou outros administradores designados recebem uma notificação por email se uma ameaça é detectada no banco de dados. Cada notificação fornece detalhes da atividade suspeita e recomendações sobre como investigar e minimizar a ameaça. 
- A [Avaliação de Vulnerabilidade de SQL](sql-vulnerability-assessment.md) é um serviço de exame e relatório de banco de dados que permite monitorar o estado de segurança de seus bancos de dados em grande escala, além de identificar os riscos de segurança e o descompasso com uma linha de base de segurança definida por você. Após cada verificação, uma lista personalizada de scripts de correção e etapas acionáveis são fornecidos, bem como um relatório de avaliação que pode ser usado para ajudar a alcançar a conformidade. 
- O [Aplicativo de sincronização de segurança do SQL-OMS](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) utiliza APIs públicas do OMS (Operations Management Suite) no envio de logs de auditoria do SQL por push para o OMS para análise de log e a capacidade de definir alertas de detecção personalizada, incluindo: 
 - O bloco e o painel Auditoria do Banco de Dados SQL, que fornecem relatórios claros e coerentes de suas atividades de banco de dados. 
 - O Log Analytics do SQL, para analisar a atividade do banco de dados e investigar as discrepâncias e anomalias que podem indicar suspeitas de violações de segurança.
 - Regras específicas de alertas avançados em eventos observados que disparam email e alertas de runbooks de automação do Azure e de Webhook (ou seja, alterações de senha, ocorrências fora do horário comercial, comandos SQL específicos).
- A [Central de Segurança do Azure](../security-center/security-center-intro.md) oferece gerenciamento de segurança centralizado entre cargas de trabalho em execução no Azure, no local e em outras nuvens. Você pode ver se a proteção essencial para o Banco de Dados SQL, como a Auditoria e a Transparent Data Encryption, está configurada em todos os recursos e criar políticas com base em seus próprios requisitos. 

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>O Banco de Dados SQL está em conformidade com os requisitos regulatórios? Como isso ajuda na conformidade da minha organização? 
O Banco de Dados SQL do Azure atende a várias regras de conformidade regulatória. Para exibir o último conjunto de conformidades atendido, visite o [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings) e veja os detalhes sobre as conformidades que são importantes para sua organização para saber se o Banco de Dados SQL do Azure está incluído nos serviços do Azure em conformidade. É importante observar que, embora o Banco de Dados SQL do Azure possa ser certificado como um serviço em conformidade, ele ajuda na conformidade do serviço de sua organização, mas não garante essa conformidade automaticamente. 

## <a name="database-maintenance-and-monitoring-after-migration"></a>Manutenção de banco de dados e o monitoramento após a migração

### <a name="how-do-i-monitor-growth-in-data-size-and-resource-utilization"></a>Como fazer para monitorar o aumento na utilização de recursos e o tamanho dos dados?

- Você pode monitorar as métricas sobre a utilização de recursos e do tamanho do seu banco de dados no gráfico 'Monitoramento' no portal do Azure. 

  ![Gráfico de monitoramento](./media/sql-database-manage-after-migration/monitoring-chart.png)

- Para obter informações mais detalhadas nas consultas, você pode usar a opção 'Análise de Desempenho de Consulta' disponível no portal do Azure. Isso exigirá que a opção 'Repositório de Consultas' esteja ativa no seu banco de dados.

  ![Análise de desempenho de consultas](./media/sql-database-manage-after-migration/query-performance-insight.png)

- Como alternativa, você pode exibir as métricas usando também as DMVs (Exibições de Gerenciamento Dinâmico) usando [sys.DM db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database). 

### <a name="how-often-do-i-need-to-run-consistency-checks-like-dbcccheckdb"></a>Com que frequência preciso executar verificações de consistência como DBCC_CHECKDB?
DBCC_CHECKDB verifica a integridade lógica e física de todos os objetos no banco de dados. Você não precisa fazer essas verificações porque elas são gerenciadas pela Microsoft no Azure. Para saber mais, confira [Integridade dos dados no Banco de Dados SQL do Azure](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="monitor-performance-and-resource-utilization-after-migration"></a>Monitorar o desempenho e a utilização de recursos após a migração

### <a name="how-do-i-monitor-performance-and-resource-utilization-in-azure-sql-database"></a>Como fazer para monitorar o desempenho e utilização de recursos no Banco de Dados SQL do Azure?
Você pode monitorar o desempenho e a utilização de recursos no Banco de Dados SQL do Azure usando os seguintes métodos:

- **Portal do Azure**: o portal do Azure mostra a utilização de um banco de dados único selecionando o banco de dados e clicando no gráfico no painel Visão geral. Você pode modificar o gráfico para mostrar várias métricas, incluindo a porcentagem de CPU, a porcentagem de DTU, a porcentagem de E/S de dados, a porcentagem de sessões e a porcentagem do tamanho do banco de dados. 
  ![utilização de recursos](./media/sql-database-manage-after-migration/resource-utilization.png)

  Nesse gráfico, você também pode configurar alertas por recurso. Esses alertas permitem que você responda às condições dos recursos com um email, grave em um ponto de extremidade HTTP/HTTPS ou execute uma ação. Consulte [Monitorando o desempenho do banco de dados no Banco de Dados SQL do Azure](sql-database-single-database-monitor.md) para obter instruções detalhadas.

- **Modos de exibição**: você pode consultar o modo de exibição de gerenciamento dinâmico [sys.DM db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) para retornar o histórico de estatísticas de consumo de recursos na última hora e o modo de exibição de catálogo do sistema [sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) para retornar o histórico dos últimos 14 dias. 

- **Análise de Desempenho de Consultas**: a [Análise de Desempenho de Consultas](sql-database-query-performance.md) permite que você veja um histórico das principais consultas de consumo de recursos e de longa execução para um banco de dados específico. Este recurso requer que o [Repositório de Consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) esteja habilitado e ativo para o banco de dados.

- **Análise de SQL do Azure (versão prévia) no Log Analytics**: o [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md) permite coletar e visualizar as principais métricas de desempenho do SQL Azure, dando suporte a até 150.000 bancos de dados SQL do Azure e a 5.000 pools elásticos SQL por espaço de trabalho. Você pode usá-lo para monitorar e receber notificações. Você pode monitorar métricas do Banco de Dados SQL do Azure e de pool elástico em várias assinaturas do Azure e pools elásticos, e isso pode ser usado para identificar problemas em cada camada da pilha de um aplicativo. 

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>Como fazer para garantir que estou usando o nível de desempenho e a camada de serviço apropriados?
Monitore os modos de exibição de gerenciamento dinâmico [sys.DM db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) para compreender o consumo de CPU, memória e E/S. Você também pode usar a [Análise de Desempenho de Consultas](sql-database-query-performance.md) do Banco de Dados SQL para ver o consumo de recursos. Se você estiver em execução consistentemente com uma alta porcentagem de recursos disponíveis, considere mudar para um nível mais alto de desempenho dentro da camada de serviço existente ou para uma camada de serviço superior. Por outro lado, se você estiver consistentemente usando uma porcentagem baixa de recursos disponíveis, poderá considerar mudar para um nível de desempenho ou camada de serviço inferior.

### <a name="i-am-seeing-performance-issues-how-does-my-azure-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Estou tendo problemas de desempenho. Como a minha metodologia de solução de problemas do Banco de Dados SQL difere do SQL Server?
Muitos aspectos da sua metodologia de solução de problemas de desempenho são os mesmos no Banco de Dados SQL do Azure, mas há algumas diferenças. Por exemplo, se você notar uma degradação no desempenho geral, monitore a exibição de gerenciamento dinâmico [sys.DM db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) para compreender o consumo de CPU, memória e E/S. Talvez seja necessário alterar o nível de desempenho e/ou a camada de serviço com base nas demandas da carga de trabalho.
Para um conjunto abrangente de recomendações de ajuste dos problemas de desempenho, confira [Ajustando o desempenho do Banco de Dados SQL do Azure](sql-database-performance-guidance.md). 

### <a name="do-i-need-to-maintain-indexes-and-statistics"></a>É necessário manter índices e estatísticas?
O Banco de Dados SQL do Azure não mantém os índices e as estatísticas automaticamente como parte do serviço. Você é responsável por agendar a manutenção de índices e estatísticas. O artigo a seguir, Métodos da Automação do Azure, fornece detalhes sobre as várias opções para o agendamento de trabalhos de manutenção no Banco de Dados SQL do Azure.

## <a name="data-movement-after-migration"></a>Movimentação de dados após a migração

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-azure-sql-database"></a>Como fazer para exportar e importar dados como arquivos BACPAC do Banco de Dados SQL do Azure? 

- **Exportar**: você pode exportar seu Banco de Dados SQL do Azure como um arquivo BACPAC no portal do Azure.

  ![Exportar como um BACPAC](./media/sql-database-export/database-export.png)

- **Importar**: você pode importar como arquivo BACPAC para um banco de dados usando o portal do Azure.

  ![Importar um BACPAC](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-azure-sql-database-sql-server-2016--2012"></a>Como fazer para sincronizar dados entre o SQL Server do Banco de Dados SQL do Azure 2016 e o 2012?
O recurso [a Sincronização de Dados](sql-database-sync-data.md) ajuda a sincronizar dados bidirecionalmente entre vários bancos de dados locais do SQL Server e Banco de Dados SQL do Azure. No entanto, já que ele se baseia em gatilhos, a consistência eventual é garantida (sem perda de dados), mas não há garantia de consistência da transação. 

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o [Banco de Dados SQL do Azure](sql-database-technical-overview.md).
