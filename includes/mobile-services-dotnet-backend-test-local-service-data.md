Nesta seção você usará o Visual Studio para hospedar o aplicativo e o serviço móvel localmente em sua estação de trabalho de desenvolvimento no IIS Express. Em seguida, você testará o aplicativo e o serviço de back-end.


1. No Visual Studio, pressione a tecla F7 ou clique em **Compilar solução** no menu **Compilar** para compilar o aplicativo da Windows Store e o serviço móvel. Verifique se os dois projetos são compilados sem erros na janela de saída do Visual Studio

2. No Visual Studio, pressione a tecla F5 ou clique em **Iniciar Depuração** no menu **Depurar** para executar o aplicativo e hospedar o serviço móvel localmente no IIS Express.

 
3. Digite um novo texto de todoitem. Em seguida, clique em **Salvar**. Isso insere um novo todoItem no banco de dados criado pelo serviço móvel hospedado localmente no IIS Express.

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/new-local-todoitem.png)

4. Clique na caixa de seleção de um dos itens para marcá-lo como concluído.

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/local-item-checked.png)

5. No Visual Studio, você pode exibir as alterações no banco de dados criado para o serviço de back-end abrindo o Gerenciador de Servidores e expandindo as Conexões de Dados. Clique com o botão direito do mpuse na tabela TodoItems em **MS\_TableConnectionString** e clique em **Mostrar Dados da Tabela**

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/vs-show-local-table-data.png)

<!---HONumber=August15_HO6-->