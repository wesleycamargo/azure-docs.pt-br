<properties 
	pageTitle="Como usar a API do Engagement no Android" 
	description="SDK mais recente do Android - Como usar a API do Engagement no Android"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />

#Como usar a API do Engagement no Android

Este documento é um complemento para o documento [Como integrar o Engagement no Android](mobile-engagement-android-integrate-engagement.md): fornece detalhes aprofundado sobre como usar a API do Engagement para relatar as estatísticas do aplicativo.

Tenha em mente que se desejar que o Engagement somente relate as sessões, atividades, falhas e informações técnicas do seu aplicativo, a maneira mais simples é fazer com que todas as suas subclasses  `Activity` herdam da classe  `EngagementActivity` correspondente.

Se você quiser fazer mais, por exemplo, se precisar relatar eventos, erros e trabalhos específicos do aplicativo, ou se você tiver que relatar as atividades do seu aplicativo de maneira diferente daquela implementada nas classes  `EngagementActivity`, você precisará usar a API do Engagement.

A API do Engagement é fornecida pela classe  `EngagementAgent`. Uma instância desta classe pode ser recuperada chamando o método estático  `EngagementAgent.getInstance(Context)` (Observe que o objeto  `EngagementAgent` retornado é um singleton).

##Conceitos de contrato

As seguintes partes refinam os [Conceitos do Mobile Engagement] comuns(mobile-engagement-concepts.md), para a plataforma Android.

### `Sessão` e  `Atividade`

Se o usuário permanecer mais de alguns segundos ocioso entre duas  *atividades*, a sua sequência de  *atividades* é dividida em duas  *sessões* diferentes. Esses poucos segundos são chamados de "tempo limite da sessão".

Uma  *atividade* geralmente está associada a uma tela do aplicativo, isso significa que a  *atividade* é iniciada quando a tela é exibida e é interrompida quando a tela é fechada: esse é o caso quando o SDK do contrato é integrado usando as classes  `EngagementActivity`.

Mas as  *atividades* também podem ser controladas manualmente usando a API do Engagement. Isso permite dividir uma determinada tela em várias subpartes para obter mais detalhes sobre o uso desta tela (por exemplo, para saber a frequência e por quanto tempo as caixas de diálogo são usadas dentro desta tela).

##Relatório de atividades

> [AZURE.IMPORTANT] Não é necessário relatório de atividades, como descrito nesta seção se você estiver usando a classe  `EngagementActivity` e suas variantes, conforme explicado em Como integrar o Engagement em documento Android.

### Usuário inicia uma nova atividade

			EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
			// Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

É necessário chamar o  `startActivity()` sempre que o usuário altera a atividade. A primeira chamada para essa função inicia uma nova sessão de usuário.

O melhor lugar para chamar essa função é em cada retorno de chamada  `onResume` de atividade.

### Usuário encerra sua atividade atual

			EngagementAgent.getInstance(this).endActivity();

É necessário chamar  `endActivity()` pelo menos uma vez quando o usuário conclui sua última atividade. Isso informa o SDK do Engagement que o usuário está ocioso no momento e que a sessão do usuário precisa ser fechada quando o tempo limite da sessão expirar (se você chamar  `startActivity()` antes de expirar o tempo limite da sessão, a sessão será simplesmente retomada).

O melhor lugar para chamar essa função é em cada retorno de chamada  `onPause` de atividade.

##Eventos de relatório

### Eventos de sessão

Os eventos de sessão são geralmente usados para relatar as ações executadas por um usuário durante a sessão.

**Exemplo sem dados extras:**

			public MyActivity extends EngagementActivity {
			   [...]
			   @Override
			   public boolean onPrepareOptionsMenu(Menu menu) {
			      getEngagementAgent().sendSessionEvent("menu_shown", null);
			   }
			   [...]
			}

**Exemplo com dados extras:**

			public MyActivity extends EngagementActivity {
			  [...]
			  @Override
			  public boolean onMenuItemSelected(int featureId, MenuItem item) {
			    Bundle extras = new Bundle();
			    extras.putInt("id", item.getItemId());
			    getEngagementAgent().sendSessionEvent("menu_selected", extras);
			  }
			  [...]
			}

### Eventos independentes

Ao contrário dos eventos de sessão, os eventos independentes podem ocorrer fora do contexto de uma sessão.

**Exemplo:**

Suponha que você deseja relatar eventos que ocorrem quando um receptor de difusão é disparado:

			/** Triggered by Intent.ACTION_BATTERY_LOW */
			public BatteryLowReceiver extends BroadcastReceiver {
			  [...]
			  @Override
			  public void onReceive(Context context, Intent intent) {
			    EngagementAgent.getInstance(context).sendEvent("battery_low", null);
			  }
			  [...]
			}

##Relatório de erros

### Erros de sessão

Os erros de sessão são geralmente usados para relatar os erros que afetam o usuário durante a sessão.

**Exemplo:**

			/** The user has entered invalid data in a form */
			public MyActivity extends EngagementActivity {
			  [...]
			  public void onMyFormSubmitted(MyForm form) {
			    [...]
			    /* The user has entered an invalid email address */
			    getEngagementAgent().sendSessionError("sign_up_email", null);
			    [...]
			  }
			  [...]
			}

### Erros de independentes

Ao contrário dos erros de sessão, podem ocorrer erros independentes fora do contexto de uma sessão.

**Exemplo:**

O exemplo a seguir mostra como relatar um erro sempre que a memória diminui no telefone enquanto seu processo de aplicativo está em execução.

			public MyApplication extends EngagementApplication {
			
			  @Override
			  protected void onApplicationProcessLowMemory() {
			    EngagementAgent.getInstance(this).sendError("low_memory", null);
			  }
			}

##Relatório de trabalhos

### Exemplo

Suponha que você deseja relatar durante o processo de logon:
			
			[...]
			public void signIn(Context context, ...) {
			
			  /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
			  EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
			
			  /* Report sign in job has started */
			  engagementAgent.startJob("sign_in", null);
			
			  [... sign in ...]
			
			  /* Report sign in job is now ended */
			  engagementAgent.endJob("sign_in");
			}
			[...]

### Relatar erros durante um trabalho

Os erros podem estar relacionados a um trabalho em execução, em vez de estarem relacionados à sessão de usuário atual.

**Exemplo:**

Suponha que você deseja relatar um erro durante o seu processo de logon:

[...]
public void signIn(Context context, ...) {

			  /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
			  EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
			
			  /* Report sign in job has been started */
			  engagementAgent.startJob("sign_in", null);
			
			  /* Try to sign in */
			  while(true)
			    try {
			      trySignin();
			      break;
			    }
			    catch(Exception e) {
			      /* Report the error to Engagement */
			      engagementAgent.sendJobError("sign_in_error", "sign_in", null);
			
			      /* Retry after a moment */
			      sleep(2000);
			    }
			  [...]
			  /* Report sign in job is now ended */
			  engagementAgent.endJob("sign_in");
			}
			[...]

### Relatar eventos durante um trabalho

Os erros podem estar relacionados a um trabalho em execução, em vez de estarem relacionados à sessão de usuário atual.

**Exemplo:**

Suponha que temos uma rede social e usamos um trabalho de relatório durante todo o tempo que o usuário está conectado ao servidor. O usuário pode permanecer conectado em segundo plano, mesmo quando ele estiver usando outro aplicativo ou quando o telefone estiver em repouso, portanto, não há nenhuma sessão.

O usuário pode receber mensagens de seus amigos, esse é um evento de trabalho.
			
			[...]
			public void signin(Context context, ...) {
			  [...Sign in code...]
			  EngagementAgent.getInstance(context).startJob("connection", null);
			}
			[...]
			public void signout(Context context) {
			  [...Sign out code...]
			  EngagementAgent.getInstance(context).endJob("connection");
			}
			[...]
			public void onMessageReceived(Context context) {
			  [...Notify in status bar...]
			  EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
			}
			[...]

##Parâmetros extras

Os dados arbitrários podem ser anexados a trabalhos, atividades, erros e eventos.

Esses dados podem ser estruturados, eles usam a classe de pacote do Android (na verdade, eles funcionam como parâmetros extras no Android Intents). Observe que um pacote pode conter matrizes ou outras instâncias de pacote.

> [AZURE.IMPORTANT] Se você colocar os parâmetros parceláveis ou serializáveis, certifique-se de que o seu método  `toString()` seja implementado para retornar uma cadeia de caracteres legível. As classes serializáveis que contêm campos não transitórios que não são serializáveis farão com que o Android falhe quando você chamar `bundle.putSerializable("key",value);`

> [AZURE.WARNING] Não há suporte para matrizes esparsas em parâmetros extras, ou seja, ela não será serializada como uma matriz. Você deve convertê-las em matrizes padrão antes de usá-las em parâmetros extras.

### Exemplo

			Bundle extras = new Bundle();
			extras.putString("video_id", 123);
			extras.putString("ref_click", "http://foobar.com/blog");
			EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### Limites

#### simétricas

Cada chave no  `Bundle` deve coincidir com a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Isso significa que as chaves devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### Tamanho

Os extras são limitados a **1024** caracteres por chamada (uma vez codificado em JSON pelo serviço do Engagement).

No exemplo anterior, o JSON enviado para o servidor tem 58 caracteres de comprimento:

			{"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##Relatório de informações de aplicativo

Você pode relatar informações manualmente (ou quaisquer outras informações específicas do aplicativo) de controle usando a função  `sendAppInfo()`.

Observe que essas informações podem ser enviadas incrementalmente: somente o último valor para uma determinada chave será mantido por um determinado dispositivo.

Assim como os extras de evento, a classe de pacote é usada para abstrair as informações do aplicativo, observe que as matrizes ou subpacotes serão tratados como cadeias de caracteres simples (usando a serialização JSON).

### Exemplo

Aqui está um exemplo de código para enviar a data de nascimento e sexo do usuário:

			Bundle appInfo = new Bundle();
			appInfo.putString("status", "premium");
			appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
			EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### Limites

#### simétricas

Cada chave no  `Bundle` deve coincidir com a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Isso significa que as chaves devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### Tamanho

As informações do aplicativo são limitadas a **1024** caracteres por chamada (uma vez codificadas em JSON pelo serviço do Engagement).

No exemplo anterior, o JSON enviado para o servidor tem 44 caracteres de comprimento:

			{"expiration":"2016-12-07","status":"premium"}

<!--HONumber=47-->
