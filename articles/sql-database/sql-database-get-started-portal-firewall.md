---
title: 'Portal do Azure: Criar uma regra de firewall do Banco de Dados SQL | Microsoft Docs'
description: Criar uma regra de firewall no nível do servidor do Banco de Dados SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 589d3fa8c0ee8c8f374cd4f34f17401caa46d265
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462249"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-your-sql-database-using-the-azure-portal"></a>Início rápido: Criar uma regra de firewall no nível do servidor para o Banco de Dados SQL usando o portal do Azure

Este início rápido mostra como criar uma regra de firewall no nível do servidor para um Banco de Dados SQL do Azure para que você possa se conectar a ele de um recurso local.

## <a name="prerequisites"></a>Pré-requisitos

Esse Início Rápido usa os recursos criados em [Criar um Banco de Dados SQL do Azure no portal do Azure](sql-database-get-started-portal.md) como ponto de partida.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall no nível de servidor

O serviço de Banco de Dados SQL cria um firewall no nível do servidor. Esse firewall impede que os aplicativos e as ferramentas se conectem ao servidor ou qualquer banco de dados do servidor, a menos que você crie uma regra de firewall para abrir o firewall. Para uma conexão a partir de um endereço IP fora do Azure, crie uma regra de firewall para um endereço IP específico ou intervalo de endereços. Para obter mais informações sobre as regras de firewall, confira [Regras de firewall no nível de servidor para o Banco de Dados SQL](sql-database-firewall-configure.md).

> [!NOTE]
> O Banco de Dados SQL se comunica pela porta 1433. Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 talvez não seja permitido pelo firewall da rede. Se isto acontecer, você não poderá conectar o servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

Execute essas etapas para criar uma regra de firewall no nível de servidor para o endereço IP do seu cliente e habilite a conectividade externa por meio do firewall do Banco de Dados SQL somente para seu endereço IP.

1. Após a conclusão da implantação dos [pré-requisitos do Banco de Dados SQL do Azure](#prerequisites), selecione **bancos de dados SQL** no menu à esquerda e escolha **mySampleDatabase** na página **Bancos de Dados SQL**. A página de visão geral de seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver-20170824.database.windows.net**) e fornece opções para configurações adicionais.

2. Copie esse nome do servidor totalmente qualificado para usar quando se conectar ao servidor e aos bancos de dados nos outros inícios rápidos.

   ![nome do servidor](./media/sql-database-get-started-portal/server-name.png)

3. Selecione **Definir firewall do servidor** na barra de ferramentas. A página **Configurações do firewall** do servidor de Banco de Dados SQL é aberta.

   ![regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Selecione **Adicionar IP do cliente** na barra de ferramentas para adicionar seu endereço IP atual a uma nova regra de firewall. Uma regra de firewall pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

   > [!IMPORTANT]
   > Por padrão, o acesso através do firewall do Banco de Dados SQL está habilitado para todos os serviços do Azure. Selecione **DESATIVAR** nessa página para desabilitar todos os serviços do Azure.
   >

5. Clique em **Salvar**. Uma regra de firewall no nível do servidor é criada para a porta de abertura 1433 de seu endereço IP atual no servidor de Banco de Dados SQL.

6. Feche a página **Configurações do Firewall**.

Usando o SQL Server Management Studio ou outra ferramenta de sua escolha, agora é possível conectar-se ao servidor do Banco de Dados SQL e a seus bancos de dados a partir desse endereço IP com a conta do administrador do servidor criada anteriormente.

## <a name="clean-up-resources"></a>Limpar recursos

Salvar esses recursos se você deseja ir para as [Próximas etapas](#next-steps) e saiba como se conectar e consultar o banco de dados usando vários métodos diferentes. No entanto, se você quer excluir os recursos criados nesse início rápido, use as etapas a seguir.

1. No menu à esquerda no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione **myResourceGroup**.
2. Em sua página de grupo de recursos, selecione **Excluir**, digite **myResourceGroup** na caixa de texto e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Agora que você tem um banco de dados, você pode [se conectar e consultar](sql-database-connect-query.md) usando uma das suas ferramentas ou linguagens favoritas, incluindo
  - [Conectar e consultar usando o SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Conectar e consultar usando o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para saber como projetar seu primeiro banco de dados, criar tabelas e inserir dados, consulte um destes tutoriais:
  - [Projetar seu primeiro banco de dados SQL do Azure usando o SSMS](sql-database-design-first-database.md)
  - [Criar um banco de dados SQL do Azure e conectar-se com C# e o ADO.NET](sql-database-design-first-database-csharp.md)
