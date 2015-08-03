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
 ms.date="04/22/2015" 
 ms.author="krisragh"/>
 
# Limites, padrões e códigos de erro do Agendador

## Aceleradores, limites, padrões e cotas do Agendador

A tabela a seguir descreve cada uma das principais cotas, limites, padrões e limitações no Agendador do Azure.

|Recurso|Descrição de limite|
|---|---|
|**Tamanho do trabalho**|O tamanho máximo do trabalho é 16K. Se um PUT ou PATCH resultarem em um trabalho maior do que esses limites, é retornado um código de status 400 Solicitação incorreta.|
|**Tamanho da solicitação de URL**|O tamanho máximo da URL da solicitação é de 2048 caracteres.|
|**Tamanho do cabeçalho de agregação**|O tamanho máximo do cabeçalho agregação é de 4096 caracteres.|
|**Contagem de cabeçalho**|A contagem máxima do cabeçalho é de 50 cabeçalhos.|
|**Tamanho do corpo**|O tamanho máximo do corpo é de 8192 caracteres.|
|**Intervalo de recorrência**|O período máximo de recorrência é de 18 meses.|
|**Hora de início da hora**|O "tempo para a hora de início" máximo é de 18 meses.|
|**Histórico de trabalho**|O corpo de resposta máximo armazenado no histórico de trabalho é de 2048 bytes.|
|**Frequência**|A cota de frequência máxima padrão é de 1 hora em uma coleção de trabalhos livre e de 1 minuto em um conjunto de trabalhos padrão. A frequência máxima é configurável em uma coleção de trabalhos que seja menor que o máximo. Todos os trabalhos na coleção de trabalhos são limitados ao valor definido na coleção de trabalhos. Se você tentar criar um trabalho com uma frequência superior à frequência máxima na coleção de trabalhos a solicitação falhará com um código de status 409 Conflito.|
|**Trabalhos**|A cota máxima padrão é de 5 trabalhos em uma coleção de trabalhos livre e 50 trabalhos em uma coleção de trabalhos padrão. O número máximo de trabalhos é configurável em uma coleção de trabalhos. Todos os trabalhos na coleção de trabalhos são limitados ao valor definido na coleção de trabalhos. Se você tentar criar mais trabalhos do que a cota máxima de trabalhos, a solicitação falhará com um código de status 409 Conflito.|
|**Retenção de histórico de trabalho**|O histórico do trabalho é retido por até 2 meses.|
|**Retenção de trabalhos concluídos e com falha**|Os trabalhos concluídos e com falha são vendidos após 60 dias.|
|**Tempo limite**|Há um tempo limite da solicitação (não configurável) estático de 30 segundos para ações de HTTP. Para operações mais demoradas em execução, execute protocolos assíncronos HTTP; por exemplo, retornar um 202 imediatamente, mas continuar a trabalhar no plano de fundo.|

## O cabeçalho x-ms-request-id

Cada solicitação feita no serviço de Agendador retorna um cabeçalho de resposta chamado * * x-ms-solicitação-id * *. Esse cabeçalho contém um valor opaco que identifica exclusivamente a solicitação.

Se uma solicitação estiver falhando consistentemente e você tiver verificado que a solicitação foi formulada corretamente, você poderá usar esse valor para relatar o erro à Microsoft. No seu relatório, inclua o valor ofx-ms-request-id, o tempo aproximado que a solicitação foi feita, o identificador da assinatura, serviço de nuvem, coleção de trabalhos e/ou trabalho e o tipo de operação que a solicitação tentou.

## Códigos de erro e status do Agendador

Além dos códigos de status HTTP padrão, a API REST do Agendador do Azure retorna códigos de erro estendidos e mensagens de erro. Os códigos estendidos não substituem os códigos de status HTTP padrão, mas fornecem informações adicionais e acionáveis que podem ser usadas em conjunto com os códigos de status HTTP padrão.

Por exemplo, um erro HTTP 404 pode ocorrer por várias razões, portanto, ter informações adicionais na mensagem estendida pode ajudá-lo na resolução do problema. Para obter mais informações sobre os códigos HTTP padrões retornados pela API REST, consulte [Status do gerenciamento de serviço e códigos de erro](https://msdn.microsoft.com/library/windowsazure/ee460801.aspx). As operações da API REST para a API de gerenciamento de serviço retornam códigos de status HTTP padrão, conforme definido nas [Definições de código de Status HTTP/1.1](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). A tabela a seguir descreve os erros comuns que podem ser retornados pelo serviço.

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

 [Conceitos, terminologia e hierarquia de entidades do Agendador](scheduler-concepts-terms.md)
 
 [Introdução à utilização do Agendador no Portal de Gerenciamento](scheduler-get-started-portal.md)
 
 [Planos e cobrança no Agendador do Azure](scheduler-plans-billing.md)
 
 [Como criar agendas complexas e recorrência avançada com o Agendador do Azure](scheduler-advanced-complexity.md)
 
 [Referência da API REST do Agendador](https://msdn.microsoft.com/library/dn528946)
 
 [Alta disponibilidade e confiabilidade do Agendador](scheduler-high-availability-reliability.md)
 
 [Autenticação de saída do Agendador](scheduler-outbound-authentication.md)

  

<!---HONumber=July15_HO4-->