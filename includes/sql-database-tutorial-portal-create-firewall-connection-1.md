---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: ab31ee82e8035fe888fa70b5796aef2c2b2939b2
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728520"
---
## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/)

## <a name="create-a-blank-sql-database"></a>Criar um banco de dados SQL em branco

Existe um Banco de Dados SQL do Azure dentro de um conjunto definido de [recursos de computação e armazenamento](../articles/sql-database/sql-database-service-tiers-dtu.md). O banco de dados funciona em um [grupo de recursos do Azure](../articles/azure-resource-manager/resource-group-overview.md) e um [servidor lógico do Banco de Dados SQL do Azure](../articles/sql-database/sql-database-features.md).

Siga estas etapas para criar um banco de dados SQL em branco.

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.

1. Na página **Novo**, selecione **Bancos de Dados** > **Banco de Dados SQL**.

   ![criar banco de dados vazio](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

1. No painel **Banco de Dados SQL**, digite ou selecione os seguintes valores:

   | Configuração       | Valor sugerido | DESCRIÇÃO |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome do banco de dados** | *yourDatabase* | Para ver os nomes do banco de dados válidos, confira [Identificadores do banco de dados](/sql/relational-databases/databases/database-identifiers). |
   | **Assinatura** | *yourSubscription*  | Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
   | **Grupo de recursos** | *yourResourceGroup* | Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/naming-conventions). |
   | **Selecionar fonte** | Banco de dados em branco | Especifica que um banco de dados em branco deve ser criado. |

   ![criar banco de dados](../articles/sql-database/media/sql-database-design-first-database/create-database.png)

   1. Selecione **Servidor** para configurar um servidor para seu novo banco de dados. Em seguida, digite ou selecione os seguintes valores:

      | Configuração       | Valor sugerido | DESCRIÇÃO |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Nome do servidor** | Qualquer nome exclusivo globalmente | Para ver os nomes do servidor válidos, consulte [Regras e restrições de nomenclatura](/azure/architecture/best-practices/naming-conventions). |
      | **Logon de administrador do servidor** | Qualquer nome válido | Para ver os nomes de logon válidos, confira [Identificadores do banco de dados](/sql/relational-databases/databases/database-identifiers).|
      | **Senha** | Qualquer senha válida | Sua senha deve ter, pelo menos, 8 caracteres e deve usar caracteres de três das seguintes categorias: caracteres com letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos. |
      | **Localidade** | Qualquer local válido | Para obter mais informações sobre as regiões, confira [Regiões do Azure](https://azure.microsoft.com/regions/). |

      Escolha **Selecionar**.

      ![criar database-server](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

   1. Selecione **Tipo de preço** para especificar a camada de serviço, o número de DTUs e a quantidade de armazenamento. Explore as opções para as DTUs e o armazenamento disponível em cada camada de serviço.

      Depois de selecionar a camada de servidor, o número de DTUs e a quantidade de armazenamento, selecione **Aplicar**.

   1. Insira uma **Ordenação** para o banco de dados em branco (para este tutorial, use o valor padrão). Para obter mais informações sobre ordenações, confira [Ordenações](/sql/t-sql/statements/collations).

1. Agora que você concluiu o formulário **Banco de Dados SQL**, selecione **Criar** para criar o banco de dados. Esta etapa pode levar até um minuto e meio para ser concluída.

1. Na barra de ferramentas, clique em **Notificações** para monitorar o processo de implantação.

     ![notificação](../articles/sql-database/media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>Criar uma regra de firewall

O serviço do Banco de Dados SQL cria um firewall no nível do servidor para impedir que os aplicativos e ferramentas externos conectem-se ao servidor ou a bancos de dados no servidor. Siga estas etapas para criar uma [regra de firewall no nível de servidor de Banco de Dados SQL](../articles/sql-database/sql-database-firewall-configure.md) para o endereço IP do seu cliente. Esse processo permite conectividade externa por meio do firewall do Banco de Dados SQL para seu endereço IP somente.

> [!NOTE]
> O Banco de Dados SQL se comunica pela porta 1433. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall de sua rede. Se isso acontecer, você não poderá conectar o servidor de Banco de Dados SQL do Azure, a menos que o administrador abra a porta 1433.

1. Depois da implantação ser concluída, escolha **Bancos de dados SQL** no menu à esquerda, depois, selecione *yourDatabase* na página **Bancos de dados SQL**. A página **Visão Geral** de seu banco de dados é aberta, mostrando o nome do servidor totalmente qualificado (como *yourserver.database.windows.net*) e fornece opções para configurações adicionais.

1. Copie o nome totalmente qualificado do servidor a ser usado para se conectar ao seu servidor e seus bancos de dados em etapas posteriores.

   ![nome do servidor](../articles/sql-database/media/sql-database-design-first-database/server-name.png)

1. Selecione **Definir firewall do servidor** na barra de ferramentas. A página **Configurações do firewall** do servidor de Banco de Dados SQL é aberta.

   ![regra de firewall do servidor](../articles/sql-database/media/sql-database-design-first-database/server-firewall-rule.png)

   1. Selecione **Adicionar IP do cliente** na barra de ferramentas para adicionar seu endereço IP atual a uma nova regra de firewall. Uma regra de firewall pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

   1. Escolha **Salvar**. Uma regra de firewall no nível do servidor é criada para a porta de abertura 1433 de seu endereço IP atual no servidor lógico.

   1. Selecione **OK** e, em seguida, feche a página **Configurações do Firewall**.

Seu endereço IP agora pode passar pelo firewall e conectar-se ao servidor de Banco de Dados SQL e seus respectivos bancos de dados usando o SSMS ou outra ferramenta de sua escolha. Use a conta do administrador do servidor criada anteriormente.

> [!IMPORTANT]
> Por padrão, o acesso através do firewall do Banco de Dados SQL está habilitado para todos os serviços do Azure. Selecione **DESLIGAR** nessa página para desabilitar todos os serviços do Azure.
