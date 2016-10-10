<properties
   pageTitle="Sincronização do Azure AD Connect: extensões do Directory | Microsoft Azure"
   description="Este tópico descreve o recurso de extensões de diretório no Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/19/2016"
   ms.author="andkjell"/>

# Sincronização do Azure AD Connect: extensões do Directory
Extensões de diretório permite que você estenda o esquema no Azure AD com seus próprios atributos do Active Directory local. Esse recurso permite criar aplicativos de LOB que consumem atributos que você continua gerenciando localmente. Esses atributos podem ser consumidos por meio de [extensões de diretório do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou [Microsoft Graph](https://graph.microsoft.io/). Você pode ver os atributos disponíveis usando [gerenciador do Azure AD Graph](https://graphexplorer.cloudapp.net) e [o gerenciador do Microsoft Graph](https://graphexplorer2.azurewebsites.net/) respectivamente.

No momento, nenhuma carga de trabalho do Office 365 consome esses atributos.

Configure quais atributos adicionais você deseja sincronizar no caminho de configurações personalizadas no assistente de instalação. ![Assistente de extensão do esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) A instalação mostra os seguintes atributos, que são candidatos válidos:

- Tipos de objeto de Usuário e de Grupo
- Atributos de valor único: String, Boolean, Integer, Binary
- Atributos de vários valores: String, Binary

A lista de atributos é lida do cache criado durante a instalação do Azure AD Connect. Se você estendeu o esquema do Active Directory com atributos adicionais, o [esquema deve ser atualizado](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) para que esses novos atributos fiquem visíveis.

Um objeto pode ter até 100 atributos de extensões de diretório. O comprimento máximo é de 250 caracteres. Se um valor de atributo for mais longo, ele será truncado pelo mecanismo de sincronização.

Durante a instalação do Azure AD Connect, é registrado um aplicativo no qual esses atributos estão disponíveis. Você pode ver esse aplicativo no Portal do Azure. ![Aplicativo de extensão do esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

Esses atributos agora estão disponíveis por meio do Graph: ![Gráfico](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Os atributos são prefixados com extension\_{AppClientId}\_. O AppClientId tem o mesmo valor para todos os atributos no seu diretório do Azure AD.

## Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Saiba mais sobre como [Integrar suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0928_2016-->