1. Em uma nova janela do navegador, entre no [Portal do Azure](https://portal.azure.com/).
2. No menu esquerdo, clique em **Novo**, clique em **Bancos de Dados** e então em **Azure Cosmos DB**, clique em **Criar**. 
   
   ![Captura de tela do Portal do Azure, realçando Mais Serviços e BD Cosmos do Azure](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. Na página **Nova conta**, insira as configurações da nova conta do Azure Cosmos DB. 
 
    Configuração|Valor sugerido|Descrição
    ---|---|---
    ID|*Insira um nome exclusivo*|Insira um nome exclusivo para identificar essa conta do Azure Cosmos DB. Como *documents.Azure.com* é acrescentado à ID que você fornece para criar o URI, use uma ID exclusiva mas identificável.<br><br>A ID pode conter apenas letras minúsculas, números e hifens (-), e deve conter de três a 50 caracteres.
    API|tabela do Azure|A API determina o tipo de conta a ser criada. O Azure Cosmos DB fornece cinco APIs para atender às necessidades de seu aplicativo: SQL (banco de dados de documentos), Gremlin (banco de dados de gráfico), MongoDB (banco de dados de documentos), Tabela do Azure e Cassandra, cada um atualmente exigindo uma conta separada.<br><br>Selecione **Tabela do Azure** porque neste guia de início rápido você está criando uma tabela que funciona com a API de Tabela.<br><br>[Saiba mais sobre a API de Tabela](../articles/cosmos-db/table-introduction.md) |
    Assinatura|*Insira o mesmo nome exclusivo como fornecido acima na ID*|Selecione a assinatura do Azure que você deseja usar para essa conta do Azure Cosmos DB. 
    Grupo de recursos|*O mesmo valor que a ID*|Insira o novo nome de grupo de recursos para sua conta. Para simplificar, você pode usar um nome igual à sua ID. 
    Local|*Selecione a região mais próxima de seus usuários*|Selecione a localização geográfica na qual hospedar a sua conta do Azure Cosmos DB. Use o local mais próximo dos usuários para fornecer a eles acesso mais rápido aos dados.
    Habilitar a redundância geográfica| Deixar em branco | Isso cria uma versão replicada do banco de dados em uma segunda região (par). Deixe em branco.  
    Fixar no painel | Selecionar | Marque esta caixa para que a sua nova conta de banco de dados seja adicionada ao seu painel do portal para fácil acesso.

    Em seguida, clique em **Criar**.  

    ![Captura de tela da folha Novo BD Cosmos do Azure](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-2.png)

4. A criação da conta leva alguns minutos. Durante a criação da conta, o portal exibe o bloco **Implantando o Azure Cosmos DB**.

    ![O painel Notificações do portal do Azure](./media/cosmos-db-create-dbaccount-table/deploying-cosmos-db.png)

    Depois que a conta é criada, a página **Parabéns. Sua conta do Azure Cosmos DB foi criada** é exibida.
