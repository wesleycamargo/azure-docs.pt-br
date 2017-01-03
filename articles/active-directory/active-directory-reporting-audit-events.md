---
title: "Eventos do relatório de auditoria do Azure Active Directory | Microsoft Docs"
description: "Eventos de auditoria que estão disponíveis para exibição e download no Active Directory do Azure"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: mbaldwin
editor: 
ms.assetid: 307eedf7-05bc-448d-a84d-bead5a4c5770
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2016
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 6950072b8970dfc1b80674efe0be6cb7496fd5ce
ms.openlocfilehash: 2dda60160bbcaa729bc8e5cec4f47efc10d120cf


---
# <a name="azure-active-directory-audit-report-events"></a>Eventos de relatório de auditoria do Azure Active Directory
*Esta documentação é parte do [Guia de Relatórios do Azure Active Directory](active-directory-reporting-guide.md).*

O relatório de auditoria do Active Directory do Azure ajuda os clientes a identificar ações privilegiadas que ocorreram nos seus respectivos Active Directory do Azure. Ações com privilégios incluem alterações de elevação (por exemplo, criação de funções ou redefinições de senha), alteração de configurações de política (por exemplo, políticas de senha) ou alterações na configuração de diretório (por exemplo, as alterações às configurações de federação de domínio). Os relatórios fornecem o registro de auditoria para o nome do evento, o ator que executou a ação, o recurso de destino afetado pela alteração e a data e hora (em UTC). Os clientes são capazes de recuperar a lista dos eventos de auditoria para o Azure Active Directory por meio de [Portal do Azure](https://portal.azure.com/), como descrito em [Exibir seus Logs de Auditoria](active-directory-reporting-azure-portal.md).

## <a name="list-of-audit-report-events"></a>Lista de eventos de relatório de auditoria
<!--- audit event descriptions should be in the past tense --->

| Eventos | Descrição do evento |
| --- | --- |
| **Eventos de usuário** | |
| Adicionar usuário |Um usuário foi adicionado ao diretório. |
| Excluir usuário |Um usuário foi excluído do diretório. |
| Definir propriedades de licença |Defina as propriedades de licença para um usuário no diretório. |
| Redefinir senha de usuário |Redefina a senha de um usuário no diretório. |
| Alterar senha de usuário |A senha de um usuário no diretório foi alterada. |
| Alterar licença de usuário |A licença atribuída a um usuário no diretório foi alterada. Para ver quais licenças foram atualizadas, examine as propriedades de [Atualizar usuário](#update-user-attributes) abaixo |
| Atualizar usuário |Um usuário no diretório foi atualizado. [Veja abaixo](#update-user-attributes) os atributos que podem ser atualizados. |
| Definir alteração forçada de senha de usuário |Defina a propriedade que força o usuário a alterar sua senha no logon. |
| Atualizar credenciais de usuário |O usuário alterou a senha |
| **Eventos de grupo** | |
| Adicionar grupo |Criou um grupo no diretório. |
| Atualizar grupo |Atualizou um grupo no diretório. Para ver quais propriedades de grupo foram atualizadas, veja [Propriedades do Grupo Auditadas](#update-group-attributes) na seção abaixo |
| Excluir grupo |Excluiu um grupo no diretório. |
| CreateGroupSettings |Configurações de grupo criadas |
| UpdateGroupSettings |Configurações de grupo atualizadas. Para ver quais configurações de grupo foram atualizadas, veja [Propriedades de Grupo Auditadas](#update-group-attributes) na seção abaixo |
| DeleteGroupSettings |Configurações de grupo excluídas |
| SetGroupLicense |Definir licença de grupo. |
| SetGroupManagedBy |Definir grupo a ser gerenciado pelo usuário |
| AddGroupMember |Membro adicionado ao grupo |
| RemoveGroupMember |Remover membro do grupo |
| AddGroupOwner |Proprietário adicionado ao grupo |
| RemoveGroupOwner |Proprietário removido do grupo |
| **Eventos de aplicativo** | |
| Adicionar entidade de serviço |Uma entidade de serviço foi adicionada ao diretório. |
| Remover entidade de serviço |Uma entidade de serviço foi removida do diretório. |
| Adicionar credenciais de entidade de serviço |Credenciais adicionadas a uma entidade de serviço. |
| Remover credenciais de entidade de serviço |Credenciais removidas de uma entidade de serviço. |
| Adicionar entrada de delegação |[OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) criado no diretório. |
| Definir entrada de delegação |[OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) atualizado no diretório. |
| Remover entrada de delegação |[OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) excluído do diretório. |
| **Eventos de função** | |
| Adicionar um membro da função à Função |Um usuário foi adicionado a uma função de diretório. |
| Remover membro de função da Função |Um usuário foi removido de uma função de diretório. |
| AddRoleDefinition |Obter definição da função. |
| UpdateRoleDefinition |Atualizar definição de função. Para ver quais configurações de função foram atualizadas, veja [Propriedades de Definição da Função Auditadas](#update-role-definition-attributes) na seção abaixo |
| DeleteRoleDefinition |Definição de função excluída. |
| AddRoleAssignmentToRoleDefinition |Atribuição de função adicionada à definição de função. |
| RemoveRoleAssignmentFromRoleDefinition |Atribuição de função removida da definição de função. |
| AddRoleFromTemplate |Função adicionada de modelo. |
| UpdateRole |Função atualizada. |
| AddRoleScopeMemberToRole |Membro de escopo adicionado à função. |
| RemoveRoleScopedMemberFromRole |Membro de escopo removido da função. |
| **Eventos de Dispositivo (todos os novos eventos)** | |
| AddDevice |Dispositivo adicionado. |
| UpdateDevice |Dispositivo atualizado. Para ver quais propriedades de dispositivo foram atualizadas, veja [Propriedades de Dispositivo Auditadas](#update-device-attributes) na seção abaixo |
| DeleteDevice |Dispositivo excluído. |
| AddDeviceConfiguration |Configuração de dispositivo adicionado. |
| UpdateDeviceConfiguration |Configuração de dispositivo atualizado. Para ver quais propriedades de configuração de dispositivo foram atualizadas, veja [Propriedades de Configuração de Dispositivo Auditadas](#update-device-configuration-attributes) na seção abaixo |
| DeleteDeviceConfiguration |Configuração de dispositivo excluído. |
| AddRegisteredOwner |Proprietário registrado adicionado ao dispositivo. |
| AddRegisteredUsers |Usuários registrados adicionados ao dispositivo. |
| RemoveRegisteredOwner |Remover proprietário registrado do dispositivo. |
| RemoveRegisteredUsers |Remover usuários registrados do dispositivo. |
| RemoveDeviceCredentials |Remover credenciais de dispositivo. |
| **Eventos B2B** | |
| Convites do Lote carregados. |Um administrador carregou um arquivo que contém os convites a serem enviados para os usuários do parceiro. |
| Convites do Lote processados. |Um arquivo que contém os convites para os usuários do parceiro foi processado. |
| Convide um usuário externo. |Um usuário externo foi convidado para o diretório. |
| Resgate um convite para usuário externo. |Um usuário externo resgatou seu convite para o diretório. |
| Adicione o usuário externo ao grupo. |Um usuário externo recebeu associação a um grupo no diretório. |
| Atribua um usuário externo ao aplicativo. |Um usuário externo recebeu acesso direto a um aplicativo. |
| Criação de locatário viral. |Um novo locatário foi criado no AD do Azure pelo resgate do convite. |
| Criação de usuário viral. |Um usuário foi criado em um locatário existente no AD do Azure pelo resgate do convite. |
| **Unidades Administrativas (todos os novos eventos)** | |
| AddAdministrativeUnit |Adicionar unidade administrativa. |
| UpdateAdministrativeUnit |Atualizar unidade administrativa. Para ver quais propriedades de unidade administrativa foram atualizadas, veja [Propriedades de Unidade Administrativa Auditadas](#update-administrative-unit-attributes) na seção abaixo |
| DeleteAdministrativeUnit |Excluir unidade administrativa. |
| AddMemberToAdministrativeUnit |Adicionar membro a unidade administrativa. |
| RemoveMemberFromAdministrativeUnit |Remover membro de unidade administrativa. |
| **Eventos de diretório** | |
| Adicionar um parceiro à empresa |Um parceiro foi adicionado ao diretório. |
| Remover o parceiro da empresa |Um parceiro foi removido do diretório. |
| DemotePartner |Rebaixar parceiro. |
| Adicionar domínio a empresa |Um domínio foi adicionado ao diretório. |
| Remover o domínio da empresa |Um domínio foi removido do diretório. |
| Domínio de atualização |Um domínio no diretório foi atualizado. Para ver quais propriedades de Domínio foram atualizadas, veja [Propriedades de Domínio Auditadas](#update-domain-attributes) na seção abaixo |
| Definir a autenticação de domínio |A configuração de domínio padrão para a empresa foi alterada. |
| Definir informações de contato da empresa |Defina preferências de contato do nível da empresa. Isso inclui endereços de email para marketing, bem como notificações técnicas sobre os Serviços Online da Microsoft. |
| Definir configurações de federação no domínio |As configurações de federação para um domínio foram atualizadas. |
| Verificar domínio |Um domínio no diretório foi verificado. |
| Verificar domínio por email |Um domínio no diretório foi verificado usando verificação por email. |
| Definir o sinalizador DirSyncEnabled na empresa |Defina a propriedade que habilita um diretório para o Azure AD Sync. |
| Definir política de senha |Defina as restrições de comprimento e de caractere para senhas de usuário. |
| Definir informações da empresa |Atualizadas as informações de nível de empresa. Consulte o cmdlet [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) do PowerShell para obter mais detalhes. |
| SetCompanyAllowedDataLocation |Definir local de dados permitidos da empresa. |
| SetCompanyDirSyncEnabled |Definir sinalizador DirSyncEnabled. |
| SetCompanyDirSyncFeature |Definir recurso DirSync. |
| SetCompanyInformation |Definir Informações da Empresa. |
| SetCompanyMultiNationalEnabled |Definir recurso multinacional de empresa habilitado. |
| SetDirectoryFeatureOnTenant |Definir recurso de diretório no locatário. |
| SetTenantLicenseProperties |Definir propriedades de licença de locatário. |
| CreateCompanySettings |Criar configurações da empresa |
| UpdateCompanySettings |Atualizar configurações da empresa. Para ver quais propriedades de Empresa foram atualizadas, veja [Propriedades de Empresa Auditadas](#update-company-attributes) na seção abaixo |
| DeleteCompanySettings |Excluir configurações da empresa |
| SetAccidentalDeletionThreshold |Definir limite de exclusão acidental. |
| SetRightsManagementProperties |Definir propriedades de gerenciamento de direitos. |
| PurgeRightsManagementProperties |Limpar propriedades de gerenciamento de direitos. |
| UpdateExternalSecrets |Atualizar segredos externos |
| **Eventos de Política (todos os novos eventos)** | |
| AddPolicy |Adicionar política. |
| UpdatePolicy |Atualizar política. |
| DeletePolicy |Excluir política. |
| AddDefaultPolicyApplication |Adicionar política ao aplicativo. |
| AddDefaultPolicyServicePrincipal |Adicionar política à entidade de serviço. |
| RemoveDefaultPolicyApplication |Remover política do aplicativo. |
| RemoveDefaultPolicyServicePrincipal |Remover política da entidade de serviço. |
| RemovePolicyCredentials |Remover credenciais de política. |

## <a name="audit-report-retention"></a>Retenção do relatório de auditoria
Os eventos no relatório de auditoria do Azure AD são mantidos por 180 dias. Para saber mais sobre retenção de relatórios, confira [Políticas de retenção de relatório do Active Directory do Azure](active-directory-reporting-retention.md).

Para clientes interessados no armazenamento de seus eventos de auditoria por períodos mais longos, a API de Relatório pode ser usada regularmente receber eventos de auditoria em um armazenamento de dados separado. Confira [Introdução à API de Relatório](active-directory-reporting-api-getting-started.md) para obter detalhes.

## <a name="properties-included-with-each-audit-event"></a>Propriedades incluídas com cada evento de auditoria
| Propriedade | Descrição |
| --- | --- |
| Data e hora |A data e hora em que o evento de auditoria ocorreu |
| Ator |O usuário ou a entidade de serviço que executou a ação |
| Ação |A ação que foi executada |
| Destino |O usuário ou a entidade de serviço em que a ação foi executada |

## <a name="update-user-attributes"></a>Atributos de “Atualizar Usuário”
O evento de auditoria "Atualizar usuário" inclui informações adicionais sobre quais atributos de usuário foram atualizados. Para cada atributo, o valor anterior e o novo valor são ambos incluídos.

| Atributo | Descrição |
| --- | --- |
| AccountEnabled |O usuário pode entrar. |
| AssignedLicense |Todas as licenças que foram atribuídas ao usuário. |
| AssignedPlan |Planos de serviço resultante das licenças atribuídas ao usuário. |
| LicenseAssignmentDetail |Detalhes sobre licenças atribuídas ao usuário. Por exemplo, se o licenciamento baseado em grupo estava envolvido, isso inclui o grupo que concedeu a licença. |
| Móvel |O celular do usuário. |
| OtherMail |O endereço de email alternativo do usuário. |
| OtherMobile |O celular alternativo do usuário. |
| StrongAuthenticationMethod |Uma lista de métodos de verificação configurados pelo usuário para Multi-Factor Authentication, como o código de chamada de voz, SMS ou verificação de um aplicativo móvel. |
| StrongAuthenticationRequirement |Se a Multi-Factor Authentication é imposta, habilitada ou desabilitada para este usuário. |
| StrongAuthenticationUserDetails |Número de telefone do usuário, número de telefone alternativo e endereço de email usado para verificação de Multi-Factor Authentication e redefinição de senha. |
| StrongAuthenticationPhoneAppDetail |Detalhes de aplicativos registrados para executar logon no 2FA |
| TelephoneNumber |O número de telefone do usuário. |
| AlternativeSecurityId |Uma ID de segurança alternativa para o objeto. |
| CreationType |Método de criação do usuário (seja por convite ou viral). |
| InviteTicket |Lista de tíquetes de convite para o usuário. |
| InviteReplyUrl |Lista de urls para responder mediante a aceitação do convite. |
| InviteResources |Lista de recursos aos quais o usuário foi convidado. |
| LastDirSyncTime |Última vez em que o objeto foi atualizado devido à sincronização do diretório autoritativo (cliente, local). |
| MSExchRemoteRecipientType |Mapeia para tipos de destinatário do MSO. Consulte [tipos de destinatário MSO] https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx para ver os tipos de destinatário |
| PreferredDataLocation |O local preferido dos dados do usuário, do grupo, do contato, da pasta pública ou do dispositivo. |
| ProxyAddresses |O endereço pelo qual um objeto de destinatário do Exchange Server é reconhecido em um sistema de email externo. |
| StsRefreshTokensValidFrom |Carrega informações de revogação de token de atualização - qualquer token de atualização STS emitido antes desse momento serão considerados como expirados. |
| UserPrincipalName |O UPN é um nome de logon do estilo da Internet para um usuário. |
| UserState |Status de User PendingApproval/PendingAcceptance/Accepted/PendingVerification. |
| UserStateChangedOn |Carimbo de data/hora da última alteração feita em UserState. Usado para disparar fluxos de trabalho do ciclo de vida. |
| UserType |Tipo de usuário. Membro (0), Convidado (1), Viral(2). |

## <a name="update-group-attributes"></a>Atributos de "Atualizar Grupo"
| Atributo | Descrição |
| --- | --- |
| Classificação |A classificação de um Grupo Unificado (ICA, ICM etc). |
| Descrição |Frases descritivas legíveis sobre o objeto. |
| DisplayName |O nome de exibição para um objeto |
| DirSyncEnabled |Indica se a sincronização ocorre de um diretório autoritativo (cliente, local). |
| GroupLicenseAssignment |Atribuição de licença de um grupo |
| GroupType |Tipo de Grupo, Unificado (0) |
| IsMembershipRuleLocked |Indica que a propriedade MembershipRule é definida pelo serviço de gerenciamento de grupos de autoatendimento e não pode ser alterada pelos usuários. Aplicável apenas a grupos onde GroupType inclui GroupType.DynamicMembership |
| IsPublic |Sinalizador para indicar se o grupo é público/privado. |
| LastDirSyncTime |Última vez em que o objeto foi atualizado como resultado da sincronização do diretório autoritativo (cliente, local). |
| Email |Endereço de email principal |
| MailEnabled |Indica se este grupo tem a funcionalidade de email. |
| MailNickname |Moniker para um objeto do catálogo de endereços, geralmente a parte de seu nome de email precedida do símbolo "@". |
| MembershipRule |Uma cadeia de caracteres que expressa os critérios usados pelo serviço de gerenciamento de grupos de autoatendimento para determinar quais membros deverão pertencer a esse grupo. Veja também IsMembershipRuleLocked. Aplicável somente aos grupos onde GroupType inclui GroupType.DynamicMembership. |
| MembershipRuleProcessingState |Um valor de enumeração definido pelo serviço de gerenciamento de grupos de autoatendimento que define o status de processamento de associação para esse grupo. Aplicável somente aos grupos onde GroupType inclui GroupType.DynamicMembership. |
| ProxyAddresses |O endereço pelo qual um objeto de destinatário do Exchange Server é reconhecido em um sistema de email externo. |
| RenewedDateTime |Registro de carimbo de data/hora de quando um grupo foi renovado pela última vez. |
| SecurityEnabled |Indica se a associação ao grupo pode influenciar as decisões de autorização. |
| WellKnownObject |Rotula um objeto de diretório, indicando-o como parte de um conjunto predefinido. |

## <a name="update-device-attributes"></a>Atributos de "Atualizar Dispositivo"
| Atributo | Descrição |
| --- | --- |
| AccountEnabled |Indica se uma entidade de segurança pode ser autenticada. |
| CloudAccountEnabled |Indica se uma entidade de segurança pode ser autenticada. Gravado pelo InTune quando o dispositivo é controlado no local. |
| CloudDeviceOSType |Tipo de dispositivo com base no sistema operacional, por exemplo, Windows RT, iOS. Quando definido por um serviço de nuvem (como o Intune), este atributo se torna autoritativo no diretório para DeviceOSType. |
| CloudDeviceOSVersion |Versão do sistema operacional. Quando definido por um serviço de nuvem (como o Intune), este atributo se torna autoritativo no diretório para DeviceOSVersion. |
| CloudDisplayName |Valor do atributo LDAP displayName. Quando definido por um serviço de nuvem (como o Intune), este atributo se torna autoritativo no diretório de displayName. |
| CloudCreated |Indica se o objeto foi criado por serviços de nuvem. |
| CompliantUntil |Até que horas o dispositivo será considerado compatível. |
| DeviceMetadata |Metadados personalizados para o dispositivo |
| DeviceObjectVersion |Este atributo é usado para identificar a versão do esquema do dispositivo. |
| DeviceOSType |Tipo de dispositivo com base no sistema operacional, por exemplo, Windows RT, iOS. Gravado pelo Serviço de Registro e se destina a ser atualizado pelo serviço de gerenciamento do MDM ou pelo serviço de gerenciamento leve do STS. |
| DeviceOSVersion |Versão do sistema operacional. Gravado pelo Serviço de Registro e se destina a ser atualizado pelo serviço de gerenciamento do MDM ou pelo serviço de gerenciamento leve do STS. |
| DevicePhysicalIds |Atributo com diversos valores para armazenar identificadores do dispositivo físico. Isso pode incluir IDs de BIOS, impressões digitais de TPM, IDs específicas do hardware etc. |
| DirSyncEnabled |Indica se a sincronização ocorre de um diretório autoritativo (cliente, local). |
| DisplayName |O nome de exibição para um objeto |
| IsCompliant |Este atributo é usado para gerenciar o status de gerenciamento de dispositivos móveis do dispositivo. |
| IsManaged |Este atributo é usado para indicar que o dispositivo é gerenciado por um MDM de nuvem. |
| LastDirSyncTime |Última vez em que o objeto foi atualizado devido à sincronização do diretório autoritativo (cliente, local). |

## <a name="update-device-configuration-attributes"></a>Atributos de "Atualizar Configuração do Dispositivo"
| Atributo | Descrição |
| --- | --- |
| MaximumRegistrationInactivityPeriod |O número máximo de dias que um dispositivo pode ficar inativo antes de ser considerado para remoção. |
| RegistrationQuota |Política usada para limitar o número de registros de dispositivos permitidos para um único usuário. |

## <a name="update-service-principal-configuration-attributes"></a>Atributos de "Atualizar Configuração de Entidade de Serviço"
| Atributo | Descrição |
| --- | --- |
| AccountEnabled |Indica se uma entidade de segurança pode ser autenticada. |
| AppPrincipalId |Identidade externa, definida pelo aplicativo para uma entidade de segurança. |
| DisplayName |O nome de exibição para um objeto |
| ServicePrincipalName |Um nome de entidade de serviço que contém "nome/autoridade", onde o nome especifica um valor de classe do aplicativo e a autoridade contém pelo menos nomedohost[:porta] ou "nome", que especifica um identificador para a entidade de serviço. |

## <a name="update-app-attributes"></a>Atributos de "Atualizar Aplicativo"
| Atributo | Descrição |
| --- | --- |
| AppAddress |O conjunto de endereços (URLs de redirecionamento) atribuídos a uma entidade de serviço. |
| AppId |ID do Aplicativo do Aplicativo |
| AppIdentifierUri |URI do Aplicativo, que identifica o aplicativo.  Geralmente, é a URL de acesso do aplicativo. |
| AppLogoUrl |A url para a imagem de logotipo do aplicativo armazenada em uma CDN. |
| AvailableToOtherTenants |Se verdadeiro, o aplicativo é um aplicativo multilocatário (ou seja, pode ser usado por outros locatários). |
| DisplayName |O nome de exibição para um Nome de Aplicativo |
| Direito |Lista de direitos do aplicativo. |
| ExternalUserAccountDelegationsAllowed |Sinalizador que indica se o aplicativo de recurso é confiável e se pode criar entradas de delegação para contas de usuário externo. |
| GroupMembershipClaims |A política de declarações de associação de grupo. |
| PublicClient |Se verdadeiro, o cliente não pode ser mantido em segredo (ou seja, cliente não confidencial no OAuth 2.0) |
| RecordConsentConditions |Tipos de condições de consentimento, conforme definido pelos termos do contrato: None (0), SilentConsentForPartnerManagedApp(1). Esse valor será exposto no esquema da API do Graph e só pode ser definido/alterado por administradores de locatários. |
| RequiredResourceAccess |Conteúdo XML de um Valor da propriedade RequiredResourceAccess. |
| WebApp |Se verdadeiro, indica que esse aplicativo é um aplicativo Web. |
| WwwHomepage |A página da Web principal. |

## <a name="update-role-attributes"></a>Atributos de "Atualizar Função"
| Atributo | Descrição |
| --- | --- |
| AppAddress |O conjunto de endereços (URLs de redirecionamento) atribuídos a uma entidade de serviço. |
| BelongsToFirstLoginObjectSet |Se for verdadeiro, indica que esse objeto pertence ao conjunto de objetos necessários para habilitar o logon do primeiro administrador de um novo locatário. |
| Builtin |Indica se o tempo de vida de um objeto é de propriedade do sistema. |
| Descrição |Frases descritivas legíveis sobre o objeto. |
| DisplayName |O nome de exibição para um objeto |
| MailNickname |Moniker para um objeto do catálogo de endereços, geralmente a parte de seu nome de email precedida do símbolo "@". |
| RoleDisabled |Indica se a função deve ser ignorada para fins de verificações de acesso. |
| RoleTemplateId |Identidade do modelo de função. |
| ServiceInfo |Informações de provisionamento específicas do serviço que podem ser consumidas pelo MOAC e/ou por outras instâncias de serviço (do mesmo tipo de serviço ou de tipos diferentes). |
| TaskSetScopeReference |Identifica um TaskSet e um conjunto de Escopos associados a uma Função ou RoleTemplate. |
| ValidationError |As informações publicadas por um serviço federado que descreve um erro não transitório específico do serviço relacionado às propriedades ou vinculado de uma ação de administrador de objetos a ser resolvida. |
| WellKnownObject |Rotula um objeto de diretório, indicando-o como parte de um conjunto predefinido. |

## <a name="update-role-definition-attributes"></a>Atributos "Atualizar Definição de Função"
| Atributo | Descrição |
| --- | --- |
| AssignableScopes |Coleção de escopos de autorização que podem ser referenciados na atribuição dessa RoleDefinition a uma entidade de segurança. |
| DisplayName |O nome de exibição para um objeto |
| GrantedPermissions |Permissões concedidas por esta RoleDefinition. |

## <a name="update-administrative-unit-attributes"></a>Atributos de "Atualizar Unidade Administrativa"
| Atributo | Descrição |
| --- | --- |
| Descrição |Essa propriedade é atualizada quando você altera a descrição de uma unidade administrativa. |
| DisplayName |Essa propriedade é atualizada quando você altera o nome de uma unidade administrativa. |

## <a name="update-company-attributes"></a>Atributos de "Atualizar Empresa"
| Atributo | Descrição |
| --- | --- |
| AllowedDataLocation |Um local em que os usuários da empresa podem ser provisionados. |
| AuthorizedServiceInstance |Nomes de instâncias de serviço em que um plano pode ser implantado. |
| DirSyncEnabled |Indica se a sincronização ocorre de um diretório autoritativo (cliente, local). |
| DirSyncStatus |Indica se a sincronização de objetos de catálogo de endereços neste contexto de locatário ocorre de um diretório autoritativo (cliente, local); uma expansão da propriedade DirSyncEnabled em objetos Empresa. |
| DirSyncFeatures |Sinalizador de bits para manter o controle de recursos de dirsync habilitados e desabilitados para o locatário. |
| DirectoryFeatures |Recursos de diretório habilitados/desabilitados. |
| DirSyncConfiguration |Contém todas as configurações de DirSync específicos do locatário atual. |
| DisplayName |O nome de exibição para um objeto |
| IsMnc |Um sinalizador Booliano definido como "true" caso a empresa esteja habilitada para o recurso de empresa multinacional. |
| ObjectSettings |Uma coleção de configurações aplicáveis ao escopo do objeto. |
| PartnerCommerceUrl |URL para o site de comércio do Parceiro. |
| PartnerHelpUrl |URL para o site de Ajuda do Parceiro. |
| PartnerSupportEmail |URL para o email de suporte do Parceiro. |
| PartnerSupportTelephone |URL para o telefone de suporte do Parceiro. |
| PartnerSupportUrl |URL para o site de suporte do Parceiro. |
| StrongAuthenticationDetails |Detalhes relacionados a StrongAuthentication. |
| StrongAuthenticationPolicy |Política de autenticação forte para a empresa. |
| TechnicalNotificationMail |O endereço de email para notificar problemas técnicos relativos a uma empresa. |
| TelephoneNumber |Os números de telefone em conformidade com a recomendação E.123 da ITU. |
| TenantType |O tipo de um locatário. Se esse valor não for especificado, o locatário será uma Empresa. Caso contrário, os valores possíveis são: MicrosoftSupport (0), SyndicatePartner (1), BreadthPartner (2) BreadthPartnerDelegatedAdmin (3) ResellerPartnerDelegatedAdmin (4) ValueAddedResellerPartnerDelegatedAdmin (5). |
| VerifiedDomain |Um conjunto de nomes de domínio DNS associado a uma Empresa. |

## <a name="update-domain-attributes"></a>Atributos de "Atualizar Domínio"
| Atributo | Descrição |
| --- | --- |
| Funcionalidades |Sinalizadores de bits que descrevem os recursos do domínio, se houver. |
| Padrão |Indica se o domínio é o valor padrão; por exemplo, o sufixo padrão UserPrincipalName quando um administrador cria um novo usuário no MOAC. |
| Inicial |Indica se o domínio é o domínio inicial para a empresa, como alocado pelo OCP. O domínio inicial é um subdomínio exclusivo de um domínio Microsoft Online; por exemplo, contoso3.microsoftonline.com. |
| LiveType |Tipo do namespace do Windows Live correspondente, se houver. |
| Nome |Identificador para o ponto de extremidade. |
| PasswordNotificationWindowDays |O número de dias antes que uma senha expire em que o usuário será notificado. |
| PasswordValidityPeriodDays |O número de dias que uma senha valerá antes de ter de ser alterada. |

Registros de auditoria são um controle necessário para muitas regulamentações de conformidade. Para clientes que usam o Relatório de Auditoria do Azure Active Directory para atender aos seus regulamentos de conformidade, recomenda-se que o cliente envie uma cópia deste tópico de ajuda com a cópia do relatório de auditoria exportado do cliente para ajudar a explicar os detalhes do relatório. Se o auditor gostaria de entender os regulamentos de conformidade que o Azure atende atualmente, indique a [Página de conformidade](https://azure.microsoft.com/support/trust-center/compliance/) da Central de confiabilidade do Microsoft Azure ao auditor.




<!--HONumber=Dec16_HO5-->


