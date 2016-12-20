## <a name="create-an-event-hub"></a>Criar um Hub de Evento
1. Faça logon no [portal do Azure][portal do Azure] e clique em **Novo** na parte superior esquerda da tela.
2. Clique em **Internet das Coisas** e, em seguida, clique em **Hubs de Eventos**.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub9.png)
3. Na folha **Criar um namespace** , insira um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub1.png)
4. Depois de verificar se o nome do namespace está disponível, escolha o tipo de preço (Básico ou Standard). Além disso, escolha uma assinatura do Azure, o grupo de recursos e o local no qual o recurso será criado. 
5. Clique em **Criar** para criar o namespace.
6. Na lista de namespaces do Hubs de Eventos, clique no namespace recém-criado.      
   
    ![](./media/event-hubs-create-event-hub/create-event-hub2.png)
7. Na folha do namespace, clique em **Hubs de Eventos**.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub3.png)
8. Na parte superior da folha, clique em **Adicionar Hub de Eventos**.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub4.png)
9. Digite um nome para seu Hub de Eventos e clique em **Criar**.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub5.png)
10. Na lista de Hubs de Eventos, clique no nome do Hub de Eventos recém-criado. 
    
     ![](./media/event-hubs-create-event-hub/create-event-hub6.png)
11. Novamente na folha do namespace (e não na folha do Hub de Eventos específico), clique em **Políticas de acesso compartilhado** e clique em **RootManageSharedAccessKey**.
    
     ![](./media/event-hubs-create-event-hub/create-event-hub7.png)
12. Clique no botão de cópia para copiar a cadeia de conexão **RootManageSharedAccessKey** na área de transferência. Salve essa cadeia de conexão para usá-la posteriormente no tutorial.
    
     ![](./media/event-hubs-create-event-hub/create-event-hub8.png)

Seu Hub de Evento foi criado, e você tem as cadeias de conexão que precisa para enviar e receber eventos.

[portal do Azure]: https://portal.azure.com/

<!--HONumber=Nov16_HO4-->


