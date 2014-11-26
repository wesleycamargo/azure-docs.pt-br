1.  Clique na guia **Aplicativos** na página do seu diretório no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  Clique no registro integrado de aplicativos.

3.  Clique em **Configuar** na página do aplicativo e role até a seção **chaves** da página.
4.  Clique na duração de **1 ano** para uma nova chave. Em seguida, clique em **Salvar** e o portal exibirá o valor da nova chave.
5.  Copie a **ID do Cliente** e a **Chave** exibidas após salvar. Observe que o valor-chave será exibido apenas uma vez após o salvamento.

    ![][0]

6.  Role até o final da página de configuração do aplicativo integrado e habilite a permissão **Ler dados do diretório** para o aplicativo e clique em **Salvar**.

    ![][1]

7.  No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], navegue até seu serviço móvel e clique na guia **Configurar**. Role até a seção **configurações do aplicativo**, adicione as seguintes configurações do aplicativo e clique em **Salvar**.

    | Nome das configurações do aplicativo | Descrição                                                                      |
    |--------------------------------------|--------------------------------------------------------------------------------|
    | AAD\_CLIENT\_ID                      | A ID do cliente que você copiou do aplicativo integrado nas etapas acima.      |
    | AAD\_CLIENT\_KEY                     | A chave do aplicativo gerada no aplicativo integrado do AAD nas etapas acima.  |
    | AAD\_TENANT\_DOMAIN                  | O nome de domínio do AAD. Deve ser semelhante a “meudominio.onmicrosoft.com"   |
    | AAD\_GROUP\_ID                       | A ID do grupo que você anotou com relação ao grupo de Vendas na seção anterior |

    ![][2]

  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/client-id-and-key.png
  [1]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/app-perms.png
  [2]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/aad-app-settings.png
