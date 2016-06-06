### Pré-requisitos

- Uma conta [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol)  


Antes de usar a conta SFTP em um aplicativo lógico, você deve autorizar o aplicativo lógico a se conectar à sua conta SFTP. Felizmente, você pode fazer isso facilmente dentro do seu aplicativo lógico no Portal do Azure.

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à sua conta SFTP:
1. Para criar uma conexão com o SFTP, no designer do aplicativo lógico, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa, em seguida, digite *SFTP* na caixa de pesquisa. Selecione o gatilho ou ação que gostaria de usar: ![](./media/connectors-create-api-sftp/sftp-1.png)  
2. Se você não tiver criado nenhuma conexão com o SFTP antes, suas credenciais do SFTP serão solicitadas. Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar aos dados da sua conta SFTP e usá-los: ![](./media/connectors-create-api-sftp/sftp-2.png)  
3. Observe que a conexão foi criada e agora você pode continuar com as outras etapas no seu aplicativo lógico: ![](./media/connectors-create-api-sftp/sftp-3.png)  

<!---HONumber=AcomDC_0525_2016-->