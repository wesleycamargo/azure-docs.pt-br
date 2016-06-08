<properties
	pageTitle="Sincronização de dados offline em aplicativos móveis do Azure | Microsoft Azure"
	description="Visão geral e referência conceitual do recurso de sincronização de dados offline para aplicativos móveis do Azure"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="03/14/2016"
	ms.author="wesmc"/>

# Sincronização de dados offline em Aplicativos Móveis do Azure

## O que é sincronização de dados offline?

A sincronização de dados offline é um recurso do SDK para cliente e servidor de Aplicativos Móveis do Azure que torna mais fácil para os desenvolvedores criarem aplicativos que funcionem sem uma conexão de rede do servidor.

Quando seu aplicativo está no modo offline, os usuários ainda podem criar e modificar dados que serão salvos em um repositório local. Quando o aplicativo estiver online novamente, você pode sincronizar as alterações locais com o back-end do aplicativo Móvel do Azure. O recurso também inclui suporte para detectar conflitos quando o mesmo registro é alterado no cliente e no back-end. Os conflitos podem ser tratados no servidor ou cliente.

A sincronização offline tem vários benefícios:

* Melhora a capacidade de resposta do aplicativo armazenando em cache os dados do servidor localmente no dispositivo
* Crie aplicativos robustos que permanecem úteis quando há problemas de rede
* Permite que usuários finais criem e modifiquem dados mesmo quando não há acesso à rede, com suporte para cenários com pouca ou nenhuma conectividade
* Sincroniza dados entre vários dispositivos e detecta conflitos quando o mesmo registro é modificado por dois dispositivos
* Limite o uso de rede em redes de alta latência ou monitoradas

Os tutoriais a seguir mostram como adicionar sincronização offline para os clientes móveis usando aplicativos móveis do Azure:

* [Android: Habilitar a sincronização offline]
* [iOS: Habilitar a sincronização offline]
* [Xamarin iOS: Habilitar a sincronização offline]
* [Xamarin Android: Habilitar a sincronização offline]
* [Windows 8.1: Habilitar a sincronização offline]

## O que é uma tabela de sincronização?

Para acessar o ponto de extremidade "/tables", os SDKs do cliente móvel do Azure fornecem interfaces como `IMobileServiceTable` (SDK do cliente .NET) ou `MSTable` (cliente iOS). Essas APIs se conectam diretamente ao back-end do aplicativo móvel do Azure e falharão se o dispositivo cliente não tiver uma conexão de rede.

Para oferecer suporte a uso offline, seu aplicativo deve usar as APIs de *tabela de sincronização*, como `IMobileServiceSyncTable` (SDK do cliente .NET) ou `MSSyncTable` (cliente iOS). As mesmas operações CRUD (Criar, Ler, Atualizar, Excluir) funcionam em relação às APIs de tabela de sincronização, exceto que agora vão ler ou gravar em um *armazenamento local*. Antes de qualquer operação de tabela de sincronização poder ser executada, o armazenamento local deve ser inicializado.

## O que é um armazenamento local?

Um armazenamento local é a camada de persistência de dados no dispositivo cliente. Os SDKs de cliente de aplicativos móveis do Azure oferecem uma implementação padrão do armazenamento local. No Windows, Xamarin e Android, ele é baseado em SQLite; no iOS, é baseado em Core Data.

Para usar a implementação baseada em SQLite no Windows Phone ou Windows Store 8.1, é preciso instalar uma extensão do SQLite. Para obter mais detalhes, veja [Windows 8.1: habilitar a sincronização offline]. Android e iOS são fornecidos com uma versão do SQLite no próprio sistema operacional do dispositivo; portanto, não é necessário fazer referência a sua própria versão do SQLite.

Os desenvolvedores também podem implementar seu próprio armazenamento local. Por exemplo, se você quiser armazenar dados em um formato criptografado no cliente móvel, poderá definir um armazenamento local que usa SQLCipher para criptografia.

## O que é um contexto de sincronização?

Um *contexto de sincronização* é associado a um objeto de cliente móvel (como `IMobileServiceClient` ou `MSClient`) e rastreia as alterações que são feitas com tabelas de sincronização. O contexto de sincronização mantém uma *fila de operação* que conserva uma lista ordenada de operações CUD (Criar, Atualizar, Excluir) que será posteriormente enviada ao servidor.

Um armazenamento local é associado ao contexto de sincronização usando um método initialize como `IMobileServicesSyncContext.InitializeAsync(localstore)` no [SDK do cliente do .NET].

## <a name="how-sync-works"></a>Como a sincronização offline funciona

Ao usar tabelas de sincronização, o código do cliente controla quando as alterações locais serão sincronizadas com um back-end do aplicativo móvel do Azure. Nada será enviado ao back-end até que haja uma chamada para alterações locais de *push*. Da mesma forma, o armazenamento local é preenchido com dados novos somente quando há uma chamada para dados de *pull*.

* **Push**: push é uma operação no contexto de sincronização e envia todas as alterações de CUD desde o último envio por push. Observe que não é possível enviar apenas alterações de uma tabela individual, pois as operações poderiam ser enviadas fora da ordem. O envio por push executa uma série de chamadas REST ao back-end do seu aplicativo móvel do Azure, que por sua vez, modificará o banco de dados do servidor.

* **Pull**: o pull é executado por tabela e pode ser personalizado com uma consulta para recuperar apenas um subconjunto dos dados do servidor. Os SDKs de cliente móvel do Azure inserem então os dados resultantes no armazenamento local.

* **Pushes implícitos**: se um pull é executado em comparação a uma tabela que tenha atualizações locais pendentes, o pull primeiro executará um envio por push no contexto de sincronização. Isso ajuda a minimizar conflitos entre as alterações que já estão na fila e novos dados do servidor.

* **Sincronização Incremental**: o primeiro parâmetro para a operação de pull é um *nome de consulta* que é usado apenas no cliente. Se você usar um nome de consulta não nulo, o SDK móvel do Azure executará uma *sincronização incremental*. Cada vez que uma operação de pull retornar um conjunto de resultados, o carimbo de data/hora `updatedAt` mais recente desse conjunto de resultados será armazenado nas tabelas do sistema local do SDK. As operações de pull subsequentes recuperarão somente registros após esse carimbo de data/hora.

  Para usar a sincronização incremental, o servidor deve retornar valores `updatedAt` significativos e também deve oferecer suporte à classificação por este campo. No entanto, como o SDK adiciona sua própria classificação no campo updatedAt, não é possível usar uma consulta de pull que tem sua própria cláusula `$orderBy$`.

  O nome da consulta pode ser qualquer sequência de caracteres que você escolher, mas deve ser exclusivo para cada consulta lógica em seu aplicativo. Caso contrário, operações de pull diferentes poderiam substituir o mesmo carimbo de data/hora de sincronização incremental e as consultas poderiam retornar resultados incorretos.

  Se a consulta tiver um parâmetro, uma maneira de criar um nome de consulta exclusivo é incorporar o valor do parâmetro. Por exemplo, se você estiver filtrando userid, o nome da consulta pode ser da seguinte maneira (em C#):

		await todoTable.PullAsync("todoItems" + userid, 
			syncTable.Where(u => u.UserId = userid));

  Se você deseja recusar a sincronização incremental, passe `null` como a ID da consulta. Nesse caso, todos os registros serão recuperados em cada chamada de `PullAsync`, que é potencialmente ineficiente.

* **Limpeza**: é possível limpar o conteúdo do armazenamento local usando `IMobileServiceSyncTable.PurgeAsync`. Isso pode ser necessário se você tiver dados obsoletos no banco de dados do cliente, ou se quiser descartar todas as alterações pendentes.

  Uma limpeza removerá uma tabela do armazenamento local. Se houver operações pendentes de sincronização com o banco de dados do servidor, a limpeza lançará uma exceção, a menos que o parâmetro *forçar limpeza* estiver definido.

  Como exemplo de dados obsoletos no cliente, suponha que no exemplo "lista de tarefas", Device1 mantém apenas os itens que não foram concluídos. Em seguida, um todoitem "Comprar leite" é marcado como concluído no servidor por outro dispositivo. No entanto, Device1 ainda terá o todoitem "Comprar leite" em armazenamento local porque ele só está obtendo itens que não são marcados como concluídos. Uma limpeza limpará esse item obsoleto.

## Próximas etapas

* [iOS: Habilitar a sincronização offline]
* [Xamarin iOS: Habilitar a sincronização offline]
* [Xamarin Android: Habilitar a sincronização offline]
* [Windows 8.1: Habilitar a sincronização offline]

<!-- Links -->
[SDK do cliente do .NET]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Habilitar a sincronização offline]: app-service-mobile-android-get-started-offline-data.md
[iOS: Habilitar a sincronização offline]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Habilitar a sincronização offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Habilitar a sincronização offline]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Windows 8.1: Habilitar a sincronização offline]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md

<!---HONumber=AcomDC_0518_2016-->