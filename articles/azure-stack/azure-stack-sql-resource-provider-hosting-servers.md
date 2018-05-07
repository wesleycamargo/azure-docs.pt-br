---
title: Servidores na pilha do Azure de hospedagem SQL | Microsoft Docs
description: Como adicionar instâncias do SQL para o provisionamento por meio do provedor de recursos do adaptador de SQL
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.openlocfilehash: a89e5bf48c24abf72f18ee98f2dcb0eda6db35cd
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Adicionar servidores de hospedagem para o provedor de recursos do SQL
Você pode usar instâncias do SQL em máquinas virtuais dentro de seu [Azure pilha](azure-stack-poc.md), ou uma instância fora de seu ambiente de pilha do Azure, fornecido o provedor de recursos pode se conectar a ele. Os requisitos gerais são:

* A instância do SQL deve ser dedicada para uso por cargas de trabalho do usuário e RP. Você não pode usar uma instância do SQL que está sendo usada por outro consumidor, incluindo serviços de aplicativos.
* O provedor de recursos do SQL VM não está ingressado no domínio e só pode se conectar usando a autenticação do SQL.
* Você deve configurar uma conta com privilégios apropriados para uso pelo provedor de recursos.
* O provedor de recursos e os usuários, como aplicativos Web, usam a rede do usuário, para que conectividade com a instância do SQL nesta rede seja necessária. Esse requisito geralmente significa que o IP de suas instâncias do SQL deve estar em uma rede pública.
* Gerenciamento de seus hosts e as instâncias do SQL é até você. o provedor de recursos não executar a aplicação de patch, o backup, credencial rotação, etc.
* SKUs podem ser usados para criar classes diferentes de recursos do SQL, como desempenho, sempre ativa, etc.

Um número de imagens de máquinas virtuais IaaS do SQL está disponível por meio do recurso de gerenciamento do Marketplace. Certifique-se de sempre baixar a versão mais recente do **extensão SQL IaaS** antes de implantar uma VM usando um item do Marketplace. As imagens do SQL são o mesmo que as VMs do SQL que estão disponíveis no Azure. Para VMs do SQL criado dessas imagens, a extensão de IaaS e aprimoramentos do portal correspondentes fornecem recursos como a aplicação de patch automática e recursos de backup.

Há outras opções para a implantação de VMs do SQL, incluindo modelos de [Galeria de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Quaisquer servidores de hospedagem instalados em uma pilha de vários nós do Azure devem ser criados de uma assinatura de usuário. Eles não não possível criar a assinatura de provedor padrão. Eles devem ser criados no portal de usuário ou uma sessão do PowerShell com um logon adequado. Todos os servidores de hospedagem são passíveis de cobrança VMs e devem ter as licenças apropriadas do SQL. O administrador do serviço _pode_ ser o proprietário da assinatura.


### <a name="required-privileges"></a>Privilégios necessários

Você pode criar um novo usuário administrativo com privilégios de sysadmin menor completo. As operações específicas que precisam ser permitidos são:

- Banco de dados: Criar, alterar, com contenção (sempre ativada somente), Drop, Backup
- O grupo de disponibilidade: Alter, Join, adicionar ou remover banco de dados
- Logon: Criar, selecionar, Alter, Drop, revogar
- Selecionar operações: \[mestre\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn), sys. availability_replicas (AlwaysOn), sys. Databases, \[mestre\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[mestre\].\[ sys\].\[ availability_groups\] (AlwaysOn), sys. master_files



## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Fornecem a capacidade de conexão com um autônomo que hospeda o SQL server
Você pode usar autônomo (não-HA) servidores SQL usando qualquer edição do SQL Server 2014 ou SQL Server 2016. Verifique se que você tem as credenciais para uma conta com privilégios de administrador do sistema.

Para adicionar um autônomo que hospeda o servidor que já foi provisionado, siga estas etapas:

1. Entrar no portal de administração de pilha do Azure como um administrador de serviço

2. Clique em **procurar** &gt; **recursos ADMINISTRATIVOS** &gt; **servidores de hospedagem SQL**.

  ![](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

  O **servidores de hospedagem SQL** folha é onde você pode conectar o provedor de recursos do SQL Server para instâncias do SQL Server reais que servem como back-end do provedor de recursos.

  ![Servidores de hospedagem](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. Preencha o formulário com os detalhes de conexão da instância do SQL Server.

  ![Novo servidor de hospedagem](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Opcionalmente, você pode incluir um nome de instância, e um número de porta pode ser fornecido se a instância não está atribuída a porta padrão 1433.

  > [!NOTE]
  > Como a instância do SQL pode ser acessada pelo usuário e administrador do Azure Resource Manager, ele pode ser colocado sob controle do provedor de recursos. A instância do SQL __deve__ ser atribuídos exclusivamente para RP.

4. Como adicionar servidores, você deve atribuí-los a uma SKU de nova ou existente para diferenciar as ofertas de serviço. Por exemplo, você pode ter uma instância de SQL Enterprise fornecendo:
  - capacidade do banco de dados
  - backup automático
  - reserva de servidores de alto desempenho para departamentos individuais
  - E assim por diante.

  O nome da SKU deve refletir as propriedades para que os usuários podem colocar seus bancos de dados adequadamente. Todos os servidores de hospedagem em um SKU devem ter os mesmos recursos.

> [!IMPORTANT]
> Caracteres especiais, incluindo espaços e pontos, não há suporte para o **família** ou **camada** nomes quando você cria uma SKU para os provedores de recursos do SQL e MySQL.

Um exemplo:

![SKUs](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
> SKUs podem demorar até uma hora para ser visível no portal. Os usuários não é possível criar um banco de dados até que o SKU totalmente seja criado.

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>Fornecem a capacidade de usar SQL grupos de disponibilidade AlwaysOn
A configuração de instâncias do SQL Always On requer etapas adicionais e envolve a pelo menos três VMs (ou máquinas físicas).

> [!NOTE]
> O adaptador SQL RP _somente_ dá suporte a SQL 2016 SP1 Enterprise ou posteriores instâncias para o AlwaysOn, pois exige novos recursos do SQL, como a propagação automática. Além da lista anterior e comuns de requisitos:

* Você deve fornecer um servidor de arquivos, além de computadores do SQL Always On. Há um [modelo de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-ha) que você pode criar esse ambiente para você. Ela também poderá servir como um guia para criar sua própria instância.

* Você deve configurar os servidores SQL. Especificamente, você deve habilitar [a propagação automática](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) em cada grupo de disponibilidade para cada instância do SQL Server.

```
ALTER AVAILABILITY GROUP [<availability_group_name>]
    MODIFY REPLICA ON 'InstanceName'
    WITH (SEEDING_MODE = AUTOMATIC)
GO
```

Em instâncias secundárias
```
ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
GO

```



Para adicionar servidores de hospedagem SQL Always On, siga estas etapas:

1. Entrar no portal de administração de pilha do Azure como um administrador de serviço

2. Clique em **procurar** &gt; **recursos ADMINISTRATIVOS** &gt; **servidores de hospedagem SQL** &gt; **+ adicionar**.

    O **servidores de hospedagem SQL** folha é onde você pode conectar o provedor de recursos do SQL Server para instâncias do SQL Server reais que servem como back-end do provedor de recursos.


3. Preencha o formulário com os detalhes de conexão da instância do SQL Server, certificando-se de usar o endereço FQDN ou IPv4 do ouvinte sempre em (e o número de porta opcional). Forneça as informações de conta para a conta configurada com privilégios de administrador do sistema.

4. Marque esta caixa para habilitar o suporte para instâncias de SQL sempre no grupo de disponibilidade.

    ![Servidores de hospedagem](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Adicione a instância do SQL Always On a uma SKU. Você não pode misturar servidores autônomos com instâncias de AlwaysOn no SKU do mesmo. Que será determinada ao adicionar o primeiro servidor de hospedagem. Tentativa de combinar tipos posteriormente resultará em erro.


## <a name="making-sql-databases-available-to-users"></a>Disponibilizar os bancos de dados SQL para usuários

Crie planos e ofertas para disponibilizar os bancos de dados SQL para os usuários. Adicione o serviço de Microsoft.SqlAdapter para o plano e adicionar uma cota de existente ou crie um novo. Se você criar uma cota, você pode especificar a capacidade de permitir que o usuário.

![Criar planos e ofertas para incluir bancos de dados](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)


## <a name="next-steps"></a>Próximas etapas

[Adicionar bancos de dados](azure-stack-sql-resource-provider-databases.md)
