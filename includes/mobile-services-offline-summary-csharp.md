Para oferecer suporte aos recursos offline dos serviços móveis, usamos a interface `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` com um repositório local. Nesse caso, o repositório local era um banco de dados SQLite.

As operações CRUD normais nos serviços móveis funcionam como se o aplicativo ainda estivesse conectado, mas todas as operações ocorrem no repositório local.

Quando desejamos sincronizar o armazenamento local com o servidor, usamos os métodos `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

* Para executar push das mudanças no servidor, chamamos `IMobileServiceSyncContext.PushAsync()`. Esse método é um membro de `IMobileServicesSyncContext` em vez da tabela de sincronização porque enviará as alterações a todas as tabelas:
  
   Somente os registros que foram modificados, de alguma forma, localmente (por meio de operações CUD) serão enviados ao servidor.
* Para executar pull de dados de uma tabela no servidor para o aplicativo, chamamos `IMobileServiceSyncTable.PullAsync`.
  
    Uma pull sempre envia um push primeiro. Isso serve para garantir que todas as tabelas no repositório local juntamente com os relacionamentos permaneçam consistentes.
  
    Também há sobrecargas de `PullAsync()` que permitem que uma consulta seja especificada para filtrar os dados armazenados no cliente. Se uma consulta não for passada, o `PullAsync()` efetuará a recepção de todas as linhas da tabela correspondente (ou consulta). Você pode passar a consulta para filtrar apenas as alterações que seu aplicativo precisa para sincronizar com.
* Para habilitar a sincronização incremental, passe uma ID de consulta para `PullAsync()`. A ID de consulta é usada para armazenar o último carimbo de data/hora atualizado dos resultados da última operação de recepção. A ID da consulta deve ser uma cadeia de caracteres descritiva que é exclusiva para cada consulta lógica em seu aplicativo. Se a consulta tiver um parâmetro, então o mesmo valor de parâmetro deve ser parte da ID da consulta.
  
    Por exemplo, se você estiver filtrando userid, ele precisa fazer parte da ID da consulta:
  
        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));
  
    Se você deseja recusar a sincronização incremental, passe `null` como a ID da consulta. Nesse caso, todos os registros serão recuperados em cada chamada de `PullAsync`, que é potencialmente ineficiente.
* Para remover registros de armazenamento local do dispositivo quando eles tiverem sido excluídos do banco de dados do serviço móvel, você deve habilitar a [Exclusão Reversível]. Caso contrário, seu aplicativo deve chamar periodicamente `IMobileServiceSyncTable.PurgeAsync()` para limpar o armazenamento local.

<!---HONumber=Oct15_HO3-->