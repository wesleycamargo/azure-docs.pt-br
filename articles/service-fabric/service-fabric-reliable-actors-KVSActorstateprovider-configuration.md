<properties
   pageTitle="Visão geral da configuração de KVSActorStateProvider de atores confiáveis da malha do serviço"
	description="Saiba como configurar atores com monitoração de estado do serviço malha do tipo 'KVSActorStateProvider'"
	services="Service-Fabric"
	documentationCenter=".net"
	authors="sumukhs"
	manager="anuragg"
	editor=""/>

<tags
   ms.service="Service-Fabric"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="NA"
	ms.date="08/26/2015"
	ms.author="sumukhs"/>

# Atores confiáveis para configuração - KVSActorStateProvider
A configuração padrão do KVSActorStateProvider pode ser modificada modificando-se o arquivo "settings.xml" gerado na raiz pacote do Visual Studio dentro da pasta "Config" para o ator especificado em questão.

O tempo de execução da malha do serviço procura nomes de seção predefinidos no arquivo "settings.xml" e consome os valores de configuração ao criar os componentes de tempo de execução subjacentes.

> [AZURE.NOTE]**NÃO** exclua/modifique os nomes de seção das configurações a seguir no arquivo "settings.xml" que é gerado no Visual Studio Solution.

## Configuração de segurança do replicador
As configurações de segurança do replicador servem para proteger o canal de comunicação que é usado durante a replicação. Isso significa que os serviços não poderão ver o tráfego de replicação uns dos outros, garantindo que os dados que têm alta disponibilidade também estejam seguros. Por padrão, uma seção de configuração de segurança vazia não habilita a segurança de replicação.
### Nome da seção
&lt;ActorName&gt;ServiceReplicatorSecurityConfig
### Nomes da configuração
Consulte [Segurança de replicação](../service-fabric/service-fabric-replication-security.md)

## Configuração do replicador
Configurações do replicador servem para configurar o replicador que será responsável por tornar o provedor de estado do ator altamente confiável. A configuração padrão é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre configurações adicionais que estão disponíveis para ajustar o replicador.
### Nome da seção
&lt;ActorName&gt;ServiceReplicatorConfig
### Nomes da configuração

|Nome|Unidade|Valor Padrão|Comentários|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Segundos|0,05|Período de tempo pelo qual o replicador no secundário espera após o recebimento de uma operação antes de enviar novamente uma confirmação ao primário. Todas as outras confirmações a serem enviadas para operações e processadas dentro deste intervalo são enviadas como uma única resposta.|
|ReplicatorEndpoint|N/D|N/A - RequiredParameter|Endereço IP e porta que o replicador primário/secundário usará para se comunicar com outros replicadores no conjunto de réplicas. Eles devem fazer referência a um ponto de extremidade do recurso de TCP no manifesto de serviço. Consulte [Recursos do Manifesto do serviço](service-fabric-service-manifest-resources.md) para saber mais sobre como definir os recursos de ponto de extremidade no manifesto de serviço. |
|RetryInterval|Segundos|5|Período de tempo após o qual o replicador transmite novamente uma mensagem caso não receba uma confirmação para uma operação.|
|MaxReplicationMessageSize|Bytes|50 MB|Tamanho máximo de dados de replicação que podem ser transmitidos em uma única mensagem.|
|MaxPrimaryReplicationQueueSize|Número de operações|1024|Número máximo de operações na fila principal. Uma operação é liberada depois que o replicador primário recebe uma confirmação de todos os replicadores secundários. Esse valor deve ser maior que 64 e uma potência de 2.|
|MaxSecondaryReplicationQueueSize|Número de operações|2048|Número máximo de operações na fila secundária. Uma operação é liberada depois de tornar seu estado de altamente disponível por meio de persistência. Esse valor deve ser maior que 64 e uma potência de 2.|

## Configuração de armazenamento
As configurações de armazenamento servem para configurar o armazenamento local que é usado para manter o estado que está sendo replicado. A configuração padrão é gerada pelo modelo do Visual Studio e deve ser suficiente. Esta seção fala sobre configurações adicionais que estão disponíveis para ajustar o armazenamento local.
### Nome da seção
&lt;ActorName&gt;ServiceLocalStoreConfig
### Nomes da configuração

|Nome|Unidade|Valor Padrão|Comentários|
|----|----|-------------|-------|
|MaxAsyncCommitDelay|Milissegundos|200|Define o intervalo máximo de envio em lotes para a confirmação do local de armazenamento durável .|
|MaxVerPages|Número de páginas|8192|O número máximo de páginas de versão no banco de dados do armazenamento local. Ele determina o número máximo de transações pendentes.|

## Exemplo do arquivo de configuração

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```
## Comentários

BatchAcknowledgementInterval controla a latência de replicação. Um valor '0' resulta na menor latência possível, ao custo de taxa de transferência (como mais mensagens de confirmação devem ser enviadas e processadas, cada uma contendo menos confirmações). Quanto maior o valor para BatchAcknowledgementInterval, maior será a produtividade geral da replicação, ao custo da maior latência de operação. Isso se converte diretamente para a latência de confirmações de transações.
 

<!---HONumber=August15_HO9-->