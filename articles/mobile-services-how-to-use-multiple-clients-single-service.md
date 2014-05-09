<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Considerações para oferecer suporte a vários clientes em um único serviço móvel" authors="krisragh" solutions="" manager="" editor="" />

# Considerações para oferecer suporte a vários clientes de um único serviço móvel
Um dos principais benefícios de usar os Serviços Móveis do Azure para oferecer suporte ao desenvolvimento de aplicativos móveis é a capacidade de usar um único serviço de back-end que oferece suporte a um aplicativo em várias plataformas de cliente. Os Serviços Móveis fornecem bibliotecas de cliente nativas para todas as principais plataformas de dispositivos. Para obter mais informações, consulte [Tutoriais e recursos].

Embora os Serviços Móveis facilitem a migração de seu aplicativo nativo entre várias plataformas de cliente usando um único serviço de back-end, há alguns fatores para os quais você precisa planejar. Este tópico fornece orientação sobre como fazer as notificações por push funcionarem em todas as suas plataformas de cliente. Também descreve como solucionar um problema com o uso do logon único direcionado por cliente usando a Conta da Microsoft em aplicativos da Windows Store e do Windows Phone. Finalmente, este tópico aborda algumas práticas recomendadas para reutilizar código em projetos do Visual Studio.

## Notificações por push
Esta seção descreve duas abordagens para enviar notificações por push do serviço móvel para aplicativos cliente em várias plataformas.

### Utilizar Hubs de Notificação

Os Hubs de Notificação do Azure são um serviço que é uma solução escalonável para enviar notificações por push do seu serviço móvel (ou de qualquer back-end) para aplicativos cliente em todas as principais plataformas de dispositivos. Para obter mais informações, consulte [Hubs de Notificação do Azure]. 

Os Hubs de Notificação fornecem uma infraestrutura consistente e unificada para criar e gerenciar registros de dispositivos e para enviar notificações por push a dispositivos que executam em todas as principais plataformas de dispositivos. Para obter mais informações, consulte [Introdução aos Hubs de Notificação]. Os Hubs de Notificação oferecem suporte a registros de modelos específicos à plataforma, que permitem que você use uma única chamada de API para enviar uma notificação a seu aplicativo em execução em qualquer plataforma registrada. Para obter mais informações, consulte [Enviar notificações entre plataformas aos usuários].

Os Hubs de Notificação são a solução recomendada para envio de notificações do serviço móvel para várias plataformas de cliente.

### Usar uma tabela de registro comum e um identificador de plataforma 

Se você optar por não usar os Hubs de Notificação, ainda poderá oferecer suporte às notificações por push para vários clientes do serviço móvel definindo um mecanismo comum de registro de dispositivos. Use uma única tabela para armazenar as informações específicas ao dispositivo usadas pelos serviços de notificação por push de sua plataforma com suporte. Os tutoriais de **Introdução às notificações por push** ([C# na Windows Store][Get started with push Windows dotnet]/[JavaScript na Windows Store][Get started with push Windows js]/[Windows Phone][Get started with push Windows Phone]/[iOS][Get started with push iOS]/[Android][Get started with push Android]) usam uma tabela **Registrations** e armazenam o URI do canal (Windows), o token do dispositivo (iOS) ou o identificador (Android) em uma coluna chamada _handle_. 

<div class="dev-callout"><b>Observação</b>
	<p>Quando você usa o assistente de Adicionar Notificação por Push no Visual Studio 2013 para adicionar notificações por push a um aplicativo da Windows Store, o assistente cria automaticamente uma tabela chamada <strong>Channel</strong> para armazenar os URIs dos canais. O tutorial <strong>Introdução às notificações por push no Visual Studio 2012</strong> (<a href="/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012">C# na Windows Store</a>/<a href="/pt-br/develop/mobile/tutorials/get-started-with-push-js-vs2012">JavaScript na Windows Store</a>) mostra como habilitar notificações por push usando a tabela <strong>Registrations</strong>.</p>
</div>

Para oferecer suporte a vários clientes nesta única tabela de registro, inclua uma coluna _platform_ na tabela, onde esse campo seja definido como a plataforma do cliente inserindo uma linha durante o registro. Por exemplo, a seguinte definição da classe **Registrations** de um aplicativo C# da Windows Store ou do Windows Phone mapeia o campo client_ChannelUri_ para a coluna _handle_ na tabela Registrations. 
		
		public class Registrations
		{
			[JsonProperty(PropertyName = "platform")]			
			public string Platform { 
				get
				{
					return "windowsstore";
					// return "windowsphone";
				}
				set {}
			}
			
		    public string Id { get; set; }
		
			[JsonProperty(PropertyName = "handle")]
			public string ChannelUri { get; set; }
		}

Observe que neste dispositivo do Windows, o campo _Platform_ sempre retorna `windowsstore` (ou `windowsphone`). Com o esquema dinâmico habilitado (o padrão), os Serviços Móveis adicionam uma coluna de plataforma na tabela Registrations, se a coluna ainda não existir. Para obter mais informações, consulte [Esquema dinâmico]. 

Em seu script do lado do servidor que envia notificações, use o campo de plataforma para determinar qual função específica à plataforma deve ser chamada no [objeto push].  O script a seguir é uma modificação do script de servidor dos tutoriais **Introdução às notificações por push** ([C# da Windows Store C#][Introdução ao dotnet do Windows de envio por push]/[JavaScript da Windows Store][Introdução ao js do Windows de envio por push]/[Windows Phone][Introdução ao Windows Phone de envio por push]/[iOS][Introdução ao iOS de envio por push]/[Android][Introdução ao Android de envio por push]) para habilitar várias plataformas de cliente:

		function insert(item, user, request) {
		    request.execute({
		        success: function() {
		            request.respond();
		            sendNotifications();
		        }
		    });
		
		    function sendNotifications() {
		        var registrationsTable = tables.getTable('Registrations');
		        registrationsTable.read({
		            success: function(registrations) {
		                registrations.forEach(function(registration) {
		                    if (registration.platform === 'winstore') {
		                        push.wns.sendToastText04(registration.handle, {
		                            text1: item.text
		                        }, {
		                            success: pushCompleted
		                        });
		                    } else if (registration.platform === 'winphone') {
		                        push.mpns.sendFlipTile(registration.handle, {
		                            title: item.text
		                        }, {
		                            success: pushCompleted
		                        });
		                    } else if (registration.platform === 'ios') {
		                        push.apns.send(registration.handle, {
		                            alert: "Toast: " + item.text,
		                            payload: {
		                                inAppMessage: item.text
		                            }
		                        });
		                    } else if (registration.platform === 'android') {
		                        push.gcm.send(registration.handle, item.text, {
		                            success: pushCompleted
		                        });
		                    } else {
		                        console.error("Unknown push notification platform");
		                    }
		                });
		            }
		        });
		    }
		
		    function pushCompleted(pushResponse) {
		        console.log("Sent push:", pushResponse);
		    }
		}



## Registro de aplicativo do Windows

Para usar a autenticação de cliente com logon único usando a Conta da Microsoft em aplicativos da Windows Store e do Windows Phone, você deve registrar o aplicativo da Windows Store primeiro no painel da Windows Store. Isso ocorre porque quando cria um registro do Live Connect para o Windows Phone, você não pode criar um registro para a Windows Store. Para obter mais informações sobre como fazer isso, leia o tópico **Autenticar seu aplicativo da Windows Store com o logon único do Live Connect** ([Windows Store][SSO Windows Store]/[Windows Phone][SSO Windows Phone]).

## Práticas recomendadas para reutilizar código em projetos do Visual Studio

Bibliotecas de classes portáteis permitem escrever e compilar assemblies gerenciados que funcionam em mais de uma plataforma, como a Windows Store e o Windows Phone. Você pode criar classes que contêm código que você deseja compartilhar entre vários projetos e, em seguida, fazer referência a essas classes em diferentes tipos de projetos. 

Você pode usar a Biblioteca de Classes Portátil do .NET Framework para implementar o padrão MVVM (Modelo Model-View-View) e compartilhar assemblies em várias plataformas. É possível implementar o modelo e exibir as classes do modelo em um projeto da Biblioteca de Classes Portátil no Visual Studio 2012 e, em seguida, criar exibições personalizadas para diferentes plataformas. O código do modelo comum entre as plataformas pode, por exemplo, recuperar os dados de uma origem, como um Serviço Móvel do Azure, de uma maneira independente de plataforma. A Biblioteca MSDN fornece uma <a href="http://msdn.microsoft.com/pt-br/library/gg597391(v=vs.110)">visão geral e exemplo</a>, uma discussão de <a href="http://msdn.microsoft.com/pt-br/library/gg597392(v=vs.110)">Diferenças de API</a>, um exemplo de <a href="http://msdn.microsoft.com/pt-br/library/hh563947(v=vs.110)">usando bibliotecas de classes portáteis para implementar o padrão MVVM</a>, <a href="http://msdn.microsoft.com/pt-br/library/windowsphone/develop/jj714086(v=vs.105).aspx">orientação prescritiva</a> adicional e informações sobre <a href="http://msdn.microsoft.com/pt-br/library/hh871422(v=vs.110)">gerenciamento de recursos</a> em projetos da biblioteca de classes portátil.

<!-- URLs -->

[Hubs de Notificação do Azure]: /pt-br/develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows Store]: /pt-br/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /pt-br/develop/mobile/tutorials/single-sign-on-wp8/
[Tutoriais e recursos]: /pt-br/develop/mobile/resources/
[Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/getting-started-windows-dotnet/
[Enviar notificações entre plataformas aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Introdução ao dotnet do Windows de envio por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Introdução ao js do Windows de envio por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Introdução ao Windows Phone de envio por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-wp8/
[Introdução ao iOS de envio por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-ios/
[Introdução ao Android de envio por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-android/
[Esquema dinâmico]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj193175.aspx
[objeto push]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554217.aspx

