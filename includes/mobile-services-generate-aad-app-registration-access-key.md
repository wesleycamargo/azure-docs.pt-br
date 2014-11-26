1.  Clique na guia **Aplicativos** na sua página de diretório no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  Clique no registro integrado do seu aplicativo.

3.  Clique em **Configurar** na página do aplicativo e role para baixo até a seção **chaves** da página.
4.  Clique na duração de **1 ano** para uma nova chave. Clique em **Salvar** e o portal exibirá o valor da sua nova chave.
5.  Copie a **ID do Cliente** e a **Chave** exibidas após você salvar. Observe que o valor da chave só será exibido uma única vez após você salvar.

    ![](./media/mobile-services-generate-aad-app-registration-access-key/client-id-and-key.png)

6.  Role para baixo até a parte inferior da página de configuração do aplicativo integrado e habilite a permissão **Ler dados de diretório** para o aplicativo e clique em **Salvar**.

    ![](./media/mobile-services-generate-aad-app-registration-access-key/app-perms.png)

7.  No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], navegue de volta a seu serviço móvel e clique na guia **Configurar**. Role para baixo até a seção **configurações de aplicativo**, adicione as configurações de aplicativo a seguir e clique em **Salvar**.

    <table border="1">
    <tr>
    <th>Nome da configuração de aplicativo</th><th>Descrição</th>
    </tr>
    <tr>
    <td>AAD_CLIENT_ID</td><td>A ID dp cliente que você copiou de seu aplicativo integrado nos passos acima.</td>
    </tr>
    <tr>
    <td>AAD_CLIENT_KEY</td><td>A chave do aplicativo que você gerou em seu aplicativo integrado do AAD nas etapas acima.</td>
    </tr>
    <tr>
    <td>AAD_TENANT_DOMAIN</td><td>Seu nome de domínio do AAD. Deve ser semelhante a "mydomain.onmicrosoft.com"</td>
    </tr>
    </table><br/>

    ![](./media/mobile-services-generate-aad-app-registration-access-key/aad-app-settings.png)

  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  
