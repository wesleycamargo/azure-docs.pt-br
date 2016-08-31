1. Faça logon no [portal do Azure][].

2. No painel de navegação esquerdo do portal, clique em **Novo**, depois em **Integração Corporativa** e em **Barramento de Serviço**.

4. Na caixa de diálogo **Criar um namespace**, digite um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível.

5. Depois de verificar se o nome do namespace está disponível, escolha o tipo de preço (Básico, Standard ou Premium).

7. No campo **Assinatura**, escolha uma assinatura do Azure na qual criar o namespace.

9. No campo **Grupo de Recursos**, escolha um grupo de recursos existente no qual o namespace residirá, ou então crie um novo.

8. Em **Local**, escolha o país ou região no qual o namespace deve ser hospedado.

	![Criar um namespace][create-namespace]

6. Selecione o botão **Criar**. Agora, o sistema cria o seu namespace e o habilita. Talvez você precise aguardar vários minutos, conforme o sistema fornece recursos para sua conta.
 
### Obter as credenciais de gerenciamento

1. Na lista de namespaces, clique no nome do namespace recém-criado.
 
3. Na folha **Namespace do Barramento de Serviço**, clique em **Políticas de acesso compartilhado**.

4. Na folha **Políticas de acesso compartilhado**, clique em **RootManageSharedAccessKey**.

	![connection-info][connection-info]

5. Na folha **Política: RootManageSharedAccessKey**, clique no botão copiar ao lado da **Chave primária da cadeia de conexão** para copiar a cadeia de conexão na área de transferência para uso posterior.

	![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[portal do Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0824_2016-->