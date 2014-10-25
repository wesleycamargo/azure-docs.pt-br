Finalmente, você deve atualizar o script registrado para a operação de inserção na tabela TodoItem para enviar notificações.

1.  Clique em **TodoItem**, clique em **Script** e selecione **Inserir**.

    ![][]

2.  Substitua a função de inserção com o seguinte código e **Salvar**:

        function insert(item, user, request) {
        // Define a payload for the Windows Store toast notification.
        var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
            '<binding template="ToastText01">  <text id="1">' +
            item.text + '</text></binding></visual></toast>';

        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.wns.send(null, payload, 'wns/toast', {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse);
                        request.respond();
                        },              
                        error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
                            request.respond(500, { error: pushResponse });
                            }
                        });
                    }
                });
        }

    Esse script de inserção envia uma notificação por push (com o texto do item inserido) a todos os registros do aplicativo da Windows Store depois que a inserção é executada com êxito.

  []: ./media/mobile-services-javascript-update-script-notification-hubs/mobile-insert-script-push2.png
