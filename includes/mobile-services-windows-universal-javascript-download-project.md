
Este tutorial se baseia no [aplicativo GetStartedWithMobileServices](http://go.microsoft.com/fwlink/p/?LinkID=510826), que é um projeto de aplicativo Windows universal no Visual Studio 2013. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido dos Serviços Móveis, a não ser pelos os itens adicionados que são armazenados localmente na memória. 

1. Baixe a versão do JavaScript do aplicativo de exemplo GetStartedWithMobileServices do [site de exemplos de código do desenvolvedor]. 

3. No Visual Studio 2013, abra o projeto baixado, expanda a pasta js no Gerenciador de Soluções e examine o arquivo default.js.

   	Observe que os objetos **TodoItem** adicionados estão armazenados em um  `WinJS.Binding.List`.

4. Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

5. No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

   	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png) 

   	Notice that the saved text is displayed.

6. Clique com o botão direito do mouse no projeto Windows Phone 8.1, clique em **Definir como projeto inicial**, em seguida, pressione **F5** para iniciar o aplicativo da Loja do Windows Phone.  

	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png)

7. Repita as etapas 3 e 4 para verificar se o exemplo se comporta da mesma maneira.
\<!--HONumber=42-->
