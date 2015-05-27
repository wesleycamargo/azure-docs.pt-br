
Este tutorial se baseia no aplicativo [GetStartedWithMobileServices](http://go.microsoft.com/fwlink/p/?LinkID=510826), que é um projeto de aplicativo do Windows universal no Visual Studio 2013. A interface do usuário desse aplicativo é idêntica a do aplicativo gerado pelo início rápido dos Serviços Móveis, a não ser pelos os itens adicionados que são armazenados localmente na memória.

1. Baixe a versão C# do aplicativo de exemplo GetStartedWithMobileServices do [site de exemplos de código do desenvolvedor]. 

2. No Visual Studio 2013, abra o projeto baixado e examine o arquivo MainPage.xaml.cs encontrado na pasta do projeto GetStartedWithData.Shared.

   	Observe que os objetos **TodoItem** adicionados estão armazenados em um **ObservableCollection&lt;TodoItem&gt;** na memória.

3. Pressione a tecla **F5** para recompilar o projeto e iniciar o aplicativo.

4. No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

   	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png)

   	Observe que o texto salvo é exibido.

5. Clique com o botão direito do mouse no projeto Windows Phone 8.1, clique em **Definir como projeto inicial**, em seguida, pressione **F5** para iniciar o aplicativo da Loja do Windows Phone.

	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png)

6. Repita as etapas 3 e 4 para verificar se o exemplo se comporta da mesma maneira.
<!--HONumber=54-->