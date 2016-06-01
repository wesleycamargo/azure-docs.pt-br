<properties
	pageTitle="Configuração avançada do SDK do Azure Mobile Engagement para Android"
	description="Descreve as opções de configuração avançada, incluindo o Manifesto do Android com o SDK do Azure Mobile Engagement para Android"
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
	ms.date="05/12/2016"
	ms.author="piyushjo;ricksal" />

# Configuração avançada do SDK do Azure Mobile Engagement para Android

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-logging.md)

Este procedimento descreve como definir várias opções de configuração para aplicativos Azure Mobile Engagement do Android.

## Pré-requisitos

[AZURE.INCLUDE [Pré-requisitos](../../includes/mobile-engagement-android-prereqs.md)]

## Requisitos de permissão
Um número de opções exigem permissões específicas, que estão relacionadas aqui para referência, bem como alinhadas com o recurso específico. Adicionar essas permissões ao AndroidManifest.xml do projeto imediatamente anteriores ou posteriores à marcação `<application>`.

O código de permissão deve se parecer com o código a seguir, no qual você preenche a permissão apropriada da tabela abaixo.

	<uses-permission android:name="android.permission.[specific permission]"/>


| Permissão | Quando usado |
| ---------- | --------- |
| INTERNET | Obrigatório. Para relatório básico |
| ACCESS\_NETWORK\_STATE | Obrigatório. Para relatório básico |
| RECEIVE\_BOOT\_COMPLETED | Obrigatório. Para mostrar o centro de notificações após a reinicialização do dispositivo |
| WAKE\_LOCK | Altamente recomendável. Habilita a coleta de dados ao usar Wi-Fi ou quando a tela estiver desligada |
| VIBRATE | Opcional. Habilita a vibração após o recebimento de notificações |
| DOWNLOAD\_WITHOUT\_NOTIFICATION | Opcional. Habilita a notificação de visão geral do Android |
| WRITE\_EXTERNAL\_STORAGE | Opcional. Habilita a notificação de visão geral do Android |
| ACCESS\_COARSE\_LOCATION | Opcional. Habilita o relatório de local em tempo real |
| ACCESS\_FINE\_LOCATION | Opcional. Habilita o relatório de local baseado em GPS |

A partir do Android M, [algumas permissões são gerenciadas em tempo de execução](mobile-engagement-android-location-reporting.md#Android-M-Permissions).

Se você já estiver usando o ``ACCESS_FINE_LOCATION``, não precisará usar o ``ACCESS_COARSE_LOCATION`` também.

## Opções de configuração do manifesto do Android

### Relatório de falha

Se você deseja desabilitar relatórios de falha, adicione (entre as marcas `<application>` e `</application>`):

	<meta-data android:name="engagement:reportCrash" android:value="false"/>

### Limite de intermitência

Por padrão, o serviço Engagement reporta logs em tempo real. Se seu aplicativo reporta logs com muita frequência, é melhor armazenar os logs em buffer e relatá-los todos de uma vez em uma base de tempo normal (isso é chamado de "modo de intermitência"). Para fazer isso, adicione este código entre as marcações `<application>` e `</application>`:

	<meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

O modo de intermitência aumenta ligeiramente a vida útil da bateria, mas tem um impacto no Monitor do Engagement: a duração de todas as sessões e trabalhos será arredondada para o limite de intermitência (portanto, as sessões e trabalhos mais curtos do que o limite de intermitência podem não estar visíveis). É recomendável usar um limite de intermitência não maior que 30.000 (30s).

### Tempo limite da sessão

Por padrão, uma sessão é encerrada 10s após o término de sua última atividade (que geralmente ocorre pressionando a tecla Página Inicial ou Voltar, definindo o telefone como ocioso ou indo diretamente para outro aplicativo). Isso é para evitar uma divisão de sessão cada vez que o usuário sair e retornar para o aplicativo rapidamente (o que pode acontecer quando ele pegar uma imagem, verificar uma notificação, etc.). Convém modificar esse parâmetro. Para fazer isso, adicione (entre as marcas `<application>` e `</application>`):

	<meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## Desabilitar o relatório de log

### Usando uma chamada de método

Se desejar que o Engagement pare de enviar logs, você pode chamar:

	EngagementAgent.getInstance(context).setEnabled(false);

Essa chamada é persistente: ela utiliza um arquivo de preferências compartilhado.

Se o Engagement está ativo quando você chama essa função, pode levar um minuto para parar o serviço. No entanto, ele nem sequer abrirá o serviço na próxima vez que você iniciar o aplicativo.

Você pode habilitar o log de relatórios novamente chamando a mesma função com `true`.

### Integração em seu próprio `PreferenceActivity`

Em vez de chamar essa função, você também pode integrar esta configuração diretamente em seu arquivo existente `PreferenceActivity`.

Você pode configurar o Engagement para usar o arquivo de preferências (com o modo desejado) no arquivo `AndroidManifest.xml` com `application meta-data`:

-   A chave `engagement:agent:settings:name` é usada para definir o nome do arquivo de preferências compartilhado.
-   A chave `engagement:agent:settings:mode` é usada para definir o modo do arquivo de preferências compartilhado. Você deve usar o mesmo modo que em seu `PreferenceActivity`. O modo deve ser passado como um número: se você estiver usando uma combinação de sinalizadores constantes em seu código, verifique o valor total.

O Engagement sempre usa a chave booliana `engagement:key` dentro do arquivo de preferências para gerenciar esta configuração.

O exemplo a seguir de `AndroidManifest.xml` mostra os valores padrão:

	<application>
	    [...]
	    <meta-data
	      android:name="engagement:agent:settings:name"
	      android:value="engagement.agent" />
	    <meta-data
	      android:name="engagement:agent:settings:mode"
	      android:value="0" />

Em seguida, você pode adicionar um `CheckBoxPreference` em seu layout de preferência como o seguinte:

	<CheckBoxPreference
	  android:key="engagement:enabled"
	  android:defaultValue="true"
	  android:title="Use Engagement"
	  android:summaryOn="Engagement is enabled."
	  android:summaryOff="Engagement is disabled." />

<!---HONumber=AcomDC_0518_2016-->