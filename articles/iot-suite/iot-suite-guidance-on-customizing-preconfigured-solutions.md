---
title: "Personalizando soluções pré-configuradas | Microsoft Docs"
description: "Fornece orientação sobre como personalizar as soluções pré-configuradas do Pacote IoT do Azure."
services: 
suite: iot-suite
documentationcenter: .net
author: aguilaaj
manager: timlt
editor: 
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2016
ms.author: aguilaaj
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6cca62c7245304bf49b47c1548aa3352b7c2a76e


---
# <a name="customize-a-preconfigured-solution"></a>Personalizar uma solução pré-configurada
As soluções pré-configuradas fornecidas com o Pacote IoT do Azure demonstram os serviços no pacote trabalhando juntos para fornecer uma solução de ponta a ponta. Do ponto de partida, há uma variedade de lugares em que você pode estender e personalizar a solução para cenários específicos. As seções a seguir descrevem esses pontos comuns de personalização.

## <a name="finding-the-source-code"></a>Localizando o código-fonte
O código-fonte para a solução pré-configurada está disponível no GitHub nos seguintes repositórios:

* Monitoramento remoto: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* Manutenção preditiva: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

O código-fonte para as soluções pré-configuradas é fornecido para demonstrar os padrões e as práticas usadas para implementar a funcionalidade de ponta a ponta de uma solução IoT usando o Azure IoT Suite. Você pode encontrar mais informações sobre como compilar e implantar as soluções em repositórios GitHub.

## <a name="changing-the-preconfigured-rules"></a>Alterando as regras predefinidas
A solução de monitoramento remoto inclui três trabalhos do [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/) para implementar informações do dispositivo, telemetria e lógica de regras exibidas para a solução.

Os três trabalhos de Stream Analytics e sua sintaxe são descritos em detalhes na [Passo a passo da solução pré-configurada de monitoramento remoto](iot-suite-remote-monitoring-sample-walkthrough.md). 

Você pode editar esses trabalhos diretamente para alterar a lógica ou adicionar lógica específica para seu cenário. Você pode encontrar os trabalhos do Stream Analytics da seguinte maneira:

1. Vá para o [portal do Azure](https://portal.azure.com).
2. Navegue até o grupo de recursos com o mesmo nome da sua solução IoT. 
3. Selecione o trabalho de Stream Analytics do Azure que você deseja modificar. 
4. Interrompa o trabalho selecionando **Parar**no conjunto de comandos. 
5. Edite as entradas, consulta e saídas.
   
    Uma modificação simples é alterar a consulta para o trabalho **Regras** para usar um **"<"**, em vez de um **">"**. O portal de solução ainda mostrará **">"** quando a regra for editada, mas você perceberá que o comportamento é invertido devido à alteração no trabalho subjacente.
6. Iniciar o trabalho

> [!NOTE]
> O painel de monitoramento remoto depende de dados específicos, por isso, alterar os trabalhos pode fazer com que o painel falhe.
> 
> 

## <a name="adding-your-own-rules"></a>Adicionando suas próprias regras
Além de alterar os trabalhos de Stream Analytics do Azure, você pode usar o portal do Azure para adicionar novos trabalhos ou adicionar novas consultas para trabalhos existentes.

## <a name="customizing-devices"></a>Personalizando dispositivos
Uma das atividades mais comuns de extensão é o trabalho com dispositivos específicos ao seu cenário. Há vários métodos para trabalhar com dispositivos. Esses métodos incluem alterar um dispositivo simulado de acordo com seu cenário, ou usar o [SDK do Dispositivo IoT][SDK do Dispositivo IoT] para conectar o seu dispositivo físico à solução.

Para obter um guia passo a passo para adicionar dispositivos à solução pré-configurada de monitoramento remoto, veja [Dispositivos de Conexão do Iot Suite](iot-suite-connecting-devices.md) e o [Exemplo de SDK de monitoramento remoto em C](https://github.com/Azure/azure-iot-sdks/tree/master/c/serializer/samples/remote_monitoring) que foi projetado para trabalhar com a solução pré-configurada de monitoramento remoto.

### <a name="creating-your-own-simulated-device"></a>Criando seu próprio dispositivo simulado
Um simulador .NET é incluído no código-fonte da solução de monitoramento remota (referenciada acima). Esse simulador é provisionado como parte da solução e pode ser alterado para enviar metadados diferentes, telemetria ou responder a comandos diferentes.

O simulador pré-configurado na solução pré-configurada de monitoramento remoto é um dispositivo mais interessante que emite a telemetria de temperatura e umidade. Você pode modificar o simulador no projeto [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) quando tiver bifurcado o repositório GitHub.

### <a name="available-locations-for-simulated-devices"></a>Locais disponíveis para dispositivos simulados
O conjunto padrão de locais fica em Seattle/Redmond, Washington, Estados Unidos. Você pode alterar esses locais em [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="building-and-using-your-own-physical-device"></a>Compilando e usando seu próprio dispositivo (físico)
Os [SDKs do Azure IoT](https://github.com/Azure/azure-iot-sdks) fornecem bibliotecas para conectar a vários tipos de dispositivo (linguagens e sistemas operacionais) em soluções de IoT.

## <a name="modifying-dashboard-limits"></a>Modificar limites do painel
### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Número de dispositivos exibida na lista suspensa do painel
O padrão é 200. Você pode alterar esse número em [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Número de marcações a serem exibidas no controle do Mapa do Bing
O padrão é 200. Você pode alterar esse número em [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Período do gráfico de telemetria
O padrão é 10 minutos. Você pode alterar esse número em [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="manually-setting-up-application-roles"></a>Configuração manual de funções de aplicativo
O procedimento a seguir descreve como adicionar as funções de aplicativo **Admin** e **ReadOnly** a uma solução pré-configurada. Observe que as soluções pré-configuradas provisionadas no site azureiotsuite.com já incluem as funções **Admin** e **ReadOnly**.

Membros da função **ReadOnly** podem ver o painel e a lista de dispositivos, mas não têm permissão para adicionar dispositivos, alterar os atributos do dispositivo ou enviar comandos.  Membros da função **Admin** têm acesso total a todos os recursos da solução.

1. Acesse o [Portal clássico do Azure][lnk-classic-portal].
2. Selecione **Active Directory**.
3. Clique no nome do locatário AAD que você usou ao provisionar a solução.
4. Clique em **Aplicativos**.
5. Clique no nome do aplicativo que corresponda ao nome da solução pré-configurada. Se você não vir seu aplicativo na lista, selecione **Aplicativos que minha empresa** possui na lista suspensa **Mostrar** e clique na marca de seleção.
6. Na parte inferior da página, clique em **Gerenciar Manifesto** e em **Baixar Manifesto**.
7. Isso baixa um arquivo .json para seu computador local.  Abra esse arquivo para edição em um editor de texto de sua escolha.
8. Na terceira linha do arquivo .json, você encontrará:
   
   ```
   "appRoles" : [],
   ```
   Substitua isso pelo seguinte:
   
   ```
   "appRoles": [
   {
   "allowedMemberTypes": [
   "User"
   ],
   "description": "Administrator access to the application",
   "displayName": "Admin",
   "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
   "isEnabled": true,
   "value": "Admin"
   },
   {
   "allowedMemberTypes": [
   "User"
   ],
   "description": "Read only access to device information",
   "displayName": "Read Only",
   "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
   "isEnabled": true,
   "value": "ReadOnly"
   } ],
   ```
9. Salve o arquivo .json atualizado (você pode substituir o arquivo existente).
10. No Portal de Gerenciamento do Azure, na parte inferior da página, selecione **Gerenciar Manifesto** e, em seguida, **Carregar Manifesto** para carregar o arquivo .json salvo na etapa anterior.
11. Agora você adicionou as funções **Admin** e **ReadOnly** ao seu aplicativo.
12. Para atribuir uma dessas funções a um usuário no diretório, veja [Permissões no site do azureiotsuite.com][lnk-permissions].

## <a name="feedback"></a>Comentários
Há alguma personalização que você gostaria que fosse abordada neste documento? Adicione sugestões de recursos ao [User Voice](https://feedback.azure.com/forums/321918-azure-iot)ou faça comentários sobre este artigo abaixo. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as opções para personalizar as soluções pré-configuradas, confira:

* [Conectar um aplicativo lógico à solução pré-configurada de monitoramento remoto do Azure IoT Suite][lnk-logicapp]
* [Usar telemetria dinâmica com a solução pré-configurada de monitoramento remoto][lnk-dynamic]
* [Metadados de informações de dispositivo na solução pré-configurada de monitoramento remoto][lnk-devinfo]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[SDK do Dispositivo IoT]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com



<!--HONumber=Nov16_HO3-->


