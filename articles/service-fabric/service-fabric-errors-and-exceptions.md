<properties
   pageTitle="Exceções comuns geradas pelo FabricClient | Microsoft Azure"
   description="Descreve as exceções e erros comuns que podem ser gerados pelas APIs FabricClient ao executar operações de gerenciamento de aplicativos e clusters."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# Exceções e erros comuns ao trabalhar com as APIs FabricClient
As APIs [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) permitem que administradores de clusters e aplicativos executem tarefas administrativas em um aplicativo, serviço ou cluster do Service Fabric. Por exemplo, implantação, atualização e remoção de aplicativos, verificação da integridade de clusters ou teste de serviços. Desenvolvedores de aplicativos e administradores de clusters podem usar as APIs FabricClient para desenvolver ferramentas para gerenciar clusters e aplicativos do Service Fabric.

Há muitos tipos de operações diferentes que podem ser executados usando o FabricClient. Cada método pode gerar exceções para erros decorrentes de entrada incorreta, erros de tempo de execução ou problemas de infra-estrutura transitórios. Consulte a documentação de referência da API para localizar quais exceções são geradas por um método específico. Há algumas exceções, no entanto, que podem ser geradas por várias APIs [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) diferentes. A tabela a seguir lista as exceções que são comuns entre as APIs FabricClient.

|Exceção| Gerada quando|
|---------|:-----------|
|[System.Fabric.FabricObjectClosedException](https://msdn.microsoft.com/library/system.fabric.fabricobjectclosedexception.aspx)|O objeto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) está em um estado fechado. Descarte o objeto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) que você está usando e crie uma instância para um novo objeto [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx). |
|[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)|A operação atingiu o tempo limite. [OperationTimedOut](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) é retornado quando a operação leva mais do que MaxOperationTimeout para ser concluída.|
|[System.UnauthorizedAccessException](https://msdn.microsoft.com/pt-BR/library/system.unauthorizedaccessexception.aspx)|Ocorreu uma falha na verificação de acesso para a operação. E\_ACCESSDENIED é retornado.|
|[System.Fabric.FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)|Ocorreu um erro de tempo de execução ao executar a operação. Qualquer um dos métodos FabricClient pode gerar [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx). A propriedade [ErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricexception.errorcode.aspx) indica a causa exata da exceção. Códigos de erro são definidos na enumeração [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx).|
|[System.Fabric.FabricTransientException](https://msdn.microsoft.com/library/system.fabric.fabrictransientexception.aspx)|A operação falhou devido a algum tipo de condição de erro transitório. Por exemplo, uma operação pode falhar porque um quorum de réplicas está inacessível temporariamente. Exceções temporárias correspondem a operações com falha que podem ser tentadas novamente.|

Alguns erros [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) que podem ser retornados em uma [FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx):

|Erro| Condição|
|---------|:-----------|
|CommunicationError|Um erro de comunicação fez com que a operação falhasse e fosse tentada novamente.|
|InvalidCredentialType|O tipo de credencial é inválido.|
|InvalidX509FindType|O X509FindType é inválido.|
|InvalidX509StoreLocation|O local do repositório X509 é inválido.|
|InvalidX509StoreName|O nome do repositório X509 é inválido.|
|InvalidX509Thumbprint|A cadeia de caracteres de impressão digital do certificado X509 é inválida.|
|InvalidProtectionLevel|O nível de proteção é inválido.|
|InvalidX509Store|Não é possível abrir o repositório de certificados X509.|
|InvalidSubjectName|O nome da entidade é inválido.|
|InvalidAllowedCommonNameList|O formato da cadeia de caracteres de lista de nome comum é inválido. Ele deve ser uma lista separada por vírgulas.|

<!---HONumber=AcomDC_0831_2016-->