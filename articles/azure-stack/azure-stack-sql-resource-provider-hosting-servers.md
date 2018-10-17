---
title: Servidores no Azure Stack de hospedagem SQL | Microsoft Docs
description: Como adicionar instâncias do SQL para o provisionamento por meio do provedor de recursos do adaptador de SQL.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: a0a482e44617732c9dc6cd6609672e8204001dbc
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362051"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Adicionar servidores de hospedagem para o provedor de recursos do SQL

Você pode hospedar uma instância do SQL em uma máquina virtual (VM) em [do Azure Stack](azure-stack-poc.md), ou em uma VM fora de seu ambiente do Azure Stack, desde que o provedor de recursos do SQL pode se conectar à instância.

> [!NOTE]
> Bancos de dados SQL devem ser criados no servidor do provedor de recursos de SQL. O provedor de recursos do SQL deve ser criado na assinatura padrão do provedor, enquanto os servidores de hospedagem SQL devem ser criados em uma assinatura faturável, do usuário. O servidor do provedor de recursos não deve ser usado para hospedar bancos de dados do usuário.

## <a name="overview"></a>Visão geral

Antes de adicionar um servidor de hospedagem do SQL, examine os seguintes requisitos obrigatórios e gerais.

### <a name="mandatory-requirements"></a>Requisitos obrigatórios

* Habilite a autenticação do SQL na instância do SQL Server. Como o provedor de recursos do SQL VM não está ingressado no domínio, ele só pode se conectar ao servidor de hospedagem usando a autenticação do SQL.
* Configure os endereços IP para as instâncias do SQL como pública, quando instalado no Azure Stack. O provedor de recursos e usuários, como aplicativos Web, se comunicar pela rede, o usuário para que a conectividade com a instância do SQL nesta rede é necessária.

### <a name="general-requirements"></a>Requisitos gerais

* Dedica a instância do SQL para uso pelas cargas de trabalho de usuário e o provedor de recursos. Você não pode usar uma instância do SQL que está sendo usada por qualquer outro consumidor. Essa restrição também se aplica aos serviços de aplicativos.
* Configure uma conta com os níveis de privilégio adequado para o provedor de recursos (descrito abaixo).
* Você é responsável por gerenciar as instâncias do SQL e seus hosts.  Por exemplo, o provedor de recursos não aplicar as atualizações, lidar com backups ou manipular rotação de credenciais.

### <a name="sql-server-virtual-machine-images"></a>Imagens de máquina virtual do SQL Server

Imagens de máquinas virtuais de IaaS do SQL estão disponíveis por meio do recurso de gerenciamento do Marketplace. Essas imagens são o mesmo que as VMs do SQL que estão disponíveis no Azure.

Certifique-se de sempre baixar a versão mais recente do **extensão SQL IaaS** antes de implantar uma VM do SQL usando um item do Marketplace. A extensão de IaaS e o portal correspondente aprimoramentos fornecem recursos adicionais, como a aplicação de patch automática e de backup. Para obter mais informações sobre essa extensão, consulte [automatizar tarefas de gerenciamento em máquinas virtuais do Azure com a extensão do SQL Server Agent](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

> [!NOTE]
> É a extensão SQL IaaS _necessária_ para todos os SQL nas imagens do Windows no marketplace; a VM não implantar se você não baixou a extensão. Ele não é usado com imagens de máquinas virtuais do SQL com base em Linux.

Há outras opções para a implantação de VMs do SQL, incluindo modelos na [Galeria de início rápido do Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Quaisquer servidores de hospedagem instalados em uma pilha do Azure com vários nós devem ser criados de uma assinatura de usuário e não padrão assinatura do provedor. Eles devem ser criados a partir do portal do usuário ou uma sessão do PowerShell com um logon adequado. Todos os servidores de hospedagem são faturáveis VMs e devem ter as licenças apropriadas do SQL. O administrador de serviço _pode_ ser o proprietário da assinatura.

### <a name="required-privileges"></a>Privilégios necessários

Você pode criar um usuário administrativo com menos privilégios que um sysadmin SQL. O usuário precisa apenas de permissões para as seguintes operações:

* Banco de dados: Criar, alterar com contenção (para Always On apenas), soltar, de Backup
* Grupo de disponibilidade: Alter, unir, adicionar ou remover banco de dados
* Logon: Criar, selecionar, alterar, descartar, revogar
* Operações Select: \[mestre\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn), sys. availability_replicas (AlwaysOn), sys. Databases, \[mestre\].\[ sys\].\[ dm_os_sys_memory\], como SERVERPROPERTY, \[mestre\].\[ sys\].\[ availability_groups\] (AlwaysOn), sys. master_files

### <a name="additional-security-information"></a>Informações de segurança adicionais

As informações a seguir fornecem orientação de segurança adicional:

* Todo o armazenamento do Azure Stack é criptografado usando o BitLocker, portanto, qualquer instância do SQL no Azure Stack usará o armazenamento de blob criptografado.
* O provedor de recursos do SQL totalmente compatível com TLS 1.2. Certifique-se de que qualquer SQL Server que é gerenciado por meio de RP SQL está configurado para o TLS 1.2 _apenas_ padrão para que o RP. Consulte todas as versões com suporte do TLS 1.2, o suporte do SQL Server [suporte a TLS 1.2 para o Microsoft SQL Server](https://support.microsoft.com/en-us/help/3135244/tls-1-2-support-for-microsoft-sql-server).
* Use o SQL Server Configuration Manager para definir a **ForceEncryption** opção para garantir que todas as comunicações para o SQL server são sempre criptografadas. Ver [para configurar o servidor para forçar conexões criptografadas](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#ConfigureServerConnections).
* Certifique-se de que qualquer aplicativo cliente também está se comunicando através de uma conexão criptografada.
* A RP é configurada para confiar nos certificados usados pelas instâncias do SQL Server.

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Forneça capacidade conectando-se para um autônomo hospedando o SQL server

Você pode usar autônomo (não-alta disponibilidade) SQL servers usando qualquer edição do SQL Server 2014 ou SQL Server 2016. Verifique se que você tem as credenciais para uma conta com privilégios de sysadmin.

Para adicionar um servidor de hospedagem autônomo que já está configurado, siga estas etapas:

1. Entre no portal do Azure Stack de operador como um administrador de serviço.

2. Selecione **todos os serviços** &gt; **recursos ADMINISTRATIVOS** &gt; **servidores de hospedagem SQL**.

   ![Servidores de hospedagem SQL](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   Sob **servidores de hospedagem SQL**, o provedor de recursos do SQL pode se conectar a instâncias do SQL Server que servirá como back-end do provedor de recursos.

   ![Painel de adaptador de SQL](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.png)

3. Clique em **Add** e, em seguida, forneça os detalhes de conexão para sua instância do SQL Server no **adicionar um servidor de hospedagem SQL** folha.

   ![Adicionar um servidor de hospedagem SQL](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Opcionalmente, forneça um nome de instância e especifique um número de porta, se a instância não está atribuída à porta padrão 1433.

   > [!NOTE]
   > Desde que a instância do SQL pode ser acessada pelo usuário e administrador do Azure Resource Manager, ele pode ser colocado sob controle do provedor de recursos. A instância do SQL __deve__ ser alocada exclusivamente para o provedor de recursos.

4. Conforme você adiciona servidores, você deve atribuí-los a uma SKU existente ou criar uma nova SKU. Sob **adicionar um servidor de hospedagem**, selecione **SKUs**.

   * Para usar uma SKU existente, escolha um SKU disponível e, em seguida, selecione **criar**.
   * Para criar uma SKU, selecione **+ criar novo SKU**. Na **criar SKU**, insira as informações necessárias e, em seguida, selecione **Okey**.

     ![Criar uma SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Fornecer alta disponibilidade usando o SQL grupos de disponibilidade AlwaysOn

A configuração de instâncias do SQL Always On requer etapas adicionais e exige três VMs (ou máquinas físicas). Este artigo pressupõe que você já tenha uma compreensão sólida dos grupos de disponibilidade Always On. Para obter mais informações, consulte os seguintes artigos:

* [Introdução ao SQL Server Always On grupos de disponibilidade em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Sempre em grupos de disponibilidade (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> O provedor de recursos do adaptador SQL _apenas_ dá suporte ao SQL 2016 SP1 Enterprise ou instâncias mais tarde para grupos de disponibilidade AlwaysOn. Essa configuração de adaptador requer novos recursos do SQL, como a propagação automática.

### <a name="automatic-seeding"></a>Propagação automática

Você deve habilitar [a propagação automática](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) em cada grupo de disponibilidade para cada instância do SQL Server.

Para habilitar a propagação automática em todas as instâncias, editar e, em seguida, execute o seguinte comando SQL na réplica primária para cada instância secundária:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

O grupo de disponibilidade deve ser colocado entre colchetes.

Em nós secundários, execute o seguinte comando SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>Configurar a autenticação de banco de dados independente

Antes de adicionar um banco de dados independente a um grupo de disponibilidade, verifique se a opção de servidor de autenticação de banco de dados independente é definida como 1 em cada instância de servidor que hospeda uma réplica de disponibilidade para o grupo de disponibilidade. Para obter mais informações, consulte [opção de configuração de servidor contained database authentication](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Use estes comandos para definir a opção de servidor de autenticação de banco de dados independente para cada instância:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Adicionar SQL sempre nos servidores de hospedagem

1. Entrar no portal de administração do Azure Stack como um administrador de serviço.

2. Selecione **navegue** &gt; **recursos ADMINISTRATIVOS** &gt; **servidores de hospedagem SQL** &gt; **+ adicionar**.

   Sob **servidores de hospedagem SQL**, o provedor de recursos do SQL Server pode se conectar a instâncias do SQL Server reais que servem como back-end do provedor de recursos.

3. Preencha o formulário com os detalhes de conexão para sua instância do SQL Server. Certifique-se de que você use o endereço FQDN do ouvinte AlwaysOn (e o nome de instância e o número de porta opcional). Forneça as informações para a conta configurada com privilégios de sysadmin.

4. Marque a caixa de grupo de disponibilidade AlwaysOn para habilitar o suporte para instâncias do SQL sempre no grupo de disponibilidade.

   ![Habilitar Always On](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Adicione a instância do SQL Always On para uma SKU.

   > [!IMPORTANT]
   > Você não pode misturar servidores autônomos com instâncias de Always On na mesma SKU. Tentativa de combinar tipos depois de adicionar os primeiros resultados de servidor de hospedagem em um erro.

## <a name="sku-notes"></a>Notas SKU
Use um nome SKU que descreve os recursos dos servidores na SKU, como capacidade e desempenho. O nome serve como um auxílio para ajudar os usuários a implantar seus bancos de dados para a SKU adequada. Por exemplo, você pode usar nomes de SKU para diferenciar as ofertas de serviço, as seguintes características:
  
* alta capacidade
* alto desempenho
* alta disponibilidade

Como prática recomendada, todos os servidores de hospedagem em um SKU devem ter as mesmas características de desempenho e recursos.

SKUs não podem ser atribuídos a usuários ou grupos específicos.

SKUs podem demorar até uma hora para ser visível no portal. Os usuários não é possível criar um banco de dados até que a SKU foi totalmente criada.

Para editar uma SKU, vá para **todos os serviços** > **adaptador SQL** > **SKUs**. Selecione a SKU para modificar, faça as alterações necessárias e clique em **salvar** para salvar as alterações. Para excluir uma SKU que não é mais necessário, vá para **todos os serviços** > **adaptador SQL** > **SKUs**. O nome da SKU com o botão direito e selecione **excluir** para excluí-lo.

> [!TIP]
> Você pode editar ou excluir as cotas de provedor de recursos do SQL no mesmo local.

## <a name="make-sql-databases-available-to-users"></a>Disponibilizar os bancos de dados SQL para os usuários

Crie planos e ofertas para tornar os bancos de dados SQL disponíveis para os usuários. Adicione a **Microsoft.SqlAdapter** ao plano de serviço e criar uma nova cota.

## <a name="next-steps"></a>Próximas etapas

[Adicionar bancos de dados](azure-stack-sql-resource-provider-databases.md)
