<properties
   pageTitle="Autenticar com o Data Lake Store usando o Active Directory | Microsoft Azure"
   description="Saiba como autenticar com o Data Lake Store usando o Active Directory"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>


# <a name="authenticate-with-data-lake-store-using-azure-active-directory"></a>Autenticar com o Data Lake Store usando o Azure Active Directory

O Azure Data Lake Store usa o Azure Active Directory para autenticação. Antes de criar um aplicativo que funciona com o Azure Data Lake Store ou com o Azure Data Lake Analytics, primeiro você deve decidir como deseja autenticar seu aplicativo no Azure Active Directory (Azure AD). As duas principais opções disponíveis são:

* Autenticação de usuário final e 
* Autenticação serviço a serviço. 

As duas opções resultam no fornecimento de um token OAuth 2.0 a seu aplicativo, que é anexado a cada solicitação feita ao Azure Data Lake Store ou ao Azure Data Lake Analytics.


## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* A ID de sua assinatura. Você pode habilitá-la no Portal do Azure. Por exemplo, está disponível na folha da conta do Data Lake Store.

    ![Obter ID da assinatura](./media/data-lake-store-authenticate-using-active-directory/get-subscription-id.png)

* O nome de domínio do Azure AD. Você pode recuperá-lo passando o mouse sobre o canto superior direito do Portal do Azure. 

    ![Obter domínio do AAD](./media/data-lake-store-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>Autenticação do usuário final

Essa é a abordagem recomendada se você quer que um usuário final entre em seu aplicativo por meio do Azure AD. Seu aplicativo será capaz de acessar recursos do Azure com o mesmo nível de acesso do usuário final que fez logon. O usuário final precisará fornecer suas credenciais periodicamente para manter o acesso de seu aplicativo.

O resultado de fazer o usuário final entrar é que seu aplicativo recebe um token de acesso e um token de atualização. O token de acesso é anexado a cada solicitação feita ao Data Lake Store ou ao Data Lake Analytics e é válido por uma hora por padrão. O token de atualização pode ser usado para obter um novo token de acesso e é válido por até duas semanas, por padrão, se usado regularmente. Você pode usar duas abordagens diferentes para o logon do usuário final.

### <a name="using-the-oauth-2.0-pop-up"></a>Usando o pop-up OAuth 2.0

Seu aplicativo pode disparar um pop-up de autorização OAuth 2.0 no qual o usuário final pode inserir suas credenciais. Essa janela pop-up também funciona com o processo de autenticação do Azure AD de dois fatores (2FA), se necessário. 

>[AZURE.NOTE] Esse método ainda não tem suporte na ADAL (biblioteca de autenticação do Azure AD) para Python ou Java.

### <a name="directly-passing-in-user-credentials"></a>Transmitindo credenciais do usuário diretamente

Seu aplicativo pode fornecer credenciais de usuário ao Azure AD diretamente. Esse método funciona apenas com as contas de usuário da ID organizacional; ele não é compatível com contas de usuário pessoais/"live ID", incluindo aquelas que terminam em @outlook.com ou @live.com. Além disso, esse método não é compatível com as contas de usuário que exigem 2FA (Autenticação de Dois Fatores) do Azure AD.

### <a name="what-do-i-need-to-use-this-approach?"></a>O que é necessário para usar essa abordagem?

* Nome de domínio do Azure AD (já listado no pré-requisito deste artigo).

* **Aplicativo cliente nativo**do Azure AD. 

* ID do cliente para o aplicativo de cliente nativo do Azure AD.

* URI de Redirecionamento para o aplicativo cliente nativo do Azure AD. 

Para obter instruções sobre como criar um aplicativo do Azure AD e recuperar a ID do cliente, confira [Criar um aplicativo do Active Directory](../resource-group-create-service-principal-portal.md#create-an-active-directory-application). 

>[AZURE.NOTE] As instruções nos links acima são para um aplicativo Web do Azure AD. No entanto, as etapas são exatamente as mesmas até quando você opta por criar um aplicativo de cliente nativo.

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço

Essa é a abordagem recomendada se você deseja que seu aplicativo autentique automaticamente no Azure AD, sem a necessidade de um usuário final fornecendo suas credenciais. Seu aplicativo será capaz de se autenticar desde que suas credenciais sejam válidas, que podem ser personalizadas para durar anos.

### <a name="what-do-i-need-to-use-this-approach?"></a>O que é necessário para usar essa abordagem?

* Nome de domínio do Azure AD (já listado no pré-requisito deste artigo).

* **Aplicativo web**do Azure AD.

* ID do cliente para o aplicativo Web do Azure AD.

    >[AZURE.NOTE] Para obter instruções sobre como criar um aplicativo do Azure AD e recuperar a ID do cliente, confira [Criar um aplicativo do Active Directory](../resource-group-create-service-principal-portal.md#create-an-active-directory-application).
    
* Configure o aplicativo Web do Azure AD para usar o segredo do cliente ou um certificado. Para criar um aplicativo Web usando um certificado, confira [Criar uma entidade de serviço com certificado](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

* Habilite o acesso para o aplicativo Web do Azure AD no arquivo/pasta do Data Lake Store ou na conta do Data Lake Analytics em que você deseja trabalhar. Para obter instruções sobre como fornecer acesso a um aplicativo do Azure AD a um arquivo/pasta do Data Lake Store, confira [Atribuir usuários ou grupo de segurança como ACLs no sistema de arquivos do Azure Data Lake Store](data-lake-store-secure-data.md#filepermissions).

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Repositório Azure Data Lake usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)
- [Introdução ao Azure Data Lake Store usando o SDK do Java](data-lake-store-get-started-java-sdk.md)



<!--HONumber=Oct16_HO2-->


