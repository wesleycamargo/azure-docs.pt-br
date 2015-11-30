<properties
   pageTitle="Serviços Confiáveis de Backup e Restauração | Microsoft Azure"
   description="Documentação conceitual dos Serviços Confiáveis de Backup e Restauração do Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2015"
   ms.author="mcoskun"/>

# Reliable Services de Backup e Restauração

O Service Fabric é uma plataforma de alta disponibilidade e replica o estado em vários nós a fim de manter essa alta disponibilidade. Portanto, mesmo se um nó do cluster falhar, os serviços continuarão disponíveis. Embora essa redundância interna fornecida pela plataforma possa ser suficiente para algumas pessoas, em certos casos, é recomendável fazer o backup dos dados do serviço (em um repositório externo).

Por exemplo, convém fazer o backup dos dados de um serviço nos seguintes cenários:

* No caso de perda permanente de um cluster inteiro do Service Fabric, ou de todos os nós que estão em execução em uma determinada partição (ou seja, todos os nós do cluster). Isso pode acontecer quando, por exemplo, você não estiver replicado geograficamente e todo o cluster estiver em um data center, e o data center inteiro ficar inativo.
* Erros administrativos nos quais o estado é acidentalmente excluído/corrompido. Isso pode ocorrer quando um administrador com privilégios suficientes exclui erroneamente o serviço.
* Bugs no serviço causando a corrupção de dados. Isso pode acontecer quando, por exemplo, houver uma atualização de serviço que grava dados com falha em uma coleção, forçando a necessidade de reverter o serviço e os dados para um estado anterior.
* Processamento de dados offline. Talvez seja conveniente processar no modo offline os dados de business intelligence que ocorrem separadamente do serviço de geração de dados.

O recurso de Backup/Restauração permite que os serviços criados na API de Reliable Services criem e restaurem os backups. As APIs de backup fornecidas pela plataforma permitem que os backups do estado de uma partição sejam realizados sem bloquear as operações de leitura ou de gravação. As APIs de restauração permitem que o estado da partição seja restaurado a partir de um backup escolhido.



### Como fazer backup

O Autor do serviço tem controle total sobre quando realizar backups e onde os backups serão armazenados.

Para iniciar um backup, o serviço precisa chamar **IReliableStateManager.BackupAsync**. Os backups só podem ser realizados a partir de Réplicas primárias e exigem a concessão do Status de Gravação.

Conforme mostrado abaixo, a sobrecarga mais simples de **BackupAsync** usa a Função<< BackupInfo  bool >> chamada **backupCallback**.

        await this.StateManager.BackupAsync(this.BackupCallbackAsync);

**BackupInfo** fornece informações sobre o backup, incluindo o local da pasta na qual o tempo de execução salvou o backup (BackupInfo.Directory). A função de retorno de chamada espera mover o BackupInfo.Directory para um Armazenamento externo ou outro local. Além disso, esta função retorna um booliano que indica se ele foi capaz de mover a pasta de backup para seu local de destino.

O código a seguir demonstra como a backupCallback pode ser usada para carregar o backup no Armazenamento do Azure:

```C#
        private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo)
        {
            var backupId = Guid.NewGuid();

            await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, CancellationToken.None);

            return true;
        }
```

No exemplo acima, **ExternalBackupStore** é a classe de exemplo usada para realizar a interface com o Armazenamento de Blobs do Azure, e **UploadBackupFolderAsync** é o método que compacta a pasta e a coloca no Armazenamento de Blobs do Azure.

>[AZURE.NOTE]Pode haver apenas um **BackupAsync** por réplica em execução a qualquer momento. Mais de uma chamada de **BackupAsync** por vez lançará **FabricBackupInProgressException** para limitar a execução de backups para apenas uma.[AZURE.NOTE]Se uma réplica passar por failover enquanto um backup estiver em andamento, talvez o backup não tenha sido concluído. Portanto, após a conclusão do failover, é responsabilidade do serviço reiniciar o backup invocando **BackupAsync**, conforme necessário.

### Como restaurar dados

É possível classificar da seguinte maneira os cenários de restauração nos quais o serviço em execução precisa restaurar os dados do repositório de backup:

1. A partição do serviço perdeu os dados. Por exemplo, o disco de duas entre três réplicas de uma partição (incluindo a réplica primária) é corrompido/apagado. Os dados do novo primário precisam ser restaurados.

2. O serviço inteiro é perdido. Por exemplo, um administrador remove todo o serviço e, portanto, o serviço e os dados precisam ser restaurados.

3. O serviço replica dados corrompidos de aplicativo (por exemplo, devido a um erro de aplicativo). Nesse caso, o serviço precisa ser atualizado/revertido a fim de remover a causa do dano e os dados não corrompidos precisam ser restaurados.

Embora muitas abordagens sejam possíveis, oferecemos alguns exemplos sobre como usar o RestoreAsync para recuperar os cenários acima.

#### Perda de dados da partição

Nesse caso, o tempo de execução detectaria automaticamente a perda de dados e chamaria a API **OnDataLossAsync**.

O autor do serviço precisa executar o seguinte para se recuperar: substituir **IReliableStateManager** para retornar um novo ReliableStateManager e fornecer uma função de retorno de chamada para ser chamada no caso de um evento de perda de dados. - Localizar o backup mais recente no local externo que contém os backups do serviço. - Baixar o backup mais recente (e descompactar o backup na pasta de backups, se ele estiver compactado).-Chamar **IReliableStateManager.RestoreAsync** com o caminho para a pasta de backups. - Retornar true se a restauração for bem-sucedida.

Veja a seguir um exemplo de implementação do método **OnDataLossAsync** junto com a substituição de **IReliableStateManager**.

```C#
        protected override IReliableStateManager CreateReliableStateManager()
        {
            return new ReliableStateManager(new ReliableStateManagerConfiguration(
                    onDataLossEvent: this.OnDataLossAsync));
        }

        protected override async Task<bool> OnDataLossAsync(CancellationToken cancellationToken)
        {
            var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

            await this.StateManager.RestoreAsync(backupFolder);

            return true;
        }
```

>[AZURE.NOTE]O RestorePolicy é definido como Seguro por padrão. Isso significa que a API RestoreAsync falhará com ArgumentException se detectar que a pasta de backups contém o estado mais antigo ou igual ao estado contido nesta réplica. RestorePolicy.Force pode ser usado para ignorar a verificação de segurança.

#### Serviço perdido ou excluído

Se um serviço for removido, primeiro recrie o serviço antes de restaurar os dados. É importante criar o serviço com a mesma configuração, por exemplo, esquema de particionamento, para que os dados possam ser restaurados perfeitamente. Quando o serviço estiver funcionando, a API para restauração dos dados (**OnDataLossAsync** acima) precisará ser chamada em todas as partições desse serviço. Uma maneira de conseguir isso é usando **FabricClient.ServiceManager.InvokeDataLossAsync** em cada partição. Para saber mais sobre **InvokeDataLossAsync**, consulte o capítulo sobre Capacidade de teste.

Neste ponto, a implementação é igual ao cenário anterior. Cada partição deve restaurar o backup mais recente relevante do armazenamento externo. Uma limitação é que a ID de partição pode ter sido alterada, uma vez que o tempo de execução cria IDs de partição dinamicamente). Portanto, o serviço precisa armazenar o nome do serviço e as informações de partição apropriadas para identificar o backup correto mais recente para restauração em cada partição.


#### Replicação de dados de aplicativo corrompidos

Se houver um bug no aplicativo após a atualização que possa causar corrupção de dados, por exemplo, uma atualização do aplicativo pode começar a atualizar todos os números de telefone registrados em um Dicionário Confiável com um código de área inválido. Nesse caso, os números de telefone inválidos serão replicados, uma vez que o Service Fabric não tem conhecimento da natureza dos dados que estão sendo armazenados.

A primeira coisa a ser feita após a detecção de um bug tão sério e que causa a corrupção de dados é congelar o serviço no nível do aplicativo e, se possível, atualizar para a versão do código do aplicativo que não tenha o bug. No entanto, mesmo depois que o código do serviço é corrigido, os dados ainda podem estar corrompidos e, portanto, talvez seja necessário restaurá-los. Nesses casos, talvez não seja suficiente restaurar o backup mais recente, uma vez que os backups mais recentes também podem estar corrompidos. Assim, é necessário localizar o backup mais recente realizado antes de os dados serem corrompidos.

Uma maneira de fazer isso é implantar um novo cluster do Service Fabric e restaurar os backups das partições afetadas, assim como o cenário de "Serviço Excluído" acima. Ao encontrar o backup mais recente realizado antes da corrupção dos dados, será possível mover/excluir todos os backups desta partição realizados após este backup. Agora, quando **OnDataLossAsync** for chamado na partição do cluster de produção, o último backup localizado no armazenamento externo será aquele escolhido pelo processo acima.

Agora, as etapas no "Serviço Excluído" podem ser usadas para restaurar o estado do backup do serviço para o estado anterior à corrupção do estado pelo código com bug.


>[AZURE.NOTE]Sempre que restauramos há uma chance do backup restaurado ser mais antigo do que o estado da partição antes de os dados serem perdidos. Por isso, essa Restauração deve ser usada apenas como último recurso para recuperar o máximo possível de dados.

>[AZURE.NOTE]A cadeia de caracteres que representa o caminho da pasta de backup, bem como os caminhos dos arquivos dentro da pasta de backup pode ser maior do que 255 caracteres, dependendo do caminho de FabricDataRoot e do comprimento do nome do Tipo de Aplicativo. Isso pode fazer com que alguns métodos .Net, como **Directory.Move** lance o **PathTooLongException**. Como uma opção alternativa, é possível chamar diretamente as APIs kernel32 como **CopyFile**.


## Nos bastidores: mais detalhes sobre backup e restauração

### Backup

O Gerenciador de Estado Confiável permite a criação de backups consistentes sem bloquear qualquer operação de leitura e gravação exigida pelo serviço. Para fazer isso, ele utiliza um mecanismo de ponto de verificação e persistência de log. O Gerenciador de Estado Confiável usa pontos de verificação (leves) difusos em determinados pontos para aliviar a pressão do log transacional e melhorar os tempos de recuperação. Quando IReliableStateManager.**BackupAsync** é chamado, o Gerenciador de Estado Confiável instrui a todos os Objetos Confiáveis para copiar seus arquivos de ponto de verificação mais recentes para uma pasta de backup local. Em seguida, o Gerenciador de Estado Confiável copia todos os registros de log desde o “ponteiro inicial" até o registro de log mais recente na pasta de backup. Como todos os registros de log, até o último, são incluídos no backup, e o Gerenciador de Estado Confiável preserva o Log Write-Ahead, o Gerenciador de Estado Confiável garante que todas as transações confirmadas (CommitAsync retornou com êxito) sejam incluídas no backup.

Qualquer transação confirmada após o **BackupAsync** ser chamado pode ou não estar no backup. Após o preenchimento da pasta de backup local pela plataforma (ou seja, backup local concluído pelo tempo de execução), o retorno de chamada de backup do serviço é invocado. Esse retorno de chamada é responsável por mover a pasta de backups para um local externo, por exemplo, o Armazenamento do Azure.

### Restaurar

O Gerenciador de Estado Confiável permite a restauração de um backup usando a API IReliableStateManager.RestoreAsync. O método RestoreAsync só pode ser chamado dentro do método OnDataLossAsync. O booliano retornado por OnDataLossAsync indica se o serviço restaurou seu estado de uma fonte externa. Se o OnDataLossAsync retornar true, o Service Fabric recompilará todas as outras réplicas a partir dessa Primária. A réplica que recebe OnDataLossAsync será transferida primeiro para a função Primária, mas não receberá o Status de Leitura ou o Status de Gravação. Isso significa que, para os implementadores de StatefulService, RunAsync não será chamado até que OnDataLossAsync seja concluído com êxito. Até que um serviço conclua essa API com êxito (retornando true ou false) e conclua a reconfiguração relevante, a API continuará sendo chamada.

Primeiro, RestoreAsync descarta todos os estados existentes na réplica Primária na qual foi chamado. Depois, o Gerenciador de Estado Confiável cria todos os Objetos Confiáveis que existem na pasta de backup. Em seguida, os Objetos Confiáveis são instruídos a restaurar a partir dos pontos de verificação na pasta de backup. Finalmente, o Gerenciador de Estado Confiável recupera seu próprio estado a partir dos registros de log na pasta de backup e executa a recuperação. Como parte do processo de recuperação, as operações que começaram do "ponto de partida" e confirmaram os registros de log na pasta de backup são reproduzidas aos Objetos Confiáveis. Essa etapa garante que o estado recuperado seja consistente.

<!---HONumber=Nov15_HO4-->