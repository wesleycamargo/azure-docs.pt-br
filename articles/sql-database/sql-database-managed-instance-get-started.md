---
title: 'Portal do Azure: Criar uma Instância Gerenciada do SQL | Microsoft Docs'
description: Crie uma Instância Gerenciada do SQL, o ambiente de rede e a VM do cliente para acesso.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: Carlrab
manager: craigg
ms.date: 09/23/2018
ms.openlocfilehash: ebdb78a762aa212566395734804b9c021e84e6cd
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140867"
---
# <a name="create-an-azure-sql-database-managed-instance"></a>Criar uma Instância Gerenciada do Banco de Dados SQL do Azure

Este Início Rápido orienta sobre como criar uma [Instância Gerenciada](sql-database-managed-instance.md) do Banco de Dados SQL do Azure no portal do Azure. 

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Criar uma Instância Gerenciada

As etapas a seguir mostram como criar uma Instância Gerenciada.

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Localize **Instância Gerenciada** e selecione **Instância Gerenciada do Banco de Dados SQL do Azure**.
3. Clique em **Criar**.

   ![Criar Instância Gerenciada](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. Preencha o formulário de Instância Gerenciada com as informações solicitadas, usando as informações na tabela abaixo:

   | Configuração| Valor sugerido | DESCRIÇÃO |
   | ------ | --------------- | ----------- |
   | **Assinatura** | Sua assinatura | Uma assinatura na qual você tem permissão para criar novos recursos |
   |**Nome da Instância Gerenciada**|Qualquer nome válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Logon de administrador da Instância Gerenciada**|Um nome de usuário válido|Para ver os nomes válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Não use “serveradmin”, que é uma função de nível de servidor reservada.| 
   |**Senha**|Qualquer senha válida|A senha deve ter no mínimo 16 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Grupo de recursos**|Um grupo de recursos novo ou existente|Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Localidade**|O local no qual você deseja criar a Instância Gerenciada|Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).|
   |**Rede virtual**|Selecione **Criar nova rede virtual** ou uma rede virtual que você criou previamente no grupo de recursos indicado anteriormente neste formulário| Para configurar uma rede virtual para uma Instância Gerenciada com configurações personalizadas, confira [Configurar o modelo de ambiente de rede virtual de Instância Gerenciada do SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) no Github. Para obter informações sobre os requisitos para configurar o ambiente de rede para uma Instância Gerenciada, confira [Configurar uma VNet para a Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-vnet-configuration.md) |

   ![formulário de instância gerenciada](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Clique em **Tipo de preço** para dimensionar os recursos de computação e armazenamento e revisar as opções de tipo de preço. O tipo de preço de Uso Geral com 32 GB de memória e 16 vCores é o valor padrão.
6. Use os controles deslizantes ou as caixas de texto para especificar a quantidade de armazenamento e o número de núcleos virtuais. 
7. Ao concluir, clique em **Aplicar** para salvar sua seleção.  
8. Clique em **Criar** para implantar a Instância Gerenciada.
9. Clique no ícone **Notificações** para exibir o status da implantação.

    ![andamento da implantação da instância gerenciada](./media/sql-database-managed-instance-get-started/deployment-progress.png)

10. Clique em **Implantação em andamento** para abrir a janela Instância Gerenciada e monitorar melhor o progresso da implantação. 

> [!IMPORTANT]
> Para a primeira instância em uma sub-rede, o tempo de implantação é normalmente muito maior do que no caso de instâncias subsequentes. Não cancele a operação de implantação porque ela dura mais que o esperado. A criação da segunda Instância Gerenciada na sub-rede levará apenas alguns minutos.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>Examinar os recursos e recuperar o nome do servidor totalmente qualificado

Depois que a implantação for concluída com êxito, examine os recursos criados e recupere o nome do servidor totalmente qualificado para uso em inícios rápidos posteriores.

1. Abra o grupo de recursos para a Instância Gerenciada e exiba seus recursos que foram criados para você no início rápido [Criar uma Instância Gerenciada](sql-database-managed-instance-get-started.md).

   ![Recursos da Instância Gerenciada](./media/sql-database-managed-instance-get-started/resources.png)Abra o recurso da Instância Gerenciada no portal do Azure.

2. Clique na Instância Gerenciada.
3. Na guia **Visão geral**, localize a propriedade **Host** e copie o endereço de host totalmente qualificado para a Instância Gerenciada.


   ![Recursos da Instância Gerenciada](./media/sql-database-managed-instance-get-started/host-name.png)

   O nome será semelhante a este: **nome_do_seu_computador.neu15011648751ff.database.windows.net**.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como se conectar a uma Instância Gerenciada, confira:
  - Para obter uma visão geral das opções de conexão para aplicativos, confira [Conectar seus aplicativos à Instância Gerenciada](sql-database-managed-instance-connect-app.md).
  - Para ver um início rápido que mostra como se conectar a uma Instância Gerenciada de uma máquina virtual do Azure, confira [Configurar uma conexão de máquina virtual do Azure](sql-database-managed-instance-configure-vm.md).
  - Para ver um início rápido que mostra como se conectar a uma Instância Gerenciada de um computador cliente local usando uma conexão ponto a site, confira [Configurar uma conexão ponto a site](sql-database-managed-instance-configure-p2s.md).
- Para restaurar um banco de dados do SQL Server existente do local para uma Instância Gerenciada, use o [DMS (Serviço de Migração de Banco de Dados do Azure) para realizar a migração](../dms/tutorial-sql-server-to-managed-instance.md) para restaurar de um arquivo de backup do banco de dados ou o [comando T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) para restaurar de um arquivo de backup do banco de dados.
- Para monitoramento avançado de desempenho de banco de dados de Instância Gerenciada com inteligência de solução de problemas, veja [Monitorar banco de dados SQL do Azure usando a Análise de SQL do Azure ](../log-analytics/log-analytics-azure-sql.md)
