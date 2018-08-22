---
title: TDE - BYOK (Bring Your Own Key) - Banco de Dados SQL do Microsoft Azure| Microsoft Docs
description: Suporte BYOK (Bring Your Own Key) para TDE (Transparent Data Encryption) com Azure Key Vault para Banco de Dados SQL e Data Warehouse. Visão geral de TDE com BYOK, benefícios, como funciona, considerações e recomendações.
keywords: ''
services: sql-database
documentationcenter: ''
author: aliceku
manager: craigg
ms.prod: ''
ms.reviewer: carlrab
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.custom: ''
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: aliceku
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: 4a2f751c55a3bd386c6a984a3b5a16a24166f90c
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043232"
---
# <a name="transparent-data-encryption-with-bring-your-own-key-support-for-azure-sql-database-and-data-warehouse"></a>Transparent Data Encryption com suporte Bring Your Own Key para Data Warehouse e Banco de Dados SQL do Azure

O suporte BYOK (Bring Your Own Key) para [TDE (Transparent Data Encryption)]((https://docs.microsoft.com/sql/relational-databases/security/transparent-data-encryption) permite criptografar a DEK (Chave de Criptografia) do Banco de Dados com uma chave assimétrica chamada Protetor de TDE.  O Protetor de TDE é armazenado sob seu controle no [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), o sistema de gerenciamento de chave externa baseado em nuvem do Azure. O Azure Key Vault é o primeiro serviço de gerenciamento de chaves com o qual a TDE integrou suporte para BYOK. A DEK de TDE, que é armazenada na página de inicialização de um banco de dados, é criptografada e descriptografada pelo protetor de TDE. O Protetor de TDE é armazenado no Azure Key Vault e nunca sai do cofre de chaves. Se o acesso do servidor ao cofre da chaves for revogado, um banco de dados não poderá ser descriptografado e lido na memória.  O protetor de TDE é definido no nível do servidor lógico e é herdado por todos os bancos de dados associados a esse servidor. 

Com o suporte BYOK, os usuários agora podem controlar as principais tarefas de gerenciamento, incluindo rotações de chave, permissões de cofre de chaves, excluir chaves e habilitar auditoria/relatório em todos os protetores de TDE usando a funcionalidade do Azure Key Vault. O Key Vault fornece gerenciamento central de chaves, utiliza HSMs (Módulos de Segurança de Hardware) rigidamente monitorados e permite a separação de funções entre o gerenciamento de chaves e dados para ajudar no cumprimento da conformidade regulatória.  


A TDE com BYOK fornece os seguintes benefícios:
- Maior transparência e controle granular com a capacidade de autogerenciar o protetor de TDE   
- Gerenciamento central de protetores de TDE (em conjunto com outras chaves e segredos usados em outros serviços do Azure) hospedando-os no Key Vault
- Separação das principais responsabilidades de gerenciamento de dados dentro da organização para dar suporte à separação de funções
- Maior confiança dos próprios clientes, pois o Key Vault é projetado para que a Microsoft não veja nem extraia nenhuma chave de criptografia. 
- Suporte para rotação de chave

> [!IMPORTANT]
> Para aqueles que usam TDE de serviço gerenciado e que gostariam de começar a usar o Key Vault, a TDE permanecerá habilitada durante o processo de mudança para um protetor de TDE no Key Vault. Não há tempo de inatividade nem nova criptografia dos arquivos do banco de dados. A mudança de uma chave de serviço gerenciado para uma chave do Key Vault requer apenas a nova criptografia da DEK (Chave de Criptografia) do banco de dados, que é uma operação rápida e online. 
>

## <a name="how-does-tde-with-byok-support-work"></a>Como funciona TDE com suporte BYOK?
 
![Autenticação do Servidor para o Key Vault](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Quando o TDE é configurado pela primeira vez para usar um protetor de TDE do Key Vault, o servidor envia a DEK de cada banco de dados habilitado por TDE para Key Vault para uma solicitação de chave de encapsulamento. O Key Vault retorna a chave de criptografia do banco de dados criptografada, que é armazenada no banco de dados do usuário.  

>[!IMPORTANT]
>É importante observar que **depois que um Protetor de TDE é armazenado no Azure Key Vault, o protetor nunca sai do Azure Key Vault**. O servidor lógico somente pode enviar solicitações de operação de chave para o material da chave de proteção de TDE dentro do Key Vault e **nunca acessa ou armazena em cache o protetor de TDE** . O administrador do Key Vault tem o direito de revogar as permissões do Key Vault do servidor a qualquer momento e, nesse caso, todas as conexões com o servidor serão cortadas. 
>


## <a name="guidelines-for-configuring-tde-with-byok"></a>Diretrizes para configurar a TDE com BYOK

### <a name="general-guidelines"></a>Diretrizes gerais
- Certifique-se de que o Azure Key Vault e o Banco de Dados SQL do Azure estejam no mesmo locatário.  Interações do servidor e do cofre de chaves entre locatários **não têm suporte**.
- Decida quais assinaturas serão usadas para os recursos necessários - mover o servidor pelas assinaturas posteriormente requer uma nova configuração de TDE com BYOKs. Saiba mais sobre a [movimentação de recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-move-resources)
- Ao configurar TDE com BYOK, é importante considerar a carga colocada no cofre de chaves por meio de repetidas operações de encapsulamento/desencapsulamento. Por exemplo, como todos os bancos de dados associados a um servidor lógico usam o mesmo protetor de TDE, um failover desse servidor disparará o máximo de operações de chave no cofre, pois há bancos de dados no servidor. Com base em nossa experiência e nos [limites de serviço do cofre de chaves](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-service-limits) documentados, é recomendável associar no máximo 200 bancos de dados Comercialmente Crítico/Premium ou 500 Standard/Uso Geral com um Azure Key Vault em uma única assinatura para garantir alta disponibilidade consistente ao acessar o protetor de TDE no cofre. 
- Recomendável: mantenha uma cópia do protetor de TDE nas instalações.  Isso requer um dispositivo de HSM para criar um Protetor de TDE localmente e um sistema de caução de chave para armazenar uma cópia local do Protetor de TDE.  Saiba [como transferir uma chave de um HSM local para o Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-hsm-protected-keys).


### <a name="guidelines-for-configuring-azure-key-vault"></a>Diretrizes para configurar o Azure Key Vault

- Crie um cofre de chaves com [exclusão reversível](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) habilitada para proteger contra perda de dados em caso de exclusão acidental de chave ou cofre de chaves.  É necessário usar o [PowerShell para habilitar a propriedade "exclusão reversível"](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-soft-delete-powershell) no cofre de chaves (essa opção ainda não está disponível no Portal do AKV - mas exigida pelo SQL):  
  - Os recursos excluídos por software são retidos por um período de tempo definido, 90 dias, a menos que sejam recuperados ou limpos.
  - As ações de **recuperação** e **limpeza** têm suas próprias permissões associadas em uma política de acesso de cofre de chaves. 
- Defina um bloqueio de recurso no cofre de chaves para controlar quem pode excluir esse recurso crítico e ajudar a evitar a exclusão acidental ou não autorizada.  [Saiba mais sobre bloqueios de recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-lock-resources)

- Conceda ao servidor lógico acesso ao cofre de chaves usando a identidade do Azure AD (Azure Active Directory).  Ao usar a interface do usuário do portal, a identidade do Azure AD é criada automaticamente e as permissões de acesso ao cofre de chaves são concedidas ao servidor.  Usando o PowerShell para configurar TDE com BYOK, a identidade do Azure AD deve ser criada e a conclusão verificada. Consulte [Configurar TDE com BYOK](transparent-data-encryption-byok-azure-sql-configure.md) para obter as instruções detalhadas passo a passo ao usar o PowerShell.

  >[!NOTE]
  >Se a identidade do Azure AD **for excluída acidentalmente ou as permissões do servidor forem revogadas** usando a política de acesso do cofre de chaves, o servidor perderá o acesso ao cofre de chaves e os bancos de dados criptografados por TDE serão removidos dentro de 24 horas.
  >

- Configure o Azure Key Vault sem uma VNET ou firewall.  Se o SQL perder o acesso ao cofre de chaves, os bancos de dados criptografados por TDE serão removidos dentro de 24 horas.
- Habilite auditoria e relatórios em todas as chaves de criptografia: o Key Vault fornece logs que são fáceis de serem injetados em outras ferramentas de SIEM (gerenciamento de eventos e informações de segurança). O OMS (Operations Management Suite) [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) é um exemplo de um serviço que já está integrado.
- Para garantir alta disponibilidade dos bancos de dados criptografados, configure cada servidor lógico com dois Azure Key Vaults que residem em regiões diferentes.


### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key-stored-in-azure-key-vault"></a>Diretrizes para configurar o Protetor de TDE (chave assimétrica) armazenado no Azure Key Vault

- Crie a chave de criptografia localmente em um dispositivo de HSM local. Certifique-se de que esta seja uma chave RSA 2048 assimétrica para que possa ser armazenada no Azure Key Vault.
- Garanta a chave em um sistema de caução de chave.  
- Importe o arquivo de chave de criptografia (.pfx, .byok ou .backup) para o Azure Key Vault. 
    

>[!NOTE] 
    >Para fins de teste, é possível criar uma chave com o Azure Key Vault, no entanto, essa chave não pode ser garantida porque a chave privada nunca pode sair do cofre de chaves.  Sempre faça backup e armazene as chaves usadas para criptografar os dados de produção, pois a perda da chave (exclusão acidental no cofre de chaves, expiração, etc.) resulta em perda permanente de dados.
    >
    
- Use uma chave sem uma data de validade – e nunca defina uma data de validade em uma chave já em uso: **quando a chave expira, os bancos de dados criptografados perdem o acesso ao Protetor de TDE e são removidos dentro de 24 horas**.
- Certifique-se de que a chave esteja habilitada e tenha permissões para executar as operações *get*, *codificar chave* e *decodificar chave*.
- Crie um backup de chave do Azure Key Vault antes de usar a chave no Azure Key Vault pela primeira vez. Saiba mais sobre o comando [Backup-AzureKeyVaultKey](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1).
- Crie um novo backup sempre que forem feitas alterações na chave (por exemplo, adicionar ACLs, adicionar marcas, adicionar atributos de chave).
- **Mantenha as versões anteriores** da chave no cofre de chaves ao fazer rotação de chaves para que os backups mais antigos do banco de dados possam ser restaurados. Quando o Protetor de TDE é alterado para um banco de dados, os backups antigos do banco de dados **não são atualizados** para usar o Protetor de TDE mais recente.  Cada backup precisa do Protetor de TDE em que foi criado na hora da restauração. As rotações de chave podem ser executadas seguindo as instruções em [Girar o Protetor de Transparent Data Encryption usando PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Mantenha todas as chaves usadas anteriormente no Azure Key Vault depois de voltar às chaves de serviço gerenciado.  Isso garante que os backups do banco de dados possam ser restaurados com os protetores de TDE armazenados no Azure Key Vault.  Os protetores de TDE criados com o Azure Key Vault devem ser mantidos até que todos os backups armazenados sejam criados com chaves de serviço gerenciado.  
- Faça cópias de backup recuperáveis dessas chaves usando [Backup-AzureKeyVaultKey](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1).
- Para remover uma chave potencialmente comprometida durante um incidente de segurança sem o risco de perda de dados, siga as etapas em [Remover uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).


## <a name="high-availability-geo-replication-and-backup--restore"></a>Alta disponibilidade, replicação geográfica e backup/restauração

### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de desastres

Como configurar alta disponibilidade com Azure Key Vault depende da configuração do banco de dados e do servidor lógico, e aqui estão as configurações recomendadas para dois casos distintos.  O primeiro caso é um banco de dados independente ou servidor lógico sem redundância geográfica configurada.  O segundo caso é um banco de dados ou servidor lógico configurado com grupos de failover ou redundância geográfica, onde deve ser assegurado que cada cópia com redundância geográfica tenha um Azure Key Vault local dentro do grupo de failover para garantir que os failovers geográficos funcionem. No primeiro caso, se você precisar de alta disponibilidade de um banco de dados e servidor lógico sem nenhuma redundância geográfica configurada, é altamente recomendável configurar o servidor para usar dois cofres de chaves diferentes em duas regiões diferentes com o mesmo material da chave.  Isso pode ser feito, criando um protetor de TDE usando o Key Vault primário colocalizado na mesma região do servidor lógico e clonando a chave em um cofre de chaves em uma região do Azure diferente, de modo que o servidor tenha acesso a um segundo cofre de chaves, caso o Key Vault primário sofra uma interrupção enquanto o banco de dados estiver em execução. Use o cmdlet Backup-AzureKeyVaultKey para recuperar a chave no formato criptografado do Key Vault primário e, em seguida, use o cmdlet Restore-AzureKeyVaultKey e especifique um cofre de chaves na segunda região.


![HA de servidor único e sem Geo-DR](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Como configurar Geo-DR com Azure Key Vault

Para manter a alta disponibilidade de Protetores de TDE para bancos de dados criptografados, é necessário configurar Azure Key Vaults redundantes com base nos grupos de failover existentes ou desejados do Banco de Dados SQL ou em instâncias de replicação geográfica ativas.  Cada servidor com replicação geográfica requer um cofre de chaves separado, que deve estar colocalizado com o servidor na mesma região do Azure. Se um banco de dados primário ficar inacessível devido a uma indisponibilidade em uma região e um failover for disparado, o banco de dados secundário poderá assumir o controle usando o cofre de chaves secundário. 
 
Para bancos de dados SQL com Replicação Geográfica, a seguinte configuração do Azure Key Vault é necessária:
- Um banco de dados primário com um cofre de chaves na região e um banco de dados secundário com um cofre de chaves na região. 
- Pelo menos um secundário é necessário e há suporte para até quatro secundários. 
- Não há suporte para secundários de secundários (encadeamento).

A seção a seguir descreverá as etapas de configuração e configuração em mais detalhes. 

### <a name="azure-key-vault-configuration-steps"></a>Etapas de configuração do Azure Key Vault

- Instalar o [PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.6.0) 
- Crie dois Azure Key Vaults em duas regiões diferentes usando o [PowerShell para habilitar a propriedade "exclusão reversível"](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-soft-delete-powershell) nos cofres de chaves (essa opção ainda não está disponível no Portal do AKV, mas é exigida pelo SQL).
- Ambos os Azure Key Vaults devem estar localizados nas duas regiões disponíveis na mesma área geográfica do Azure para que o backup e a restauração das chaves funcionem.  Se você precisar que os dois cofre de chaves estejam localizados em diferentes áreas geográficas para atender aos requisitos de Geo-DR de SQL, siga o [Processo de BYOK](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-hsm-protected-keys) que permite que as chaves sejam importadas de um HSM local.
- Crie uma nova chave no primeiro cofre de chaves:  
  - Chave RSA/RSA-HSA 2048 
  - Sem datas de validade 
  - A chave está habilitada e tem permissões para executar operações get, codificar chave e decodificar chave 
- Faça backup da chave primária e restaure a chave para o segundo cofre de chaves.  Consulte [BackupAzureKeyVaultKey](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1) e [Restore-AzureKeyVaultKey](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-5.5.0). 

### <a name="azure-sql-database-configuration-steps"></a>Etapas de configuração do Banco de Dados SQL do Azure

As etapas de configuração a seguir diferem se iniciar com uma nova implantação de SQL ou se estiver trabalhando com uma implantação de Geo-DR de SQL já existente.  Primeiro, serão descritas as etapas de configuração de uma nova implantação e, em seguida, explicado como atribuir Protetores de TDE armazenados no Azure Key Vault a uma implantação existente que já tenha um vínculo de Geo-DR estabelecido. 

Etapas para uma nova implantação:
- Crie os dois servidores SQL lógicos nas mesmas duas regiões que os cofres de chaves criados anteriormente. 
- Selecione o painel de TDE do servidor lógico e, para cada servidor SQL lógico:  
   - Selecione o AKV na mesma região 
   - Selecione a chave para usar como Protetor de TDE – cada servidor usará a cópia local do Protetor de TDE. 
   - Fazer isso no Portal criará uma [AppID](https://docs.microsoft.com/en-us/azure/active-directory/managed-service-identity/overview) para o servidor SQL lógico, que é usada para atribuir as permissões lógicas do SQL Server para acessar o cofre de chaves – não exclua essa identidade. O acesso pode ser revogado, removendo as permissões no Azure Key Vault para o servidor SQL lógico, que é usado para atribuir as permissões lógicas do SQL Server para acessar o cofre de chaves.
- Crie o banco de dados primário. 
- Siga as [diretrizes de replicação geográfica ativa](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview) para concluir o cenário, e essa etapa criará o banco de dados secundário.

![Grupos de failover e Geo-DR](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

>[!NOTE]
>É importante garantir que os mesmos Protetores de TDE estejam presentes em ambos os cofres de chaves, antes de proceder ao estabelecimento do vínculo geográfico entre os bancos de dados.
>

Etapas para um banco de dados SQL existente com a implantação de Geo-DR:

Como os servidores SQL lógicos já existem e os bancos de dados primário e secundário já estão atribuídos, as etapas para configurar o Azure Key Vault devem ser executadas na seguinte ordem: 
- Inicie com o SQL Server lógico que hospeda o banco de dados secundário: 
   - Atribuir o cofre de chaves localizado na mesma região 
   - Atribuir o Protetor de TDE 
- Agora, vá para o SQL Server lógico que hospeda o banco de dados primário: 
   - Selecione o mesmo Protetor de TDE usado para o banco de dados secundário
   
![Grupos de failover e Geo-DR](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

>[!NOTE]
>Ao atribuir o cofre de chaves ao servidor, é importante iniciar com o servidor secundário.  Na segunda etapa, atribua o cofre de chaves ao servidor primário e atualize o Protetor de TDE, o vínculo de Geo-DR continuará funcionando porque nesse momento o Protetor de TDE usado pelo banco de dados replicado está disponível para ambos os servidores.
>

Antes de habilitar a TDE com chaves gerenciadas pelo cliente no Azure Key Vault para um cenário de Geo-DR do Banco de Dados SQL, é importante criar e manter dois Azure Key Vaults com conteúdo idêntico nas mesmas regiões que serão usadas para a replicação geográfica do Banco de Dados SQL.  "Conteúdos idênticos" significa, especificamente, que ambos os cofre de chaves devem conter cópias do(s) mesmo(s) Protetor(es) de TDE para que ambos os servidores tenham acesso aos Protetores de TDE usados por todos os bancos de dados.  Subsequentemente, é necessário manter os dois cofre de chaves em sincronização, significando que devem conter as mesmas cópias dos Protetores de TDE após a rotação de chaves, manter versões antigas de chaves usadas para arquivos de log ou backups, os Protetores de TDE devem manter as mesmas propriedades de chave e os cofres de chaves devem manter as mesmas permissões de acesso para SQL.  
 
Siga as etapas em [Visão geral da replicação geográfica ativa](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) para testar e disparar um failover, que deve ser feito regularmente para confirmar que as permissões de acesso para SQL para ambos os cofres de chaves foram mantidas. 


### <a name="backup-and-restore"></a>Backup e restauração

Depois que um banco de dados for criptografado com TDE usando uma chave do Key Vault, todos os backups gerados também serão criptografados com o mesmo Protetor de TDE.

Para restaurar um backup criptografado com um Protetor de TDE do Key Vault, verifique se o material da chave ainda está no cofre original sob o nome da chave original. Quando o Protetor de TDE é alterado para um banco de dados, os backups antigos do banco de dados **não são** atualizados para usar o Protetor de TDE mais recente. Portanto, é recomendável manter todas as versões antigas do Protetor de TDE no Key Vault para que os backups do banco de dados possam ser restaurados. 

Se uma chave necessária para restaurar um backup não estiver mais no cofre de chaves original, a seguinte mensagem de erro será retornada: "O servidor de destino <Servername> não tem acesso a todos os URIs do AKV criados entre <carimbo de data/hora Nº 1> e <carimbo de data/hora Nº 2>. Tente realizar a operação novamente depois de restaurar todos os URIs do AKV. "

Para atenuar isso, execute o cmdlet [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) para retornar a lista de chaves do Key Vault que foram adicionadas ao servidor (a menos que tenham sido excluídas por um usuário). Para garantir que todos os backups possam ser restaurados, verifique se o servidor de destino para o backup tem acesso a todas essas chaves.

   ```powershell
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
Para saber mais sobre a recuperação de backup do Banco de Dados SQL, consulte [Recuperar um banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups). Para saber mais sobre a recuperação de backup do SQL Data Warehouse, consulte [Recuperar um Data Warehouse SQL do Azure](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-overview).


Consideração adicional para arquivos de log de backup: os arquivos de log de backup permanecem criptografados com o Criptografador de TDE original, mesmo que o Protetor de TDE tenha sido girado e o banco de dados agora esteja usando um novo Protetor de TDE.  No momento da restauração, ambas as chaves serão necessárias para restaurar o banco de dados.  Se o arquivo de log estiver usando um Protetor de TDE armazenado no Azure Key Vault, essa chave será necessária na hora da restauração, mesmo que o banco de dados tenha sido alterado para usar TDE de serviço gerenciado.


