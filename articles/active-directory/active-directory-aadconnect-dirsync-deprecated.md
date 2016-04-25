<properties
	pageTitle="Atualização do DirSync e do Azure AD Sync | Microsoft Azure"
	description="Descreve como atualizar do DirSync e do Azure AD Sync para o Azure AD Connect."
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
	ms.date="04/13/2016"
	ms.author="andkjell"/>


# Atualizar o Windows Azure Active Directory Sync ("DirSync") e o Azure Active Directory Sync ("Azure AD Sync")
O Azure AD Connect é a melhor maneira de conectar seu diretório local ao Azure AD e ao Office 365. Esse é um ótimo momento para atualizar para o Azure AD Connect do Windows Azure Active Directory Sync (DirSync) ou do Azure AD Sync, pois essas ferramentas foram preteridas e chegarão ao fim do suporte em 13 de abril de 2017.

As duas ferramentas de sincronização de identidade que foram preteridas eram oferecidas para clientes de floresta única (DirSync) e para clientes de várias florestas e outros clientes avançados (Azure AD Sync). Essas ferramentas mais antigas foram substituídas por uma solução única que está disponível para todos os cenários: o Azure AD Connect. Ele oferece nova funcionalidade, aprimoramentos de recursos e suporte a novos cenários. Para poder continuar a sincronizar os dados de identidade locais com o Azure AD e o Office 365, é altamente recomendável que você atualize para o Azure AD Connect.

A última versão do DirSync foi lançada em julho de 2014 e a última versão do Azure AD Sync foi lançada em maio de 2015.

## O que é o Azure AD Connect
O Azure AD Connect é o sucessor do DirSync e do Azure AD Sync. Ele combina todos os cenários aos quais essas duas ferramentas prestavam suporte. Você pode ler mais a respeito em [Como integrar suas identidades locais ao Azure Active Directory](active-directory-aadconnect.md).

## Agenda de preterição

|Data | Comentário |
| --- | --- |
| 13 de abril de 2016 | O Windows Azure Active Directory Sync ("DirSync") e o Microsoft Azure Active Directory Sync ("Azure AD Sync") são anunciados como preteridos. |
| 13 de abril de 2017 | O suporte termina. Os clientes não poderão mais abrir um caso de suporte sem atualizar primeiro para o Azure AD Connect. |

## Como fazer a transição para o Azure AD Connect
Se você está executando o DirSync, há duas maneiras de atualizar: atualização in-loco e implantação paralela. Uma atualização in-loco é recomendada para a maioria dos clientes e se você tem um sistema operacional recente e com menos de 50.000 objetos. Em outros casos, é recomendável fazer uma implantação paralela, em que a configuração do DirSync é movida para um novo servidor que executa o Azure AD Connect.

Se você usa o Azure AD Sync, recomenda-se uma atualização in-loco. Se desejar, você poderá instalar um novo servidor do Azure AD Connect em paralelo e fazer uma migração swing de seu servidor Azure AD Sync para o Azure AD Connect.

| Solução | Cenário |
| ----- | ----- |
| [Atualização do DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>Se você tiver um servidor DirSync existente já em execução.</li>
| [Atualização do Azure AD Sync](active-directory-aadconnect-upgrade-previous-version.md)| <li>Se você estiver migrando do Azure AD Sync.</li>

Para ver como fazer uma atualização in-loco do DirSync para o Azure AD Connect, confira este vídeo do Channel 9:

<!-- The next section is actually supposed to look like this, but video ids are not working at present.
> [AZURE.VIDEO azure-active-directory-connect-in-place-upgrade-from-legacy-tools]
-->

[Azure Active Directory Connect: atualização in-loco de ferramentas herdadas](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools) ![atualização in-loco](./media/active-directory-aadconnect-dirsync-deprecated/inplaceupgrade.png)

## Perguntas frequentes
**P: O DirSync/Azure AD Sync deixarão de funcionar em 13 de abril de 2017?** Não. A data em que eles não poderão mais se comunicar com o Azure AD será anunciada posteriormente. Você poderá encontrar informações sobre este tópico quando elas forem disponibilizadas.

**P: E o Azure AD Connector para FIM/MIM?** O Azure AD Connector para FIM/MIM **não** foi anunciado como preterido. Ele se encontra no estado de **congelamento de recursos**. Nenhuma funcionalidade nova é adicionada e ele não recebe correções de bugs. A Microsoft recomenda que os clientes que o utilizam planejem a migração para o Azure AD Connect. É altamente recomendável não iniciar novas implantações usando este recurso. Esse Conector será anunciado como preterido no futuro.

## Recursos adicionais

* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0413_2016-->