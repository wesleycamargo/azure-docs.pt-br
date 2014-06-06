
   * Entre na conta do Azure inserindo suas credenciais.

     Esse método é mais rápido e fácil, mas se você usá-lo, não será capaz de ver o Banco de Dados SQL ou os Serviços Móveis do Azure na janela **Gerenciador de Servidores**.

     Em **Gerenciador de Servidores**, clique no botão **Conectar-se ao Azure**. Uma alternativa é clicar duas vezes no nó **Azure** e clicar em **Conectar-se ao Azure** no menu de contexto.

   * Instale um certificado de gerenciamento que permita o acesso à sua conta.

     No **Gerenciador de Servidores**, clique com o botão direito do mouse no nó **Azure** e clique em **Gerenciar assinaturas** no menu de contexto. Na caixa de diálogo **Gerenciar Assinaturas do Azure**, clique na guia **Certificados** e, em seguida, clique em **Importar**. Siga as instruções para baixar e importar um arquivo de assinatura (também chamado de arquivo *.publishsettings*) para a sua conta do Azure.

     <div class="dev-callout"><strong>Observação de Segurança:</strong>
     <p>Baixe o arquivo de assinatura em uma pasta fora de seus diretórios de código-fonte (por exemplo, na pasta Downloads) e exclua-o após a conclusão da importação. Um usuário mal-intencionado que obtiver acesso ao arquivo de assinatura pode editar, criar e excluir seus serviços do Azure.</p></div>

	Para obter mais informações, consulte [Como conectar-se ao Azure no Visual Studio (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=324796).

