### Pré-requisitos

- Uma conta do [ServiceBus](https://azure.microsoft.com/services/service-bus/)


Antes de usar sua conta do Barramento de Serviço do Azure em um aplicativo lógico, você deve autorizá-lo a se conectar à conta do barramento de serviço. Felizmente, você pode fazer isso de forma fácil usando seu aplicativo lógico no Portal do Azure.

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à conta do barramento de serviço:

1. Para criar uma conexão com o barramento de serviço, no designer do aplicativo lógico, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e digite *barramento de serviço* na caixa de pesquisa. Selecione o gatilho ou ação que você deseja usar:  
    ![Imagem de conexão do barramento de serviço 1](./media/connectors-create-api-servicebus/servicebus-1.png)
    
2. Se você ainda não tiver criado nenhuma conexão com o barramento de serviço, será solicitado que você informe suas credenciais do barramento de serviço. Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar aos dados da sua conta do barramento de serviço e usá-los. O conector do barramento de serviço precisa da cadeia de conexão para o namespace do barramento de serviço, e também requer permissões para **Gerenciar**. Para saber se a cadeia de conexão é para o namespace ou para uma entidade específica, analise se ela contém o parâmetro `EntityPath`: se contiver, ela não será a cadeia de conexão ideal para um aplicativo lógico.  
    ![Cadeia de conexão do barramento de serviço](./media/connectors-create-api-servicebus/connectionstring.png)

1. Após receber a cadeia de conexão para o namespace, você pode usá-lo para a Conexão de API em Aplicativos Lógicos.  
    ![Imagem de conexão do barramento de serviço 2](./media/connectors-create-api-servicebus/servicebus-2.png)

3. Observe que a conexão foi criada e agora você pode continuar com as outras etapas no seu aplicativo lógico:  
    ![Imagem de conexão do barramento de serviço 3](./media/connectors-create-api-servicebus/servicebus-3.png)

<!---HONumber=AcomDC_0727_2016-->
