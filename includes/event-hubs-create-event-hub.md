## Criar um Hub de Evento

1. Faça logon no [portal do Azure][] e clique em **Novo** na parte superior esquerda da tela.

2. Clique em **Dados + Análise** e clique em **Hubs de Eventos**.

	![][9]

3. Na folha **Criar um namespace**, insira um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível.

	![][1]

4. Depois de verificar se o nome do namespace está disponível, escolha o tipo de preço (Básico ou Standard). Além disso, escolha uma assinatura do Azure, o grupo de recursos e o local no qual o recurso será criado.

2. Clique em **Criar** para criar o namespace.

6. Na lista de namespaces do Hubs de Eventos, clique no namespace recém-criado.

	![][2]

7. Na folha do namespace, clique em **Hubs de Eventos**.

	![][3]

8. Na parte superior da folha, clique em **Adicionar Hub de Eventos**.

	![][4]

3. Digite um nome para seu Hub de Eventos e clique em **Criar**.

	![][5]

4. Na lista de Hubs de Eventos, clique no nome do Hub de Eventos recém-criado.

	![][6]

5. Novamente na folha do namespace (e não na folha do Hub de Eventos específico), clique em **Políticas de acesso compartilhado** e clique em **RootManageSharedAccessKey**.

	![][7]

5. Clique no botão de cópia para copiar a cadeia de conexão **RootManageSharedAccessKey** na área de transferência. Salve essa cadeia de conexão para usá-la posteriormente no tutorial.

	![][8]

Seu Hub de Evento foi criado, e você tem as cadeias de conexão que precisa para enviar e receber eventos.

[1]: ./media/event-hubs-create-event-hub/create-event-hub1.png
[2]: ./media/event-hubs-create-event-hub/create-event-hub2.png
[3]: ./media/event-hubs-create-event-hub/create-event-hub3.png
[4]: ./media/event-hubs-create-event-hub/create-event-hub4.png
[5]: ./media/event-hubs-create-event-hub/create-event-hub5.png
[6]: ./media/event-hubs-create-event-hub/create-event-hub6.png
[7]: ./media/event-hubs-create-event-hub/create-event-hub7.png
[8]: ./media/event-hubs-create-event-hub/create-event-hub8.png
[9]: ./media/event-hubs-create-event-hub/create-event-hub9.png

[portal do Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0907_2016-->