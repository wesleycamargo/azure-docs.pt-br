<properties
   pageTitle="Gerenciamento de contêiner do ACS por meio da interface do usuário da Web"
   description="Implante contêineres para um serviço de cluster do Serviço de Contêiner do Azure usando a interface do usuário da Web do Marathon."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contêineres, Microsserviços, Mesos, Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="nepeters"/>
   
# Gerenciamento de contêiner por meio da interface do usuário da Web
   
O Mesos fornece um ambiente de implantação e dimensionamento de carga de trabalho clusterizada e, ao mesmo tempo, abstrai o hardware subjacente. Sobre o Mesos, uma estrutura gerencia o agendamento e a execução da carga de trabalho de computação. Embora haja estruturas disponíveis para várias cargas de trabalho populares, este documento fornecerá detalhes sobre a criação e o dimensionamento de implantações de contêiner com o Marathon. Antes de trabalhar com os exemplos, você precisará de um cluster Mesos configurado no ACS e de conectividade remota para esse cluster. Para obter mais informações sobre esses itens, confira os artigos a seguir.

- [Como implantar um cluster do Serviço de Contêiner do Azure](./container-service-deployment.md) 
- [Como se conectar a um Cluster ACS](./container-service-connect.md)

## Explorar a interface do usuário do Mesos

Com um Túnel SSH estabelecido, navegue até http://localhost/Mesos. Isso carregará a interface do usuário da Web do Mesos. Na página, você pode obter informações sobre cluster Mesos, como os agentes ativados, o status das tarefas e a disponibilidade dos recursos.

![Criar implantação](media/ui1.png)

## Explorar a interface do usuário do Marathon

Para ver a interface do usuário do Marathon, navegue até http://localhost/Marathon. Nessa tela, você pode iniciar um novo contêiner ou outro aplicativo no cluster Mesos do ACS e também ver informações sobre a execução de contêineres e aplicativos.

![Criar implantação](media/ui2.png)

## Implantar um contêiner do Docker

Para usar o Marathon para iniciar um novo contêiner no cluster Mesos, clique no botão `Create Application`. O formulário Novo Aplicativo é usado para definir os parâmetros de aplicativo ou contêiner. Neste exemplo, um único contêiner Nginx será implantado. Insira as seguintes informações. Clique em criar' quando tiver concluído.
 
Campo | Valor
----------------|-----------
ID | nginx
Imagem | nginx
Rede | Com ponte
Porta de contêiner | 80
Porta de host | 80
Protocolo | TCP

![Criar implantação](media/ui3.png)

Na página principal do Marathon, o status da implantação para o contêiner pode ser visto.

![Criar implantação](media/ui4.png)

Se alternar de volta para o aplicativo Mesos (http://localhost/Mesos), agora você verá que uma tarefa, neste caso, um contêiner do Docker, está em execução no cluster Mesos. Você também pode ver o nó de cluster no qual a tarefa está em execução.

![Criar implantação](media/ui5.png)

## Dimensionar um contêiner do Docker

A interface do usuário da Web do Marathon também pode ser usada para dimensionar a contagem de instâncias de um contêiner. Para fazer isso, navegue até a página do Marathon, selecione o contêiner que deseja dimensionar e clique no botão `scale`. Na janela Dimensionar Aplicativo, insira o número da instância de contêiner desejado e selecione `Scale Application`.

![Criar implantação](media/ui6.png)

Quando a operação de dimensionamento for concluída, você verá várias instâncias da mesma tarefa espalhados pelos agentes Mesos.

![Criar implantação](media/ui8.png)

<!---HONumber=AcomDC_0218_2016-->