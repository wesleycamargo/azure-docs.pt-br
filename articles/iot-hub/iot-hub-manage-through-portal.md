<properties
	 pageTitle="Usar o portal do Azure para gerenciar Hub IoT | Microsoft Azure"
	 description="Uma visão geral de como criar e gerenciar os Hubs IoT do Azure por meio do portal do Azure"
	 services="iot-hub"
	 documentationCenter=""
	 authors="nasing"
	 manager="timlt"
	 editor=""/>

<tags
	 ms.service="iot-hub"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.tgt_pltfrm="na"
	 ms.workload="na"
	 ms.date="06/28/2016"
	 ms.author="nasing"/>

# Gerenciar Hubs IoT por meio do portal do Azure

## Introdução

Este artigo descreve como começar a usar o Hub IoT do Azure por meio do portal do Azure, como localizar Hubs IoT e como criar e gerenciar Hubs IoT.

## Onde encontrar Hubs IoT:

É possível encontrar Hubs IoT em vários lugares.

1. **+ Novo** : o **Hub IoT do Azure** é um serviço e pode ser encontrado na categoria **Internet das Coisas** em **+ Novo**, semelhante a outros serviços.

2. Os Hubs IoT também podem ser acessados por meio do Marketplace como o serviço hero em **Internet das Coisas**.

## Crie um hub IoT

Você pode criar um Hub IoT usando os métodos a seguir.

1. A criação de um Hub IoT por meio da opção **+ Novo** leva à folha mostrada na captura de tela a seguir. As etapas para a criação do Hub IoT por meio deste método, bem como por meio do marketplace são idênticas.

2. Criação de um Hub IoT por meio do Marketplace: clicar em **Criar** abre uma folha idêntica à folha anterior da experiência de **+Novo**. Há várias etapas envolvidas na criação de um Hub IoT listadas nas próximas seções.

### Escolher o nome do Hub IoT

Para criar um Hub IoT, você deve dar um nome ao hub. Observe que esse nome deve ser exclusivo entre os hubs. A eliminação de duplicação de hubs não é permitida no back-end e, portanto, é recomendável que esse hub tenha o nome mais exclusivo possível.

### Escolher a camada de preços

Você pode escolher entre quatro camadas: **Grátis**, **Padrão 1**, **Padrão 2** e **Padrão S3**. O tipo gratuito só permite a conexão de 500 dispositivos ao Hub IoT e até 8000 mensagens por dia.

**Padrão S1**: a edição S1 de Hubs IoT foi projetada para soluções IoT com um grande número de dispositivos gerando quantidades relativamente pequenas de dados por dispositivo. Cada unidade da edição S1 permite até 400.000 mensagens por dia entre todos os dispositivos conectados.

**Padrão S2**: a edição S2 de Hub IoT foi projetada para soluções IoT nas quais os dispositivos geram grandes quantidades de dados. Cada unidade da edição S2 permite a transmissão de até seis milhões de mensagens por dia entre todos os dispositivos conectados.

**S3 Padrão**: a edição S3 do Hub IoT é projetada para soluções de IoT que geram grandes quantidades de dados. Cada unidade da edição S3 permite a transmissão de até 300 milhões de mensagens por dia entre todos os dispositivos conectados.

![][4]

> [AZURE.NOTE] O Hub IoT só permite um hub gratuito por assinatura.

### Unidades do Hub IoT

Uma unidade do Hub IoT inclui um certo número de mensagens por dia.Portanto, escolher o número de unidades IoT significa que o número total de mensagens com suporte para esse hub será o número de unidades multiplicado pelo número de mensagens por dia desse tipo. Por exemplo, se você quiser que o Hub IoT dê suporte à entrada de 700.000 mensagens, escolha duas unidades do tipo S1.

### Dispositivo para partições de nuvem e grupo de recursos

Você pode alterar o número de partições para um Hub IoT. As partições padrão são definidas como 4; no entanto, você pode escolher um número diferente de partições em uma lista suspensa.

Para grupos de recursos, não será necessário criar explicitamente um grupo de recursos vazio. Ao criar um novo recurso, você pode optar por criar um novo grupo de recursos ou usar um grupo de recursos existente.

![][5]

### Escolher assinaturas

O Hub IoT do Azure mostra automaticamente a lista de assinaturas à qual a conta do usuário está vinculada. Você pode escolher uma dessas opções para associar o Hub IoT à assinatura.

### Escolher o local

A opção de local oferece uma lista das regiões em que o Hub IoT é oferecido. O Hub IoT está disponível para implantação nos seguintes locais: Leste da Austrália, Sudeste da Austrália, Leste da Ásia, Sudeste da Ásia, Norte da Europa, Oeste da Europa, Leste do Japão, Oeste do Japão, Leste dos EUA, Oeste dos EUA.

### Criar o Hub IoT

Quando todas as etapas anteriores forem concluídas, o Hub IoT estará pronto para ser criado. Clique em **Criar** para iniciar o processo de back-end de criação desse Hub IoT com as opções específicas e o implantar no local especificado.

Observe que pode levar alguns minutos para que o Hub IoT seja criado, já que a implantação de back-end é demorada nos servidores do local apropriado.

## Alterar as configurações do Hub IoT

Você poderá alterar as configurações de um Hub IoT existente depois que ele for criado. Clique no nome do Hub IoT para abrir a página de configurações.

![][8]

**Políticas de Acesso Compartilhado**: são as políticas que definem as permissões para que dispositivos e serviços se conectem ao Hub IoT. Você pode acessar essas políticas clicando em **Políticas de Acesso Compartilhado** em **Configurações**. Nesta folha, você pode modificar as políticas existentes ou adicionar uma nova política.

### Criar uma nova política

- Clique em **Adicionar** para abrir uma folha em que você poderá inserir o nome da nova política e as permissões que deseja associar a essa política, como mostrado na figura a seguir.

	Há várias permissões que podem ser associadas a essas políticas compartilhadas. As duas primeiras políticas, **Leitura do Registro** e **Gravação do Registro**,concedem direitos de acesso de leitura e gravação ao repositório de identidade do dispositivo ou ao registro de identidade. Observe que a escolha da opção de gravação escolhe automaticamente a opção de leitura.

 	A política de **Conexão de serviço** concede permissão para acessar os pontos de extremidade na nuvem, como o grupo de consumidores para serviços que se conectam ao hub IoT, enquanto a política **Conexão de dispositivo** concede permissões para enviar e receber mensagens nos pontos de extremidade no dispositivo do hub IoT.

- Clique em **Criar** para adicionar essa política recém-criada à lista existente.

![][10]

## Mensagens

Clique nas políticas do **Sistema de Mensagens** para exibir uma lista de propriedades do sistema de mensagens para o Hub IoT que está sendo modificado. Existem dois tipos principais de propriedades que você pode modificar ou copiar: **Nuvem para o Dispositivo** e **Dispositivo para a Nuvem**.

- **Configurações de Nuvem para o Dispositivo**: tem duas subconfigurações: **TTL (vida útil) da Nuvem para o Dispositivo** e **Tempo de retenção** para as mensagens. Quando o Hub IoT é criado, essas duas configurações são criadas com um valor padrão de uma hora. No entanto, você pode personalizá-los usando os controles deslizantes ou simplesmente digitar os valores.

- **Configurações de Dispositivo para a Nuvem**: tem várias subconfigurações, algumas das quais são nomeadas/atribuídas quando o Hub IoT é criado e só poderão ser copiadas para outras subconfigurações que sejam personalizáveis. Essas configurações estão listadas na próxima seção.

**Partições**: esse valor é definido quando o Hub IoT é criado e pode ser alterado por meio dessa configuração.

**Nome compatível e ponto de extremidade do Hub de Eventos**: quando o Hub IoT é criado, um Hub de Eventos é criado internamente e talvez tenha de ser acessado sob determinadas circunstâncias. O nome e o ponto de extremidade desse Hub de Eventos não podem ser personalizados, mas estão disponíveis para uso por meio do botão **Copiar**.

**Tempo de Retenção**: definido como um dia por padrão, mas pode ser personalizado para outros valores usando a lista suspensa. Observe que esse valor está em dias para Dispositivo para a Nuvem e não em horas, que é a configuração semelhante para Nuvem para o Dispositivo.

**Grupos de Consumidores**: os Grupos de Consumidores são uma configuração semelhante a outros sistemas de mensagens que podem ser usados para extrair dados de maneiras específicas para a conexão de outros aplicativos ou serviços ao Hub IoT. Todos os Hub IoT são criados com um grupo de consumidores padrão. No entanto, você pode adicionar ou excluir grupos de consumidores em seus Hubs IoT.

> [AZURE.NOTE] O grupo de consumidores padrão não pode ser editado ou excluído.

![][11]

## Upload de arquivos

Para usar o recurso de upload de arquivo do Hub IoT, primeiro você deve associar uma conta de Armazenamento do Azure ao hub. Selecione as configurações de **Upload de arquivo** para exibir uma lista de propriedades de upload de arquivo para o hub IoT que está sendo modificado.

**Contêiner de armazenamento**: use o portal para selecionar um contêiner de blob em uma conta de armazenamento na assinatura atual para ser associado ao Hub IoT. Se necessário, você pode criar uma nova conta de armazenamento na folha **Contas de armazenamento** e um novo contêiner de blob na folha **Contêineres**. O Hub IoT gera automaticamente os URIs de SAS com permissões de gravação para esse contêiner de blob para dispositivos a serem usados ao carregar arquivos.

![][14]

**Receber notificações para os arquivos carregados**: habilitar ou desabilitar notificações de upload de arquivo por meio de um botão de opção.

**TTL de SAS**: essa configuração é a vida útil dos URIs de SAS retornados para o dispositivo pelo Hub IoT. Definido como uma hora por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

**TTL de configurações de notificação de arquivo padrão**: a vida útil de uma notificação de upload de arquivo antes de sua expiração. Definido como um dia por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

**Contagem de entrega máxima de notificação de arquivo**: o número de vezes que o Hub IoT tentará entregar uma notificação de upload de arquivo. Definido como 10 por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

![][13]

## Preços e dimensionamento

O preço de um Hub IoT existente pode ser alterado por meio das configurações de **Preço**, com as seguintes exceções:

- Na implementação atual, um Hub IoT com um SKU gratuito não pode trocar camadas com um dos SKUs pagos ou vice-versa.
- Só pode haver um Hub IoT de camada gratuita na assinatura.

![][12]

Mudar de uma camada alta (S2 ou S3) para uma camada baixa (S1 ou S2) só é permitido quando o número de mensagens enviadas naquele dia não está em conflito. Por exemplo, se o número de mensagens por dia exceder 400.000, a camada para o hub IoT poderá ser alterada, mas se você alterar para a camada S1, o hub será limitado para esse dia.

## Excluir o Hub IoT

Você pode navegar até o Hub IoT que deseja excluir clicando em **Procurar** e escolhendo o hub apropriado para excluir. Clique no botão **Excluir** abaixo do nome do hub para excluí-lo.

## Próximas etapas

Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

- [Gerenciamento em massa de dispositivos IoT][lnk-bulk]
- [Métricas de uso][lnk-metrics]
- [Monitoramento de operações][lnk-monitor]
- [Gerenciar o acesso ao Hub IoT][lnk-itpro]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

- [Projetar sua solução][lnk-design]
- [Guia do desenvolvedor][lnk-devguide]
- [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
- [Simular um dispositivo com o SDK do Gateway][lnk-gateway]
- [Proteger sua solução de IoT desde o início][lnk-securing]


  [4]: ./media/iot-hub-manage-through-portal/create-iothub.png
  [5]: ./media/iot-hub-manage-through-portal/location1.png
  [8]: ./media/iot-hub-manage-through-portal/portal-settings.png
  [10]: ./media/iot-hub-manage-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-manage-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-manage-through-portal/pricing-error.png
  [13]: ./media/iot-hub-manage-through-portal/file-upload-settings.png
  [14]: ./media/iot-hub-manage-through-portal/file-upload-container-selection.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-itpro]: iot-hub-itpro-info.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0727_2016-->