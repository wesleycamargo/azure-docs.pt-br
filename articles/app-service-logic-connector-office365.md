<properties 
   pageTitle="Conector do Office 365" 
   description="Como usar o Conector do Office 365" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="sutalasi" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Usando o Conector do Office 365 em seu aplicativo lógico#

Aplicativos lógicos podem ser disparados com base em diversas fontes de dados e oferecem conectores para obter e processar dados como parte do fluxo.  O Conector do Office 365 permite enviar e receber emails, gerenciar calendários e contatos em sua conta do Office 365.  Você pode executar várias ações, como enviar, receber e obter emails, criar e excluir eventos de seu calendário e criar, atualizar, obter e excluir seus contatos.

**Ações básicas**

- Novos Emails (Gatilho)
- Enviar Email
- Responder Email
- Enviar Evento
- Adicionar Contato


## Criar um aplicativo lógico##
Para usar o Conector do Office 365 em seu aplicativo lógico, o aplicativo de API do conector já deve ter sido criado ou você pode criá-lo rapidamente na seção de aplicativos lógicos.  Vamos criar um aplicativo lógico simples que é disparado quando um email é recebido (em sua ID de email de consulta de vendas - digamos, sales@contoso.com).  Ele cria um evento, adiciona um contato com os detalhes do remetente, envia um email à sua conta pessoal e, por fim, envia uma resposta com um reconhecimento.

1.	Faça logon no Portal do Azure e clique em "Novo -> Web + Móvel -> Aplicativo Lógico"

	![][1]

2.	Na página "Criar aplicativo lógico", forneça os detalhes necessários, como nome, plano de serviço de aplicativo e local.

	![][2]

3.	Clique em "Gatilhos e Ações" e a tela do editor de Aplicativos Lógicos aparecerá.

	![][3]

4.	Expanda "Criar Novo" na Galeria para ver todos os aplicativos de API disponíveis.  Selecione "Office 365" na galeria e o "Gatilho do Office 365" será adicionado ao fluxo.
	![][4]

5.	Isso cria um novo aplicativo de API do "Conector do Office 365" no mesmo grupo de recursos que o aplicativo lógico.  Serão necessários cerca de 30 segundos para que o aplicativo de API seja criado.

6.	Após a criação do aplicativo, clique em "Autorizar" para fornecer credenciais de logon do Office 365.

	![][5]

7.	Você será redirecionado à página de logon do Office 365 e poderá ser autenticado com suas credenciais de conta do Office 365.

	![][6]

	![][7]

8.	Quando a autorização for concluída, gatilhos do Office 365 serão exibidos.

	![][8]

9.	Selecione o gatilho "Nova Mensagem" e os parâmetros de entrada serão exibidos.


10.	Mude a frequência do gatilho para 'Minutes' e clique em ✓. 

	![][9]

11. O gatilho 'New Email' do Office 365 é configurado e você pode ver que os parâmetros de saída também são exibidos.
	
	![][10]

12.	Selecione "Conector do Office 365" na seção "Usados Recentemente" da galeria e uma nova ação do "Office 365" é adicionada.

13.	Selecione "Enviar Evento" na lista de ações e os parâmetros de entrada da ação "Enviar Evento" serão exibidos.

	![][11]

14.	Especifique os detalhes do evento e clique em ✓.

	![][12]

15.	Selecione "Conector do Office 365" na seção "Usados Recentemente" da galeria e uma nova ação do "Office 365" é adicionada.

16.	Selecione "Adicionar Contato" na lista de ações e os parâmetros de entrada da ação "Adicionar Contato" serão exibidos.

	![][13]

17.	Clique em "+" ao lado do campo 'Email Address' e selecione o valor 'From' do campo de saída do gatilho.

	![][14]

18. Clique em ✓ e a configuração da ação será concluída.

	![][15]

19.	Selecione "Conector do Office 365" na seção "Usados Recentemente" da galeria e uma nova ação do "Office 365" é adicionada.


20.	Selecione "Enviar Email" na lista de ações e os parâmetros de entrada da ação 'Enviar Email' serão exibidos.

	![][19]

21.	Forneça os detalhes necessários para enviar o email.  Você pode criar uma mensagem digitando algo como o que está abaixo.  Uma vez que a ação 'Send Email' estiver configurada, clique em ✓.

		Body - @concat('You got a new sales enquiry from',triggers().output.body.From)

	![][20]
22.	Selecione "Conector do Office 365" na seção "Usados Recentemente" da galeria e uma nova ação do "Office 365" é adicionada.


23.	Selecione "Responder" na lista de ações e os parâmetros de entrada da ação "Responder" serão exibidos.

	![][16]

24.	Clique em "+" ao lado do campo 'From' e selecione o valor da ID da mensagem de saída do gatilho e clique em ✓.

	![][17]

25. Clique em OK na tela do editor de aplicativo lógico e clique em 'Create'.  Serão necessários cerca de 30 segundos para a conclusão da criação.

26. Envie um email para a conta com que você configurou o gatilho e você verá um email na sua conta de email pessoal e um evento de calendário e contato em sua conta de email profissional.  Além disso, você deve obter uma resposta confirmando que a consulte de venda será respondida em breve.

<!--Image references-->
[1]: ./media/app-service-logic-connector-office365/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-office365/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-office365/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-office365/4_Select_Office365_Gallery.png
[5]: ./media/app-service-logic-connector-office365/5_Office365_Authorize.png
[6]: ./media/app-service-logic-connector-office365/6_Office365_Login.png
[7]: ./media/app-service-logic-connector-office365/7_Office365_User_Consent.png
[8]: ./media/app-service-logic-connector-office365/8_Office365_Trigger.png
[9]: ./media/app-service-logic-connector-office365/9_Office365_Trigger_Settings.png
[10]: ./media/app-service-logic-connector-office365/10_Office365_Trigger_Configured.png
[11]: ./media/app-service-logic-connector-office365/11_Office365_Actions_List.png
[12]: ./media/app-service-logic-connector-office365/12_Office365_Create_Event_Inputs.png
[13]: ./media/app-service-logic-connector-office365/13_Office365_Add_Contact_Inputs.png
[14]: ./media/app-service-logic-connector-office365/14_Office365_Add_Contact_Email_FromTrigger.png
[15]: ./media/app-service-logic-connector-office365/15_Office365_Add_Contacts_Configured.png
[16]: ./media/app-service-logic-connector-office365/16_Office365_Reply_To_Inputs.png
[17]: ./media/app-service-logic-connector-office365/17_Office365_Reply_To_MessageId.png
[18]: ./media/app-service-logic-connector-office365/18_Office365_Reply_To_Configured.png
[19]: ./media/app-service-logic-connector-office365/19_Office365_Send_Inputs.png
[20]: ./media/app-service-logic-connector-office365/20_Office365_Send_Configured.png


<!--HONumber=49-->