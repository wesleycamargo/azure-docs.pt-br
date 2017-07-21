## Faça logon no Portal do Azure
<a id="log-in-to-the-azure-portal" class="xliff"></a>

Faça logon no [Portal do Azure](https://portal.azure.com/).

## Criar um Banco de Dados SQL em branco usando o portal do Azure
<a id="create-a-blank-sql-database-using-the-azure-portal" class="xliff"></a>

Um banco de dados SQL do Azure é criado com um conjunto definido de [recursos de computação e armazenamento](../articles/sql-database/sql-database-service-tiers.md). O banco de dados é criado dentro de um [grupo de recursos do Azure](../articles/azure-resource-manager/resource-group-overview.md) e em um [servidor lógico de banco de dados SQL do Azure](../articles/sql-database/sql-database-features.md). 

Siga estas etapas para criar um banco de dados SQL em branco. 

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

2. Selecione **Bancos de Dados** na página **Novo** e **Banco de Dados SQL** na página **Bancos de Dados**. 

   ![criar banco de dados vazio](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

3. Preencha o formulário do Banco de Dados SQL com as informações abaixo, conforme mostrado na imagem anterior:   

   | Configuração | Valor sugerido | Descrição |
   | --------| --------------- | ----------- | 
   | **Nome do banco de dados** | mySampleDatabase | Para ver os nomes do banco de dados válidos, consulte [Identificadores do Banco de Dados](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). | 
   | **Assinatura** | Sua assinatura  | Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
   | **Grupo de recursos** | myResourceGroup | Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Selecionar fonte** | Banco de dados em branco | Especifica que um banco de dados em branco deve ser criado. |
   ||||

4. Clique em **Servidor** para criar e configurar um novo servidor para o novo banco de dados. Preencha o **formulário Novo servidor** com as seguintes informações: 

   | Configuração | Valor sugerido | Descrição |
   | --------| --------------- | ----------- | 
   | **Nome do servidor** | Qualquer nome exclusivo globalmente. | Para ver os nomes do servidor válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). | 
   | **Logon de administrador do servidor** | Qualquer nome válido. | Para ver os nomes de logon válidos, consulte [Identificadores do Banco de Dados](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers).|
   | **Senha** | Qualquer senha válida. | Sua senha deve ter, pelo menos, oito caracteres e deve conter caracteres de três das seguintes categorias: caracteres com letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos. |
   | **Localidade** | Qualquer local válido. | Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/). |
   ||||

   ![criar database-server](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

5. Clique em **Selecionar**.

6. Clique em **Tipo de preço** para especificar o nível de desempenho e o tipo de serviço para o novo banco de dados. Para este tutorial, selecione **20 DTUs** e **250** GB de armazenamento.

   ![Criar database-s1](../articles/sql-database/media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. Clique em **Aplicar**.  

8. Selecione um **agrupamento** para o banco de dados em branco (para este tutorial, use o valor padrão). Para obter mais informações sobre agrupamentos, consulte [Agrupamentos](https://docs.microsoft.com/sql/t-sql/statements/collations)

9. Clique em **Criar** para provisionar o banco de dados. O provisionamento leva cerca de um minuto e meio para concluir. 

10. Na barra de ferramentas, clique em **Notificações** para monitorar o processo de implantação.

   ![notificação](../articles/sql-database/media/sql-database-get-started-portal/notification.png)

## Criar uma regra de firewall de nível de servidor usando o portal do Azure
<a id="create-a-server-level-firewall-rule-using-the-azure-portal" class="xliff"></a>

O serviço Banco de Dados SQL cria um firewall no nível do servidor. Inicialmente, o firewall impede ferramentas externas e aplicativos de se conectarem ao servidor ou a bancos de dados no servidor. As conexões são permitidas depois que uma regra de firewall é criada para abrir os endereços IP específicos. Execute estas etapas a fim de criar uma [regra de firewall no nível do servidor do Banco de Dados SQL](../articles/sql-database/sql-database-firewall-configure.md) para o endereço IP do seu cliente e habilitar a conectividade externa por meio do firewall do Banco de Dados SQL somente para seu endereço IP. 


> [!NOTE]
> O Banco de Dados SQL do Azure se comunica pela porta 1433. Você só pode se conectar ao banco de dados SQL depois que o firewall da sua rede permite o tráfego de saída pela porta 1433.


1. Depois da implantação ser concluída, clique em **Bancos de dados SQL** no menu à esquerda, depois, clique em **mySampleDatabase** na página **Bancos de dados SQL**. Uma página de visão geral de seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver20170313.database.windows.net**) e fornece opções para configurações adicionais. Copie esse nome totalmente qualificado do servidor para um uso posterior.

   > [!IMPORTANT]
   > Você precisará desse nome totalmente qualificado do servidor para conectar o servidor e seus bancos de dados nos inícios rápidos subsequentes.
   > 

   ![nome do servidor](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 

2. Clique em **Definir o firewall do servidor** na barra de ferramentas, conforme mostrado na imagem anterior. A página **Configurações do firewall** do servidor de Banco de Dados SQL é aberta. 

   ![regra de firewall do servidor](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule.png) 


3. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar seu endereço IP atual a uma nova regra de firewall. Uma regra de firewall pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

4. Clique em **Salvar**. Uma regra de firewall no nível do servidor é criada para a porta de abertura 1433 de seu endereço IP atual no servidor lógico.

   ![definir regra de firewall do servidor](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. Clique em **OK**, em seguida, feche a página **Configurações do Firewall**.

Agora você pode se conectar ao servidor Banco de Dados SQL e a seus bancos de dados usando uma ferramenta como o SS;S (SQL Server Management Studio). A conexão vem desse endereço IP e usa a conta de administrador do servidor criada anteriormente.


> [!IMPORTANT]
> Por padrão, o acesso através do firewall do Banco de Dados SQL está habilitado para todos os serviços do Azure. Clique em **DESATIVAR** nesta página para desabilitar todos os serviços do Azure.


## Obter valores de cadeia de conexão usando o portal do Azure
<a id="get-connection-string-values-using-the-azure-portal" class="xliff"></a>

Obtenha o nome de servidor totalmente qualificado para o servidor de Banco de Dados SQL do Azure no Portal do Azure. Use o nome do servidor totalmente qualificado para se conectar ao servidor usando o SQL Server Management Studio.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).

2. Selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de Dados SQL**. 

3. No painel **Essentials**, na página do Portal do Azure de seu banco de dados, localize e copie o **Nome do servidor**.

   ![informações da conexão](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 
