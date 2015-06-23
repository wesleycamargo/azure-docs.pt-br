O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue até o local onde você salvou os arquivos compactados do projeto e expanda os arquivos em seu computador no diretório de projetos do Android Studio.

2. Abra o Android Studio. Se você estiver trabalhando com um projeto e ele for exibido, feche o projeto (Arquivo => Fechar Projeto).

3. Selecione **Abrir um projeto existente do Android Studio**, navegue até o local do projeto e, em seguida, clique em **OK**.

 	![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. Na janela esquerda do **Gerenciador de Projetos**, certifique-se de que a guia *Projeto* está selecionada, abra o **aplicativo**, **src**, **java** e clique duas vezes em **ToDoactivity**,

   	![](./media/mobile-services-android-get-started/Android-Studio-quickstart.png)


5. Se você baixou a versão 2.0 do SDK, você precisa atualizar o código com a Url e a chave do seu serviço móvel:
	- 	Encontre o método **OnCreate** em **TodoActivity.java** e localize o código que instancia o cliente de serviços móveis. O código está visível na imagem anterior.
	- 	Substitua "MobileServiceUrl" pela verdadeira Url do serviço móvel.
	- 	Substitua "AppKey" pela chave do serviço móvel.
	- 	Para obter mais detalhes, consulte o tutorial [Adicionar Serviços Móveis a um aplicativo existente](../articles/mobile-services/mobile-services-android-get-started-data.md). 

6. No menu **Executar**, clique em **Executar** para iniciar o projeto no emulador Android.

	> [AZURE.IMPORTANT]Para que seja possível executar o projeto no emulador do Android, você deve definir no mínimo um AVD (Dispositivo Virtual Android). Use o Gerenciador do AVD para criar e gerenciar esses dispositivos.

7. No aplicativo, digite um texto significativo, como _Concluir o tutorial_ e clique em **Adicionar**.

   	![][10]

   	Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

	> [AZURE.NOTE]Você pode examinar o código que acessa o serviço móvel para consultar e inserir dados, que estão localizados no arquivo ToDoActivity.java.

8. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

   	![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

   	Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

   	![](./media/mobile-services-android-get-started/mobile-data-browse.png)


<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-android-get-started/Android-Studio-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/android-studio-import-project.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Add Mobile Services to an existing app]: ../articles/mobile-services/mobile-services-android-get-started-data.md
[Get started with authentication]: ../articles/mobile-services/mobile-services-android-get-started-users.md
[Get started with push notifications]: ../articles/mobile-services/mobile-services-javascript-backend-android-get-started-push.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/
<!--HONumber=54-->