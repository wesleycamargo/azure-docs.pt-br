1. Faça logon no portal clássico do Azure.

2. No painel de navegação esquerdo do portal, clique em **Barramento de Serviço**.

3. No painel inferior do portal, clique em **Criar**.

    ![Selecionar Criar][select-create]
   
4. No diálogo **Adicionar um novo namespace**, digite um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível.

    ![Nome do namespace][namespace-name]
  
5. Depois de verificar se o nome do namespace está disponível, escolha o país ou a região na qual o namespace deve ser hospedado.

6. Deixe os outros campos na caixa de diálogo com seus valores padrão (**Mensagens** e **Camada padrão**), em seguida, clique na marca de seleção OK. Agora, o sistema cria o seu namespace e o habilita. Talvez você precise aguardar vários minutos, enquanto o sistema provisiona recursos para sua conta.
 
    ![Criado com êxito][created-successfully]

###Obter as credenciais
1. No painel de navegação esquerdo, clique no nó **Barramento de Serviço** para exibir a lista de namespaces disponíveis:
 
    ![Selecionar barramento de serviço][select-service-bus]
  
2. Selecione o namespace que você acabou de criar na lista abaixo:
 
    ![Selecionar namespace][select-namespace]
 
3. Clique em **Informações de Conexão**.

    ![Informações da conexão][connection-information]
  
4. No painel **Acessar as informações de conexão**, encontre a cadeia de conexão que contém a chave SAS e o nome da chave.

    ![Acessar informações de conexão][access-connection-information]
  
5. Anote a chave ou copie-a na área de transferência.

<!--Image references-->

[select-create]: ./media/service-bus-create-namespace-portal/select-create.png
[namespace-name]: ./media/service-bus-create-namespace-portal/namespace-name.png
[created-successfully]: ./media/service-bus-create-namespace-portal/created-successfully.png
[select-service-bus]: ./media/service-bus-create-namespace-portal/select-service-bus.png
[select-namespace]: ./media/service-bus-create-namespace-portal/select-namespace.png
[connection-information]: ./media/service-bus-create-namespace-portal/connection-information.png
[access-connection-information]: ./media/service-bus-create-namespace-portal/access-connection-information.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0615_2016-->