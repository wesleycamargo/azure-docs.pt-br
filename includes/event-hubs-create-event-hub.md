## Criar um Hub de Evento

1. Faça logon no [portal clássico do Azure][] e clique em **NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativos**, em seguida, **Barramento de Serviço**, em seguida, **Hub de eventos** e, em seguida, **Criação rápida**.

	![][1]

3. Digite um nome para o Hub de Evento, selecione a região desejada e clique em **Criar um novo Hub de Evento**.

	![][2]

4. Se você não selecionou explicitamente um namespace existente em uma determinada região, o portal criará um para você (geralmente, ***o nome do hub de eventos*-ns**). Clique no namespace (neste exemplo, **eventhub-ns**).

	![][3]

5. Na parte inferior da página, clique em **Informações da Conexão**. Clique no botão de cópia (mostrado na figura a seguir) para copiar a cadeia de conexão **RootManageSharedAccessKey** na área de transferência. Salve essa cadeia de conexão para usá-la posteriormente no tutorial.

	![][4]

Seu Hub de Evento foi criado, e você tem as cadeias de conexão que precisa para enviar e receber eventos. 

[1]: ./media/event-hubs-create-event-hub/create-event-hub1.png
[2]: ./media/event-hubs-create-event-hub/create-event-hub2.png
[3]: ./media/event-hubs-create-event-hub/create-event-hub3.png
[4]: ./media/event-hubs-create-event-hub/create-conn-str1.png


