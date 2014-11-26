Este tutorial é baseado no [aplicativo GetStartedWithMobileServices][aplicativo GetStartedWithMobileServices], que é um projeto de aplicativo universal do Windows no Visual Studio 2013. A interface de usuário deste aplicativo é idêntica ao aplicativo gerado pelo quickstart dos Serviços Móveis, exceto pelo fato de que os itens adicionados são armazenados localmente na memória.

1.  Baixe a versão do JavaScript do aplicativo de exemplo GetStartedWithMobileServices do [site de exemplos de código do desenvolvedor].

2.  No Visual Studio 2013, abra o projeto baixado, expanda a pasta js no Gerenciador de Soluções e examine o arquivo default.js.

    Observe que os objetos **TodoItem** adicionados estão armazenados em um `WinJS.Binding.List`.

3.  Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

4.  No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

    ![][0]

    Observe que o texto salvo é exibido.

5.  Clique com o botão direito do mouse no projeto Windows Phone 8.1, clique em **Definir como projeto inicial**, em seguida, pressione **F5** para iniciar o aplicativo da Loja do Windows Phone.

    ![][1]

6.  Repita as etapas 3 e 4 para verificar se o exemplo se comporta da mesma maneira.

  [aplicativo GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?LinkID=510826
  [0]: ./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png
  [1]: ./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png
