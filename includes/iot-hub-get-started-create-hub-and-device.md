## <a name="create-an-iot-hub"></a>Crie um hub IoT

1. No [portal do Azure](https://portal.azure.com/), clique em **Novo** > **Internet das Coisas** > **Hub IoT**.

   ![Criar um hub iot no portal do Azure](../articles/iot-hub/media/iot-hub-create-hub-and-device/1_create-azure-iot-hub-portal.png)
1. No painel **Hub IoT**, insira as seguintes informações para o Hub IoT:

   **Nome**: o nome para o hub IoT. Se o nome for válido, uma marca de seleção verde é exibida.

   **Tipo e escala de preço**: selecione a camada gratuita F1. Essa opção é suficiente para esta demonstração. Veja [camada de preços e de escala](https://azure.microsoft.com/pricing/details/iot-hub/).

   **Grupo de recursos**: criar um grupo de recursos para hospedar o hub IoT ou usar um existente. Veja [Uso dos grupos de recursos para gerenciar seus recursos do Azure](../articles/azure-resource-manager/resource-group-portal.md).

   **Local**: selecione o local mais próximo a você onde o hub IoT é criado.

   **Fixe o painel**: marque esta opção para facilitar o acesso ao seu hub IoT do painel.

   ![Preencher os campos para a criação de seu Hub IoT do Azure](../articles/iot-hub/media/iot-hub-create-hub-and-device/2_fill-in-fields-for-azure-iot-hub-portal.png)

1. Clique em **Criar**. Pode levar alguns minutos para que o hub IoT seja criado. Você pode ver o progresso no **notificações** painel.

   ![Consultar as notificações de seu progresso de criação do Hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/3_notification-azure-iot-hub-creation-progress-portal.png)

1. Depois de criar seu hub IoT, clique no painel. Anote o **Nome do host** e clique em **Políticas de acesso compartilhado**.

   ![Obter nome de host do seu Hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. No painel **Políticas de acesso compartilhado**, clique na política **iothubowner**, em seguida, copie e anote a **Cadeia de caracteres de conexão** de seu Hub IoT. Para saber mais, veja [Controlar o acesso ao Hub IoT](../articles/iot-hub/iot-hub-devguide-security.md).

   ![Obter sua cadeia de conexão do Hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-the-your-device"></a>Registrar um dispositivo no Hub IoT para o dispositivo

1. No [portal do Azure](https://portal.azure.com/), abra o hub IoT.
1. Clique em **Gerenciador de Dispositivos**.
1. No painel Gerenciador de Dispositivo, clique em **Adicionar** para adicionar um dispositivo ao Hub IoT.

   **ID do Dispositivo**: a ID do novo dispositivo.

   **Tipo de Autenticação**: selecione a **Chave Simétrica**.

   **Autogerar Chaves**: marque este campo.

   **Conectar o dispositivo ao Hub IoT**: clique em **Habilitar**.

   ![Adicionar um dispositivo no gerenciador de dispositivos de seu Hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

1. Clique em **Salvar**.
1. Depois do dispositivo ser criado, abra-o no painel **Gerenciador de Dispositivos**.
1. Anote a chave primária da cadeia de conexão.

   ![Obter a cadeia de conexão do dispositivo](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)