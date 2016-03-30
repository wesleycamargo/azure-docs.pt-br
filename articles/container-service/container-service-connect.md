<properties
   pageTitle="Conectar a um cluster do Serviço de Contêiner do Azure | Microsoft Azure"
   description="Conecte-se a um cluster do Serviço de Contêiner do Azure usando um Túnel SSH."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
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
   ms.author="rogardle"/>


# Conectar a um cluster do Serviço de Contêiner do Azure

Os clusters Mesos e Swarm implantados pelo Serviço de Contêiner do Azure expõem os pontos de extremidade REST. No entanto, esses pontos de extremidade não estão abertos para o mundo exterior. Para gerenciar esses pontos de extremidade, você deve criar um túnel Secure Shell (SSH). Após o estabelecimento de um túnel SSH, você pode executar comandos em relação aos pontos de extremidade do cluster e exibir a interface do usuário do cluster por meio de um navegador em seu próprio sistema. Este documento orienta você ao longo da criação de um túnel SSH do Linux, do OSX e do Windows.

>[AZURE.NOTE] Você pode criar uma sessão SSH com um sistema de gerenciamento de cluster. No entanto, isso não é recomendado. Trabalhar diretamente em um sistema de gerenciamento acarreta o risco de alterações de configuração acidentais.

## Criar um túnel SSH no Linux ou no OS X

A primeira coisa que você faz quando cria um túnel SSH no Linux ou no OS X é localizar o nome DNS público de mestres de balanceamento de carga. Para fazer isso, expanda o grupo de recursos, de modo que todos os recursos sejam exibidos. Localize e selecione o endereço IP público do mestre. Isso abrirá uma folha com informações sobre o endereço IP público, o que incluirá o nome DNS. Salve o nome para uso posterior. <br />

![Nome DNS público](media/pubdns.png)

Agora, abra um shell e execute o seguinte comando, em que:

**PORTA** é a porta do ponto de extremidade que você deseja expor. Para o Swarm, é 2375. Para o Mesos, use a porta 80. **NOME DE USUÁRIO** é o nome de usuário fornecido quando você implantou o cluster. **PREFIXODEDNS** é o prefixo de DNS que você forneceu ao implantar o cluster. **REGIÃO** é a região em que o grupo de recursos está localizado.

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Túnel Mesos

Para abrir um túnel para pontos de extremidade relacionados a Mesos, execute um comando semelhante ao seguinte.

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Agora você pode acessar os pontos de extremidade relacionados ao Mesos:

- Mesos: `http://localhost/mesos`
- Marathon: `http://localhost/marathon`
- Chronos: `http://localhost/chronos`

Da mesma forma, as APIs rest para cada aplicativo podem ser acessadas por este túnel: Marathon -- `http://localhost/marathon/v2`. Para saber mais sobre as diversas APIs disponíveis, veja a documentação do Mesosphere para a [API do Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html). Veja a [API do Chronos](https://mesos.github.io/chronos/docs/api.html) e a documentação do Apache para a [API do Agendador Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

### Túnel do Swarm

Para abrir um túnel para o ponto de extremidade do Swarm, execute um comando semelhante ao seguinte:

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Agora você pode definir a variável de ambiente DOCKER\_HOST da maneira a seguir e continuar a usar a CLI (interface de linha de comando) do Docker da maneira normal.

```
export DOCKER_HOST=:2375
```

## Criar um túnel SSH no Windows

Há várias opções para a criação de túneis SSH no Windows. Este documento descreverá como usar o PuTTY para fazer isso.

Baixe o PuTTY para seu sistema Windows e execute o aplicativo.

Insira um nome de host que consista no nome de usuário do administrador do cluster e no nome DNS público do primeiro mestre no cluster. O **Nome de Host** será semelhante a: `adminuser@PublicDNS`. Insira 2200 para a **Porta**.

![Configuração do PuTTY 1](media/putty1.png)

Selecione `SSH` e `Authentication`. Adicione o arquivo de chave privada para autenticação.

![Configuração do PuTTY 2](media/putty2.png)

Selecione `Tunnels` e configure as seguintes portas encaminhadas:
- **Porta de Origem:** sua preferência -- use 80 para Mesos e 2375 para Swarm.
- **Destino:** use localhost:80 para o Mesos ou localhost:2375 para o Swarm

O exemplo a seguir é configurado para o Mesos, mas seria semelhante para o Docker Swarm.

>[AZURE.NOTE] A porta 80 não deve estar em uso quando você criar esse túnel.

![Configuração do PuTTY 3](media/putty3.png)

Quando tiver terminado, salve a configuração de conexão e conecte a sessão do PuTTY. Quando você se conectar, poderá ver a configuração da porta no log de eventos do PuTTY.

![Log de eventos do PuTTY](media/putty4.png)

Quando você tiver configurado o túnel para o Mesos, poderá acessar o ponto de extremidade relacionado em:

- Mesos: `http://localhost/mesos`
- Marathon: `http://localhost/marathon`
- Chronos: `http://localhost/chronos`

Quando você tiver configurado o túnel para o Docker Swarm, poderá acessar o cluster do Swarm por meio da CLI do Docker. Primeiro, você precisará configurar uma variável de ambiente do Windows chamada `DOCKER_HOST` com o valor ` :2375`.

## Próximas etapas

Implantar e gerenciar contêineres com Mesos ou Swarm.

- [Trabalhar com o Mesos e o Serviço de Contêiner do Azure](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0323_2016-->