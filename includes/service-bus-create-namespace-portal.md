1. Faça logon no portal clássico do Azure.

2. No painel de navegação esquerdo do portal, clique em **Barramento de Serviço**.

3. No painel inferior do portal, clique em **Criar**.

    ![Selecionar Criar][1]
   
4. No diálogo **Adicionar um novo namespace**, digite um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível.

    ![Nome do namespace][2]
  
5. Depois de verificar se o nome do namespace está disponível, escolha o país ou a região na qual o namespace deve ser hospedado.

6. Deixe os outros campos na caixa de diálogo com seus valores padrão (**Mensagens** e **Camada padrão**), em seguida, clique na marca de seleção OK. Agora, o sistema cria o seu namespace e o habilita. Talvez você precise aguardar vários minutos, enquanto o sistema provisiona recursos para sua conta.
 
    ![Criado com êxito][3]

###Obter as credenciais
1. No painel de navegação esquerdo, clique no nó **Barramento de Serviço** para exibir a lista de namespaces disponíveis:
 
    ![Selecionar barramento de serviço][4]
  
2. Selecione o namespace que você acabou de criar na lista abaixo:
 
    ![Selecionar namespace][5]
 
3. Clique em **Informações de Conexão**.

    ![Informações da conexão][6]
  
4. No painel **Acessar as informações de conexão**, encontre a cadeia de conexão que contém a chave SAS e o nome da chave.

    ![Acessar informações de conexão][7]
  
5. Anote a chave ou copie-a na área de transferência.

<!--Image references-->

[1]: ./media/service-bus-create-namespace-portal/select-create.png
[2]: ./media/service-bus-create-namespace-portal/namespace-name.png
[3]: ./media/service-bus-create-namespace-portal/created-successfully.png
[4]: ./media/service-bus-create-namespace-portal/select-service-bus.png
[5]: ./media/service-bus-create-namespace-portal/select-namespace.png
[6]: ./media/service-bus-create-namespace-portal/connection-information.png
[7]: ./media/service-bus-create-namespace-portal/access-connection-information.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0608_2016-->