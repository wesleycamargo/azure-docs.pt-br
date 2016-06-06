### Pré-requisitos

- Uma conta de [Armazenamento de Blobs do Azure](https://azure.microsoft.com/documentation/services/storage/)  


Antes de usar a conta de Armazenamento de Blobs do Azure em um aplicativo lógico, você deve autorizar o aplicativo lógico a se conectar à sua conta de Armazenamento de Blobs do Azure. Felizmente, você pode fazer isso facilmente dentro do seu aplicativo lógico no Portal do Azure.

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à sua conta de Armazenamento de Blobs do Azure:
1. Para criar uma conexão com o Armazenamento de Blobs do Azure, no designer do aplicativo lógico, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa, em seguida, digite *Armazenamento de Blobs do Azure* na caixa de pesquisa. Selecione o gatilho ou ação que gostaria de usar: ![Etapa de criação da conexão com o Armazenamento de Blobs do Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. Se você não tiver criado quaisquer conexões com o Armazenamento de Blobs do Azure antes, suas credenciais do Armazenamento de Blobs do Azure serão solicitadas. Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar aos dados da sua conta de Armazenamento de Blobs do Azure e usá-los: ![Etapa de criação da conexão com o Armazenamento de Blobs do Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-2.png)  
3. Observe que a conexão foi criada e agora você pode continuar com as outras etapas no seu aplicativo lógico: ![Etapa de criação da conexão com o Armazenamento de Blobs do Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

<!---HONumber=AcomDC_0525_2016-->