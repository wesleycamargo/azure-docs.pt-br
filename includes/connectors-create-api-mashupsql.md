### Pré-requisitos
- Acesso a um banco de dados do SQL Server

Antes de usar sua conta do Mashup SQL em um aplicativo lógico, você deve autorizar o aplicativo lógico a se conectar à sua conta do Mashup SQL. Felizmente, você pode fazer isso facilmente a partir de seu aplicativo lógico no Portal do Azure.

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à sua conta do Mashup SQL:

1. Para criar uma conexão com o Mashup SQL, no designer do aplicativo lógico, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa, em seguida, digite *Mashup SQL* na caixa de pesquisa. Selecione o gatilho ou ação que gostaria de usar: ![Etapa 1 do Mashup SQL](./media/connectors-create-api-mashupsql/mashupsql-1.png)
2. Se você não tiver criado quaisquer conexões Mashup SQL antes, será solicitado a fornecer suas credenciais do Mashup SQL. Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar aos dados da sua conta do Mashup SQL e usá-los: ![Etapa 2 do Mashup SQL](./media/connectors-create-api-mashupsql/mashupsql-2.png)
5. Observe que a conexão foi criada e agora você pode continuar com as outras etapas no seu aplicativo lógico: ![Etapa 3 do Mashup SQL](./media/connectors-create-api-mashupsql/mashupsql-3.png)