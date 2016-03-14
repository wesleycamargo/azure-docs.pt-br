<properties 
	pageTitle="Como usar o API do Engagement no Windows Phone Silverlight" 
	description="Como usar o API do Engagement no Windows Phone Silverlight"	
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede"
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

#Como usar o API do Engagement no Windows Phone Silverlight

Este documento é um complemento para o documento [Como integrar o Mobile Engagement em seu aplicativo do Windows Phone Silverlight](../mobile-engagement-windows-phone-integrate-engagement/). Ele fornece detalhes aprofundados sobre como usar a API do Engagement para relatar as estatísticas do aplicativo.

Se você desejar apenas relatar as sessões, atividades, falhas e informações técnicas do seu aplicativo, então a maneira mais simples é fazer todas as suas subclasses `PhoneApplicationPage` herdarem da classe `EngagementPage`.

Se você quiser fazer mais, por exemplo, se você precisar reportar eventos, erros e trabalhos específicos do aplicativo ou se você tiver que relatar as atividades do seu aplicativo de maneira diferente de um implementado nas classes `EngagementPage`, você precisará usar a API do Engagement.

A API do Engagement é fornecida pela classe `EngagementAgent`. Você pode obter acesso a esses métodos por meio de `EngagementAgent.Instance`.

Mesmo se o módulo de agente não foi inicializado, cada chamada para a API é adiada e será executada novamente quando o agente está disponível.

##Conceitos de Engagement

As seguintes partes refinam os Conceitos de Mobile Engagement para a plataforma do Windows Phone.

### `Session` e `Activity`

Uma *atividade* geralmente está associada com uma página de aplicativo, isso significa que a *atividade* inicia quando a página é exibida e é interrompida quando a página for fechada: esse é o caso quando o SDK Engagement é integrado usando a classe `EngagementPage`.

Mas as *atividades* também podem ser controladas manualmente usando a API do Engagement. Isso permite dividir uma determinada página em várias partes secundárias para obter mais detalhes sobre o uso desta página (por exemplo para saber com que frequência e por quanto tempo as caixas de diálogo são usadas dentro desta página).

##Relatório de atividades

### O usuário inicia uma nova atividade

#### Referência

			void StartActivity(string name, Dictionary<object, object> extras = null)

É necessário chamar `StartActivity()` sempre que houver alterações de atividade do usuário. A primeira chamada para essa função inicia uma nova sessão de usuário.

> [AZURE.IMPORTANT] O SDK chama automaticamente o método EndActivity quando o aplicativo é fechado. Portanto, é ALTAMENTE recomendado chamar o método StartActivity sempre que a atividade do usuário for alterada para NUNCA chamar o método EndActivity, visto que chamar esse método força o encerramento da sessão atual.

#### Exemplo

			EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### O usuário encerra sua atividade atual

#### Referência

			void EndActivity()

É necessário chamar `EndActivity()` pelo menos uma vez quando o usuário conclui sua última atividade. Isso informa o SDK do Engagement que o usuário está ocioso no momento e que a sessão do usuário precisa ser fechada quando o tempo limite da sessão expirar (se você chamar `StartActivity()` antes de expirar o tempo limite da sessão, a sessão será simplesmente retomada).

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

			void SendCrash(ApplicationUnhandledExceptionEventArgs e)

O Engagement também fornece um método para enviar as exceções sem tratamento. Isso é especialmente útil quando usado dentro do manipulador de eventos do aplicativo UnhandledException do silverlight.

Esse método **SEMPRE** encerrará a sessão de engagement depois de ser chamado.

#### Exemplo

Você pode usá-lo para implementar seu próprio manipulador UnhandledException (especialmente se você tiver desativado a recurso de relatório de falhas automático do Engagement). Por exemplo, no método `Application_UnhandledException` do arquivo `App.xaml.cs` :

			// In your App.xaml.cs file
			
			// Code to execute on Unhandled Exceptions
			private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
			{
			  // your own code
			
			  EngagementAgent.Instance.SendCrash(e);
			}

##OnActivated

### Referência

			void OnActivated(ActivatedEventArgs e)

Quando o usuário navega para frente, para longe de um aplicativo, após o evento Deactivated ser gerado, o sistema operacional tenta colocar o aplicativo em um estado inativo. Depois, o aplicativo é a marcado para exclusão. Nesse processo, um aplicativo é encerrado, mas alguns dados sobre o estado do aplicativo e as páginas individuais dentro do aplicativo são preservados.

Você precisa inserir `EngagementAgent.Instance.OnActivated(e)` no método `Application_Activated` do arquivo App.xaml.cs para redefinir o Agente do Engagement quando o aplicativo foi marcado para a exclusão.

### Exemplo

			// Inside your App.xaml.cs file
			
			// Code to execute when the application is activated (brought to foreground)
			// This code will not execute when the application is first launched
			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			  EngagementAgent.Instance.OnActivated(e);
			}

##Id do dispositivo

			String GetDeviceId()

Você pode obter a id do dispositivo do engagement chamando esse método.

##Parâmetros adicionais

Dados arbitrários podem ser anexados a um evento, um erro, uma atividade ou um trabalho. Esses dados podem ser estruturados usando um dicionário. Chaves e valores podem ser de qualquer tipo.

Dados adicionais são serializados para que se desejar inserir seu próprio tipo em adicionais, você precisará adicionar um contrato de dados para esse tipo.

### Exemplo

Criamos uma nova classe "Person".

			using System.Runtime.Serialization;
			
			namespace Engagement.Agent
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

> [AZURE.WARNING] Se você colocar outros tipos de objetos, verifique se o método ToString () é implementado para retornar uma cadeia de caracteres legível humana.

### Limites

#### simétricas

Cada chave no objeto deve corresponder à seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Isso significa que as chaves devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

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
			   {"subscription", "2013-12-07"},
			   {"premium", "true"}
			};
			
			EngagementAgent.Instance.SendAppInfo(appInfo);

### Limites

#### simétricas

Cada chave no objeto deve corresponder à seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Isso significa que as chaves devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### Tamanho

As informações do aplicativo estão limitadas a **1024** caracteres por chamada.

No exemplo anterior, o JSON enviado para o servidor tem 44 caracteres:

			{"subscription":"2013-12-07","premium":"true"}
 

<!---HONumber=AcomDC_0302_2016-->