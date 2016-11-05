---
title: Interagindo com clusters do Service Fabric usando a CLI | Microsoft Docs
description: Como usar a CLI do Azure para interagir com um cluster do Service Fabric
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2016
ms.author: subramar

---
# <a name="using-the-azure-cli-to-interact-with-a-service-fabric-cluster"></a>Usando a CLI do Azure para interagir com um Cluster do Service Fabric
Você pode interagir com o cluster do Service Fabric por meio de computadores Linux usando a CLI do Azure no Linux.

A primeira etapa é obter a versão mais recente da CLI do representante de git e configurá-la em seu caminho usando os seguintes comandos:

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

Para cada comando com suporte, você pode digitar o nome do comando para obter ajuda para o comando. Há suporte para o preenchimento automático para os comandos. Por exemplo, o comando a seguir fornece ajuda para todos os comandos do aplicativo. 

```sh
 azure servicefabric application 
```

Você pode filtrar ainda mais a ajuda para um comando específico, como mostra o exemplo a seguir:

```sh
 azure servicefabric application  create
```

Para habilitar o preenchimento automático na CLI, execute os seguintes comandos:

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

Os comandos a seguir se conectam ao cluster e mostram os nós no cluster:

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

Para usar parâmetros nomeados e descobrir o que eles são, você pode digitar -–ajuda após um comando. Por exemplo:

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

Ao se conectar a um cluster com vários computadores por meio de um computador **que não faz parte do cluster**, use o seguinte comando:

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

Substitua a marcação PublicIPorFQDN por um IP ou FQDN real, conforme apropriado. Ao se conectar a um cluster com vários computadores por meio de um computador **que faz parte do cluster**, use o seguinte comando:

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

Você pode usar o PowerShell ou a CLI para interagir com o Cluster do Service Fabric do Linux criado por meio do portal do Azure. 

**Cuidado:** esses agrupamentos não são seguros, portanto, você pode abrir a caixa um ao adicionar o endereço IP público no manifesto do cluster.

## <a name="using-the-azure-cli-to-connect-to-a-service-fabric-cluster"></a>Usando a CLI do Azure para se conectar a um Cluster do Service Fabric
Os seguintes comandos da CLI do Azure descrevem como se conectar a um cluster seguro. Os detalhes do certificado devem corresponder a um certificado em nós do cluster.

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```

Se o certificado tiver ACs (autoridades de certificação), você precisará adicionar o parâmetro --ca-cert-path, como no seguinte exemplo: 

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Se você tiver várias ACs, use uma vírgula como o delimitador.

Se o Nome Comum no certificado não coincidir com o ponto de extremidade de conexão, você poderá usar o parâmetro `--strict-ssl` para ignorar a verificação, conforme mostrado no seguinte comando: 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl false 
```

Se você quiser ignorar a verificação de AC, poderá adicionar o parâmetro --reject-unauthorized, conforme mostrado no seguinte comando: 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized false 
```

Depois de se conectar, você poderá executar outros comandos da CLI para interagir com o cluster. 

## <a name="deploying-your-service-fabric-application"></a>Implantando o aplicativo da Malha do Serviço
Execute os seguintes comandos para copiar, registrar e iniciar o aplicativo Service Fabric:

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## <a name="upgrading-your-application"></a>Atualizando seu aplicativo
O processo é semelhante ao [processo no Windows](service-fabric-application-upgrade-tutorial-powershell.md)).

Crie, copie, registre e crie seu aplicativo por meio de um diretório raiz do projeto. Se a instância do aplicativo for denominada fabric:/MySFApp e o tipo for MySFApp, os comandos serão da seguinte maneira:

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

Faça a alteração em seu aplicativo e recompile o serviço modificado.  Atualize o arquivo de manifesto do serviço modificado (ServiceManifest.xml) com as versões atualizadas para o Serviço (e Código, Configuração ou Dados, conforme apropriado). Modifique também o manifesto do aplicativo (ApplicationManifest.xml) com o número da versão atualizada do aplicativo e o serviço modificado.  Agora, copie e registre seu aplicativo atualizado usando os seguintes comandos:

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

Agora, você pode iniciar a atualização de aplicativo com o seguinte comando:

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0  --rolling-upgrade-mode UnmonitoredAuto
```

Agora você pode monitorar a atualização de aplicativo usando SFX. Em alguns minutos, o aplicativo seria atualizado.  Você também pode experimentar um aplicativo atualizado com um erro e verificar a funcionalidade de reversão automática no Service Fabric.

## <a name="troubleshooting"></a>Solucionar problemas
### <a name="copying-of-the-application-package-does-not-succeed"></a>A cópia do pacote de aplicativos não é bem-sucedida
Verifique se `openssh` está instalado. Por padrão, a área de trabalho do Ubuntu não está instalada. Instale-o usando o comando a seguir:

```
 sudo apt-get install openssh-server openssh-client**
```

Se o problema persistir, tente desabilitar o PAM para ssh alterando o arquivo **sshd_config** usando os seguintes comandos:

```sh
 sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
 sudo service sshd reload
```

Se o problema persistir, tente aumentar o número de sessões ssh executando os seguintes comandos:

```sh
 sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
 sudo service sshd reload
```
O uso de chaves para autenticação ssh (em vez de senhas) ainda não tem suporte (porque a plataforma usa ssh para copiar os pacotes), portanto use a autenticação de senha.

## <a name="next-steps"></a>Próximas etapas
Configurar o ambiente de desenvolvimento e implantar um aplicativo do Service Fabric em um cluster do Linux.

<!--HONumber=Oct16_HO2-->


