<properties
   pageTitle="Conectar a um cluster do Serviço de Contêiner do Azure | Microsoft Azure"
   description="Conectar-se a um cluster do Serviço de Contêiner do Azure usando um Túnel SSH."
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

Os clusters Mesos e Swarm implantados pelo Serviço de Contêiner do Azure expõem pontos de extremidade REST. No entanto, esses pontos de extremidade não estão abertos para o mundo exterior. Para gerenciar esses pontos de extremidade, deve ser criado um túnel SSH. Após o estabelecimento de um túnel SSH, você pode executar comandos em relação aos pontos de extremidade do cluster e exibir a interface do usuário do cluster por meio de um navegador em seu próprio sistema. Este documento o orientará ao longo da criação de um túnel SSH do Linux, do OSX e do Windows.

> Você pode criar uma sessão SSH com um sistema de gerenciamento de cluster. No entanto, isso não é recomendado. Trabalhar diretamente em um sistema de gerenciamento acarreta o risco de alterações de configuração acidentais.

## Túnel SSH no Linux/OSX

Primeiro, é preciso localizar o nome DNS público dos mestres com balanceamento de carga. Para fazer isso, expanda o grupo de recursos, de modo que todos os recursos sejam exibidos. Localize e selecione o endereço IP público do mestre. Isso abrirá uma folha que contém informações sobre o endereço IP público, o que incluirá o nome DNS. Salve o nome para uso posterior. <br />

![Conexão do Putty](media/pubdns.png)

Agora, abra um shell e execute o seguinte comando, em que:

**PORTA** é a porta do ponto de extremidade que você deseja expor. Para o Swarm, é 2375; para Mesos, use a porta 80. **NOME DE USUÁRIO** é o nome de usuário fornecido quando você implantou o cluster. **PREFIXO DE DNS** é o prefixo de DNS que você forneceu ao implantar o cluster. **REGIÃO** é a região em que o grupo de recursos está localizado.

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Túnel Mesos

Para abrir um túnel para pontos de extremidade relacionados a Mesos, execute um comando semelhante ao seguinte.

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Os pontos de extremidade relacionados a Mesos agora podem ser acessados em:

- Mesos - `http://localhost/mesos`
- Marathon - `http://localhost/marathon`
- Chronos -`http://localhost/chronos`. 

Da mesma forma, as APIs rest para cada aplicativo podem ser acessadas por esse túnel: Marathon - `http://localhost/marathon/v2`. Para obter mais informações sobre as várias APIs disponíveis, confira a documentação da Mesosphere para a [API Maratho](https://mesosphere.github.io/marathon/docs/rest-api.html) e a [API Chronos](https://mesos.github.io/chronos/docs/api.html) e a documentação da Apache para a [API do Agendador Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/)

### Túnel do Swarm

Para abrir um túnel para o ponto de extremidade do Swarm, execute um comando semelhante ao seguinte.

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Agora você pode definir a variável de ambiente DOCKER\_HOST da maneira a seguir e continuar a usar a CLI do Docker da maneira normal.

```
export DOCKER_HOST=:2375
```

## Túnel SSH no Windows

Há várias opções disponíveis para a criação de túneis SSH no Windows. Este documento fornecerá detalhes referentes ao uso do Putty.

Baixe o Putty para seu sistema Windows e execute o aplicativo.

Insira um nome de host que consista no nome de usuário do administrador do cluster e no nome DNS público do primeiro mestre no cluster. O Nome de Host será semelhante a:> `adminuser@PublicDNS`. Digite 2200 para a Porta.

![Conexão do Putty](media/putty1.png)

Selecione `SSH` e `Authentication` e adicione o arquivo de chave privada para autenticação.

![Conexão do Putty](media/putty2.png)

Selecione `Tunnels` e `configure` as seguintes portas encaminhadas:- **Porta de Origem:** sua preferência (80 para Mesos e 2375 para Swarm) - **Destino:** localhost:80 (para Mesos) ou localhost:2375 (para Swarm)

O exemplo a seguir é configurado para Mesos, mas seria semelhante para o Docker Swarm.

> A porta 80 não deve estar em uso ao ser criado esse túnel.

![Conexão do Putty](media/putty3.png)

Ao concluir, salve a configuração de conexão e conecte a sessão do Putty. Quando a conexão for estabelecida, a configuração da porta poderá ser vista no log de eventos do Putty.

![Conexão do Putty](media/putty4.png)

Quando o túnel tiver sido configurado para Mesos, o ponto de extremidade relacionado poderá ser acessado em:

- Mesos - `http://localhost/mesos`
- Marathon - `http://localhost/marathon`
- Chronos -`http://localhost/chronos`. 

Quando o túnel tiver sido configurado para o Docker Swarm, o cluster do Swarm poderá ser acessado por meio da CLI do Docker. Primeiro, você precisará configurar uma variável de Ambiente do Windows chamada `DOCKER_HOST` com o valor ` :2375`.

## Próximas etapas
 
Implantar e gerenciar contêineres com Mesos ou Swarm.
 
- [Como trabalhar com ACS e Mesos](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0218_2016-->