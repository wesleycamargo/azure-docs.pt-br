<properties
   pageTitle="Objetos de Aplicativo e de Entidade de Serviço"
   description="Uma discussão sobre a relação entre objetos de Aplicativo e ServicePrincipal no Active Directory do Azure"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   services="active-directory"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/08/2015"
   ms.author="mbaldwin"/>


# Objetos de Aplicativo e de Entidade de Serviço

Este diagrama ilustra a relação entre um objeto de Aplicativo e um objeto ServicePrincipal no contexto de um aplicativo de exemplo chamado **aplicativo HR**. Há três locatários: **Adatum**, o locatário que desenvolve o aplicativo, e **Contoso** e **Fabrikam**, os locatários que consomem o **aplicativo HR**.

![Relação entre um objeto de Aplicativo e um objeto ServicePrincipal](./media/active-directory-application-objects/application-objects-relationship.png)


Quando você registra um aplicativo no Portal de Gerenciamento do Azure, dois objetos são criados em seu locatário de diretório:

- **Objeto de aplicativo**: esse objeto representa uma definição para seu aplicativo. Você pode encontrar uma descrição detalhada de suas propriedades na seção **Objeto de Aplicativo** abaixo.

- **Objeto ServicePrincipal**: esse objeto representa uma instância de seu aplicativo em seu locatário de diretório. Você pode aplicar políticas a objetos ServicePrincipal, incluindo a atribuição de permissões para o ServicePrincipal que permitem que o aplicativo leia dados de diretório do locatário. Sempre que você altera o objeto de Aplicativo, as alterações também são aplicadas ao objeto ServicePrincipal associado em seu locatário.


> [AZURE.NOTE]Se seu aplicativo estiver configurado para acesso externo, as alterações no objeto de aplicativo não serão refletidas no ServicePrincipal de um locatário consumidor até que o locatário do consumidor remova o acesso e conceda-o novamente.
 


No diagrama acima, a etapa "1" é o processo de criação de objetos de Aplicativo e ServicePrincipal.

Na etapa 2, quando um administrador de empresa concede acesso, um objeto ServicePrincipal é criado no locatário do AD do Azure da empresa e é atribuído a ele o nível de acesso de diretório que o administrador da empresa concedeu.

Na etapa 3, os locatários do consumidor de um aplicativo (por exemplo, Contoso e Fabrikam) têm, cada um, seu próprio objeto ServicePrincipal que representa sua instância do aplicativo. Nesse exemplo, cada um deles tem um ServicePrincipal que representa o aplicativo de RH.
 




## Propriedades de Objeto de Aplicativo

As tabelas a seguir listam todas as propriedades de um objeto de aplicativo e incluem detalhes importantes para os desenvolvedores. Essas propriedades se aplicam a aplicativos Web, APIs Web e aplicativos cliente nativos que são registrados com o AD do Azure.

 
### Geral

Propriedade | Descrição
| ------------- | ----------- 
| Nome | Nome de exibição do aplicativo. Propriedade necessária no assistente para **Adicionar Aplicativo**.
| Logotipo | Logotipo do aplicativo que representa seu aplicativo ou empresa. Esse logotipo permite que usuários externos associem mais facilmente a solicitação de concessão de acesso a seu aplicativo. Ao carregar um logotipo, siga as especificações no assistente para **Carregar logotipo**. Se você não fornecer um logotipo, o logotipo padrão será exibido.
| Acesso externo | Determina se os usuários em organizações externas podem conceder a seu aplicativo o logon único e o acesso aos dados no diretório da organização. Esse controle afeta apenas a capacidade de conceder acesso. Ele não altera o acesso já foi concedido. Somente Administradores de Empresas podem conceder acesso.
 

### Logon Único
 
Propriedade | Descrição
| ------------- | ----------- 
| URI da ID do Aplicativo | Um identificador exclusivo lógico para seu aplicativo. Propriedade necessária no assistente para **Adicionar Aplicativo**. <br><br>Como o URI de ID do aplicativo é um identificador lógico, ele não precisa ser resolvido como um endereço da Internet. Ele é apresentado por seu aplicativo ao enviar uma solicitação de logon único ao AD do Azure. O AD do Azure identifica seu aplicativo e envia a resposta de logon (um token SAML) à URL de resposta que foi fornecida durante o registro do aplicativo. Use o valor do URI de ID de Aplicativo para definir a propriedade wtrealm (para WS-Federation) ou a propriedade Issuer (para SAML-P) ao fazer uma solicitação de entrada. O **URI de ID de Aplicativo** deve ser um valor exclusivo no AD do Azure de sua organização.<br><br>\*\*Observação\*\*: ao se habilitar um aplicativo para usuários externos, o valor do URI de ID de Aplicativo do aplicativo deve ser um endereço em um dos domínios verificados do diretório. Como resultado, ele não pode ser um URN. Essa segurança impede que outras organizações especifiquem (e utilizem) uma propriedade exclusiva que pertence à sua organização. Durante o desenvolvimento, você pode alterar o URI de ID de aplicativo para um local no domínio inicial de sua organização (se você ainda não verificou um domínio personalizado/intuitivo) e atualizar seu aplicativo para usar esse novo valor. O domínio inicial é o domínio de nível 3 que você cria durante a inscrição, como contoso.onmicrosoft.com.
| URL do aplicativo | O endereço de uma página da Web em que os usuários podem entrar e usar seu aplicativo. Propriedade em necessária no assistente para **Adicionar Aplicativo**.<br><BR>\*\*Observação\*\*: o valor definido para essa propriedade no assistente para Adicionar Aplicativo também é definido como o valor da URL de resposta.
| URL de resposta | O endereço físico de seu aplicativo. O AD do Azure envia um token com a resposta de logon único para esse endereço. Durante o primeiro registro no assistente para **Adicionar Aplicativo**, o valor definido para a URL do aplicativo também é definido como o valor da URL de resposta. Ao fazer uma solicitação de entrada, use o valor de URL de resposta para definir a propriedade wreply (para WS-Federation) ou a propriedade **AssertionConsumerServiceURL** (para SAML-P).<br><BR>\*\*Observação\*\*: ao se habilitar um aplicativo para usuários externos, a URL de resposta deve ser um endereço **https://**. | URL de metadados de Federação | (Opcional). Representa a URL física do documento de metadados de federação para seu aplicativo. É necessário para dar suporte à saída de SAML-P. O AD do Azure baixa o documento de metadados que é hospedado nesse ponto de extremidade e usa-o para descobrir a parte pública do certificado que você usa para verificar a assinatura em suas solicitações de saída e a URL de saída do aplicativo. Você não pode configurar essa propriedade ao adicionar seu aplicativo. Ele só pode ser configurado mais tarde.<br><BR>**Observação\*\*: se você precisar dar suporte à saída de SAML-P, mas não tiver um ponto de extremidade de metadados de federação para seu aplicativo, entre em contato com o Atendimento ao Cliente para obter outras opções.
 

### Chamar o Graph API ou APIs Web
 
Propriedade | Descrição
| ------------- | ----------- 
| ID do cliente | O identificador exclusivo lógico para seu aplicativo. Você precisa usar esse identificador em chamadas para o Graph API ou outras APIs Web registradas no AD do Azure. O AD do Azure gera automaticamente esse valor durante o registro do aplicativo e ele não pode ser alterado.<BR><BR>Para habilitar seu aplicativo a acessar o diretório (para acesso de leitura ou gravação) por meio do Graph API, você precisa de uma ID de Cliente e de uma chave (conhecida no OAuth 2.0 como um segredo do cliente). Seu aplicativo usa a ID do cliente e a chave para solicitar um token de acesso do ponto de extremidade de token OAuth do 2.0 do AD do Azure. (Para exibir todos os pontos de extremidade do AD do Azure, na barra de comandos, clique em **Exibir pontos de extremidade**.) Ao usar o Graph API para obter ou definir dados de diretório (alteração), seu aplicativo usa esse token de acesso no cabeçalho de Autorização da solicitação para o Graph API.
| Chaves | Se seu aplicativo lê ou grava dados no AD do Azure, como dados que são disponibilizados por meio do Graph API, seu aplicativo precisa de uma chave. Quando você solicita um token de acesso para chamar o Graph API, seu aplicativo fornece sua **ID de cliente** e **Chave**. O ponto de extremidade do token usa a ID e a chave para autenticar seu aplicativo antes de emitir o token de acesso. Você pode criar várias chaves para lidar com cenários de substituição de chave. Além disso, pode excluir chaves expiradas, comprometidas ou não mais em uso.
| Gerenciar acesso | Escolha um dos três diferentes níveis de acesso: SSO (logon único), SSO e dados de diretório de leitura ou SSO e dados de diretório de leitura/gravação. Você também pode remover o acesso. Para obter mais informações sobre o acesso ao diretório, consulte [Níveis de acesso do aplicativo](https://msdn.microsoft.com/library/azure/b08d91fa-6a64-4deb-92f4-f5857add9ed8#BKMK_AccessLevels).<br><BR>\*\*Observação\*\*: as alterações no nível de acesso de diretório do aplicativo se aplicam somente ao seu diretório. As alterações não se aplicam aos clientes que têm acesso a seu aplicativo.
 
 
### Clientes nativos
 
Propriedade | Descrição
| ------------- | ----------- 
| URI de redirecionamento | O URI para o qual o AD do Azure redirecionará o agente do usuário em resposta a uma solicitação de autorização OAuth 2.0. O valor não precisa ser um ponto de extremidade físico, mas deve ser um URI válido.

##


 
 

<!---HONumber=July15_HO5-->