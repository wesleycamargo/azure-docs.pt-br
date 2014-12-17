1.No arquivo insert.js para a tabela **canais**, localize as seguintes linhas de código, comente-as ou remova-as do arquivo, e salve as alterações.

		sendNotifications(item.channelUri);

		function sendNotifications(uri) {
		    console.log("Uri: ", uri);
		    push.wns.sendToastText01(uri, {
		        text1: "Sample toast from sample insert"
		    }, {
		        success: function (pushResponse) {
		            console.log("Sent push:", pushResponse);
		        }
		    });
		}
		
	Quando você salva as alterações para o arquivo insert.js, uma nova versão do script é carregada para o serviço móvel.

2. No Gerenciador de Servidores, expanda a tabela TodoItem, abra o arquivo insert.js e substitua a função atual de inserção com o seguinte código, e salve as alterações: 

		function insert(item, user, request) {
			request.execute({
				success: function() {
					request.respond();
					sendNotifications();
				}
			});
		
			function sendNotifications() {
				var channelsTable = tables.getTable('channels');
				channelsTable.read({
					success: function(devices) {
						devices.forEach(function(device) {
							push.wns.sendToastText04(device.channelUri, {
								text1: item.text
							}, {
								success: function(pushResponse) {
									console.log("Sent push:", pushResponse);
								}
							});
						});
					}
				});
			}
		}
		
	Agora, quando você insere uma nova TodoItem, uma notificação por push é enviada para todos os dispositivos registrados. 
