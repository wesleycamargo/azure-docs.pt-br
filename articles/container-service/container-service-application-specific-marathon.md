<properties
   pageTitle="Serviço do Marathon específico do usuário ou do aplicativo | Microsoft Azure"
   description="Criar um serviço do Marathon específico do usuário ou do aplicativo"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contêineres, Marathon, microsserviços, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# Criar um serviço do marathon específico do usuário ou do aplicativo

O Serviço de Contêiner do Azure fornece um conjunto de servidores mestres no qual podemos pré-configurar o Apache Mesos e o Marathon. Eles podem ser usados para orquestrar seus aplicativos no cluster, mas é melhor não usar os mestres para essa finalidade. Por exemplo, ajustar a configuração do Marathon exige o logon nos próprios mestres e fazer as alterações; isso incentiva a existência de servidores mestres exclusivos que sejam um pouco diferentes do padrão e que precisam ser cuidados e gerenciados de forma independente. Além disso, a configuração exigida por uma equipe pode não ser a configuração ideal para outra equipe. Neste artigo, explicaremos como adicionar um serviço do Marathon específico do usuário ou do aplicativo.

Como esse serviço pertencerá a um único usuário ou a uma única equipe, poderá ser configurado de qualquer forma desejada. Além disso, o Serviço de Contêiner do Azure garante que o serviço continuará a ser executado; se o serviço falhar, o Serviço de Contêiner do Azure o reiniciará para você. Na maioria das vezes, você nem mesmo perceberá o tempo de inatividade.

## Pré-requisitos

[Implantar uma instância do Serviço de Contêiner do Azure](container-service-deployment.md) com o tipo de orquestrador DCOS, [garantir que o cliente possa se conectar ao cluster](container-service-connect.md) e[AZURE.INCLUDE [instalar a CLI do DC/OS](../../includes/container-service-install-dcos-cli-include.md)].

## Criação de um serviço do Marathon específico do usuário ou do aplicativo.

Comece pela criação de um arquivo de configuração JSON que defina o nome do serviço de aplicativo que você deseja criar. Aqui usamos `marathon-alice` como o nome da estrutura. Salve o arquivo como algo como `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Em seguida, use a CLI do DC/OS para instalar a instância do Marathon com as opções definidas no arquivo de configuração:

```bash
dcos package install --options=marathon-alice.json marathon
```

Agora você deverá ver seu serviço `marathon-alice` em execução na guia de serviços da interface do usuário do DC/OS. A interface do usuário estará `http://<hostname>/service/marathon-alice/` caso você queira acessá-la diretamente.

## Configuração da CLI do DC/OS para acessar o serviço

Opcionalmente, você pode configurar a CLI do DC/OS para acessar esse novo serviço, definindo a propriedade `marathon.url` para apontar para a instância `marathon-alice` da seguinte maneira:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Você usar o comando `dcos config show` para verificar em qual instância do Marathon sua CLI está trabalhando e pode reverter usando seu serviço mestre do Marathon com o comando `dcos config unset marathon.url`.

<!---HONumber=AcomDC_0525_2016-->