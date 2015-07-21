1. Clique na guia **Aplicativos** na página do seu diretório no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/).
  
2. Clique no registro integrado de aplicativos.

3. Clique em **Configurar** na página do aplicativo e role até a seção **chaves** da página.
4. Clique na duração de **1 ano** para uma nova chave. Em seguida, clique em **Salvar** e o portal exibirá o valor da nova chave.
5. Copie a **ID do Cliente** e a **Chave** exibidas após salvar. Observe que o valor-chave será exibido apenas uma vez após o salvamento. 

    ![](./media/mobile-services-generate-aad-app-registration-access-key/client-id-and-key.png)

6. Role até o final da página de configuração do aplicativo integrado e habilite a permissão **Ler dados do diretório** para o aplicativo e clique em **Salvar**.

    ![](./media/mobile-services-generate-aad-app-registration-access-key/app-perms.png)


7. No [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/), navegue até seu serviço móvel e clique na guia **Configurar**. Role até a seção **configurações do aplicativo**, adicione as seguintes configurações do aplicativo e clique em **Salvar**.

    <table border="1">
    <tr> <th>Nome de Configuração do Aplicativo</th><th>Descrição</th>
    </tr>
    <tr> <td>AAD_CLIENT_ID</td><td>A id do cliente que copiou do seu aplicativo integrado nas etapas acima.</td>
    </tr>
    <tr> <td>AAD_CLIENT_KEY</td><td>A chave do aplicativo gerada no aplicativo integrado do AAD nas etapas acima.</td>
    </tr>
    <tr> <td>AAD_TENANT_DOMAIN</td><td>O seu nome de domínio do AAD. Deve ser semelhante a “meudominio.onmicrosoft.com"</td>
    </tr>
    </table><br/>

 
    ![](./media/mobile-services-generate-aad-app-registration-access-key/aad-app-settings.png)
  

<!----HONumber=July15_HO2-->