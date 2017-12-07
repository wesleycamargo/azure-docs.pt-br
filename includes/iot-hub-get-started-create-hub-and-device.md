## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Agora que você criou um hub IoT, localize as informações importantes que você usa para se conectar a dispositivos e aplicativos para o hub IoT. 

1. Depois de criar o hub IoT, clique nele no painel. Anote o **Nome do host** e clique em **Políticas de acesso compartilhado**.

   ![Obter nome de host do seu Hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. No painel **Políticas de acesso compartilhado**, clique na política **iothubowner**, em seguida, copie e anote a **Cadeia de caracteres de conexão** de seu Hub IoT. Para saber mais, veja [Controlar o acesso ao Hub IoT](../articles/iot-hub/iot-hub-devguide-security.md).

> [!NOTE] 
Você não precisará dessa cadeia de conexão iothubowner para este tutorial de configuração. No entanto, talvez você precise dela para alguns tutoriais em cenários de IoT diferentes após a conclusão desta instalação.

   ![Obter sua cadeia de conexão do Hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Registrar um dispositivo no hub IoT para o dispositivo

1. No [portal do Azure](https://portal.azure.com/), abra o hub IoT.

2. Clique em **Gerenciador de Dispositivos**.
3. No painel Gerenciador de Dispositivo, clique em **Adicionar** para adicionar um dispositivo ao Hub IoT. Faremos o seguinte:

   **ID do Dispositivo**: insira a ID do novo dispositivo. As IDs de Dispositivo diferenciam maiúsculas de minúsculas.

   **Tipo de Autenticação**: selecione a **Chave Simétrica**.

   **Gerar Chaves Automaticamente**: marque essa caixa de seleção.

   **Conectar o dispositivo ao Hub IoT**: clique em **Habilitar**.

   ![Adicionar um dispositivo no Device Explorer de seu hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Clique em **Salvar**.
5. Depois do dispositivo ser criado, abra-o no painel **Gerenciador de Dispositivos**.
6. Anote a chave primária da cadeia de conexão.

   ![Obter a cadeia de conexão do dispositivo](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)
