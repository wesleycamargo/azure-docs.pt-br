

1. Abra o Gerenciador do SDK do Android clicando em **Janela** na barra de ferramentas superior do Eclipse. Localize a versão de destino do SDK do Android especificado nas propriedades do projeto, abra-o e escolha **APIs do Google**.

2. Role até **Extras**, expanda-o e escolha **Google Play Services**, conforme mostrado abaixo. Clique em **Instalar Pacotes**. Anote o caminho do SDK a ser usado na etapa a seguir. Reinicie o Eclipse.

   	![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. Instale o SDK do Google Play Services no projeto. No Eclipse, clique em **Arquivo** e em  **Importar**. Selecione **Android**, em seguida, **Código do Android existente no espaço de trabalho** e clique em **Avançar**. Clique em **Procurar**, navegue até o caminho do SDK do Android (normalmente em uma pasta chamada `adt-bundle-windows-x86_64`) e, em seguida, vá até a subpasta `\extras\google\google_play_services\libproject`, selecione a pasta google-play-services-lib e clique em **OK**. Marque a caixa de seleção **Copiar projetos na área de trabalho** e, em seguida, clique em **Concluir**.

	![](./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png)

5. Em seguida você deve referenciar a biblioteca de SDK do Google Play que você acabou de importar do seu projeto. 

2. No **Gerenciador de Pacotes**, clique com o botão direito do mouse no projeto e escolha *Propriedades*.
 
3. Na janela Propriedades, escolha Android à esquerda.

	![](./media/mobile-services-android-get-started-push/mobile-google-set-project-properties.png)


5.  Na seção **Biblioteca**, escolha **Adicionar**, selecione o projeto Google Play Services (*google-play serviços lib*) e clique em **OK**.

6. Clique em **Aplicar** e **OK**.




