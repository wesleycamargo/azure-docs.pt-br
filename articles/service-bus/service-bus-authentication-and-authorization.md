<properties 
   pageTitle="Autenticação e Autorização do Barramento de Serviço | Microsoft Azure"
   description="Visão geral da autenticação SAS."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/04/2015"
   ms.author="sethm" />

# Autenticação e autorização do Barramento de Serviço

Os aplicativos podem se autenticar no Barramento de Serviço do Azure usando a autenticação SAS (assinatura de acesso compartilhado) ou por meio do Controle de Acesso do Active Directory do Azure (também conhecido como Serviço de Controle de Acesso ou ACS). A autenticação SAS permite que os aplicativos se autentiquem no Barramento de Serviço usando uma tecla de acesso configurada no namespace ou em uma entidade com a qual tenha direitos específicos associados. Em seguida, você pode usar essa tecla para gerar um token SAS que os clientes podem usar para se autenticar no Barramento de Serviço.

É recomendável o SAS em vez do ACS, pois ele fornece um esquema de autenticação simples, flexível e fácil de usar no Barramento de Serviço. Os aplicativos podem usar SAS em cenários em que eles não precisam gerenciar a noção de um "usuário" autorizado.

## Autenticação SAS

A [autenticação SAS](service-bus-sas-overview.md) permite que você conceda a um usuário acesso a recursos de Barramento de Serviço com direitos específicos. A autenticação SAS no Barramento de Serviço envolve a configuração de uma chave criptográfica com direitos associados em um recurso do Barramento de Serviço. Os clientes podem obter acesso a esse recurso apresentando um token SAS que consiste em acessar o URI de recurso e assinar uma expiração com a tecla configurada.

É possível configurar chaves para SAS em um namespace do Barramento de Serviço. A chave se aplica a todas as entidades de mensagens nesse namespace. Também é possível configurar chaves em tópicos e filas do Barramento de Serviço. Também há suporte para SAS nas retransmissões do Barramento de Serviço.

Para usar a SAS, você pode configurar um objeto [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) em um namespace, fila ou tópico que consista no seguinte:

- *KeyName*, que identifica a regra.

- *PrimaryKey*, que é uma chave de criptografia usada para assinar/validar tokens SAS.

- *SecondaryKey*, que é uma chave de criptografia usada para assinar/validar tokens SAS.

- *Direitos*, que representa a coleção de direitos de Escuta, Envio ou Gerenciamento concedidos.

As regras de autorização configuradas no nível de namespace podem conceder acesso a todas as entidades em um namespace para clientes com tokens assinados usando a tecla correspondente. Até 12 regras de autorização podem ser configuradas em um namespace, fila ou tópico do Barramento de Serviço. Por padrão, uma [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) com todos os direitos é configurada para cada namespace quando ele é provisionado pela primeira vez.

Para acessar uma entidade, o cliente requer um token SAS gerado usando uma determinada [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). O token SAS é gerado usando o HMAC-SHA256 de uma cadeia de caracteres de recurso que consiste no URI de recurso ao qual o acesso é solicitado e em uma expiração com uma chave criptográfica associada à regra de autorização.

O suporte à autenticação SAS para o Barramento de Serviço está incluído no .NET SDK do Azure versão 2.0 e posterior. A SAS dá suporte a uma [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Todas as APIs que aceitam uma cadeia de conexão como parâmetro incluem suporte para cadeias de conexão SAS.

## Autenticação ACS

A autenticação do Barramento de Serviço por meio do ACS é gerenciada através de um namespace de complemento "-sb" do ACS. Se quiser que um namespace do ACS complementar seja criado para um namespace do Barramento de Serviço, você não pode criar o seu namespace do Barramento de Serviço usando o portal clássico do Azure. Você deve criar o namespace usando o cmdlet do PowerShell [New-AzureSBNamespace](https://msdn.microsoft.com/library/azure/dn495165.aspx). Por exemplo:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

Para evitar a criação de um namespace do ACS, emita o seguinte comando:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

Por exemplo, se você criar um namespace de Barramento de Serviço chamado **contoso.servicebus.windows.net**, um namespace complementar ACS chamado **contoso-sb.accesscontrol.windows.net** será provisionado automaticamente. Para todos os namespaces que foram criados antes de agosto de 2014, um namespace do ACS de acompanhamento também foi criado.

Um “proprietário” de identidade de serviço padrão, com todos os direitos, é provisionado por padrão neste namespace do ACS de complemento. Você pode obter controle refinado de qualquer entidade de Barramento de Serviço por meio do ACS configurando as relações de confiança apropriadas. Você pode configurar identidades de serviço adicionais para gerenciar o acesso a entidades do Barramento de Serviço.

Para acessar uma entidade, o cliente solicita um token SWT do ACS com as declarações adequadas apresentando suas credenciais. O token SWT deve ser enviado como parte da solicitação ao Barramento de Serviço para habilitar a autorização do cliente para o acesso à entidade.

O suporte à autenticação ACS para o Barramento de Serviço está incluído no .NET SDK do Azure versão 2.0 e posterior. Essa autenticação inclui suporte para um [SharedSecretTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedsecrettokenprovider.aspx). Todas as APIs que aceitam uma cadeia de conexão como parâmetro incluem suporte para cadeias de conexão do ACS.

## Próximas etapas

Continue lendo [Autenticação SAS com Barramento de Serviço](service-bus-shared-access-signature-authentication.md) para obter mais detalhes sobre SAS.

Para obter uma visão geral de alto nível do SAS no Barramento de Serviço, confira [Assinaturas de Acesso Compartilhado](service-bus-sas-overview.md).

Você pode encontrar mais informações sobre tokens de ACS em [Como solicitar um token do ACS usando o protocolo OAuth WRAP](https://msdn.microsoft.com/library/hh674475.aspx).

<!---HONumber=AcomDC_1203_2015-->