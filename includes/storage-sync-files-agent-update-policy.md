O agente de Sincronização de arquivos do Azure é atualizado regularmente para adicionar novos recursos e resolver problemas. Recomendamos que você configure o Microsoft Update para obter atualizações para o agente de Sincronização de arquivos do Azure à medida que elas ficarem disponíveis. Entendemos que algumas organizações gostam de controlar e testar as atualizações rigidamente.

Para as implantações que usam versões mais antigas do agente de Sincronização de arquivos do Azure:

- Após o lançamento inicial de uma nova versão principal, o Serviço de Sincronização de Armazenamento mantém a versão principal anterior por três meses. Por exemplo, o Serviço de Sincronização de Armazenamento dá suporte à versão 1.\* por até três meses após o lançamento da versão 2\*.
- Depois de três meses, o Serviço de Sincronização de Armazenamento começa a bloquear a sincronização dos servidores registrados com a versão expirada com seus grupos de sincronização.
- Durante os primeiros três meses de tolerância da versão principal anterior, todas as correções de bug vão apenas para a versão principal (nova) atual.

> [!Note]  
> Se sua versão da Sincronização de arquivos do Azure expirar dentro de três meses, você será notificado por meio de notificação do sistema no portal do Azure.
