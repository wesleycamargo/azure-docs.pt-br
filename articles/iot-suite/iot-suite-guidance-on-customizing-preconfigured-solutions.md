<properties
	pageTitle="Personalizando soluções pré-configuradas | Microsoft Azure"
	description="Fornece orientação sobre como personalizar as soluções pré-configuradas do Pacote IoT do Azure."
	services=""
    suite="iot-suite"
	documentationCenter=".net"
	authors="stevehob"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/02/2016"
     ms.author="stevehob"/>

# Personalizar soluções pré-configuradas

As soluções pré-configuradas fornecidas com o Pacote IoT do Azure demonstram os serviços no pacote trabalhando juntos para fornecer uma solução de ponta a ponta. Do ponto de partida, há uma variedade de lugares em que você pode estender e personalizar a solução para cenários específicos. As seções a seguir descrevem esses pontos comuns de personalização.

## Localizando o código-fonte

O código-fonte para a solução pré-configurada está disponível no GitHub nos seguintes repositórios:

- Monitoramento remoto: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- Manutenção preditiva: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

O código-fonte para as soluções pré-configuradas é fornecido para demonstrar os padrões e as práticas usadas para implementar a funcionalidade de ponta a ponta de uma solução IoT usando o Azure IoT Suite. A orientação sobre a criação e a implantação de linha de comando está no wiki do GitHub para cada solução pré-configurada:

- [Wiki de monitoramento remoto](https://github.com/Azure/azure-iot-remote-monitoring/wiki)
- [Wiki de manutenção preditiva](https://github.com/Azure/azure-iot-predictive-maintenance/wiki)

## Gerenciar as permissões em uma solução pré-configurada
O portal de solução para cada solução pré-configurada é criado como um novo aplicativo do Azure Active Directory. Você pode gerenciar as permissões para o portal de solução (aplicativo AAD) da seguinte maneira:

1. Abra no [portal clássico do Azure](https://manage.windowsazure.com)
2. Navegue até o aplicativo AAD selecionando **Aplicativos que minha empresa possui** e clique na marca de seleção.
3. Navegue até **Usuários** e atribua membros no seu locatário do Azure Active Directory a uma função. 

Por padrão, o aplicativo é provisionado com as funções **Administrador**, **Somente Leitura** e **Somente Leitura Implícita**. **Somente leitura implícita** é concedida aos usuários que são membros do locatário do Azure Active Directory, mas não têm função atribuída. Você pode modificar o [RolePermissions.cs](https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs) depois de bifurcar o repositório GitHub e reimplantar sua solução.

## Alterando as regras predefinidas

A solução de monitoramento remoto inclui três trabalhos do [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/) para implementar a lógica de regras, a telemetria e informações de dispositivo exibidas para a solução.

Os três trabalhos de Stream Analytics e sua sintaxe estão descritos em detalhes na [Orientação passo a passo da solução pré-configurada de monitoramento remoto](iot-suite-remote-monitoring-sample-walkthrough.md).

Você pode editar esses trabalhos diretamente para alterar a lógica ou adicionar lógica específica para seu cenário. Você pode encontrar os trabalhos do Stream Analytics da seguinte maneira:
 
1. Vá para o [portal do Azure](https://portal.azure.com).
2. Navegue até o grupo de recursos com o mesmo nome da sua solução IoT. 
3. Selecione o trabalho de Stream Analytics do Azure que você deseja modificar. 
4. Interrompa o trabalho selecionando **Parar**no conjunto de comandos. 
5. Edite as entradas, consulta e saídas.

    É uma modificação simples a fim de alterar a consulta para o trabalho **Regras**, que passa a usar um **"<"** em vez de um **">"**. O portal de solução ainda mostrará **">"** quando a regra for editada, mas você perceberá que o comportamento é invertido devido à alteração no trabalho subjacente.

6. Iniciar o trabalho

> [AZURE.NOTE] O painel de monitoramento remoto depende de dados específicos, por isso, alterar os trabalhos pode fazer com que o painel falhe.

## Adicionando suas próprias regras

Além de alterar os trabalhos de Stream Analytics do Azure, você pode usar o portal do Azure para adicionar novos trabalhos ou adicionar novas consultas para trabalhos existentes.

## Personalizando dispositivos

Uma das atividades mais comuns de extensão é o trabalho com dispositivos específicos ao seu cenário. Há vários métodos para trabalhar com dispositivos. Esses métodos incluem alterar um dispositivo simulado de acordo com seu cenário, ou usar o [SDK do Dispositivo IoT][] para conectar o seu dispositivo físico à solução.

Para ter um guia passo a passo para adicionar dispositivos à solução pré-configurada de monitoramento remoto, consulte [Dispositivos de conexão do Pacote IoT](iot-suite-connecting-devices.md).

### Criando seu próprio dispositivo simulado

Um simulador .NET é incluído no código-fonte da solução de monitoramento remota (referenciada acima). Esse simulador é provisionado como parte da solução e pode ser alterado para enviar metadados diferentes, telemetria ou responder a comandos diferentes.

O simulador pré-configurado na solução pré-configurada de monitoramento remoto é um dispositivo mais legal que emite a telemetria de temperatura e umidade. Você pode modificar o simulador no projeto [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) quando tiver bifurcado o repositório GitHub do projeto.

Além disso, o Azure IoT fornece um [Exemplo SDK C](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring) que foi projetado para funcionar com a solução pré-configurada de monitoramento remoto.

### Compilando e usando seu próprio dispositivo (físico)

Os [SDKs do Azure IoT](https://github.com/Azure/azure-iot-sdks) fornecem bibliotecas para conectar a vários tipos de dispositivo (linguagens e sistemas operacionais) em soluções de IoT.

## Próximas etapas

Tem alguma personalização que você gostaria de ver abordada neste documento? Adicione as sugestões de recursos no [User Voice](https://feedback.azure.com/forums/321918-azure-iot), faça comentários sobre este artigo abaixo ou envie-no um email para iotsolhelp@microsoft.com.

Para obter mais informações sobre dispositivos IoT, consulte o [Site de desenvolvedores do Azure IoT](https://azure.microsoft.com/develop/iot/) para encontrar links e documentação.

[SDK do Dispositivo IoT]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/

<!---HONumber=AcomDC_0309_2016-->