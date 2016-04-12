<properties
   pageTitle="Gerenciamento de contêiner do Serviço de Contêiner do Azure por meio da interface do usuário da Web | Microsoft Azure"
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

O Mesos fornece um ambiente de implantação e dimensionamento de cargas de trabalho clusterizadas e, ao mesmo tempo, abstrai o hardware subjacente. Sobre o Mesos, há uma estrutura que gerencia o agendamento e a execução das cargas de trabalho de computação.

Embora haja estruturas disponíveis para várias cargas de trabalho populares, este documento descreverá como você pode criar e dimensionar implantações de contêiner com o Marathon. Antes de trabalhar nos exemplos, você precisará de um cluster Mesos configurado no Serviço de Contêiner do Azure. Você também precisa ter conectividade remota com esse cluster. Para saber mais sobre esses itens, confira os artigos a seguir.

- [Como implantar um cluster do Serviço de Contêiner do Azure](./container-service-deployment.md)
- [Conexão a um cluster do Serviço de Contêiner do Azure](./container-service-connect.md)

## Explorar a interface do usuário do Mesos

Com um túnel SSH estabelecido, navegue até http://localhost/Mesos. Isso carregará a interface do usuário da Web do Mesos. Na página, você pode obter informações sobre cluster Mesos, como os agentes ativados, o status das tarefas e a disponibilidade dos recursos.

![Criar interface do usuário de implantação 1](media/ui1.png)

## Explorar a interface do usuário do Marathon

Para ver a interface do usuário do Marathon, navegue até http://localhost/Marathon. Nessa tela, você pode iniciar um novo contêiner ou outro aplicativo no cluster Mesos do Serviço de Contêiner do Azure. Você também pode ver informações sobre a execução de aplicativos e de contêineres.

![Criar interface do usuário de implantação 2](media/ui2.png)

## Implantar um contêiner formatado pelo Docker

Para usar o Marathon para iniciar um novo contêiner no cluster Mesos, clique no botão **Criar Aplicativo**. Você usa o formulário **Novo Aplicativo** para definir os parâmetros de aplicativo ou contêiner. Neste exemplo, você implantará um único contêiner Nginx. Insira as seguintes informações. Clique em **Criar** quando terminar.

Campo | Valor
----------------|-----------
ID | nginx
Imagem | nginx
Rede | Com ponte
Porta de contêiner | 80
Porta de host | 80
Protocolo | TCP

![Criar interface do usuário de implantação 3](media/ui3.png)

Novamente na página principal do Marathon, você poderá ver o status da implantação para o contêiner.

![Criar interface do usuário de implantação 4](media/ui4.png)

Se alternar de volta para o aplicativo Mesos (http://localhost/Mesos), agora você verá que uma tarefa, neste caso, um contêiner formatado do Docker, está em execução no cluster Mesos. Você também pode ver o nó de cluster no qual a tarefa está em execução.

![Criar interface do usuário de implantação 5](media/ui5.png)

## Dimensionar seus contêineres

Você também pode usar a interface do usuário da Web do Marathon para dimensionar a contagem de instâncias de um contêiner. Para fazer isso, navegue até a página do Marathon, selecione o contêiner que deseja dimensionar e clique no botão **Dimensionar**. Na caixa de diálogo **Dimensionar Aplicativo**, insira o número da instância de contêiner desejado e selecione **Dimensionar Aplicativo**.

![Criar interface do usuário de implantação 6](media/ui6.png)

Após a conclusão da operação de dimensionamento, você verá várias instâncias da mesma tarefa espalhados pelos agentes Mesos.

![Criar interface do usuário de implantação 7](media/ui8.png)

<!---HONumber=AcomDC_0406_2016-->