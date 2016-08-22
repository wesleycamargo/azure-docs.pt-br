Veja como usar o gatilho **Barramento de Serviço - quando uma mensagem é recebida em uma fila** para iniciar um fluxo de trabalho do aplicativo lógico quando um novo item for enviado para uma fila do Barramento de Serviço.

>[AZURE.NOTE]Será solicitado que você entre com sua cadeia de conexão do Barramento de Serviço se você ainda não tiver criado uma conexão com o Barramento de Serviço.

1. Digite *barramento de serviço* na caixa de pesquisa no designer de aplicativos lógicos e selecione o gatilho **Barramento de Serviço - Quando uma mensagem é recebida em uma fila**.  
![Imagem do gatilho do Barramento de Serviço 1](./media/connectors-create-api-servicebus/trigger-1.png)   
- O controle **Quando uma mensagem é recebida em uma fila** é exibido.  
![Imagem do gatilho do Barramento de Serviço 2](./media/connectors-create-api-servicebus/trigger-2.png)   
- Insira o nome da fila de Barramento de Serviço que você deseja que o gatilho monitore.  
![Imagem do gatilho do Barramento de Serviço 3](./media/connectors-create-api-servicebus/trigger-3.png)   

Seu aplicativo lógico foi configurado com um gatilho. Quando um novo item é recebido na fila selecionada, o gatilho inicia uma execução de outros disparadores e ações no fluxo de trabalho.

<!---HONumber=AcomDC_0810_2016-->