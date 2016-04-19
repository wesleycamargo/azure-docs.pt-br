## Crie um hub IoT

Você precisa criar um hub IoT ao qual o dispositivo simulado se conectará. As etapas a seguir mostram como concluir esta tarefa usando o portal do Azure.

1. Entre no [Portal do Azure][lnk-portal].

2. Na barra de navegação, clique em **Novo**, em seguida em **Internet das Coisas** e em **Hub IoT do Azure**.

    ![][1]

3. Na folha **Hub IoT**, escolha a configuração para o hub IoT.

    ![][2]

    * Na caixa **Nome**, insira um nome para identificar seu hub IoT. Se o **Nome** for válido e se estiver disponível, aparecerá uma marca de seleção verde na caixa **Nome**.
    * Selecione um [Tipo de preço e de dimensionamento][lnk-pricing]. Este tutorial não requer uma camada específica. Para este tutorial, é recomendável que você use a camada F1 livre.
    * No **Grupo de recursos**, crie um novo grupo de recursos ou selecione um existente. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure][lnk-resource-groups].
    * Em **Local**, selecione o local para hospedar o hub IoT. Para este tutorial, é recomendável que você escolha o local mais próximo.

4. Quando você tiver escolhido as opções de configuração do hub IoT, clique em **Criar**. O Azure poderá demorar alguns minutos para criar seu hub IoT. Para verificar o status, você pode monitorar o progresso no quadro inicial ou no painel Notificações.

    ![][3]

5. Quando o Hub IoT tiver sido criado com êxito, clique no novo bloco para o Hub IoT no portal para abrir a folha para o novo Hub IoT. Anote o **Nome do host** e, em seguida, clique no ícone **Chaves**.

    ![][4]

6. Na folha **Política de acesso compartilhado**, clique na política **iothubowner**, então, copie e anote a cadeia de conexão na folha **iothubowner**. Consulte o [Controle de acesso][lnk-access-control] no "Guia do desenvolvedor do Hub IoT do Azure" para obter mais informações.

    ![][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide.md#accesscontrol

<!---HONumber=AcomDC_0413_2016-->