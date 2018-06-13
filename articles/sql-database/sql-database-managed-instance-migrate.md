---
title: Migrar a instância do SQL Server para a Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como migrar uma instância do SQL Server para a Instância Gerenciada do Banco de Dados SQL do Azure.
keywords: migração de banco de dados, migração de banco de dados do sql server, ferramentas de migração de banco de dados, migrar banco de dados, migrar banco de dados sql
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: bonova
ms.openlocfilehash: 8f666bc352dc1706da4812590f85adc7695e2f13
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647655"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migração da instância do SQL Server para a Instância Gerenciada do Banco de Dados SQL do Azure

Neste artigo, você aprenderá sobre os métodos para migrar um SQL Server 2005 ou instância de versão posterior para a Instância Gerenciada do Banco de Dados SQL do Azure (versão prévia). 

A Instância Gerenciada do Banco de Dados SQL é uma expansão do serviço existente do Banco de Dados SQL, oferecendo uma terceira opção de implantação junto a pools elásticos e bancos de dados individuais.  Foi projetada para habilitar lift-and-shift do banco de dados para um PaaS totalmente gerenciado, sem a necessidade de projetar o aplicativo novamente. A Instância Gerenciada do Banco de Dados SQL oferece alta compatibilidade com o modelo de programação local do SQL Server e suporte integrado para a grande maioria dos recursos do SQL Server e ferramentas e serviços que o acompanham.

Em alto nível, o processo de migração do aplicativo é semelhante ao diagrama a seguir:

![processo de migração](./media/sql-database-managed-instance-migration/migration-process.png)

- [Avaliar a compatibilidade da Instância Gerenciada](sql-database-managed-instance-migrate.md#assess-managed-instance-compatibility)
- [Escolher a opção de conectividade do aplicativo](sql-database-managed-instance-migrate.md#choose-app-connectivity-option)
- [Implantar em uma Instância Gerenciada de tamanho ideal](sql-database-managed-instance-migrate.md#deploy-to-an-optimally-sized-managed-instance)
- [Selecionar o método de migração e migrar](sql-database-managed-instance-migrate.md#select-migration-method-and-migrate)
- [Monitorar aplicativos](sql-database-managed-instance-migrate.md#monitor-applications)

> [!NOTE]
> Para migrar um único banco de dados para um banco de dados individual ou pool elástico, consulte [Migrar um banco de dados do Microsoft SQL Server para o Banco de Dados SQL do Azure](sql-database-cloud-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Avaliar a compatibilidade da Instância Gerenciada

Primeiro, determine se a Instância Gerenciada é compatível com os requisitos do banco de dados do aplicativo. A Instância Gerenciada é projetada para fornecer migração lift-and-shift fácil para a maioria dos aplicativos existentes que utilizam o SQL Server local ou em máquinas virtuais. No entanto, às vezes é necessário recursos ou capacidades que ainda não têm suporte e o custo de implementar uma solução alternativa é muito alto. 

Utilize o [DMA (Assistente de Migração de Dados)](https://docs.microsoft.com/sql/dma/dma-overview) para detectar possíveis problemas de compatibilidade que afetam a funcionalidade do banco de dados no Banco de Dados SQL do Azure. O DMA ainda não fornece suporte à Instância Gerenciada como destino de migração, mas é recomendável executar a avaliação em relação ao Banco de Dados SQL do Azure e revisar cuidadosamente a lista de problemas de compatibilidade e paridade de recursos relatados com a documentação do produto. A maioria dos problemas de bloqueio que impedem a migração para o Banco de Dados SQL do Azure foi removida com a Instância Gerenciada. Por exemplo, recursos como consultas entre banco de dados, transações entre banco de dados dentro da mesma instância, servidor vinculado para outras fontes do SQL, CLR, tabelas temporárias globais, exibições de nível de instância, Service Broker e similares estão disponíveis em Instâncias Gerenciadas. 

No entanto, há alguns casos em que você precisa considerar uma opção alternativa, como [SQL Server em Máquinas Virtuais no Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Estes são alguns exemplos:

- Se você precisar de acesso direto ao sistema operacional ou ao sistema de arquivos, por exemplo, para instalar agentes personalizados ou de terceiros na mesma máquina virtual com o SQL Server.
- Se você tiver uma dependência estrita de recursos que ainda não têm suporte, como transações entre instâncias, PolyBase e FileStream/FileTable.
- Se for absolutamente necessário permanecer em uma versão específica do SQL Server (2012, por instância).
- Se os requisitos de computação forem muito menores, a Instância Gerenciada oferece na visualização pública (um vCore, por instância) e a consolidação de banco de dados não é uma opção aceitável.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Implantar para uma Instância Gerenciada de tamanho ideal

A Instância Gerenciada é adaptada para cargas de trabalho locais planejadas a moverem-se para a nuvem. Ela introduz um novo modelo de compra que oferece maior flexibilidade na seleção do nível certo de recursos para as cargas de trabalho. No ambiente local, você provavelmente está acostumado a dimensionar essas cargas de trabalho usando núcleos físicos. O novo modelo de compra para Instância Gerenciada é baseado em núcleos virtuais, ou "vCores", com armazenamento adicional e E/S disponíveis separadamente. O modelo vCore é uma maneira mais simples de compreender os requisitos de computação na nuvem em relação ao que você utiliza no local atualmente. Esse novo modelo permite que você dimensione adequadamente o ambiente de destino na nuvem.

É possível selecionar recursos de armazenamento e computação em tempo de implantação e, depois, alterá-lo posteriormente sem introduzir o tempo de inatividade para o aplicativo.

![dimensionamento da instância gerenciada](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Para aprender como criar a infraestrutura de VNet e uma Instância Gerenciada, consulte [Criar uma Instância Gerenciada](sql-database-managed-instance-create-tutorial-portal.md).

> [!IMPORTANT]
> É importante manter a sub-rede e VNet de destino sempre de acordo com os [requisitos de VNET da Instância Gerenciada](sql-database-managed-instance-vnet-configuration.md#requirements). Qualquer incompatibilidade pode impedi-lo de criar novas instâncias ou utilizar aquelas que você já criou.

## <a name="select-migration-method-and-migrate"></a>Selecionar o método de migração e migrar

A Instância Gerenciada direciona cenários de usuários que requerem migração de banco de dados em massa de implementações de bancos de dados IaaS ou locais. Essa é a escolha ideal quando for necessário fazer lift-and-shift do back-end dos aplicativos que utilizam regularmente as funcionalidades entre banco de dados e/ou nível de instância. Se esse for seu cenário, será possível mover uma instância inteira para um ambiente correspondente no Azure sem a necessidade reprojetar a arquitetura dos aplicativos. 

Para mover instâncias do SQL, é necessário planejar atentamente:

-   A migração de todos os bancos de dados que precisam ser colocados (os que estão executando na mesma instância)
-   A migração de objetos de nível de instância que o aplicativo depende, incluindo logons, credenciais, Operadores e Trabalhos SQL Agent e gatilhos de nível de servidor. 

A Instância Gerenciada é um serviço totalmente gerenciado que permite delegar algumas das atividades DBA regulares na plataforma, na medida em que são incorporadas. Portanto, alguns dados de nível de instância não precisam ser migrados, como trabalhos de manutenção para backups regulares ou configuração Always On, pois a [alta disponibilidade](sql-database-high-availability.md) é interna.

A Instância Gerenciada oferece suporte às opções de migração de banco de dados a seguir (atualmente, esse são os únicos métodos de migração com suporte):

- Serviço de Migração de Banco de Dados do Azure - migração com tempo de inatividade próximo de zero
- RESTORE nativo de URL - usa backups nativos do SQL Server e requer algum tempo de inatividade
- Migrar usando o arquivo BACPAC - usa o arquivo BACPAC do SQL Server ou do Banco de Dados SQL e requer algum tempo de inatividade

### <a name="azure-database-migration-service"></a>Serviço de Migração de Banco de Dados do Azure

O [DMS (Serviço de Migração de Banco de Dados do Azure)](../dms/dms-overview.md) é um serviço totalmente gerenciado projetado para permitir migrações contínuas de várias fontes de banco de dados para plataformas de dados do Azure com um tempo de inatividade mínimo. Esse serviço simplifica as tarefas necessárias para mover bancos de dados de terceiros e SQL Server existentes para o Azure. As opções de implantação na Visualização Pública incluem Banco de Dados SQL do Azure, Instância Gerenciada e SQL Server em uma Máquina Virtual do Azure. O DMS é o método recomendado de migração para as cargas de trabalho empresariais. 

Para saber mais sobre esse cenário e as etapas de configuração do DMS, consulte [Migrar o banco de dados local para a Instância Gerenciada utilizando DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>RESTAURAÇÃO nativa da URL

A RESTAURAÇÃO de backups nativos (arquivos .bak) obtidos do SQL Server local ou [SQL Server em Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/sql-server/), disponível no [Armazenamento do Microsoft Azure](https://azure.microsoft.com/services/storage/), é uma das principais capacidades na Instância Gerenciada do BD SQL que permite migração de banco de dados offline rápida e fácil. 

O diagrama a seguir explica o processo no alto nível:

![fluxo de migração](./media/sql-database-managed-instance-migration/migration-flow.png)

A tabela a seguir fornece mais informações sobre o método que você pode utilizar, dependendo da versão do SQL Server de origem em execução:

|Etapa|Mecanismo SQL e versão|Método de backup/restauração|
|---|---|---|
|Coloque o backup no Armazenamento do Microsoft Azure|SQL 2012 SP1 CU2 anterior|Upload do arquivo .bak diretamente para Armazenamento do Microsoft Azure|
||2012 SP1 CU2 - 2016|Backup direto utilizando a sintaxe [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)|
||2016 e posterior|Backup direto utilizando [WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Restaurar do Armazenamento do Microsoft Azure para a Instância Gerenciada|[RESTORE FROM URL com SAS CREDENTIAL](sql-database-managed-instance-restore-from-backup-tutorial.md)|

> [!IMPORTANT]
> Não há suporte para restauração de bancos de dados do sistema. Para migrar objetos de nível de instância (armazenados em bancos de dados mestres ou msdb), é recomendável script e executar scripts T-SQL na instância de destino.

Para um tutorial completo que inclui a restauração de um backup de banco de dados para uma Instância Gerenciada usando uma credencial de SAS, consulte [Restaurar do backup para uma Instância Gerenciada](sql-database-managed-instance-restore-from-backup-tutorial.md).

### <a name="migrate-using-bacpac-file"></a>Migrar utilizando arquivo BACPAC

É possível importar para Banco de Dados SQL do Azure e Instância Gerenciada a partir de uma criação de uma cópia do banco de dados original, com os dados, em um arquivo BACPAC. Consulte [Importar um arquivos BACPAC para um novo Banco de Dados SQL do Azure](sql-database-import.md).

## <a name="monitor-applications"></a>Monitorar aplicativos

Acompanhe o comportamento e desempenho do aplicativo após a migração. Na Instância Gerenciada, algumas alterações são habilitadas somente quando o [nível de compatibilidade do banco de dados for alterado](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). A migração de banco de dados para o Banco de Dados SQL do Azure mantém o nível de compatibilidade original na maioria dos casos. Se o nível de compatibilidade de um banco de dados de usuário fosse 100 ou superior antes da migração, ele permaneceria o mesmo após a migração. Se o nível de compatibilidade de um banco de dados de usuário fosse 90 antes da migração, no banco de dados atualizado, o nível de compatibilidade seria definido como 100, que é o nível de compatibilidade com suporte mais baixo na Instância Gerenciada. O nível de compatibilidade dos bancos de dados do sistema é 140.

Para reduzir os riscos de migração, altere o nível de compatibilidade do banco de dados somente após o monitoramento do desempenho. Utilize o Repositório de Dados de Consultas como ferramenta ideal para obter informações sobre o desempenho da carga de trabalho, antes e após alterar o nível de compatibilidade do banco de dados, conforme explicado em [Manter a estabilidade do desempenho durante a atualização para a versão mais recente do SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

Quando estiver em uma plataforma totalmente gerenciada, aproveite as vantagens que são fornecidas automaticamente como parte do serviço de Banco de Dados SQL. Por exemplo, não é necessário criar backups em Instância Gerenciada - o serviço executa os backups para você automaticamente. Não é mais necessário preocupar-se com agendamento, execução e gerenciamento de backups. A Instância Gerenciada fornece a capacidade de restaurar a qualquer momento no período de retenção, utilizando [PITR (Recuperação Pontual)](sql-database-recovery-using-backups.md#point-in-time-restore). Durante a visualização pública, o período de retenção é fixado em sete dias.
Além disso, não é necessário preocupar-se com a configuração de alta disponibilidade, pois a [alta disponibilidade](sql-database-high-availability.md) é interna.

Para fortalecer a segurança, considere utilizar alguns dos recursos disponíveis:
- Autenticação do Azure Active Directory no nível do banco de dados
- Detecção de ameaças e auditoria para monitorar atividades
- Controlando o acesso a dados confidenciais e privilegiados ([Segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) e [Máscara de Dados Dinâmicos do Banco de Dados SQL](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)).

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre Instâncias Gerenciadas, consulte [O que é uma Instância Gerenciada?](sql-database-managed-instance.md).
- Para um tutorial que inclua uma restauração do backup, consulte [Criar uma Instância Gerenciada](sql-database-managed-instance-create-tutorial-portal.md).
- Para um tutorial mostrando a migração utilizando DMS, consulte [Migrar o banco de dados local para a Instância Gerenciada utilizando DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
