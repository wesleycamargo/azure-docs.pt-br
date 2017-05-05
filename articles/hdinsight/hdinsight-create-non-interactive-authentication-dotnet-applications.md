---
title: "Criar aplicativos .NET HDInsight de autenticação não interativa | Microsoft Docs"
description: "Saiba como criar aplicativos .NET HDInsight de autenticação não interativa."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 2b8638ffc3287346a71f591370367655c450e376
ms.lasthandoff: 04/18/2017


---
# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>Criar aplicativos .NET HDInsight de autenticação não interativa
Você pode executar seu aplicativo .NET do Azure HDInsight na própria identidade do aplicativo (não interativo) ou na identidade do usuário conectado do aplicativo (interativo). Para obter um exemplo do aplicativo interativo, consulte [Conectar-se ao Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). Este artigo mostra como criar um aplicativo .NET de autenticação não interativa para se conectar ao Azure e gerenciar o HDInsight.

Em seu aplicativo .NET não interativo, você precisa:

* Da sua ID de locatário da assinatura do Azure (também conhecida como ID de diretório). Veja [Obter a ID de locatário](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
* A ID de cliente do aplicativo do Azure Active Directory. Consulte [Criar um aplicativo do Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) e [Obter uma ID de aplicativo](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)
* A chave secreta do aplicativo do Azure Active Directory. Consulte [Obter chave de autenticação do aplicativo](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)

## <a name="prerequisites"></a>Pré-requisitos
* Cluster HDInsight. Consulte [tutorial de introdução](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).



## <a name="assign-azure-ad-application-to-role"></a>Atribuir o aplicativo do Azure AD à função
Você deve atribuir o aplicativo a uma [função](../active-directory/role-based-access-built-in-roles.md) para conceder a ele permissões para executar ações. Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissões são herdadas de níveis inferiores do escopo (por exemplo, adicionar um aplicativo à função Leitor de um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contiver). Neste tutorial, você definirá o escopo no nível de grupo de recursos. Para obter mais informações, consulte [Usar atribuições de função para gerenciar o acesso aos recursos de assinatura do Azure](../active-directory/role-based-access-control-configure.md)

**Adicionar a função de Proprietário ao aplicativo do Azure AD**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique no **Grupo de Recursos** no painel esquerdo.
3. Clique no grupo de recursos que contém o cluster HDInsight, no qual você executará a consulta Hive posteriormente neste tutorial. Se houver muitos grupos de recursos, você poderá usar o filtro.
4. Clique em **Controle de acesso (IAM)** no menu do grupo de recursos.
5. Clique em **Adicionar** na folha **Usuários**.
6. Siga as instruções para adicionar a função **Proprietário** ao aplicativo do Azure AD que você criou no último procedimento. Ao concluir a tarefa com êxito, você deverá ver o aplicativo listado na folha Usuários com a função de Proprietário.

## <a name="develop-hdinsight-client-application"></a>Desenvolver aplicativos do cliente HDInsight

1. Criar um aplicativo de console em C#
2. Adicione os seguintes pacotes NuGet:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Use o seguinte exemplo de código:

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.HDInsight;
        
        namespace CreateHDICluster
        {
            internal class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
        
                private static Guid SubscriptionId = new Guid("<Enter Your Azure Subscription ID>");
                private static string tenantID = "<Enter Your Tenant ID (A.K.A. Directory ID)>";
                private static string applicationID = "<Enter Your Application ID>";
                private static string secretKey = "<Enter the Application Secret Key>";
        
                private static void Main(string[] args)
                {
                    var key = new SecureString();
                    foreach (char c in secretKey) { key.AppendChar(c); }

                    var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
        
                    var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                    resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        
                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
        
                    var results = _hdiManagementClient.Clusters.List();
                    foreach (var name in results.Clusters)
                    {
                        Console.WriteLine("Cluster Name: " + name.Name);
                        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                        Console.WriteLine("\t Cluster location: " + name.Location);
                        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                    }
                    Console.WriteLine("Press Enter to continue");
                    Console.ReadLine();
                }

                /// Get the access token for a service principal and provided key                
                public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
                {
                    var authFactory = new AuthenticationFactory();
                    var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                    var accessToken =
                        authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
        
                    return new TokenCloudCredentials(accessToken);
                }
        
                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
            }
        }

## <a name="next-steps"></a>Próximas etapas
* [Criar o aplicativo do Azure Active Directory e a entidade de serviço usando o portal](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Autenticação de uma entidade de serviço com o Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Controle de Acesso Baseado em Função do Azure](../active-directory/role-based-access-control-configure.md)

