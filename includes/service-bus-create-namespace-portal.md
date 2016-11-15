1. Faça logon no [portal do Azure][portal do Azure].
2. No painel de navegação esquerdo do portal, clique em **Novo**, depois em **Integração Corporativa** e em **Barramento de Serviço**.
3. Na caixa de diálogo **Criar um namespace** , digite um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível.
4. Depois de verificar se o nome do namespace está disponível, escolha o tipo de preço (Básico, Standard ou Premium).
5. No campo **Assinatura** , escolha uma assinatura do Azure na qual criar o namespace.
6. No campo **Grupo de Recursos** , escolha um grupo de recursos existente no qual o namespace residirá, ou então crie um novo.      
7. Em **Localização**, escolha o país ou região no qual o namespace deve ser hospedado.
   
    ![Criar um namespace][create-namespace]
8. Clique em **Criar**. Agora, o sistema cria o seu namespace e o habilita. Talvez você precise aguardar vários minutos, conforme o sistema fornece recursos para sua conta.

### <a name="obtain-the-management-credentials"></a>Obter as credenciais de gerenciamento
1. Na lista de namespaces, clique no nome do namespace recém-criado.
2. Na folha namespace, clique em **Políticas de acesso compartilhado**.
3. Na folha **Políticas de acesso compartilhado**, clique em **RootManageSharedAccessKey**.
   
    ![informações de conexão][connection-info]
4. Na folha **Política: RootManageSharedAccessKey**, clique no botão copiar ao lado da **Chave primária da cadeia de conexão** para copiar a cadeia de conexão na área de transferência para uso posterior. Cole esse valor no Bloco de notas ou em outro local temporário.
   
    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[Portal do Azure]: https://portal.azure.com

<!--HONumber=Nov16_HO2-->


