<properties
	pageTitle="Azure AD Connect: portas | Microsoft Azure"
	description="Essa página é uma página de referência técnica para as portas que precisam estar abertas para o Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/12/2016"
	ms.author="billmath"/>

# Portas e Protocolos Requeridos para Identidade Híbrida

O documento a seguir é uma referência técnica para fornecer informações sobre as portas e os protocolos que são necessários para implementar uma solução de identidade híbrida. Use a ilustração abaixo e consulte a tabela correspondente.

![O que é o Azure AD Connect](./media/active-directory-aadconnect-ports/required1.png)


## Tabela 1 - AD do Azure Connect e AD Local
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre o servidor do Azure AD Connect e AD local.

| Protocolo |Portas |Descrição
| --------- | --------- |--------- |
| DNS|53 (TCP/UDP)| Pesquisas de DNS na floresta de destino.
|Kerberos|88 (TCP/UDP)| Autenticação Kerberos na floresta do AD.
|MS-RPC |135 (TCP/UDP)| Usado durante a configuração inicial do assistente do Azure AD Connect quando ele se vincula à floresta do AD.
|LDAP|389 (TCP/UDP)|Usado para importar dados do AD. Dados são criptografados com Sinal e Selo do Kerberos.
|LDAP/SSL|636 (TCP/UDP)|Usado para importar dados do AD. A transferência de dados é assinada e criptografada. Usado somente se você estiver usando SSL.
|RPC |1024-65353 (Porta RPC alta aleatória)(TCP/UDP)|Usado durante a configuração inicial do Azure AD Connect quando ele se vincula às florestas do AD.

## Tabela 2 - AD do Azure Connect e Azure AD
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre o servidor do Azure AD Connect e Azure AD.

| Protocolo |Portas |Descrição
| --------- | --------- |--------- |
| HTTP|80 (TCP/UDP)|Usada para baixar as CRLs (Listas de Certificados Revogados) para verificar os certificados SSL.
|HTTPS|443(TCP/UDP)|Usado para sincronizar com o Azure AD.

Para obter uma lista de portas e endereços IP do Office 365, veja [intervalos de endereços IP e URLs do Office 365.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)

## Tabela 3 - Azure AD Connect e Servidores de Federação/WAP
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre o servidor do Azure AD Connect e servidores de Federação/WAP.

| Protocolo |Portas |Descrição
| --------- | --------- |--------- |
| HTTP|80 (TCP/UDP)|Usada para baixar as CRLs (Listas de Certificados Revogados) para verificar os certificados SSL.
|HTTPS|443(TCP/UDP)|Usado para sincronizar com o Azure AD.
|WinRM|5985| Ouvinte do WinRM

## Tabela 4 - Servidores de Federação e WAP
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre os servidores de Federação e servidores WAP.

| Protocolo |Portas |Descrição
| --------- | --------- |--------- |
|HTTPS|443(TCP/UDP)|Usado para autenticação.

## Tabela 5 - WAP e Usuários
Esta tabela descreve as portas e protocolos que são necessários para a comunicação entre os usuários e os servidores WAP.

| Protocolo |Portas |Descrição
| --------- | --------- |--------- |
|HTTPS|443(TCP/UDP)|Usado para autenticação de dispositivo.
|TCP|49443 (TCP)|Usado para autenticação de certificado.


## Tabela 6a e 6b - agente do Azure AD Connect Health para (AD FS/Sync) e Azure AD
As tabelas a seguir descrevem os pontos de extremidade, portas e protocolos que são necessários para a comunicação entre agentes do Azure AD Connect Health e Azure AD

### Tabela 6a - portas e protocolos para o agente do Azure AD Connect Health para o (AD FS/Sync) e Azure AD
Esta tabela descreve as portas e protocolos de saída a seguir que são necessários para a comunicação entre os agentes do Azure AD Connect Health e o Azure AD.

| Protocolo |Portas |Descrição
| --------- | --------- |--------- |
|HTTPS|443(TCP/UDP)| Saída
|Barramento de Serviço do Azure|5671 (TCP/UDP)| Saída

### 6b - pontos de extremidade de agente do Azure AD Connect Health para (AD FS/Sync) e Azure AD
Para obter uma lista de pontos de extremidade, veja [a seção Requisitos para o agente do Azure AD Connect Health](active-directory-aadconnect-health.md#requirements)

<!---HONumber=AcomDC_0518_2016-->