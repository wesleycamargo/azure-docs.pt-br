1. Abra o Gerenciador de SDK do Android clicando no ícone na barra de ferramentas do Android Studio ou clicando em **Ferramentas** -> **Android** -> **Gerenciador de SDK** no menu. Localize a versão de destino do SDK do Android que é usada em seu projeto, abra-o e escolha **APIs do Google**, se ainda não estiver instalado.

2. Vá para **Arquivo**, **Estrutura do Projeto**. Em seguida, habilite as Notificações por Push em **Notificações**.

3. Abra **Androidmanifest** e adicione esta marcação para a marca do *aplicativo*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=AcomDC_1203_2015-->