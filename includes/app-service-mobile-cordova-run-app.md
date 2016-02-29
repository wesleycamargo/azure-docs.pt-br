
1. Visite o [Portal do Azure]. Clique em **Procurar Tudo** > **Aplicativos Móveis** > o back-end que você acabou de criar. Nas configurações do aplicativo móvel, clique em **Início Rápido** > **Cordova**. Em **Configurar o aplicativo cliente**, selecione **Criar um Novo Aplicativo** e clique em **Baixar**. Isso baixa um projeto completo do Cordova para um aplicativo pré-configurado, para conexão com o back-end.

2. Descompacte o arquivo ZIP baixado em um diretório no disco rígido.

3. Abra o projeto usando o **Visual Studio**. Clique em **Abrir** > **Projeto/solução...**.

4. Encontre o arquivo _sitename_.sln e clique em **Abrir**.

5. O emulador padrão é **Rippler - Nexus (Galaxy)**. Clique na seta suspensa ao lado do emulador e selecione **Emulador Android da Google**.

6. Clique em **Emulador Google Android**. O projeto será criado e executado. Você pode ver um aviso de segurança de rede do emulador Google Android que solicitou o acesso à rede. Finalmente, verá que o emulador Google Android será exibido e o aplicativo será executado.

7. No aplicativo, digite um texto significativo, como _Concluir o tutorial_ e depois clique no botão Adicionar. Isso envia uma solicitação POST para o back-end do Azure implantado anteriormente. Os back-end insere dados da solicitação na tabela TodoItem SQL e retorna informações sobre os itens armazenados recentemente no aplicativo móvel. O aplicativo móvel exibe esses dados na lista.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)

[Portal do Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0218_2016-->