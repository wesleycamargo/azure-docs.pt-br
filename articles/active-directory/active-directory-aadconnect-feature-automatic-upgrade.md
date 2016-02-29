<properties
   pageTitle="Azure AD Connect: atualização automática | Microsoft Azure"
   description="Este tópico descreve o recurso interno de atualização automática na sincronização do Azure AD Connect."
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
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Azure AD Connect: atualização automática
Esse recurso foi introduzido com a compilação 1.1.105.0 (lançada em fevereiro de 2016).

## Visão geral
Verificar se a instalação do Azure AD Connect está sempre atualizada nunca foi tão fácil após o lançamento do recurso de **atualização automática**. Esse recurso é habilitado por padrão para instalações expressas e garante que, quando uma nova versão for lançada, a instalação será atualizada automaticamente.

A atualização automática é habilitada por padrão para o seguinte:

- Instalação das configurações expressas
- Usar o SQL Express LocalDB, que é o que as configurações Expressas sempre usarão
- A conta do AD é a conta MSOL\_ padrão criada pelas configurações Expressas
- Tem menos de 100 mil objetos no metaverso

O estado atual da atualização automática pode ser exibido com o cmdlet `Get-ADSyncAutoUpgrade` do PowerShell. Ele contém os seguintes estados:

| Estado | Comentário |
| ---- | ---- |
| Habilitado | A atualização automática está habilitada. |
| Suspenso | Somente definido pelo sistema. O sistema não está mais qualificado para receber atualizações automáticas. |
| Desabilitado | A atualização automática está desabilitada. |

Você pode alterar entre **Habilitado** e **Desabilitado** com o `Set-ADSyncAutoUpgrade`. Somente o sistema deve definir o estado como **Suspenso**.

A atualização automática está usando o Azure AD Connect Health como a infraestrutura de atualização. Para que a atualização automática funcione corretamente, não deixe de abrir as URLs no proxy do **Azure AD Connect Health** conforme documentado em [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Se a interface do usuário **Gerenciador do Serviço de Sincronização** estiver em execução no servidor, a atualização será suspensa até que a interface do usuário seja fechada.

## Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->