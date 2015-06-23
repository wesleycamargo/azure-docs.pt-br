<properties 
	pageTitle="Como usar o API do Engagement no Windows Universal" 
	description="Como usar o API do Engagement no Windows Universal"			
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="piyushjo" />

#Como usar o API do Engagement no Windows Universal

Este documento é um complemento para o documento [Como integrar o Engagement no Windows Universal](../mobile-engagement-windows-store-integrate-engagement/): ele fornece detalhes aprofundados sobre como usar a API do Engagement para relatar as estatísticas do aplicativo.

Tenha em mente que caso queira o Engagement apenas para relatar as sessões, atividades, falhas e informações técnicas do seu aplicativo, então a maneira mais simples é fazer todas as suas subclasses `Page` herdarem da classe `EngagementPage`.

Se você quiser fazer mais, por exemplo, se você precisar reportar eventos, erros e trabalhos específicos do aplicativo ou se você tiver que relatar as atividades do seu aplicativo de maneira diferente de um implementado nas classes `EngagementPage`, você precisará usar a API do Engagement.

A API do Engagement é fornecida pela classe `EngagementAgent`. Você pode obter acesso a esses métodos por meio de `EngagementAgent.Instance`.

Mesmo se o módulo de agente não foi inicializado, cada chamada para a API é adiada e será executada novamente quando o agente está disponível.

##Conceitos de Engagement

As seguintes partes refinam os [Conceitos de Mobile Engagement](../mobile-engagement-concepts/) usuais para a plataforma Windows Universal.

### `Session` e `Activity`

Uma *atividade* geralmente está associada com uma página de aplicativo, isso significa que a *atividade* inicia quando a página é exibida e é interrompida quando a página for fechada: esse é o caso quando o SDK Engagement é integrado usando a classe `EngagementPage`.

Mas as *atividades* também podem ser controladas manualmente usando a API do Engagement. Isso permite dividir uma determinada página em várias partes secundárias para obter mais detalhes sobre o uso desta página (por exemplo para saber com que frequência e por quanto tempo as caixas de diálogo são usadas dentro desta página).

##Relatório de atividades

### O usuário inicia uma nova atividade

#### Referência

			void StartActivity(string name, Dictionary<object, object> extras = null)

É necessário chamar `StartActivity()` sempre que houver alterações de atividade do usuário. A primeira chamada para essa função inicia uma nova sessão de usuário.

> [AZURE.IMPORTANT]O SDK chama automaticamente o método EndActivity quando o aplicativo é fechado. Portanto, é ALTAMENTE recomendado chamar o método StartActivity sempre que a atividade do usuário for alterada para NUNCA chamar o método EndActivity, visto que chamar esse método força o encerramento da sessão atual.

#### Exemplo

			EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### O usuário encerra sua atividade atual

#### Referência

			void EndActivity()

Isso encerra a atividade e a sessão. Você não deve chamar esse método, a menos que você realmente sabe o que está fazendo.

#### Exemplo

			EngagementAgent.Instance.EndActivity();

##Relatório de trabalhos

### Iniciar um trabalho

#### Referência

			void StartJob(string name, Dictionary<object, object> extras = null)

Você pode usar o trabalho para acompanhar determinadas tarefa por um período de tempo.

#### Exemplo

			// An upload begins...
			
			// Set the extras
			var extras = new Dictionary<object, object>();
			extras.Add("title", "avatar");
			extras.Add("type", "image");
			
			EngagementAgent.Instance.StartJob("uploadData", extras);

### Encerrar um trabalho

#### Referência

			void EndJob(string name)

Assim que uma tarefa controlada por um trabalho foi finalizada, você deve chamar o método EndJob para esse trabalho, fornecendo o nome do trabalho.

#### Exemplo

			// In the previous section, we started an upload tracking with a job
			// Then, the upload ends
			
			EngagementAgent.Instance.EndJob("uploadData");

##Eventos de relatório

Há três tipos de eventos :

-   Eventos autônomos
-   Eventos de sessão
-   Eventos de trabalho

### Eventos autônomos

#### Referência

			void SendEvent(string name, Dictionary<object, object> extras = null)

Eventos autônomos podem ocorrer fora do contexto de uma sessão.

#### Exemplo

			EngagementAgent.Instance.SendEvent("event", extra);

### Eventos de sessão

#### Referência

			void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Eventos de sessão são geralmente usados para relatar as ações executadas por um usuário durante a sessão.

#### Exemplo

**Sem dados :**

			EngagementAgent.Instance.SendSessionEvent("sessionEvent");
			
			// or
			
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Com dados :**

			Dictionary<object, object> extras = new Dictionary<object,object>();
			extras.Add("name", "data");
			EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### Eventos de trabalho

#### Referência

			void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Eventos de trabalho são geralmente usados para relatar as ações executadas por um usuário durante um trabalho.

#### Exemplo

			EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##Erros de relatório

Há três tipos de erros:

-   Erros autônomos
-   Erros de sessão
-   Erros de trabalho

### Erros autônomos

#### Referência

			void SendError(string name, Dictionary<object, object> extras = null)

Ao contrário dos erros de sessão, os erros autônomos podem ocorrer fora do contexto de uma sessão.

#### Exemplo

			EngagementAgent.Instance.SendError("errorName", extras);

### Erros de sessão

#### Referência

			void SendSessionError(string name, Dictionary<object, object> extras = null)

Erros de sessão são geralmente usados para relatar os erros que afetam o usuário durante a sessão.

#### Exemplo

			EngagementAgent.Instance.SendSessionError("errorName", extra);

### Erros de trabalho

#### Referência

			void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Os erros podem estar relacionados a um trabalho em execução, em vez de serem relacionados a sessão do usuário atual.

#### Exemplo

			EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##Relatórios de falhas

O agente fornece dois métodos para lidar com falhas.

### Enviar uma exceção

#### Referência

			void SendCrash(Exception e, bool terminateSession = false)

#### Exemplo

Você pode enviar uma exceção a qualquer momento chamando :

			EngagementAgent.Instance.SendCrash(aCatchedException);

Você também pode usar um parâmetro opcional para encerrar a sessão do engagement ao mesmo tempo que envia a falha. Para fazer isso, chame :

			EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Se você fizer isso, os trabalhos e a sessão serão fechados apenas após o envio da falha.

### Enviar uma exceção sem tratamento

#### Referência

			void SendCrash(Exception e)

O Engagement também fornece um método para enviar as exceções sem tratamento se você tiver **DESABILITADO** o relatório automático de **falhas** do Engagement. Isso é especialmente útil quando usado dentro do manipulador de eventos do aplicativo UnhandledException.

Esse método **SEMPRE** encerrará a sessão e os trabalhos do engagement depois de ser chamado.

#### Exemplo

Você pode usá-lo para implementar seu próprio manipulador UnhandledExceptionEventArgs. Por exemplo, adicione o método `Current_UnhandledException` do arquivo `App.xaml.cs` :

			// In your App.xaml.cs file
			
			// Code to execute on Unhandled Exceptions
			void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
			{
			   EngagementAgent.Instance.SendCrash(e.Exception,false);
			}

Em App.xaml.cs em "Public App(){}" adicione:

			Application.Current.UnhandledException += Current_UnhandledException;

##Id do dispositivo

			String EngagementAgent.Instance.GetDeviceId()

Você pode obter a id do dispositivo do engagement chamando esse método.

##Parâmetros adicionais

Dados arbitrários podem ser anexados a um evento, um erro, uma atividade ou um trabalho. Esses dados podem ser estruturados usando um dicionário. Chaves e valores podem ser de qualquer tipo.

Dados adicionais são serializados para que se desejar inserir seu próprio tipo em adicionais, você precisará adicionar um contrato de dados para esse tipo.

### Exemplo

Criamos uma nova classe "Person".

			using System.Runtime.Serialization;
			
			namespace Microsoft.Azure.Engagement
			{
			  [DataContract]
			  public class Person
			  {
			    public Person(string name, int age)
			    {
			      Age = age;
			      Name = name;
			    }
			
			    // Properties
			
			    [DataMember]
			    public int Age
			    {
			      get;
			      set;
			    }
			
			    [DataMember]
			    public string Name
			    {
			      get;
			      set; 
			    }
			  }
			}

Depois, adicionaremos uma instância `Person` para um arquivo extra.

			Person person = new Person("Engagement Haddock", 51);
			var extras = new Dictionary<object, object>();
			extras.Add("people", person);
			
			EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING]Se você colocar outros tipos de objetos, verifique se o método ToString () é implementado para retornar uma cadeia de caracteres legível humana.

### Limites

#### simétricas

Cada chave no objeto deve corresponder à seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Isso significa que as chaves devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (_).

#### Tamanho

Os arquivos extras estão limitados a **1024** caracteres por chamada.

##Relatório de informações de aplicativo

### Referência

			void SendAppInfo(Dictionary<object, object> appInfos)

Você pode relatar manualmente informações (ou quaisquer outras informações específicas do aplicativo) de controle usando a função SendAppInfo().

Observe que essas informações podem ser enviadas de forma incremental: somente o último valor para uma determinada chave será mantido por um determinado dispositivo. Como eventos extras, use um Dictionary<object, object> para anexar informações.

### Exemplo

			Dictionary<object, object> appInfo = new Dictionary<object, object>()
			  {
			    {"birthdate", "1983-12-07"},
			    {"gender", "female"}
			  };
			
			EngagementAgent.Instance.SendAppInfo(appInfo);

### Limites

#### simétricas

Cada chave no objeto deve corresponder à seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Isso significa que as chaves devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (_).

#### Tamanho

As informações do aplicativo estão limitadas a **1024** caracteres por chamada.

No exemplo anterior, o JSON enviado para o servidor tem 44 caracteres:

			{"birthdate":"1983-12-07","gender":"female"}

<!--HONumber=54--> 