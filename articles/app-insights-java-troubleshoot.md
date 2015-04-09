<properties 
	pageTitle="Solucionar problemas do Application Insights em um projeto Web Java" 
	description="Guia de solução de problemas e perguntas e respostas." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="awills"/>
 
# Solução de problemas e perguntas e respostas para o Application Insights para Java

Dúvidas ou problemas com o [Visual Studio Application Insights em Java][java]? Aqui estão algumas dicas.


## Erros de compilação

*No Eclipse, ao adicionar o SDK do Application Insights por meio de Maven ou Gradle, recebo erros de validação de soma de verificação ou de compilação.*

* Se o elemento <version> de dependência estiver usando um padrão com caracteres curinga (por exemplo, <version>[0.9,)</version>), tente especificar uma versão específica, como <version>0.9.1</version>

## Sem dados 

*Adicionei o Application Insights com êxito e executei meu aplicativo, mas nunca vi dados no portal.*

* Espere um minuto e clique em Atualizar. Atualmente, a atualização não é automática.
* Verifique se você tem uma chave de instrumentação definida no arquivo ApplicationInsights.xml (na pasta de recursos em seu projeto)
* Verifique se não há um nó `<DisableTelemetry>true</DisableTelemetry>` no arquivo xml.
* Em seu firewall, você terá que abrir as portas TCP 80 e 443 para tráfego de saída de dc.services.visualstudio.com e f5.services.visualstudio.com.
* No painel inicial do Microsoft Azure, veja o mapa de status de serviço. Se houver indicações de alerta, espere até que elas tenham voltado a OK; então, feche e abra novamente a folha do Application Insights de seu aplicativo.
* Ative o log para a janela de console do IDE adicionando um elemento <SDKLogger /> sob o nó raiz no arquivo ApplicationInsights.xml (na pasta de recursos em seu projeto) e verifique se há entradas antecedidas com [Erro].


## Sem dados de uso

*Vejo dados sobre solicitações e tempos de resposta, mas não há dados de modo de exibição de página, navegador ou usuário.*

Você configurou com êxito seu aplicativo para enviar telemetria do servidor. Agora, a próxima etapa é [configurar suas páginas da Web para enviar telemetria por meio do navegador da Web][usage].

Como alternativa, se o cliente for um aplicativo em um [telefone ou outro dispositivo][platforms], você poderá enviar telemetria por meio dele. 

Use a mesma chave de instrumentação para configurar a telemetria de seu cliente e do servidor. Os dados serão exibidos no mesmo recurso do Application Insights, e você poderá correlacionar eventos do cliente e do servidor.



## Desabilitando a telemetria

*Como desabilitar a coleta da telemetria?*

No código:

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**Ou** 

Atualize o arquivo ApplicationInsights.xml (na pasta de recursos em seu projeto). Adicione o seguinte sob o nó raiz:

    <DisableTelemetry>true</DisableTelemetry>

Usando o método XML, você precisa reiniciar o aplicativo ao alterar o valor.

## Alterando o destino

*Como alterar o recurso do Azure ao qual meu projeto envia dados?*

* [Obtenha a chave de instrumentação do novo recurso.][java]
* Se você tiver adicionado o Application Insights a seu projeto usando o Kit de Ferramentas do Azure para Eclipse, clique com o botão direito do mouse em seu projeto Web, selecione **Azure**, **Configurar Application Insights** e altere a chave.
* Caso contrário, atualize a chave em ApplicationInsights.xml na pasta de recursos em seu projeto.


## A tela inicial do Azure

*Estou examinando [o portal do Azure](http://portal.azure.com). O mapa me diz algo sobre meu aplicativo?*

Não, ele mostra a integridade dos servidores do Azure em todo o mundo.

*No painel inicial do Azure (tela inicial), como localizar dados sobre meu aplicativo?*

Supondo que você [tenha configurado seu aplicativo para o Application Insights][java], clique em Procurar, selecione Application Insights e selecione o recurso de aplicativo criado para seu aplicativo. Para acessar essa opção mais rapidamente no futuro, você pode fixar o aplicativo no painel inicial.

## Servidores de intranet

*Posso monitorar um servidor em minha intranet?*

Sim, desde que o servidor possa enviar telemetria para o portal do Application Insights pela Internet pública. 

Em seu firewall, você terá que abrir as portas TCP 80 e 443 para tráfego de saída de dc.services.visualstudio.com e f5.services.visualstudio.com.

## Retenção de dados 

*Por quanto tempo os dados são mantidos no portal? É seguro?*

Consulte [Privacidade e retenção de dados][data].

## Próximas etapas

*Configurei o Application Insights para meu aplicativo de servidor Java. O que mais posso fazer?*

* [Monitorar a disponibilidade de suas páginas da Web][availability]
* [Monitorar o uso da página da Web][usage]
* [Acompanhar o uso e diagnosticar problemas em seus aplicativos de dispositivos][platforms]
* [Escrever código para acompanhar o uso de seu aplicativo][track]
* [Capturar logs de diagnóstico][javalogs]


## Obter ajuda

* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]





<!--HONumber=49-->