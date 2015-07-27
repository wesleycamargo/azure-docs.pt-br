1. Abra o Gerenciador de SDK do Android clicando no ícone na barra de ferramentas do Android Studio ou clicando em **Ferramentas** -> **Android** -> **Gerenciador de SDK** no menu. Localize a versão de destino do SDK do Android que é usada em seu projeto, abra-o e escolha **APIs do Google**, se ainda não estiver instalado.

2. Role até **Extras**, expanda-o e escolha **Google Play Services**, conforme mostrado abaixo. Clique em **Instalar Pacotes**. Anote o caminho do SDK a ser usado na etapa a seguir.

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Abra o arquivo **build.gradle** no diretório do aplicativo.

	![](./media/mobile-services-android-get-started-push/android-studio-push-build-gradle.png)

4. Adicione esta linha em *dependências*:

   		compile 'com.google.android.gms:play-services-base:6.5.87'

5. Em *defaultConfig*, alterar *minSdkVersion* para 9.
 
6. Clique no botão **Sincronizar projetos com arquivos do Gradle** na barra de ferramentas.

7. Abra **Androidmanifest** e adicione esta marcação para a marca do *aplicativo*.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 

<!---HONumber=July15_HO3-->