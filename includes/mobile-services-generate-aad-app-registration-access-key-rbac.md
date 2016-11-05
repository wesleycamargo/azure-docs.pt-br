1. Clique na guia **Aplicativos** na página do diretório no [Portal clássico do Azure](https://manage.windowsazure.com/).
2. Clique no registro integrado de aplicativos.
3. Clique em **Configurar** na página do aplicativo e role até a seção **chaves** da página.
4. Clique na duração de **1 ano** para uma nova chave. Em seguida, clique em **Salvar** e o portal exibirá o valor da nova chave.
5. Copie a **ID do Cliente** e a **Chave** exibidas após salvar. Observe que o valor-chave será exibido apenas uma vez após o salvamento. 
   
    ![](./media/mobile-services-generate-aad-app-registration-access-key-rbac/client-id-and-key.png)
6. Role até o final da página de configuração do aplicativo integrado e habilite a permissão **Ler dados do diretório** para o aplicativo e clique em **Salvar**.
   
    ![](./media/mobile-services-generate-aad-app-registration-access-key-rbac/app-perms.png)
7. No [Portal clássico do Azure](https://manage.windowsazure.com/), navegue até o seu serviço móvel e clique na guia **Configurar**. Role até a seção **configurações do aplicativo**, adicione as seguintes configurações do aplicativo e clique em **Salvar**.
   
    <table border="1"> <tr> <th>Nome de Configuração do Aplicativo</th><th>Descrição</th> </tr> <tr> <td>AAD\_CLIENT\_ID</td><td>A id do cliente que copiou do seu aplicativo integrado nas etapas acima.</td> </tr> <tr> <td>AAD\_CLIENT\_KEY</td><td>A chave do aplicativo gerada no aplicativo integrado do AAD nas etapas acima.</td> </tr> <tr> <td>AAD\_TENANT\_DOMAIN</td><td>O seu nome de domínio do AAD. Deve ser semelhante a "meudomínio.onmicrosoft.com"</td> </tr> <tr> <td>AAD\_GROUP\_ID</td><td>A ID do grupo que você anotou para o grupo de Vendas na seção anterior</td> </tr> </table><br/>

    ![](./media/mobile-services-generate-aad-app-registration-access-key-rbac/aad-app-settings.png)


<!---HONumber=AcomDC_1203_2015-->