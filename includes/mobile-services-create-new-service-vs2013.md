As etapas a seguir criam um novo serviço móvel no Azure e adicionam o código ao seu projeto que permite o acesso a esse novo serviço. Para criar o serviço móvel, importe o arquivo publishsettings da sua assinatura do Azure no Visual Studio. Isso permite que o Visual Studio se conecte ao Azure em seu nome. Ao criar um novo serviço móvel, especifique um Banco de Dados SQL do Azure que será usado pelo serviço móvel para armazenar dados do aplicativo.

1.  No Visual Studio 2013, abra o Gerenciador de Soluções, clique com o botão direito no projeto, clique em **Adicionar** e, em seguida, em **Serviço Conectado**.

    ![adicionar serviço conectado][adicionar serviço conectado]

2.  No diálogo Gerenciador de Serviços, clique em **Criar serviço...**, selecione **\<Importar...\>** em **Assinatura** no diálogo Criar Serviço Móvel.

    ![criar um novo serviço móvel do VS 2013][criar um novo serviço móvel do VS 2013]

3.  Em Importar Assinaturas do Azure, clique em **Baixar arquivo de assinatura**, faça logon em sua conta do Azure (se necessário) e clique em **Salvar** quando o navegador solicitar que você salve o arquivo.

    ![baixe o arquivo de inscrição no VS][baixe o arquivo de inscrição no VS]

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>A janela de logon &eacute; exibida no navegador, que pode estar por tr&aacute;s da janela do Visual Studio. Lembre-se de anotar onde salvou o arquivo. publishsettings baixado. Voc&ecirc; poder&aacute; ignorar esta etapa se o seu projeto j&aacute; estiver conectado &agrave; sua assinatura do Azure.</p></div>

4.  Clique em **Procurar**, navegue para o local em que salvou o arquivo .publishetings, selecione o arquivo, clique em **Abrir** e então em **Importar**.

    ![importar assinatura no VS][importar assinatura no VS]

    O Visual Studio importa os dados necessários para conectar-se à sua assinatura do Azure. Quando sua assinatura já tem um ou mais serviços móveis existentes, são exibidos os nomes de serviço.

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o de seguran&ccedil;a</strong> <p>Depois de importar as configura&ccedil;&otilde;es de publica&ccedil;&atilde;o, considere a possibilidade de excluir o arquivo .publishSettings baixado, pois ele cont&eacute;m informa&ccedil;&otilde;es que podem ser usadas por outras pessoas para acessar sua conta. Proteja o arquivo se voc&ecirc; pretende mant&ecirc;-lo para uso em outros projetos de aplicativo conectado.</p></div>

5.  Na caixa de diálogo **Criar Serviço Móvel**, selecione sua **Assinatura** e a **Região** desejada para o serviço móvel e, em seguida, digite um **Nome** para o seu serviço móvel.

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Nomes de servi&ccedil;o m&oacute;vel devem ser exclusivos. Um X vermelho &eacute; exibido ao lado de <strong>Nome</strong> quando o nome fornecido n&atilde;o est&aacute; dispon&iacute;vel. </p></div>

6.  Em **Banco de Dados**, selecione **\<Criar um Banco de Dados SQL gratuito\>**, forneça o **Nome de usuário do servidor**, a **Senha do servidor** e a **Confirmação de senha do servidor**. Depois, clique em **Criar**.

    ![criar serviço de VS 2013 parte 2][criar serviço de VS 2013 parte 2]

    > [WACOM.NOTE]
    > Como parte deste tutorial, você criará um novo servidor e uma nova instância do Banco de Dados SQL gratuito. Você pode reutilizar este novo banco de dados e administrá-lo como faria com qualquer outra instância de Banco de Dados SQL. Você só pode ter uma instância de banco de dados gratuito. Se você já tiver um banco de dados na mesma região como novo serviço móvel, você poderá escolher o banco de dados existente. Ao escolher um banco de dados existente, certifique-se de que você forneça as credenciais de login corretas. Se você fornecer credenciais de login incorreto, o serviço móvel é criado em um estado não íntegro.

    Depois que o serviço móvel é criado, uma referência à biblioteca de cliente de Serviços Móveis é adicionada ao projeto e o código-fonte do seu projeto é atualizado.

  [adicionar serviço conectado]: ./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png
  [criar um novo serviço móvel do VS 2013]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png
  [baixe o arquivo de inscrição no VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png
  [importar assinatura no VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png
  [criar serviço de VS 2013 parte 2]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png
