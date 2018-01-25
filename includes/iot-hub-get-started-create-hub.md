## <a name="create-an-iot-hub"></a>Crie um hub IoT
Crie um Hub IoT ao qual o aplicativo de dispositivo simulado poderá se conectar. As etapas a seguir mostram como concluir esta tarefa usando o portal do Azure.

1. Entre no [Portal do Azure][lnk-portal].

1. Selecione **Novo** > **Internet das Coisas** > **Hub IoT**.
   
    ![Barra de Navegação do portal do Azure][1]

1. No painel **Hub IoT**, insira as seguintes informações para o Hub IoT:

   * **Nome**: cria um nome para o hub IoT. Se o nome for válido, uma marca de seleção verde é exibida.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **Tipo de escala e preço**: para este tutorial, selecione a camada **F1 - Gratuita**. Para obter mais informações, confira [Tipo de escala e preço][lnk-pricing].

   * **Grupo de recursos**: criar um grupo de recursos para hospedar o hub IoT ou usar um existente. Para obter mais informações, confira [Usar os grupos de recursos para gerenciar seus recursos do Azure][lnk-resource-groups]

   * **Local**: Selecione o local mais próximo para você.

   * **Fixar no painel**: marque essa opção para facilitar o acesso ao seu hub IoT do painel.

    ![Janela do Hub IoT][2]

1. Clique em **Criar**. A criação do seu hub IoT pode levar alguns minutos. Você pode monitorar o progresso no painel **Notificações**.

1. Quando o Hub IoT tiver sido criado com êxito, clique no novo bloco para o Hub IoT no Portal do Azure para abrir a janela de propriedades do novo Hub IoT. Agora que você criou um hub IoT, localize as informações importantes que você usa para se conectar a dispositivos e aplicativos para o hub IoT. Anote o **Nome do host** e clique em **Políticas de acesso compartilhado**.
   
    ![Nova janela do Hub IoT][4]

1. Em **Políticas de acesso compartilhado**, clique na política **iothubowner** e copie e anote a cadeia de conexão do Hub IoT na janela **iothubowner**. Para saber mais, confira [Controle de acesso][lnk-access-control] no "Guia do desenvolvedor do Hub IoT".
   
    ![Políticas de acesso compartilhado][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
