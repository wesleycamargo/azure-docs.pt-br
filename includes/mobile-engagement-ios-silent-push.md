> [AZURE.IMPORTANT]Para receber notificações por push do Mobile Engagement, você precisa habilitar `Silent Remote Notifications` em seu aplicativo. Você precisa adicionar o valor remote-notification à matriz UIBackgroundModes no arquivo Info.plist.

1. Abra o arquivo `info.plist` no projeto
2. Clique com o botão direito no item superior na lista (`Information Property List`) e adicione uma nova linha

	![][1]

3. Na nova linha, digite `Required background modes`

	![][2]

4. Clique na seta à esquerda para expandir a linha
5. Adicione o seguinte valor para o item 0 `App downloads content in response to push notifications`

	![][3]

Após a alteração, o XML info.plist deve conter a chave e o valor a seguir:

    <key>UIBackgroundModes</key>
        <array>
            <string>remote-notification</string>
        </array>
    ...

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png
[2]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png
[3]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png

<!---HONumber=August15_HO6-->