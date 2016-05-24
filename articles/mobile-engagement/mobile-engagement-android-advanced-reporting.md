<properties
	pageTitle="Opções de relatório avançadas do Android SDK para Azure Mobile Engagement"
	description="Opções de relatório avançadas do Android SDK para Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="piyushjo;ricksal" />

# Opções de relatório com o Engagement no Android

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-advanced-reporting.md)

Este tópico descreve cenários de relatório adicionais em seu aplicativo Android. Estas são opções que você pode optar por aplicar ao aplicativo criado no tutorial [Introdução](mobile-engagement-android-get-started.md).

## Pré-requisitos

[AZURE.INCLUDE [Pré-requisitos](../../includes/mobile-engagement-android-prereqs.md)]

O tutorial que você concluiu foi deliberadamente simples e direto, mas há uma série de opções dentre as quais você pode escolher.

## Compilando com o ProGuard

Se você compilar seu pacote de aplicativo com o ProGuard, você precisa manter algumas classes. Você pode usar o seguinte trecho de código de configuração:


			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			<methods>;
		 	}

## Marcas no arquivo AndroidManifest.xml

Na marcação de serviço no seu arquivo AndroidManifest.xml, o atributo `android:label` permite que você escolha o nome do serviço do Engagement como ele aparecerá para os usuários finais na tela "Serviços em execução" dos seus respectivos telefones. É recomendável definir esse atributo para `"<Your application name>Service"` (por exemplo, `"AcmeFunGameService"`).

Especificar o atributo `android:process` garante que o serviço do Engagement será executado em seu próprio processo (executando o Engagement no mesmo processo que seu aplicativo fará o thread/interface de usuário principal potencialmente menos responsivos).

## Usando Application.onCreate()

Qualquer código que você colocar em `Application.onCreate()` e em outros retornos de chamada do aplicativo serão executados para todos os processos de seu aplicativo, incluindo o serviço Engagement. Ele pode ter efeitos colaterais indesejados, como alocações de memória desnecessárias e threads no processo do Engagement, serviços ou receptores de difusão duplicados.

Caso você substitua `Application.onCreate()`, recomenda-se adicionar o seguinte trecho de código no início da sua função `Application.onCreate()`:

			 public void onCreate()
			 {
			   if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
			     return;

			   ... Your code...
			 }

Você pode fazer a mesma coisa para `Application.onTerminate()`, `Application.onLowMemory()` e `Application.onConfigurationChanged(...)`.

Você também pode estender `EngagementApplication` em vez de ampliar `Application`: o retorno de chamada `Application.onCreate()` faz a verificação do processo e chama `Application.onApplicationProcessCreate()` somente se o processo atual não for aquele que hospeda o serviço do Engagement. As mesmas regras se aplicam para os outros retornos de chamada.

## Modificando as classes `Activity`

No [Tutorial de Introdução](mobile-engagement-android-get-started.md), era necessário apenas fazer com que suas subclasses `*Activity` herdassem das classes `Engagement*Activity` correspondentes (por exemplo, se sua atividade herdada estendesse `ListActivity`, você faria com que ela estendesse `EngagementListActivity`).

> [AZURE.IMPORTANT] Ao usar `EngagementListActivity` ou `EngagementExpandableListActivity`, certifique-se de que qualquer chamada para `requestWindowFeature(...);` seja feita antes da chamada para `super.onCreate(...);`, caso contrário, ocorrerá uma falha.

Você pode encontrar essas classes na pasta `src` e copiá-las para seu projeto. As classes também estão no **JavaDoc**.

## Método alternativo: chame `startActivity()` e `endActivity()` manualmente

Se você não pode ou não quer sobrecarregar as suas classes `Activity`, é possível iniciar suas atividades chamando diretamente os métodos de `EngagementAgent`.

> [AZURE.IMPORTANT] O SDK do Android nunca chama o método `endActivity()`, mesmo quando o aplicativo é fechado (no Android, aplicativos nunca são, na verdade, fechados). Assim, é *ALTAMENTE* recomendado chamar o método `startActivity()` no retorno de chamada `onResume` de *TODAS* as suas atividades, e o método `endActivity()` no retorno de chamada `onPause()` de *TODAS* as suas atividades. Essa é a única maneira de certificar-se de que as sessões não serão perdidas. Se ocorrer perda de uma sessão, o serviço Engagement nunca se desconectará do back-end do Engagement (desde que o serviço permaneça conectado enquanto houver uma sessão pendente).

Aqui está um exemplo:

			public class MyActivity extends Some3rdPartyActivity
			{
			  @Override
			  protected void onResume()
			  {
			    super.onResume();
			    String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
			    EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
			  }

			  @Override
			  protected void onPause()
			  {
			    super.onPause();
			    EngagementAgent.getInstance(this).endActivity();
			  }
			}

Este exemplo é muito semelhante à classe `EngagementActivity` e suas variantes, cujo código-fonte é fornecido na pasta `src`.

<!---HONumber=AcomDC_0511_2016-->