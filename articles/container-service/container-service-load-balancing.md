<properties
   pageTitle="Balanceamento de carga de um cluster do Serviço de Contêiner do Azure | Microsoft Azure"
   description="Balanceamento de carga de um cluster do Serviço de Contêiner do Azure."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contêineres, microsserviços, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="rogardle"/>

# Balanceamento de carga de um cluster do Serviço de Contêiner do Azure

Neste artigo, definiremos um front-end da Web em um Serviço de Contêiner do Azure gerenciado pelo DC/SO. Também configuraremos uma Marathon-LB para permitir que você escale verticalmente o aplicativo.

## Pré-requisitos

[Implantar uma instância do Serviço de Contêiner do Azure](container-service-deployment.md) com o tipo de orquestrador DCOS e [garantir que o cliente possa se conectar ao cluster](container-service-connect.md).

## Balanceamento de carga

Há duas camadas de balanceamento de carga no cluster do Serviço de Contêiner que criaremos:

  1. O Azure Load Balancer fornece pontos de entrada públicos (aqueles que os usuários finais acessarão). Isso é fornecido automaticamente pelo Serviço de Contêiner do Azure e, por padrão, é configurado para exibir as portas 80, 443 e 8080.
  2. O Balanceador de Carga do Marathon (marathon-lb) encaminha as solicitações de entrada para as instâncias do contêiner que atendem essas solicitações. Como dimensionamos os contêineres fornecendo nosso serviço Web, o marathon-lb se adapta dinamicamente. Esse balanceador de carga não é fornecido por padrão em seu Serviço de Contêiner, mas é muito fácil de instalar.

## Balanceador de Carga do Marathon

O Balanceador de Carga do Marathon reconfigura dinamicamente baseado nos contêineres que você implantou. Também é resistente à perda de um contêiner ou um agente - se isso ocorrer, o Apache Mesos simplesmente reiniciará o contêiner em outro lugar e o marathon-lb se adaptará.

Para instalar o Balanceador de Carga do Marathon, você pode usar a IU da Web do DC/SO ou a linha de comando.

### Instalar o Marathon-LB usando a IU da Web do DC/SO

  1. Clicar em 'Universo'
  2. Pesquisar 'Marathon-LB'
  3. Clicar em ‘Instalar’

![Instalando o marathon-lb por meio da Interface da Web do DC/SO](./media/dcos/marathon-lb-install.png)

### Instalar o Marathon-LB usando a CLI do DC/SO

Depois de instalar a CLI do DC/SO e assegurar que você pode conectar o cluster, execute o seguinte comando a partir do computador cliente:

```bash
dcos package install marathon-lb
```

## Implantar um Aplicativo Web com Carga Balanceada

Agora que temos o pacote marathon-lb, podemos implantar um servidor Web simples usando a seguinte configuração:

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

  * Defina o valor de `HAProxy_0_VHOST` para o FQDN do balanceador de carga de seus agentes. Isso está no formato`<acsName>agents.<region>.cloudapp.azure.com`. Por exemplo, se você criasse um cluster do Serviço de Contêiner com o nome `myacs`, na região `West US`, o FQDN seria `myacsagents.westus.cloudapp.azure.com`. Você também pode encontrar isso procurando o balanceador de carga com "agente" no nome ao examinar os recursos no grupo de recursos criado para o Serviço de Contêiner no [Portal do Azure](https://portal.azure.com).
  * Defina o servicePort para uma porta > = 10.000. Isso identifica o serviço que está sendo executado no contêiner; o marathon-lb o utiliza para identificar os serviços que deve balancear.
  * Defina o rótulo `HAPROXY_GROUP` para "externo".
  * Defina `hostPort` para 0. Isso significa que o Marathon alocará arbitrariamente uma porta disponível.
  * Defina `instances` para o número de instâncias que você deseja criar. Sempre é possível dimensionar isso posteriormente.

### Implantar usando a IU da Web do DC/SO

  1. Visite a página Marathon em http://localhost/marathon (depois de configurar seu [túnel SSH](container-service-connect.md)) e clique em `Create Appliction`
  2. Na caixa de diálogo `New Application`, clique `JSON Mode` no canto superior direito
  3. Colar o JSON acima no editor
  4. Clicar em `Create Appliction`

### Implantar usando a CLI do DC/SO

Para implantar esse aplicativo com a CLI do DC/SO, basta copiar o JSON acima para um arquivo chamado `hello-web.json` e executar:

```bash
dcos marathon app add hello-web.json
```

## Balanceador de carga do Azure

Por padrão, o Azure Load Balancer expõe as portas 80, 8080 e 443. Se você estiver usando uma dessas três portas (como ocorre no exemplo acima), não haverá mais nada a fazer. Você deve conseguir atingir o FQDN do balanceador de carga do agente, e cada vez que atualizar, encontrará um dos três servidores Web em round-robin. Se, no entanto, você usar uma porta diferente, será necessário adicionar uma regra de round robin e uma investigação ao balanceador de carga do Azure à porta usada. Você pode fazer isso na [CLI do Azure](../xplat-cli-azure-resource-manager.md) com os comandos `azure lb rule create` e `azure lb probe create`. Você também pode fazer isso usando o Portal do Azure.


## Cenários adicionais

Você pode ter um cenário onde usa domínios diferentes para expor serviços diferentes. Por exemplo:

meudominio1.com -> Azure LB:80 -> marathon-lb:10001 -> meuconteiner1:33292 meudomínio2.com -> Azure LB:80 -> marathon-lb:10002 -> meuconteiner2:22321

Para tanto, confira os [hosts virtuais](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), que fornecem uma maneira de associar os domínios aos caminhos específicos do marathon-lb.

Como alternativa, você pode expor portas diferentes e remapeá-las para o serviço correto por trás do marathon-lb. Por exemplo:

Azure lb:80 -> marathon-lb:10001 -> meuconteiner:233423 Azure lb:8080 -> marathon-lb:1002 -> meuconteiner2:33432


## Próximas etapas

Consulte a documentação do DC/SO para obter mais informações sobre o [marathon-lb](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/).

<!---HONumber=AcomDC_0713_2016-->