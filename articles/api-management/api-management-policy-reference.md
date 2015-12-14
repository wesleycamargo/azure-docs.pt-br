<properties 
	pageTitle="Referência de políticas do Gerenciamento de API do Azure" 
	description="Saiba mais sobre as políticas disponíveis para configurar o Gerenciamento de API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="sdanie"/>

# Referência de políticas do Gerenciamento de API do Azure

Esta seção fornece um índice para as políticas de [referência de política de Gerenciamento de API][]. Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API][].

Expressões de política podem ser usadas como valores de atributo ou texto em qualquer uma das políticas de Gerenciamento de API, a menos que a política especifique o contrário. Algumas políticas, como [Controlar fluxo][] e [Definir variável][] se baseiam em expressões de políticas. Para obter mais informações, consulte [Políticas avançadas][] e [Expressões de política][].

## Índice de referência de política

-	[Políticas de restrição de acesso][]
	-	[Verificar cabeçalho HTTP][] - Impõe a existência e/ou valor de um cabeçalho HTTP.
	-	[Limitar taxa de chamada][] - Evita picos de uso da API limitando chamadas e/ou a taxa de consumo de largura de banda.
	-	[Restringir IP do autor da chamada][] - Filtra (permite/recusa) chamadas de endereços IP específicos e/ou intervalos de endereços.
	-	[Definir cota de uso][] - Permite que você aplique uma cota renovável ou permanente de volume da chamada e/ou largura de banda.
	-	[Validar JWT][] - Impõe a existência e a validade de JWT extraída de um cabeçalho HTTP especificado ou um parâmetro de consulta especificado.
-	[Políticas avançadas][]
	-	[Controlar fluxo][] - Aplica-se condicionalmente a instruções de políticas com base nos resultados da avaliação do booliano [expressions][].
	-	[Encaminhar solicitações][] -encaminha a solicitação ao serviço de back-end.
	-	[Registrar em log no Hub de Eventos][] - envia mensagens no formato especificado para um destino de mensagem definido por uma entidade [Agente](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger).
	-	[Retornar resposta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) - Anula a execução de pipeline e retorna a resposta especificada diretamente para o chamador.
	-	[Enviar solicitação unidirecional](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) - Envia uma solicitação para a URL especificada sem aguardar uma resposta.
	-	[Enviar solicitação](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) - Envia uma solicitação para a URL especificada.
	-	[Definir método de solicitação](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) - Permite alterar o método HTTP para uma solicitação.
	-	[Definir código de status](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) - Altera o código de status de HTTP para o valor especificado.
	-	[Definir variável][] - Mantém um valor em uma variável [contexto][] para acesso posterior.
-	[Políticas de autenticação][]
	-	[Autenticar com o Basic][] - Autenticar com um serviço de back-end usando a autenticação Básica.
	-	[Autenticar com o certificado de cliente][] - Autenticar com um serviço de back-end usando certificados de cliente.
-	[Políticas de cache][] 
	-	[Obter do cache][] - Executa a pesquisa em cache e retorna uma resposta válida armazenada em cache quando uma estiver disponível.
	-	[Armazenar em cache][] - Armazena a resposta em cache de acordo com a configuração de controle de cache especificada.
-	[Políticas entre domínios][] 
	-	[Permitir chamadas entre domínios][] - Torna a API acessível por meio de clientes Adobe Flash e Microsoft Silverlight baseados em navegadores.
	-	[CORS][] - Adicionar suporte de compartilhamento de recursos entre origens (CORS) a uma operação ou a uma API para permitir chamadas entre domínios de clientes baseados em navegadores.
	-	[JSONP][] - Adiciona suporte JSON com preenchimento (JSONP) a uma operação ou a uma API para permitir chamadas entre domínios de clientes JavaScript baseados em navegadores.
-	[Políticas de transformação][] 
	-	[Converter JSON para XML][] - Converte o corpo da solicitação ou da resposta de JSON para XML.
	-	[Converter XML para JSON][] - Converte o corpo da solicitação ou da resposta de XML para JSON.
	-	[Localizar e substituir cadeia no corpo][] - Encontra uma subcadeia de uma solicitação ou resposta e a substitui por outra subcadeia.
	-	[Mascarar URLS no conteúdo][] - Reescreve (mascara) os links no corpo da resposta e no cabeçalho de local, para que eles apontem para o link equivalente por meio do gateway.
	-	[Definir o serviço de back-end][] - Altera o serviço de back-end para uma solicitação de entrada.
	-	[Definir corpo][] - Define o corpo da mensagem para solicitações de entrada e saída.
	-	[Definir cabeçalho HTTP][] - Atribui um valor a uma resposta e/ou cabeçalho de resposta existente ou adiciona uma nova resposta e/ou cabeçalho de resposta.
	-	[Definir parâmetro de cadeia de consulta][] - Adiciona, substitui o valor ou exclui parâmetros de cadeias de consulta de solicitação.
	-	[Reescrever URL][] - Converte a URL de uma solicitação de sua forma pública em sua forma esperada pelo serviço Web.

## Próximas etapas

Para obter mais informações sobre expressões de política, consulte o vídeo a seguir.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Políticas de restrição de acesso]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Verificar cabeçalho HTTP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limitar taxa de chamada]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restringir IP do autor da chamada]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Definir cota de uso]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validar JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Políticas avançadas]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Controlar fluxo]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Definir variável]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[contexto]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Encaminhar solicitações]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Registrar em log no Hub de Eventos]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Políticas de autenticação]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Autenticar com o Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Autenticar com o certificado de cliente]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Políticas de cache]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Obter do cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Armazenar em cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Políticas entre domínios]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Permitir chamadas entre domínios]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Políticas de transformação]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Converter JSON para XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Converter XML para JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Localizar e substituir cadeia no corpo]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Mascarar URLS no conteúdo]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Definir o serviço de back-end]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Definir corpo]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Definir cabeçalho HTTP]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Definir parâmetro de cadeia de consulta]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Reescrever URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Políticas de Gerenciamento de API]: api-management-howto-policies.md
[referência de política de Gerenciamento de API]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Expressões de política]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 

<!---HONumber=AcomDC_1203_2015-->