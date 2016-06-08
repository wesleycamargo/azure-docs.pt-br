### Pré-requisitos

- Uma conta [FTP](https://wikipedia.org/wiki/File_Transfer_Protocol)  


Antes de usar a conta FTP em um aplicativo lógico, você deve autorizar o aplicativo lógico a se conectar à sua conta FTP. Felizmente, você pode fazer isso facilmente dentro do seu aplicativo lógico no Portal do Azure.

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à sua conta FTP:
1. Para criar uma conexão com o FTP, no designer do aplicativo lógico, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa, em seguida, digite *FTP* na caixa de pesquisa. Selecione o gatilho ou ação que gostaria de usar: ![etapa de criação de conexão com o FTP](./media/connectors-create-api-ftp/ftp-1.png)  
2. Se você não tiver criado nenhuma conexão com o FTP antes, suas credenciais do FTP serão solicitadas. Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar aos dados da sua conta do FTP e usá-los: ![etapa de criação de conexão com o FTP](./media/connectors-create-api-ftp/ftp-2.png)  
3. Observe que a conexão foi criada e agora você pode continuar com as outras etapas no seu aplicativo lógico: ![etapa de criação de conexão com o FTP](./media/connectors-create-api-ftp/ftp-3.png)  

<!---HONumber=AcomDC_0525_2016-->