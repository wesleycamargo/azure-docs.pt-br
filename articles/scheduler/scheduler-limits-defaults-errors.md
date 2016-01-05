<properties
 pageTitle="Limites, padrões e códigos de erro do Agendador"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="12/04/2015"
 ms.author="krisragh"/>

# Limites, padrões e códigos de erro do Agendador

## Aceleradores, limites, padrões e cotas do Agendador

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## O cabeçalho x-ms-request-id

Cada solicitação feita no serviço de Agendador retorna um cabeçalho de resposta chamado * * x-ms-solicitação-id * *. Esse cabeçalho contém um valor opaco que identifica exclusivamente a solicitação.

Se uma solicitação estiver falhando consistentemente e você tiver verificado que a solicitação foi formulada corretamente, você poderá usar esse valor para relatar o erro à Microsoft. No seu relatório, inclua o valor ofx-ms-request-id, o tempo aproximado que a solicitação foi feita, o identificador da assinatura, serviço de nuvem, coleção de trabalhos e/ou trabalho e o tipo de operação que a solicitação tentou.

## Códigos de erro e status do Agendador

Além dos códigos de status HTTP padrão, a API REST do Agendador do Azure retorna códigos de erro estendidos e mensagens de erro. Os códigos estendidos não substituem os códigos de status HTTP padrão, mas fornecem informações adicionais e acionáveis que podem ser usadas em conjunto com os códigos de status HTTP padrão.

Por exemplo, um erro HTTP 404 pode ocorrer por várias razões, portanto, ter informações adicionais na mensagem estendida pode ajudá-lo na resolução do problema. Para obter mais informações sobre os códigos HTTP padrões retornados pela API REST, consulte [Status do gerenciamento de serviço e códigos de erro](https://msdn.microsoft.com/library/windowsazure/ee460801.aspx). As operações da API REST para a API de gerenciamento de serviço retornam códigos de status HTTP padrão, conforme definido nas[Definições de código de Status HTTP/1.1](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). A tabela a seguir descreve os erros comuns que podem ser retornados pelo serviço.

|Código do erro|Código de status HTTP|Mensagem do usuário|
|----|----|----|
|MissingOrIncorrectVersionHeader|Solicitação incorreta (400)|O cabeçalho de controle de versão não foi especificado ou foi especificado incorretamente.|
|InvalidXmlRequest|Solicitação incorreta (400)|O XML do corpo da solicitação era inválido ou não estava especificado corretamente.|
|MissingOrInvalidRequiredQueryParameter|Solicitação incorreta (400)|Um parâmetro de consulta necessário não foi especificado para esta solicitação ou foi especificado incorretamente.|
|InvalidHttpVerb|Solicitação incorreta (400)|O verbo HTTP especificado não foi reconhecido pelo servidor ou não é válido para este recurso.|
|AuthenticationFailed|Proibido (403)|O servidor falhou ao autenticar a solicitação. Verifique se o certificado é válido e está associado a esta assinatura.|
|ResourceNotFound|Não encontrado (404)|O recurso especificado não existe.|
|InternalError|Erro interno do servidor (500)|O servidor encontrou um erro interno. Tente novamente a solicitação.|
|OperationTimedOut|Erro interno do servidor (500)|A operação não pôde ser concluída dentro do tempo permitido ou do número máximo de tentativas possível.|
|ServerBusy|Serviço não disponível (503)|O servidor (ou um componente interno) não está disponível no momento para receber solicitações. Tente novamente a sua solicitação.|
|SubscriptionDisabled|Proibido (403)|A assinatura está em um estado desabilitado.|
|BadRequest|Solicitação incorreta (400)|Um parâmetro estava incorreto.|
|ConflictError|Conflito (409)|Ocorreu um conflito para impedir que a operação seja concluída.|
|TemporaryRedirect|Redirecionamento temporário (307)|O objeto solicitado não está disponível. Um URI temporário para o novo local do objeto pode ser obtido a partir do campo Localização na resposta. A solicitação original pode ser repetida no novo URI.|

As operações de API também podem retornar informações de erro adicionais que são definidas pelo serviço de gerenciamento. Essas informações de erro adicionais são retornadas no corpo da resposta. O corpo da resposta de erro segue o formato básico mostrado abaixo.

		<?xml version="1.0" encoding="utf-8"?>  
		<Error>  
			<Code>string-code</Code>  
			<Message>detailed-error-message</Message>  
		</Error>  

## Consulte também


 [O que é o Agendador?](scheduler-intro.md)
 
 [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)

 [Introdução à utilização do Agendador no Portal do Azure](scheduler-get-started-portal.md)

 [Planos e Cobrança no Agendador do Azure](scheduler-plans-billing.md)

 [Referência da API REST do Agendador do Azure](https://msdn.microsoft.com/library/dn528946)

 [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)

 [Alta disponibilidade e confiabilidade do Agendador do Azure](scheduler-high-availability-reliability.md)

 [Autenticação de saída do Agendador do Azure](scheduler-outbound-authentication.md)
 
  

<!---HONumber=AcomDC_1210_2015-->