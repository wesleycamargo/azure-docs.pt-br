<properties
	pageTitle="Relatório de localização para Android para o Mobile Engagement"
	description="Relatório de localização para Android para o Azure Mobile Engagement"
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
	ms.date="04/20/2016"
	ms.author="piyushjo;ricksal" />

# Relatório de Localização no Engagement no Android

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-integrate-engagement.md)

Este tópico descreve como fazer relatórios de localização do Engagement no seu aplicativo Android.

## Pré-requisitos

[AZURE.INCLUDE [Pré-requisitos](../../includes/mobile-engagement-android-prereqs.md)]


## Relatórios de local

Se quiser que locais sejam informados, você precisa adicionar algumas linhas de configuração (entre as marcas `<application>` e `</application>`).

### Relatórios de local de área lenta

O relatório de local de área lenta permite relatar o país, a região e a localidade associados aos dispositivos. Esse tipo de relatório de local usa apenas os locais de rede (com base na ID da célula ou WIFI). A área de dispositivo é relatada no máximo uma vez por sessão. O GPS nunca é usado e, portanto, esse tipo de relatório de local tem pouco impacto (ou quase nenhum) sobre a bateria.

As áreas relatadas são usadas para computar as estatísticas geográficas sobre usuários, sessões, eventos e erros. Elas também podem ser usadas como critério nas campanhas do Reach.

Para habilitar o relatório de localização de área simples, você pode fazer isso usando a configuração mencionada anteriormente neste procedimento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Você também precisa adicionar a permissão a seguir, se estiver ausente:

		<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou pode continuar usando ``ACCESS_FINE_LOCATION`` se você já usa em seu aplicativo.

### Relatórios de local em tempo real

Os relatórios de local em tempo real permitem relatar a latitude e a longitude associados aos dispositivos. Por padrão, esse tipo de relatório local usa apenas os locais de rede (com base na ID da célula ou WIFI) e o relatório estará disponível apenas quando o aplicativo for executado em primeiro plano (ou seja, durante uma sessão).

Os locais em tempo real *NÃO* são usados para calcular estatísticas. Sua única finalidade é permitir o uso do critério de isolamento geográfico em tempo real <Reach-Audience-geofencing> em Campanhas de alcance.

Para habilitar o relatório de localização em tempo real, adicione uma linha de código em que você defina a cadeia de conexão do Engagement na atividade de inicializador. O resultado terá esta aparência:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Você também precisa adicionar a permissão a seguir, se estiver ausente:

		<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou pode continuar usando ``ACCESS_FINE_LOCATION`` se você já usa em seu aplicativo.

#### Relatórios com base em GPS

Por padrão, os relatórios de local em tempo real usam apenas locais com base em rede. Para habilitar o uso do GPS com base em locais (que são muito mais precisos), use o objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Você também precisa adicionar a permissão a seguir, se estiver ausente:

		<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### Relatório de segundo plano

Por padrão, os relatórios de local em tempo real ficam ativos apenas quando o aplicativo é executado em primeiro plano (ou seja, durante uma sessão). Para habilitar o relatório também em segundo plano, use este objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Quando o aplicativo é executado em segundo plano, somente locais baseados em rede são relatados, mesmo se você tiver habilitado o GPS.

O relatório de local de segundo plano será interrompido se o usuário reiniciar o dispositivo; você pode adicionar isso para fazê-lo reiniciar automaticamente no momento de inicialização:

		<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			<intent-filter>
			    <action android:name="android.intent.action.BOOT_COMPLETED" />
			</intent-filter>
		</receiver>

Você também precisa adicionar a permissão a seguir, se estiver ausente:

		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### Permissões do Android M

A partir do Android M, algumas permissões são gerenciadas em tempo de execução e precisam de aprovação do usuário.

As permissões de tempo de execução serão desativadas por padrão para novas instalações do aplicativo se você selecionar o nível 23 da API do Android. Caso contrário, elas serão ativadas por padrão.

O usuário pode habilitar/desabilitar essas permissões no menu de configurações do dispositivo. A desativação de permissões no menu de sistema interrompe os processos em segundo plano do aplicativo; esse é um comportamento de sistema e não tem nenhum impacto na capacidade de receber push em segundo plano.

No contexto de relatório de localização do Mobile Engagement, as permissões que exigem aprovação em tempo de execução são:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`

Você deve solicitar permissões do usuário usando uma caixa de diálogo padrão do sistema. Se o usuário aprovar, é necessário pedir a ``EngagementAgent`` para levar em conta essa alteração em tempo real (caso contrário, a alteração será processada na próxima vez que o usuário iniciar o aplicativo).

Aqui está um exemplo de código para usar em uma atividade do seu aplicativo para solicitar permissões e encaminhar o resultado se for positivo para ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

<!---HONumber=AcomDC_0511_2016-->