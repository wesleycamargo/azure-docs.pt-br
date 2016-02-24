<properties
   pageTitle="Azure AD Connect: recursos em visualização | Microsoft Azure"
   description="Este tópico descreve em mais detalhes os recursos que estão na visualização no Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="01/21/2016"
   ms.author="andkjell;billmath"/>

# Mais detalhes sobre os recursos no modo de visualização
Este tópico descreve como usar recursos presentes atualmente na visualização.

## Extensões de diretório
Extensões de diretório permite que você estenda o esquema no Azure AD com seus próprios atributos do Active Directory local. Isso permite que você compile aplicativos de LOB consumindo atributos que você continua gerenciando localmente. Esses atributos podem ser consumidos por meio de [extensões de diretório do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) ou [Microsoft Graph](https://graph.microsoft.io/). Você pode ver os atributos disponíveis usando [gerenciador do Azure AD Graph](https://graphexplorer.cloudapp.net) e [o gerenciador do Microsoft Graph](https://graphexplorer2.azurewebsites.net/) respectivamente.

No momento, nenhuma carga de trabalho do Office 365 consumirá esses atributos.

Durante a instalação do Azure AD Connect, um aplicativo será registrado onde esses atributos estarão disponíveis. Você pode ver esse aplicativo no Portal do Azure. ![Aplicativo de extensão do esquema](./media/active-directory-aadconnect-feature-preview/extension3.png)

Agora, estes atributos estarão disponíveis por meio do Graph: ![Gráfico](./media/active-directory-aadconnect-feature-preview/extension4.png)

Os atributos são prefixados com extension\_{AppClientId}\_. O AppClientId terá o mesmo valor para todos os atributos no seu diretório do AD do Azure.

Há suporte apenas para atributos de valor único e os valores nos atributos não podem ter mais de 250 caracteres.

## Write-back de grupo
A opção para write-back do grupo nos recursos opcionais permitirá que você faça write-back de "Grupos do Office 365" para uma floresta com o Exchange instalado. Esse é um grupo que é sempre controlado na nuvem. Se tiver o Exchange local, você pode gravar novamente esses grupos localmente, assim os usuários com uma caixa de correio do Exchange local podem enviar e receber emails destes grupos.

Mais informações sobre Grupos do Office 365 e como usá-los podem ser encontradas [aqui](http://aka.ms/O365g).

Esse grupo será representado como um grupo de distribuição no AD DS local. Seu servidor Exchange local deve estar no Exchange 2013 com atualização cumulativa 8 (lançado em março de 2015) ou no Exchange 2016 para reconhecer esse novo tipo de grupo.

**Observações no período de visualização**

- Atualmente, o atributo de catálogo de endereços não é populado na visualização. Sem esse atributo, o grupo não estará visível na GAL. A maneira mais fácil é preencher esse atributo para usar o cmdlet `update-recipient` do PowerShell do Exchange.
- Somente as florestas com o esquema do Exchange são alvos válidos para grupos. Se nenhum Exchange for detectado, não será possível habilitar o write-back de grupo.
- Somente implantações de floresta única de organização do Exchange terão suporte atualmente. Se você tiver mais de uma organização de Exchange local, será necessária uma solução de GALSync local para que esses grupos sejam exibidos em suas outras florestas.
- O recurso de write-back de grupo não lida atualmente com grupos de segurança ou grupos de distribuição.

>[AZURE.NOTE] Uma assinatura do Azure AD Premium é necessária para write-back do grupo.

## Write-back de usuário
> [AZURE.IMPORTANT] O recurso de visualização de write-back do usuário foi removido temporariamente na atualização de agosto de 2015 do Azure AD Connect. Se você tiver habilitado, você deve desabilitar esse recurso.

O write-back do usuário está na versão inicial de visualização. Ele só pode ser usado quando o Azure AD é a origem de todos os objetos de usuário e o Active Directory local está vazio antes de habilitar o recurso (implantação em campo verde).

> [AZURE.WARNING] Esse recurso só deve ser avaliado em um ambiente de teste e não deve ser usado em um diretório do Azure AD usado para uso em produção.

.

>[AZURE.NOTE] Uma assinatura do Azure AD Premium é necessária para write-back do usuário.

## Próximas etapas
Continuar a [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Saiba mais sobre a [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->