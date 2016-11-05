## Solucionar problemas de conectividade
Use a guia **Diagnósticos** do **Gerenciador de Configuração de Gateway de Gerenciamento de Dados** para solucionar problemas de conexão.

1. Iniciar o **Gerenciador de Configuração de Gateway de Gerenciamento de Dados**. Você pode executar "C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\ConfigManager.exe" diretamente (ou) pesquisar por **Gateway** para encontrar um link para o aplicativo **Gateway de Gerenciamento de Dados da Microsoft**, conforme mostrado na imagem a seguir. 
   
    ![Gateway de pesquisa](./media/data-factory-troubleshoot-connectivity/search-gateway.png)
2. Alterne para a guia **Diagnóstico**.
   
    ![Diagnóstico de gateway](./media/data-factory-troubleshoot-connectivity/data-factory-gateway-diagnostics.png) 
3. Selecione o **tipo** de armazenamento de dados (serviço vinculado). 
4. Especifique a **autenticação** e digite as **credenciais** (ou) digite a **cadeia de conexão** para se conectar ao armazenamento de dados. 
5. Clique em **Testar Conexão** para testar a conexão com o armazenamento de dados. 

<!---HONumber=AcomDC_0420_2016-->