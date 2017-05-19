Agora você pode adicionar dados à sua nova coleção usando o Data Explorer.

1. No Data Explorer, o novo banco de dados aparece no painel Coleções. Expanda o banco de dados **Itens**, expanda a coleção **ToDoList**, clique em **Documentos** e, em seguida, clique em **Novos Documentos**. 

   ![Criar novos documentos no Data Explorer no portal do Azure](./media/cosmosdb-create-sample-data/azure-cosmosdb-data-explorer-emulator-new-document.png)
  
2. Agora adicione alguns novos documentos à coleção com a seguinte estrutura, em que você insere valores exclusivos para a ID em cada documento e altera as outras propriedades como desejar. Os novos documentos podem ter qualquer estrutura que você deseje, pois o BD Cosmos do Azure não impõe nenhum esquema para seus dados.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries."
     }
     ```

     Agora você pode usar consultas no Data Explorer para recuperar os dados. Por padrão, o Data Explorer usa SELECT * FROM c para recuperar todos os documentos na coleção, mas você podem alterar isso para SELECT * FROM c ORDER BY c.name ASC para retornar todos os documentos classificados pela propriedade name, em ordem alfabética crescente. 
 
     Você também pode usar o Data Explorer para criar procedimentos armazenados, UDFs e gatilhos para executar a lógica de negócios do servidor. O Data Explorer expõe todo o acesso a dados interno via programação disponível nas APIs, mas oferece acesso fácil aos dados no Portal do Azure.