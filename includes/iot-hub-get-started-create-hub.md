## <a name="create-an-iot-hub"></a>Crie um hub IoT
Crie um hub IoT ao qual o dispositivo simulado se conectará. As etapas a seguir mostram como concluir esta tarefa usando o portal do Azure.

1. Entre no [Portal do Azure][lnk-portal].
2. Na Barra de Navegação, clique em **Novo** > **Internet das Coisas** > **Hub IoT do Azure**.
   
    ![Barra de Navegação do portal do Azure][1]
3. Na folha **Hub IoT** , escolha a configuração para o hub IoT.
   
    ![Folha Hub IoT][2]
   
   * Na caixa **Nome** , insira um nome para identificar seu hub IoT. Se o **Nome** for válido e estiver disponível, aparecerá uma marca de seleção verde na caixa **Nome**.
   * Selecione um [tipo de preço e de dimensionamento][lnk-pricing]. Este tutorial não requer uma camada específica. Para este tutorial, use a camada F1 gratuita.
   * No **Grupo de recursos**, crie um novo grupo de recursos ou selecione um existente. Para obter mais informações sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure][lnk-resource-groups].
   * Em **Local**, selecione o local para hospedar o hub IoT. Para este tutorial, escolha o local mais próximo.
4. Quando você tiver escolhido as opções de configuração do hub IoT, clique em **Criar**.  O Azure poderá demorar alguns minutos para criar seu hub IoT. Para verificar o status, você pode monitorar o progresso no quadro inicial ou no painel Notificações.
   
    ![Novo status do Hub IoT do Azure][3]
5. Quando o Hub IoT tiver sido criado com êxito, clique no novo bloco para o Hub IoT no Portal do Azure para abrir a folha do novo Hub IoT. Anote o **Nome do host** e clique em **Políticas de acesso compartilhado**.
   
    ![Nova folha Hub IoT][4]
6. Na folha **Políticas de acesso compartilhado**, clique na política **iothubowner** e copie e anote a cadeia de conexão na folha **iothubowner**. Para saber mais, confira [Controle de acesso][lnk-access-control] no "Guia do desenvolvedor do Hub IoT do Azure".
   
    ![Folha Políticas de acesso compartilhado][5]

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
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md


<!--HONumber=Nov16_HO2-->


