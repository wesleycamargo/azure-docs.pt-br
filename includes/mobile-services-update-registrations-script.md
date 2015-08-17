

1. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida, clique na tabela **Registros**. 

	![](./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png)

2. Em **Registros**, clique na guia **Script** e selecione **Inserir**.
   
	![](./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png)

	Isso exibe a função que é chamada quando ocorre uma inserção na tabela **Registros**.

3. Substitua a função de inserção com o seguinte código e **Salvar**:

		function insert(item, user, request) {
			var registrationTable = tables.getTable('Registrations');
			registrationTable
				.where({ handle: item.handle })
				.read({ success: insertChannelIfNotFound });
	        function insertChannelIfNotFound(existingRegistrations) {
        	    if (existingRegistrations.length > 0) {
            	    request.respond(200, existingRegistrations[0]);
        	    } else {
            	    request.execute();
        	    }
    	    }
	    }

   Isso registra um novo script de inserção, que armazena as informações de registro na nova tabela.

<!---HONumber=August15_HO6-->