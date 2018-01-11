1. Entre no [Portal do Azure][Azure portal].
2. No menu esquerdo, selecione **+ Criar um recurso**. Em seguida, selecione **Enterprise Integration** > **Retransmissão**.
3. Em **Criar um namespace**, insira um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível.
4. Na caixa **Assinatura**, selecione uma assinatura do Azure na qual criar o namespace.
5. Na caixa [Grupo de recursos](../articles/azure-resource-manager/resource-group-portal.md), selecione um grupo de recursos existente no qual colocar o namespace, ou então crie um novo.  
6. Em **Localização**, selecione o país ou região no qual o namespace deve ser hospedado.
   
    ![Criar um namespace][create-namespace]
7. Selecione **Criar**. O sistema cria o seu namespace e o habilita. Depois de alguns minutos, o sistema provisiona recursos para sua conta.

### <a name="get-management-credentials"></a>Obter credenciais de gerenciamento

1. Selecione **Todos os recursos** e depois selecione o nome do namespace recém-criado.
2. Em Namespace da Retransmissão, clique em **Políticas de acesso compartilhado**.  
3. Em **Políticas de acesso compartilhado**, selecione **RootManageSharedAccessKey**.
   
    ![informações de conexão][connection-info]
4. Em **política: RootManageSharedAccessKey**, selecione o botão **Cópia** ao lado de **Cadeia de conexão – Chave primária**. Isso copiará a cadeia de conexão para a área de transferência para uso posterior. Cole esse valor no Bloco de notas ou em outro local temporário.
   
    ![connection-string][connection-string]

5. Repita a etapa anterior para copiar e colar o valor de **Chave primária** para um local temporário para uso posterior.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
