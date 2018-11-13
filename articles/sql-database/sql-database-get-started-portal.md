---
title: 'Portal do Azure: Criar um banco de dados SQL | Microsoft Docs'
description: Crie um servidor lógico, uma regra de firewall no nível do servidor e um banco de dados do Banco de Dados SQL no portal do Azure e consulte-o.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 66ee4ac8fe946696d6760891a086a672fa9fc412
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914586"
---
# <a name="quickstart-create-an-azure-sql-database-in-the-azure-portal"></a>Início Rápido: Criar um Banco de Dados SQL do Azure no portal do Azure

Este início rápido mostra como criar um banco de dados SQL no Azure usando o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md). O Banco de Dados SQL do Azure é uma oferta de "Banco de Dados como Serviço" que permite executar e dimensionar os bancos de dados do SQL Server altamente disponíveis na nuvem. Este Início Rápido mostra como começar a criar e, em seguida, a consultar um Banco de Dados SQL usando o portal do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

  >[!NOTE]
  >Este tutorial usa o modelo de compra baseado em DTU, mas o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) também está disponível.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Criar um banco de dados SQL

Um banco de dados SQL do Azure é criado com um conjunto definido de [recursos de computação e armazenamento](sql-database-service-tiers-dtu.md). O banco de dados é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e em um [servidor lógico de banco de dados SQL do Azure](sql-database-features.md).

Execute estas etapas para criar um Banco de Dados SQL que contém os dados de exemplo do Adventure Works LT.

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.

2. Selecione **Bancos de Dados** na página **Novo** e selecione **Criar** em **Banco de Dados SQL** na página **Novo**.

   ![criar database-1](./media/sql-database-get-started-portal/create-database-1.png)

3. Preencha o formulário do Banco de Dados SQL com as informações abaixo, conforme mostrado na imagem anterior:   

   | Configuração       | Valor sugerido | DESCRIÇÃO |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome do banco de dados** | mySampleDatabase | Para ver os nomes do banco de dados válidos, consulte [Identificadores do Banco de Dados](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Assinatura** | Sua assinatura  | Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
   | **Grupo de recursos**  | myResourceGroup | Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Selecionar fonte** | Exemplo (AdventureWorksLT) | Carrega o esquema AdventureWorksLT e os dados no novo banco de dados |

   > [!IMPORTANT]
   > Você deve selecionar o banco de dados de exemplo neste formulário porque ele é usado no restante deste início rápido.
   >

4. Em **Servidor**, clique em **Definir configurações obrigatórias** e preencha o formulário do SQL Server (servidor lógico) com as informações abaixo, conforme mostrado na imagem a seguir:   

   | Configuração       | Valor sugerido | DESCRIÇÃO |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome do servidor** | Qualquer nome exclusivo globalmente | Para ver os nomes do servidor válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Logon de administrador do servidor** | Qualquer nome válido | Para ver os nomes de logon válidos, consulte [Identificadores do Banco de Dados](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Senha** | Qualquer senha válida | Sua senha deve ter, pelo menos, 8 caracteres e deve conter caracteres de três das seguintes categorias: caracteres com letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos. |
   | **Assinatura** | Sua assinatura | Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
   | **Grupo de recursos** | myResourceGroup | Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Localidade** | Qualquer local válido | Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/). |

   > [!IMPORTANT]
   > O logon de administrador do servidor e a senha que você especificar aqui são necessários para fazer logon no servidor e em seus bancos de dados mais tarde neste Guia de início rápido. Lembre-se ou registre essas informações para o uso posterior.
   >  

   ![criar database-server](./media/sql-database-get-started-portal/create-database-server.png)

5. Quando tiver concluído o formulário, clique em **Selecionar**.

6. Clique em **Tipo de preço** para especificar a camada de serviço, o número de DTUs e a quantidade de armazenamento. Explore as opções para a quantidade de DTUs e o armazenamento disponível em cada camada de serviço.

   > [!IMPORTANT]
   > Mais de 1 TB de armazenamento na camada Premium está disponível atualmente em todas as regiões, exceto as seguintes: Norte do Reino Unido, Centra-oeste dos EUA, Sul do Reino 2, Leste da China, US DoD Central, Alemanha Central, US DoD Leste, US Gov Sudoeste, US Gov Centro-Sul, Nordeste da Alemanha, Norte da China, US Gov Leste. Em outras regiões, o armazenamento máximo na camada Premium é limitado a 1 TB. Consulte [Limitações atuais de P11-P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

7. Para este início rápido, selecione a camada de serviço **Standard** e use o controle deslizante para selecionar **10 DTUs (S0)** e **1** GB de armazenamento.

   ![Criar database-s1](./media/sql-database-get-started-portal/create-database-s1.png)

8. Aceite os termos da versão prévia para usar a opção **Armazenamento Complementar**.

   > [!IMPORTANT]
   > Mais de 1 TB de armazenamento na camada Premium está disponível atualmente em todas as regiões, exceto as seguintes: , Centro-Oeste dos EUA, Leste da China, USDoDCentral, USGov Iowa, Alemanha Central, USDoDEast, US Gov Sudoeste, Nordeste da Alemanha, Norte da China. Em outras regiões, o armazenamento máximo na camada Premium é limitado a 1 TB. Consulte [Limitações atuais de P11-P15]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

9. Depois de selecionar a camada de servidor, o número de DTUs e a quantidade de armazenamento, clique em **Aplicar**.  

10. Agora que você concluiu o formulário do Banco de Dados SQL, clique em **Criar** para provisionar o banco de dados. O provisionamento demora alguns minutos.

11. Na barra de ferramentas, clique em **Notificações** para monitorar o processo de implantação.

     ![notificação](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>Consultar o Banco de Dados SQL

Agora que você criou um banco de dados de exemplo no Azure, usaremos a ferramenta de consulta interna no portal do Azure para confirmar que você pode conectar o banco de dados e consultar os dados.

1. Na página de Banco de Dados SQL do seu banco de dados, clique em **Editor de consultas (versão prévia)** no menu do lado esquerdo e, em seguida, clique em **Login**.

   ![logon](./media/sql-database-get-started-portal/query-editor-login.png)

2. Selecione a autenticação do SQL Server, forneça as informações de logon necessárias e, em seguida, clique em **OK** para fazer logon.

3. Depois de autenticado como **ServerAdmin**, digite a consulta a seguir no painel do editor de consulta.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Clique em **Executar** e reveja os resultados da consulta no painel **Resultados**.

   ![resultados do editor de consultas](./media/sql-database-get-started-portal/query-editor-results.png)

5. Feche a página **Editor de consulta** e clique em **OK** para descartar suas edições não salvas.

## <a name="clean-up-resources"></a>Limpar recursos

Salvar esses recursos se você deseja ir para as [Próximas etapas](#next-steps) e saiba como se conectar e consultar o banco de dados usando vários métodos diferentes. No entanto, se você deseja excluir os recursos criados neste início rápido, use as etapas a seguir.


1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e clique em **myResourceGroup**.
2. Em sua página de grupo de recursos, clique em **Excluir**, digite **myResourceGroup** na caixa de texto e clique **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Agora que você já tem um banco de dados, será necessário criar uma regra de firewall no nível do servidor para conectar-se a ele usando suas ferramentas locais. Confira [Criar regra de firewall no nível do servidor](sql-database-get-started-portal-firewall.md)
- Se estiver criando uma regra de firewall no nível do servidor, você poderá [conectar e consultar](sql-database-connect-query.md) usando uma das suas ferramentas ou linguagens favoritas, incluindo
  - [Conectar e consultar usando o SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Conectar e consultar usando o Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Para criar bancos de dados usando a CLI do Azure, confira [Exemplos da CLI do Azure](sql-database-cli-samples.md)
- Para criar bancos de dados usando o Azure PowerShell, confira [Amostras do Azure PowerShell](sql-database-powershell-samples.md)