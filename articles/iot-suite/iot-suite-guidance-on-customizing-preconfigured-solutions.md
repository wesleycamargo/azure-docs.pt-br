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

O código-fonte para as soluções pré-configuradas é fornecido para demonstrar os padrões e as práticas usadas para implementar a funcionalidade de ponta a ponta de uma solução IoT usando o Azure IoT Suite. Você pode encontrar mais informações sobre como compilar e implantar as soluções em repositórios GitHub.

## Alterando as regras predefinidas

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

> [AZURE.NOTE] O painel de monitoramento remoto depende de dados específicos, por isso, alterar os trabalhos pode fazer com que o painel falhe.

## Adicionando suas próprias regras

Além de alterar os trabalhos de Stream Analytics do Azure, você pode usar o portal do Azure para adicionar novos trabalhos ou adicionar novas consultas para trabalhos existentes.

## Personalizando dispositivos

Uma das atividades mais comuns de extensão é o trabalho com dispositivos específicos ao seu cenário. Há vários métodos para trabalhar com dispositivos. Esses métodos incluem alterar um dispositivo simulado de acordo com seu cenário, ou usar o [SDK do Dispositivo IoT][] para conectar o seu dispositivo físico à solução.

Para ter um guia passo a passo para adicionar dispositivos à solução pré-configurada de monitoramento remoto, consulte [Dispositivos de conexão do Pacote IoT](iot-suite-connecting-devices.md).

### Criando seu próprio dispositivo simulado

Um simulador .NET é incluído no código-fonte da solução de monitoramento remota (referenciada acima). Esse simulador é provisionado como parte da solução e pode ser alterado para enviar metadados diferentes, telemetria ou responder a comandos diferentes.

O simulador pré-configurado na solução pré-configurada de monitoramento remoto é um dispositivo mais interessante que emite a telemetria de temperatura e umidade. Você pode modificar o simulador no projeto [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) quando tiver bifurcado o repositório GitHub do projeto.

Além disso, o Azure IoT fornece um [Exemplo SDK C](https://github.com/Azure/azure-iot-sdks/tree/master/c/serializer/samples/remote_monitoring) que foi projetado para funcionar com a solução pré-configurada de monitoramento remoto.

### Compilando e usando seu próprio dispositivo (físico)

Os [SDKs do Azure IoT](https://github.com/Azure/azure-iot-sdks) fornecem bibliotecas para conectar a vários tipos de dispositivo (linguagens e sistemas operacionais) em soluções de IoT.

## Configuração manual de funções de aplicativo

O procedimento a seguir descreve como adicionar as funções de aplicativo **Admin** e **ReadOnly** a uma solução pré-configurada. Observe que as soluções pré-configuradas provisionadas no site azureiotsuite.com já incluem as funções **Admin** e **ReadOnly**.

Membros da função **ReadOnly** podem ver o painel e a lista de dispositivos, mas não têm permissão para adicionar dispositivos, alterar os atributos do dispositivo ou enviar comandos. Membros da função **Admin** têm acesso completo a toda a funcionalidade na solução.

1. Vá para o [portal clássico do Azure][lnk-classic-portal].

2. Selecione **Active Directory**.

3. Clique no nome do locatário AAD que você usou ao provisionar a solução.

4. Clique em **Aplicativos**.

5. Clique no nome do aplicativo que corresponda ao nome da solução pré-configurada. Se você não visualizar seu aplicativo na lista, selecione **Aplicativos que minha empresa possui** na lista suspensa **Mostrar** e clique na marca de seleção.

6.  Na parte inferior da página, clique em **Gerenciar Manifesto** e então em **Baixar Manifesto**.

7. Isso baixa um arquivo .json para seu computador local. Abra esse arquivo para edição em um editor de texto de sua escolha.

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

10.  No Portal de Gerenciamento do Azure, na parte inferior da página, selecione **Gerenciar Manifesto** e, em seguida, **Carregar Manifesto** para carregar o arquivo .json salvo na etapa anterior.

11. Agora você adicionou as funções **Admin** e **ReadOnly** ao seu aplicativo.

12. Para atribuir uma dessas funções para um usuário no seu diretório, consulte [Permissões no site do azureiotsuite.com][lnk-permissions].

## Comentários

Tem alguma personalização que você gostaria de ver abordada neste documento? Adicione as sugestões de recursos à [Voz do Usuário](https://feedback.azure.com/forums/321918-azure-iot) ou faça comentários sobre este artigo abaixo.

## Próximas etapas

Para obter mais informações sobre dispositivos IoT, consulte o [Site de desenvolvedores do Azure IoT](https://azure.microsoft.com/develop/iot/) para encontrar links e documentação.

[SDK do Dispositivo IoT]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-classic-portal]: https://manage.windowsazure.com

<!-----------HONumber=AcomDC_0330_2016-->