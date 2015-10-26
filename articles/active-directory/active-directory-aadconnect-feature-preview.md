<properties
   pageTitle="Recursos do Azure Connect AD na visualização | Microsoft Azure"
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
   ms.date="10/13/2015"
   ms.author="andkjell;billmath"/>

# Mais detalhes sobre os recursos no modo de visualização
Este tópico descreve como usar recursos presentes atualmente na visualização.

## Write-back de usuário
> [AZURE.IMPORTANT]O recurso de visualização de write-back do usuário foi removido temporariamente na atualização de agosto do AAD Connect. Se você tiver habilitado, você deve desabilitar esse recurso.

O write-back do usuário está na versão inicial de visualização. Ele só pode ser usado quando o Azure AD é a origem de todos os objetos de usuário e o Active Directory local está vazio antes de habilitar o recurso.

> [AZURE.IMPORTANT]Esse recurso só deve ser testado em um ambiente de teste e não deve ser usado em um diretório do Azure AD usado para uso em produção.

## Write-back de grupo
A opção para write-back do grupo nos recursos opcionais permitirá que você faça write-back de "Grupos do Office 365" para uma floresta com o Exchange instalado. Esse é um novo tipo de grupo que é sempre controlado na nuvem. Você pode encontrar isso em outlook.office365.com ou em myapps.microsoft.com, conforme mostrado aqui:


![Filtragem de sincronização](./media/active-directory-aadconnect-feature-preview/office365.png)

![Filtragem de sincronização](./media/active-directory-aadconnect-feature-preview/myapps.png)

Esse grupo será representado como um grupo de distribuição no AD DS local. Seu servidor Exchange local deve estar no Exchange 2013 com atualização cumulativa 8 (lançado em março de 2015) para reconhecer esse novo tipo de grupo.

**Observação**

- Atualmente, o atributo de catálogo de endereços não é populado na visualização. A maneira mais fácil de fazer isso é usar o cmdlet update-recipient do PowerShell do Exchange.
- Somente as florestas com o esquema do Exchange são alvos válidos para grupos. Se nenhum Exchange for detectado, não será possível habilitar o write-back de grupo.
- O recurso de write-back de grupo não lida atualmente com grupos de segurança ou grupos de distribuição.

Mais informações sobre Grupos do Office 365 podem ser encontradas [aqui](http://aka.ms/O365g).

## Extensões de diretório
Extensões de diretório permite que você estenda o esquema no Azure AD com seus próprios atributos do Active Directory local.

Há suporte apenas para atributos de valor único e os valores nos atributos não podem ter mais de 250 caracteres. O metaverso e o esquema do AD do Azure serão estendidos com os atributos selecionados. No AD do Azure, é adicionado um novo aplicativo com os atributos.

![Filtragem de sincronização](./media/active-directory-aadconnect-feature-preview/extension3.png)

Estes atributos agora estarão disponíveis por meio do Gráfico:

![Filtragem de sincronização](./media/active-directory-aadconnect-feature-preview/extension4.png)

## Próximas etapas
Continuar a [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Saiba mais sobre a [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=Oct15_HO3-->