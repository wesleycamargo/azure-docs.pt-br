---
title: "Versão Prévia do Docker Compose do Azure Service Fabric | Microsoft Docs"
description: "O Azure Service Fabric aceita o formato do Docker Compose para facilitar a orquestração dos contêineres existentes usando o Service Fabric. No momento, esse suporte está na versão prévia."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/01/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 800f5bacd5197f64968fb1c169ef58330ee75e0d
ms.contentlocale: pt-br
ms.lasthandoff: 05/12/2017

---


# <a name="compose-application-support-in-service-fabric-preview"></a>Suporte ao aplicativo do Compose no Service Fabric (Versão Prévia)

O docker usa o arquivo [docker-compose.yml](https://docs.docker.com/compose) para definir aplicativos de vários contêineres. Para tornar mais fácil para os clientes familiarizados com o Docker orquestrarem aplicativos de contêiner existentes no Service Fabric, incluímos o suporte da versão prévia para o Docker Compose nativamente na plataforma. O Service Fabric pode aceitar a versão 3(+) de arquivos `docker-compose.yml`. Como esse suporte está na versão prévia, há suporte para apenas um subconjunto de diretivas do Compose. Por exemplo, não há suporte para atualizações de aplicativo. No entanto, você sempre pode remover e implantar aplicativos em vez de atualizá-los.  

Para usar essa versão prévia, você precisa instalar o SDK da versão prévia (versão 255.255.x.x) pelo portal. 

> [!NOTE]
> Esse recurso está na versão prévia e não tem suporte.

## <a name="using-a-docker-composeyml-file-with-service-fabric-preview"></a>Usando um arquivo docker-compose.yml com o Service Fabric (Versão Prévia)

Crie um aplicativo do Service Fabric em um arquivo docker-compose.yml executando o seguinte comando no PS:

```powershell
New-ServiceFabricComposeApplication -ApplicationName fabric:/TestContainerApp -Compose docker-compose.yml [-RepositoryUserName <>] [-RepositoryPassword <>] [-PasswordEnctypted]
```

RepositoryUserName e RepoistoryPassword referem-se ao nome de usuário e senha do registro de contêiner.

Se estiver usando a CLI do Azure, 2.0, execute o comando a seguir:

```bash
az sf compose create --application-id fabric:/TestContainerApp --file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```
Esses comandos criam um aplicativo do Service Fabric (chamado `fabric:/TestContainerApp` no exemplo anterior) que pode ser monitorado por meio do Service Fabric Explorer(). O `ApplicationName` especificado é usado para consultas de integridade por meio do PS, CLI do Azure 2.0 ou SFX.

Para excluir o aplicativo por meio do PS, use o seguinte comando:

```powershell
Remove-ServiceFabricComposeApplication  -ApplicationName fabric:/TestContainerApp
```

Para excluir o aplicativo por meio da CLI do Azure 2.0, use o seguinte comando:

```bash
az sf compose remove  --application-id TestContainerApp [ --timeout ]
```

Para obter o status do aplicativo do Compose, use o seguinte comando no PS:

```powershell
Get-ServiceFabricComposeApplicationStatus -ApplicationName fabric:/TestContainerApp -GetAllPages
```

Para a CLI do Azure 2.0, use o seguinte comando:

```bash
az sf compose status --application-id TestContainerApp [ --timeout ]
```



## <a name="supported-compose-directives"></a>Diretivas do Compose com suporte

Um subconjunto das opções de configuração do formato do Compose V3 tem suporte nessa versão prévia. Os seguintes primitivos têm suporte:

* Serviços->Implantar->Réplicas    
* Serviços->Implantar->Posicionamento->Restrições    
* Serviços->Implantar->Recursos->Limites 
*         -cpu-shares    
*         -memory
*         -memory-swap
* Serviços->Comandos    
* Serviços->Ambiente
* Serviços->Portas    
* Serviços->imagem
* Serviços->Isolamento (somente para Windows)
* Serviços->registro em log->driver    
* Serviços->registro em log->driver->opções
* Volume e Implantar->Volume    

O cluster deve ser configurado para impor limites de recursos, conforme descrito na [Governança de recursos do Service Fabric](service-fabric-resource-governance.md).
Todas as outras diretivas do Docker Compose não têm suporte por essa versão prévia. 

## <a name="servicednsname-computation"></a>Computação de ServiceDnsName

Se o nome do serviço especificado no arquivo do Compose for um nome de domínio totalmente qualificado (isto é, contiver um ponto, “.”), o nome DNS registrado pelo Service Fabric será `<ServiceName>`, incluindo o ponto. Caso contrário, cada segmento de caminho no ApplicationName se torna um rótulo de domínio no nome DNS do serviço com o primeiro segmento do caminho se tornando o rótulo de domínio primário. Portanto, se o nome do aplicativo especificado for `fabric:/SampleApp/MyComposeApp`, então `<ServiceName>.MyComposeApp.SampleApp` seria o nome DNS registrado.

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Diferenças entre o modelo de aplicativo do Compose (definição de instância) e do Service Fabric (definição de tipo)

O arquivo docker-compose.yml descreve um conjunto de contêineres implantável, incluindo suas propriedades e configurações (por exemplo, portas e variáveis de ambiente). Parâmetros de implantação como restrições de posicionamento, limites de recursos e nomes DNS também são especificados no arquivo docker-compose.yml.

O [modelo de aplicativo do Service Fabric](service-fabric-application-model.md) usa tipos de serviço e tipos de aplicativo, em que é possível ter várias instâncias de aplicativo do mesmo tipo (por exemplo, uma instância de aplicativo por cliente). Esse modelo baseado em tipo dá suporte a várias versões do mesmo tipo de aplicativo registrado com o tempo de execução. Por exemplo, o cliente A pode ter um aplicativo instanciado com tipo 1.0 de AppTypeA e o cliente B pode ter outro aplicativo instanciado com o mesmo tipo e versão. Os tipos de aplicativos são definidos nos manifestos de aplicativo e os parâmetros de nome e implantação de aplicativo são especificados no momento da criação do aplicativo.

Embora esse modelo ofereça flexibilidade, também estamos planejando dar suporte a um modelo de implantação baseado em instâncias mais simples em que os tipos são implícitos no arquivo de manifesto. Nesse modelo, cada aplicativo obtém seu próprio manifesto independente. Estamos fazendo a versão prévia desse esforço adicionando suporte para o docker-compose.yml, que é um formato de implantação baseado em instância.


## <a name="next-steps"></a>Próximas etapas

* Leia sobre o [modelo de aplicativo do Service Fabric](service-fabric-application-model.md).

