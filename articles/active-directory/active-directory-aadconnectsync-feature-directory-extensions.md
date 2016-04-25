<properties
   pageTitle="Sincronização do Azure AD Connect: extensões do Directory | Microsoft Azure"
   description="Este tópico descreve o recurso Extensões de diretório no Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/12/2016"
   ms.author="andkjell"/>

# Sincronização do Azure AD Connect: extensões do Directory
Extensões de diretório permite que você estenda o esquema no Azure AD com seus próprios atributos do Active Directory local. Isso permite que você compile aplicativos de LOB consumindo atributos que você continua gerenciando localmente. Esses atributos podem ser consumidos por meio de [extensões de diretório do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou [Microsoft Graph](https://graph.microsoft.io/). Você pode ver os atributos disponíveis usando [gerenciador do Azure AD Graph](https://graphexplorer.cloudapp.net) e [o gerenciador do Microsoft Graph](https://graphexplorer2.azurewebsites.net/) respectivamente.

No momento, nenhuma carga de trabalho do Office 365 consumirá esses atributos.

Configure quais atributos adicionais você deseja sincronizar no caminho de configurações personalizadas no assistente de instalação.
![Assistente de extensão do esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) A instalação mostrará os seguintes atributos, que são candidatos válidos:

- Tipos de objeto de Usuário e de Grupo
- Atributos de valor único: String, Boolean, Integer, Binary
- Atributos de vários valores: String, Binary

Um objeto pode ter até 100 atributos de extensões de diretório. O comprimento máximo é de 250 caracteres. Se o valor de um atributo for maior, ele será truncado pelo mecanismo de sincronização.

Durante a instalação do Azure AD Connect, um aplicativo será registrado onde esses atributos estarão disponíveis. Você pode ver esse aplicativo no Portal do Azure.
![Aplicativo de extensão do esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

Agora, estes atributos estarão disponíveis por meio do Graph:
![Gráfico](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Os atributos são prefixados com extension\_{AppClientId}\_. O AppClientId terá o mesmo valor para todos os atributos no seu diretório do AD do Azure.

## Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0413_2016-->