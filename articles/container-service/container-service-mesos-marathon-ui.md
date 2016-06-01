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

O DC/OS fornece um ambiente de implantação e dimensionamento de cargas de trabalho clusterizadas e, ao mesmo tempo, abstrai o hardware subjacente. Sobre o DC/OS, há uma estrutura que gerencia o agendamento e a execução das cargas de trabalho de computação.

Embora haja estruturas disponíveis para várias cargas de trabalho populares, este documento descreverá como você pode criar e dimensionar implantações de contêiner com o Marathon. Antes de trabalhar nos exemplos, você precisará de um cluster DC/OS configurado no Serviço de Contêiner do Azure. Você também precisa ter conectividade remota com esse cluster. Para saber mais sobre esses itens, confira os artigos a seguir.

- [Como implantar um cluster do Serviço de Contêiner do Azure](container-service-deployment.md)
- [Conexão a um cluster do Serviço de Contêiner do Azure](container-service-connect.md)

## Explorar a interface do usuário do DC/OS

Com um túnel SSH (Secure Shell) estabelecido, navegue até http://localhost/. Isso carregará a interface do usuário da Web do DC/OS e mostrará informações sobre o cluster, como recursos usados, agentes ativos e serviços em execução.

![](media/dcos/dcos2.png)

## Explorar a interface do usuário do Marathon

Para ver a interface do usuário do Marathon, navegue até http://localhost/Marathon. Nessa tela, você pode iniciar um novo contêiner ou outro aplicativo no cluster DC/OS do Serviço de Contêiner do Azure. Você também pode ver informações sobre a execução de aplicativos e de contêineres.

![](media/dcos/dcos3.png)

## Implantar um contêiner formatado pelo Docker

Para implantar um novo contêiner usando o Marathon, clique no botão **Criar Aplicativo** e insira as informações a seguir no formulário. Clique em **Criar Aplicativo** quando estiver pronto.

Campo | Valor
----------------|-----------
ID | nginx
Imagem | nginx
Rede | Com ponte
Porta de host | 80
Protocolo | TCP

![](media/dcos/dcos4.png)

![](media/dcos/dcos5.png)

![](media/dcos/dcos6.png)

Se você quiser mapear estaticamente a porta do contêiner para uma porta no agente, use o 'Modo JSON'. Para fazer isso, alterne o assistente de Novo Aplicativo para o Modo JSON usando o botão de alternância e insira o seguinte na seção 'portMappings' da definição do aplicativo. Este exemplo associa a porta 80 do contêiner à porta 80 do agente DC/OS. Esse assistente poderá sair do Modo JSON assim que a alteração for feita.

```none
“hostPort”: 80,
```

![](media/dcos/dcos13.png)

O cluster DC/OS é implantado com um conjunto de agentes privados e públicos. Para acessar o aplicativo da Internet, eles devem ser implantados em um agente de público. Para fazer isso, selecione a guia 'opcional' do assistente de Novo Aplicativo e insira 'slave\_public' em Funções de Recurso Aceitas'.

![](media/dcos/dcos14.png)

Novamente na página principal do Marathon, você poderá ver o status da implantação para o contêiner.

![](media/dcos/dcos7.png)

Se alternar de volta para o aplicativo DC/OS (http://localhost/), agora você verá que uma tarefa, neste caso, um contêiner formatado do Docker, está em execução no cluster DC/OS.

![](media/dcos/dcos8.png)

Você também pode ver o nó de cluster no qual a tarefa está em execução.

![](media/dcos/dcos9.png)

## Dimensionar seus contêineres

A interface do usuário do Marathon pode ser usada para dimensionar a contagem de instâncias de um contêiner. Para fazer isso, navegue até a página do Marathon, selecione o contêiner que deseja dimensionar e clique no botão **Dimensionar**. Na caixa de diálogo **Dimensionar Aplicativo**, insira o número da instância de contêiner desejado e selecione **Dimensionar Aplicativo**.

![](media/dcos/dcos10.png)

Após a conclusão da operação de dimensionamento, você verá várias instâncias da mesma tarefa espalhados pelos agentes DC/OS.

![](media/dcos/dcos11.png)

![](media/dcos/dcos12.png)

## Próximas etapas

[Trabalhar com a API do Marathon e DC/OS](container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0525_2016-->