
* Entrar na conta do Azure inserindo suas credenciais.
  
  Esse método é mais rápido e fácil, mas se você usá-lo, não será capaz de ver o Banco de Dados SQL ou os Serviços Móveis do Azure na janela **Gerenciador de Servidores**.
  
  No **Gerenciador de Servidores**, clique no botão **Conectar ao Azure**. Uma alternativa é clicar com o botão direito do mouse no nó **Azure** e clicar em **Conectar ao Azure** no menu de contexto.
* Instale um certificado de gerenciamento que permita o acesso à sua conta.
  
  No **Gerenciador de Servidores**, clique com o botão direito do mouse no nó **Azure** e clique em **Gerenciar Assinaturas** no menu de contexto. Na caixa de diálogo **Gerenciar Assinaturas do Azure**, clique na guia **Certificados** e em **Importar**. Siga as instruções para baixar e importe um arquivo de assinatura (também chamado de arquivo *.publishsettings*) para a conta do Azure.
  
  > [!NOTE]
  > Baixe o arquivo de assinatura em uma pasta fora de seus diretórios de código-fonte (por exemplo, na pasta Downloads) e exclua-o após a conclusão da importação. Um usuário mal-intencionado que consiga acesso ao arquivo de assinatura pode editar, criar e excluir os serviços do Azure.
  > 
  > 
  
  Para obter mais informações, consulte [Como se conectar ao Azure no Visual Studio](http://go.microsoft.com/fwlink/?LinkId=324796).

<!---HONumber=Oct15_HO3-->