-   Entrar na conta do Azure inserindo suas credenciais.

    Esse método é mais rápido e fácil, mas se você usá-lo, não será capaz de ver o Banco de Dados SQL ou os Serviços Móveis do Azure na janela **Gerenciador de Servidores**.

    No **Gerenciador de Servidores**, clique no botão **Conectar ao Azure**. Uma alternativa é clicar com o botão direito do mouse no nó **Azure** e clicar em **Conectar ao Azure** no menu de contexto.

-   Instale um certificado de gerenciamento que permita o acesso à sua conta.

    No **Gerenciador de Servidores**, clique com o botão direito do mouse no nó **Azure** e clique em **Gerenciar Assinaturas** no menu de contexto. Na caixa de diálogo **Gerenciar Assinaturas do Azure**, clique na guia **Certificados** e em **Importar**. Siga as instruções para baixar e importe um arquivo de assinatura (também chamado de arquivo *.publishsettings*) para a conta do Azure.

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o de Seguran&ccedil;a:</strong><br /> <p>Baixe o arquivo de assinatura em uma pasta fora de seus diret&oacute;rios de c&oacute;digo-fonte (por exemplo, na pasta Downloads) e exclua-o ap&oacute;s a conclus&atilde;o da importa&ccedil;&atilde;o. Um usu&aacute;rio mal-intencionado que consiga acesso ao arquivo de assinatura pode editar, criar e excluir os servi&ccedil;os do Azure.</p></div>

    Para obter mais informações, consulte [Como se conectar ao Azure no Visual Studio][].

  [Como se conectar ao Azure no Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=324796
