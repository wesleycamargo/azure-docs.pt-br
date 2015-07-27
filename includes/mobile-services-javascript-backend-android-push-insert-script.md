
1. No Portal de Gerenciamento, clique na guia **Dados** e clique na tabela **TodoItem**. 
 
2. Em **todoitem**, clique na guia **Script** e selecione **Inserir**.
   
   	Isso exibe a função que é chamada quando ocorre uma inserção na tabela **TodoItem**.

3. Substitua a função de inserção com o seguinte código e **Salvar**:

		function insert(item, user, request) {
		// Define a simple payload for a GCM notification.
	    var payload = {
	        "data": {
	            "message": item.text
	        }
	    };		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
		                request.respond();
		                },              
		            error: function (pushResponse) {
		                console.log("Error Sending push:", pushResponse);
		                request.respond(500, { error: pushResponse });
		                }
		            });
		        },
		    error: function(err) {
		        console.log("request.execute error", err)
		        request.respond();
		    }
		  });
		}

   	Isso registra um novo script de inserção, que usa o [objeto gcm](http://go.microsoft.com/fwlink/p/?LinkId=282645) para enviar uma notificação por push a todos os dispositivos registrados depois que a inserção for bem-sucedida.

<!---HONumber=July15_HO3-->