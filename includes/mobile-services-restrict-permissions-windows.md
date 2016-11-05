
1. No Gerenciador de Servidores no Visual Studio, expanda o nó do **Azure**, **Serviços Móveis**, e o seu serviço móvel.
2. Com o botão direito do mouse na tabela **TodoItem**, clique em **Editar Permissões**, defina todas as permissões para **Somente usuários autenticados**, e em seguida, clique em **Aplicar**. Isso garante que todas as operações em relação a tabela *TodoItem* exigem um usuário autenticado.
3. Clique com o botão direito do mouse no projeto de aplicativo do cliente, clique em **Depurar**, em seguida, **Iniciar nova instância**. Verifique se uma exceção não tratada com código de status de 401 (Não autorizado) é acionada depois de iniciar o aplicativo.
   
    Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

<!---HONumber=Oct15_HO3-->