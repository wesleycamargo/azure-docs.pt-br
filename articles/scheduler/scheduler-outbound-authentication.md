<properties
 pageTitle="Autenticação de Saída do Agendador"
 description="Autenticação de Saída do Agendador"
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
 ms.date="03/09/2016"
 ms.author="krisragh"/>

# Autenticação de Saída do Agendador

Os trabalhos do Agendador podem precisar chamar serviços que requerem autenticação. Dessa forma, um serviço chamado pode determinar se o trabalho do Agendador poderá acessar seus recursos. Alguns desses serviços incluem outros serviços do Azure, Salesforce.com, Facebook e sites seguros personalizados.

## Adição e Remoção de Autenticação

Adicionar a autenticação para um trabalho do Agendador é simples: adicione um elemento filho JSON `authentication` para o elemento `request` ao criar ou atualizar um trabalho. Os segredos passados para o serviço do Agendador em uma solicitação PUT, PATCH ou POST, como parte do objeto `authentication`, nunca são retornados em respostas. Nas respostas, as informações secretas são definidas como nulo ou podem ter um token público que representa a entidade autenticada.

Para remover a autenticação, faça PUT ou PATCH do trabalho explicitamente, configurando o objeto `authentication` como nulo. Você não verá quaisquer propriedades de autenticação na resposta.

Atualmente, os únicos tipos de autenticação com suporte são o modelo `ClientCertificate` (para uso dos certificados de cliente SSL/TLS), o modelo `Basic` (para autenticação básica) e o modelo `ActiveDirectoryOAuth` (para autenticação OAuth do Active Directory.)

## Corpo da solicitação de autenticação ClientCertificate

Ao adicionar a autenticação usando o modelo `ClientCertificate`, especifique os seguintes elementos adicionais no corpo da solicitação.

|Elemento|Descrição|
|:---|:---|
|_autenticação (elemento pai)_|Objeto de autenticação para usar um certificado de cliente SSL.|
|_tipo_|Obrigatório. Tipo de autenticação. Para certificados de cliente SSL, o valor deve ser `ClientCertificate`.|
|_pfx_|Obrigatório. Conteúdo codificado na Base64 do arquivo PFX.|
|_senha_|Obrigatório. Senha para acessar o arquivo PFX.|


## Corpo da resposta para autenticação ClientCertificate

Quando uma solicitação é enviada com as informações de autenticação, a resposta contém os seguintes elementos de autenticação.

|Elemento |Descrição |
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usar um certificado de cliente SSL.|
|_tipo_ |Tipo de autenticação. Para certificados de cliente SSL, o valor é `ClientCertificate`.|
|_certificateThumbprint_ |A impressão digital do certificado.|
|_certificateSubjectName_ |O nome distinto da entidade do certificado.|
|_certificateExpiration_ |A data de validade do certificado.|

## Corpo da solicitação de autenticação básica

Ao adicionar a autenticação usando o modelo `Basic`, especifique os seguintes elementos adicionais no corpo da solicitação.

|Elemento|Descrição|
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usar a autenticação básica.|
|_tipo_ |Obrigatório. Tipo de autenticação. Para a autenticação básica, o valor deve ser `Basic`.|
|_nome de usuário_ |Obrigatório. Nome de usuário para autenticação.|
|_senha_ |Obrigatório. Senha para autenticação.|

## Corpo da resposta de autenticação básica

Quando uma solicitação é enviada com as informações de autenticação, a resposta contém os seguintes elementos de autenticação.

|Elemento|Descrição|
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usar a autenticação básica.|
|_tipo_ |Tipo de autenticação. Para a autenticação básica, o valor é `Basic`.|
|_nome de usuário_ |O nome de usuário autenticado.|

## Corpo da solicitação de autenticação ActiveDirectoryOAuth

Ao adicionar a autenticação usando o modelo `ActiveDirectoryOAuth`, especifique os seguintes elementos adicionais no corpo da solicitação.

|Elemento |Descrição |
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usar a autenticação ActiveDirectoryOAuth.|
|_tipo_ |Obrigatório. Tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor deve ser `ActiveDirectoryOAuth`.|
|_locatário_ |Obrigatório. O identificador do locatário para o locatário do Azure AD.|
|_audiência_ |Obrigatório. Isso é definido como https://management.core.windows.net/.|
|_clientId_ |Obrigatório. Forneça o identificador de cliente para o aplicativo do AD do Azure.|
|_segredo_ |Obrigatório. Segredo do cliente que está solicitando o token.|

### Determinando o identificador do locatário

Você pode encontrar o identificador do locatário para o locatário do Azure AD executando `Get-AzureAccount` no Azure PowerShell.

## Corpo da resposta de autenticação ActiveDirectoryOAuth

Quando uma solicitação é enviada com as informações de autenticação, a resposta contém os seguintes elementos de autenticação.

|Elemento |Descrição |
|:--|:--|
|_autenticação (elemento pai)_ |Objeto de autenticação para usar a autenticação ActiveDirectoryOAuth.|
|_tipo_ |Tipo de autenticação. Para autenticação de ActiveDirectoryOAuth, o valor é `ActiveDirectoryOAuth`.|
|_locatário_ |O identificador do locatário para o locatário do Azure AD. |
|_audiência_ |Isso é definido como https://management.core.windows.net/.|
|_clientId_ |O identificador de cliente para o aplicativo do AD do Azure.|

## Consulte também


 [O que é o Agendador?](scheduler-intro.md)

 [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)

 [Introdução à utilização do Agendador no Portal do Azure](scheduler-get-started-portal.md)

 [Planos e Cobrança no Agendador do Azure](scheduler-plans-billing.md)

 [Referência da API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)

 [Alta disponibilidade e confiabilidade do Agendador do Azure](scheduler-high-availability-reliability.md)

 [Limites, padrões e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)

<!---HONumber=AcomDC_0420_2016-->