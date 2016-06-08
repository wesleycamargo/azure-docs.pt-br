### Pré-requisitos

- Uma conta do [SQL Azure](https://www.microsoft.com/sql)  


Antes de usar a conta do SQL Azure em um aplicativo lógico, você deve autorizar o aplicativo lógico a se conectar à sua conta do SQL Azure. Felizmente, você pode fazer isso facilmente dentro do seu aplicativo lógico no Portal do Azure.

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à sua conta do SQL Azure:
1. Para criar uma conexão com o SQL Azure, no designer do aplicativo lógico, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa, em seguida, digite *SQL Azure* na caixa de pesquisa. Selecione o gatilho ou ação que gostaria de usar: ![Etapa de criação da conexão com o SQL Azure](./media/connectors-create-api-sql/sql-1.png)  
2. Se você não tiver criado quaisquer conexões com o SQL Azure antes, suas credenciais do SQL Azure serão solicitadas. Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar aos dados da sua conta do SQL Azure e usá-los: ![Etapa de criação da conexão com o SQL Azure](./media/connectors-create-api-sql/sql-2.png)  
3. Observe que a conexão foi criada e agora você pode continuar com as outras etapas no seu aplicativo lógico: ![Etapa de criação da conexão com o SQL Azure](./media/connectors-create-api-sql/sql-3.png)  

<!---HONumber=AcomDC_0525_2016-->