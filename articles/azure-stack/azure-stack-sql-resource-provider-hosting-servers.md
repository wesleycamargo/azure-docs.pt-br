---
title: Servidores na pilha do Azure de hospedagem SQL | Microsoft Docs
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
ms.date: 06/29/2018
ms.author: jeffgilb
ms.openlocfilehash: 74d888ffe28e5428b47bfc73122518c22d0f0918
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128700"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Adicionar servidores de hospedagem para o provedor de recursos do SQL

Você pode hospedar uma instância do SQL em uma máquina virtual (VM) em [Azure pilha](azure-stack-poc.md), ou em uma máquina virtual fora de seu ambiente de pilha do Azure, desde que o provedor de recursos do SQL pode se conectar à instância.

## <a name="overview"></a>Visão geral

Antes de adicionar um servidor de hospedagem SQL, revise os seguintes requisitos obrigatórios e gerais.

**Requisitos obrigatórios**

* Habilite a autenticação do SQL na instância do SQL Server. Como o provedor de recursos do SQL VM não está associado a um domínio, ele só pode se conectar ao servidor de hospedagem usando a autenticação do SQL.
* Configure os endereços IP para as instâncias do SQL como público. O provedor de recursos e usuários, como aplicativos Web, se comunicar pela rede, o usuário para que conectividade com a instância do SQL nesta rede seja necessária.

**Requisitos gerais**

* Dedica a instância do SQL para uso pelas cargas de trabalho de usuário e provedor de recursos. Você não pode usar uma instância do SQL que está sendo usada por qualquer outro consumidor. Essa restrição também se aplica a serviços de aplicativos.
* Configure uma conta com os níveis de privilégio adequado para o provedor de recursos.
* Você está são responsáveis por gerenciar as instâncias do SQL e seus hosts.  Por exemplo, o provedor de recursos não aplicar atualizações, lidar com backups ou manipular rotação de credencial.

### <a name="sql-server-virtual-machine-images"></a>Imagens de máquina virtual do SQL Server

Imagens de máquinas virtuais IaaS do SQL estão disponíveis por meio do recurso de gerenciamento do Marketplace. Essas imagens são o mesmo que as VMs do SQL que estão disponíveis no Azure.

Certifique-se de sempre baixar a versão mais recente do **extensão SQL IaaS** antes de implantar uma VM do SQL usando um item do Marketplace. A extensão do IaaS e o portal correspondente aprimoramentos fornecem recursos adicionais, como a aplicação de patch automática e de backup. Para obter mais informações sobre essa extensão, consulte [automatizar tarefas de gerenciamento em máquinas virtuais do Azure com a extensão do SQL Server Agent](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Há outras opções para a implantação de VMs do SQL, incluindo modelos de [Galeria de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Quaisquer servidores de hospedagem instalados em uma pilha de vários nós do Azure devem ser criados de uma assinatura de usuário. Eles não não possível criar a assinatura de provedor padrão. Eles devem ser criados no portal de usuário ou uma sessão do PowerShell com um logon adequado. Todos os servidores de hospedagem são faturáveis VMs e devem ter as licenças apropriadas do SQL. O administrador do serviço _pode_ ser o proprietário da assinatura.

### <a name="required-privileges"></a>Privilégios necessários

Você pode criar um usuário administrativo com menos privilégios de sysadmin do SQL. O usuário precisa apenas de permissões para as seguintes operações:

* Banco de dados: Criar, alterar com contenção (para sempre em apenas), Drop, Backup
* O grupo de disponibilidade: Alter, Join, adicionar ou remover banco de dados
* Logon: Criar, selecionar, Alter, Drop, revogar
* Selecionar operações: \[mestre\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn), sys. availability_replicas (AlwaysOn), sys. Databases, \[mestre\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[mestre\].\[ sys\].\[ availability_groups\] (AlwaysOn), sys. master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Fornecem a capacidade de conexão com um autônomo que hospeda o SQL server

Você pode usar autônomo (não-HA) servidores SQL usando qualquer edição do SQL Server 2014 ou SQL Server 2016. Verifique se que você tem as credenciais para uma conta com privilégios de administrador do sistema.

Para adicionar um servidor de hospedagem autônomo que já está configurado, siga estas etapas:

1. Entrar no portal de operador de pilha do Azure como um administrador de serviço.

2. Selecione **procurar** &gt; **recursos ADMINISTRATIVOS** &gt; **servidores de hospedagem SQL**.

   ![Servidores de hospedagem SQL](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   Em **servidores de hospedagem SQL**, você pode conectar o provedor de recursos do SQL para instâncias do SQL Server que servem como back-end do provedor de recursos.

   ![Painel do adaptador de SQL](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Em **adicionar um servidor de hospedagem SQL**, forneça os detalhes de conexão para a instância do SQL Server.

   ![Adicionar um servidor de hospedagem SQL](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Opcionalmente, forneça um nome de instância e especifique um número de porta, se a instância não está atribuída à porta padrão 1433.

   > [!NOTE]
   > Como a instância do SQL pode ser acessada pelo usuário e administrador do Azure Resource Manager, ele pode ser colocado sob controle do provedor de recursos. A instância do SQL __deve__ ser atribuídos exclusivamente para o provedor de recursos.

4. Como adicionar servidores, você deve atribuí-los a um SKU existente ou criar um novo SKU. Em **adicionar um servidor de hospedagem**, selecione **SKUs**.

   * Para usar um SKU existente, escolha um SKU disponível e, em seguida, selecione **criar**.
   * Para criar um SKU, selecione **+ criar novo SKU**. Em **criar SKU**, insira as informações necessárias e, em seguida, selecione **Okey**.

     > [!IMPORTANT]
     > Caracteres especiais, incluindo espaços e pontos, não têm suporte no **nome** campo. Use os exemplos a captura de tela a seguir para inserir valores para o **família**, **camada**, e **edição** campos.

     ![Criar um SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

      SKUs podem demorar até uma hora para ser visível no portal. Os usuários não é possível criar um banco de dados até que o SKU totalmente seja criado.

### <a name="sku-notes"></a>Notas SKU

Você pode usar os SKUs para diferenciar as ofertas de serviço. Por exemplo, você pode ter uma instância de SQL Enterprise que tem as seguintes características:
  
* alta capacidade
* alto desempenho
* alta disponibilidade

Você pode criar uma SKU do exemplo anterior, limitando o acesso a grupos específicos que precisam de um banco de dados de alto desempenho.

>[!TIP]
>Use um nome SKU que reflete descreve os recursos dos servidores na SKU, como capacidade e desempenho. O nome serve como um auxílio para ajudar os usuários a implantar seus bancos de dados para a SKU apropriada.

Como prática recomendada, todos os servidores de hospedagem em um SKU devem ter as mesmas características de desempenho e recursos.

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Fornecer alta disponibilidade usando SQL grupos de disponibilidade AlwaysOn

Configurar instâncias do SQL Always On requer etapas adicionais e requer três VMs (ou máquinas físicas). Este artigo pressupõe que você já tem uma compreensão sólida dos grupos de disponibilidade AlwaysOn. Para obter mais informações, consulte os seguintes artigos:

* [Introdução ao SQL Server Always On grupos de disponibilidade em máquinas virtuais do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Sempre em grupos de disponibilidade (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> O provedor de recursos do adaptador SQL _somente_ oferece suporte a SQL 2016 SP1 Enterprise ou posterior instâncias para AlwaysOn. Essa configuração de adaptador requer novos recursos do SQL, como a propagação automática.

### <a name="automatic-seeding"></a>Propagação automática
Você deve habilitar [a propagação automática](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) em cada grupo de disponibilidade para cada instância do SQL Server.

Para habilitar a propagação automática em todas as instâncias, editar e, em seguida, execute o seguinte comando SQL para cada instância do:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON 'InstanceName'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Sobre as instâncias secundárias, editar e, em seguida, execute o seguinte comando SQL para cada instância do:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>Configurar a autenticação de banco de dados independente
Antes de adicionar um banco de dados independente a um grupo de disponibilidade, certifique-se de que a opção de servidor de autenticação de banco de dados independente é definida como 1 em cada instância de servidor que hospeda uma réplica de disponibilidade do grupo de disponibilidade. Para obter mais informações, consulte [opção de configuração de servidor contained database authentication](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Use esses comandos para definir a opção de servidor de autenticação de banco de dados independente para cada instância do:

  ```
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Para adicionar SQL sempre nos servidores de hospedagem

1. Entrar no portal de administração de pilha do Azure como um administrador de serviço.

2. Selecione **procurar** &gt; **recursos ADMINISTRATIVOS** &gt; **servidores de hospedagem SQL** &gt; **+ adicionar**.

   Em **servidores de hospedagem SQL**, o provedor de recursos do SQL Server pode se conectar a instâncias do SQL Server reais que servem como back-end do provedor de recursos.

3. Preencha o formulário com os detalhes de conexão para a instância do SQL Server. Certifique-se de que você use o endereço FQDN do ouvinte sempre em (e número da porta opcional.) Forneça as informações para a conta configurada com privilégios de administrador do sistema.

4. Marque a caixa de grupo de disponibilidade AlwaysOn para habilitar o suporte para instâncias de SQL sempre no grupo de disponibilidade.

   ![Habilitar AlwaysOn](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Adicione a instância do SQL Always On a uma SKU.

   > [!IMPORTANT]
   > Você não pode misturar servidores autônomos com instâncias de AlwaysOn no SKU do mesmo. Tentativa de combinar tipos depois de adicionar os primeiros resultados de servidor de hospedagem em um erro.

## <a name="make-the-sql-databases-available-to-users"></a>Disponibilizar os bancos de dados SQL para os usuários

Crie planos e ofertas para disponibilizar os bancos de dados SQL para os usuários. Adicionar o **Microsoft.SqlAdapter** para o plano de serviço e adicionar a cota padrão ou criar uma nova cota.

![Criar planos e ofertas para incluir bancos de dados](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="next-steps"></a>Próximas etapas

[Adicionar bancos de dados](azure-stack-sql-resource-provider-databases.md)
