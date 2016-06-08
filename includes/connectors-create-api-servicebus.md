### Pré-requisitos

- Uma conta do [ServiceBus](https://azure.microsoft.com/services/service-bus/)  


Antes de usar a conta do ServiceBus em um aplicativo lógico, você deve autorizar o aplicativo lógico a se conectar à sua conta do ServiceBus. Felizmente, você pode fazer isso facilmente dentro do seu aplicativo lógico no Portal do Azure.

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à sua conta do ServiceBus:
1. Para criar uma conexão com o ServiceBus, no designer do aplicativo lógico, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa, em seguida, digite *ServiceBus* na caixa de pesquisa. Selecione o gatilho ou ação que gostaria de usar: ![](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. Se você não tiver criado quaisquer conexões com o ServiceBus antes, suas credenciais do ServiceBus serão solicitadas. Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar aos dados da sua conta do ServiceBus e usá-los: ![](./media/connectors-create-api-servicebus/servicebus-2.png)  
3. Observe que a conexão foi criada e agora você pode continuar com as outras etapas no seu aplicativo lógico: ![](./media/connectors-create-api-servicebus/servicebus-3.png)   

<!---HONumber=AcomDC_0525_2016-->