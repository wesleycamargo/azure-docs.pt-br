Este exemplo envolve o uso do serviço [Twilio](https://www.twilio.com/) para enviar mensagens SMS a um telefone celular. O Azure Functions já tem suporte para Twilio por meio da [Associação com o Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio) e o exemplo usa esse recurso.

A primeira coisa de que você precisa é uma conta do Twilio. É possível criar uma gratuitamente em https://www.twilio.com/try-twilio. Quando tiver uma conta, adicione as três seguintes **configurações de aplicativo** ao seu aplicativo de função.

| Nome da configuração do aplicativo | Descrição do valor |
| - | - |
| **TwilioAccountSid**  | A SID de sua conta do Twilio |
| **TwilioAuthToken**   | O token de autenticação de sua conta do Twilio |
| **TwilioPhoneNumber** | O número de telefone associado à sua conta do Twilio. Ele é usado para enviar mensagens SMS. |