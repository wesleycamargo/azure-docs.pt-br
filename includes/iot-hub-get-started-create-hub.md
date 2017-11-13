## <a name="create-an-iot-hub"></a>Crie um hub IoT
Crie um Hub IoT ao qual o aplicativo de dispositivo simulado poderá se conectar. As etapas a seguir mostram como concluir esta tarefa usando o portal do Azure.

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Agora que você criou um hub IoT, localize as informações importantes que você usa para se conectar a dispositivos e aplicativos para o hub IoT. 

1. Quando o Hub IoT tiver sido criado com êxito, clique no novo bloco para o Hub IoT no Portal do Azure para abrir a janela de propriedades do novo Hub IoT. Anote o **Nome do host** e clique em **Políticas de acesso compartilhado**.
   
    ![Nova janela do Hub IoT][4]
1. Em **Políticas de acesso compartilhado**, clique na política **iothubowner** e copie e anote a cadeia de conexão do Hub IoT na janela **iothubowner**. Para saber mais, confira [Controle de acesso][lnk-access-control] no "Guia do desenvolvedor do Hub IoT".
   
    ![Políticas de acesso compartilhado][5]

<!-- Images. -->
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
