---
title: "Azure AD Connect: atualize o certificado SSL para um farm dos Serviços de Federação do Active Directory (AD FS) | Microsoft Docs"
description: Este documento detalha as etapas para atualizar o certificado SSL de um farm do AD FS usando o Azure AD Connect.
services: active-directory
keywords: "azure ad connect, atualizar ssl do adfs, atualização do certificado do adfs, alterar certificado do adfs, novo certificado do adfs, certificado do adfs, atualizar certificado ssl do adfs, atualizar adfs do certificado ssl, configurar certificado ssl do adfs, adfs, ssl, certificado, certificado de comunicação do serviço adfs, atualizar federação, configurar federação, aad connect"
authors: anandyadavmsft
manager: femila
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: d8f4181d7b51109cc5a2e205a40225a7763d0be4
ms.lasthandoff: 03/07/2017


---    

# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Atualizar o certificado SSL para um farm dos Serviços de Federação do Active Directory (AD FS)

## <a name="overview"></a>Visão geral
Este artigo descreve como o Azure AD Connect pode ser usado para atualizar o certificado SSL para o farm dos Suporte para os Serviços de Federação do Active Directory (AD FS).  Se o método de logon no Azure AD Connect é definido como o AD FS, a ferramenta Azure AD Connect pode ser usada para atualizar o certificado SSL para o farm do AD FS, entre todos os servidores WAP em 3 etapas simples e federação facilmente:

![Três etapas](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>Para saber mais sobre os certificados usados pelo AD FS, consulte o artigo [Noções básicas sobre os certificados usados pelo AD FS](https://technet.microsoft.com/library/cc730660.aspx)

##<a name="pre-requisites"></a>Pré-requisitos

* **Farm do AD FS**: o farm do AD FS deve ser 2012R2 ou superior
* **Azure AD Connect**: certifique-se de que a versão do Azure AD Connect é 1.1.443.0 ou mais recente. Você encontrará a tarefa "Atualizar certificado SSL do AD FS"

![Atualizar tarefa SSL](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

##<a name="step-1-provide-ad-fs-farm-information"></a>Etapa 1: fornecer informações do farm do AD FS

O AAD Connect tentará obter as informações sobre o farm do AD FS automaticamente por:
1. Consulta das informações do farm do AD FS (2016 ou superior)
2. Referência às informações de execuções anteriores (armazenadas localmente com o Azure AD Connect). 

A lista de servidores exibida pode ser modificada, adicionando ou removendo os servidores para refletir a configuração atual do farm do AD FS. Assim que as informações do servidor são fornecidas, o Azure AD Connect exibe a conectividade e o status atual do certificado SSL.

![Informações do servidor AD FS](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

Se a lista contém um servidor que não faz parte do farm do AD FS, clique em Remover para excluir o servidor da lista de servidores no farm do AD FS. 

![Servidor offline na lista](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)
 
>[!NOTE] 
> A remoção da lista de servidores do farm do AD FS no Azure AD Connect é uma operação local e atualiza as informações para o farm do AD FS que o Azure AD Connect mantém localmente. O Azure AD Connect não fará nenhuma modificação à configuração no AD FS para refletir a alteração.    

##<a name="step-2-provide-new-ssl-certificate"></a>Etapa 2: fornecer o novo certificado SSL

Depois que as informações sobre os servidores do farm do AD FS são confirmadas, o Azure AD Connect solicita o novo certificado SSL. Forneça um certificado PFX protegido por senha para continuar a instalação. 

![Certificado SSL](./media/active-directory-aadconnectfed-ssl-update/certificate.png)
 
Depois que o certificado é fornecido, o Azure AD Connect faz uma série de verificações de pré-requisitos do certificado para assegurar que o certificado é correto para o farm do AD FS:
1.    O SN (nome da entidade)/SAN (nome alternativo da entidade) para o certificado é o mesmo que o nome do serviço de federação ou é um certificado curinga.
2.    O certificado é válido por mais de 30 dias
3.    A cadeia confiável de certificado é válida 
4.    O certificado é protegido por senha

##<a name="step-3-select-servers-for-update"></a>Etapa 3: selecionar servidores para atualização

Na próxima etapa, selecione os servidores nos quais o certificado SSL precisa ser atualizado. Servidores offline não podem ser selecionados para a atualização. 

![Selecionar servidores para atualizar](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

Após a conclusão da configuração, o Azure AD Connect exibe a mensagem que indica o status da atualização e fornece uma opção para verificar o logon do AD FS.

![Configuração concluída](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

##<a name="faqs"></a>Perguntas frequentes

* **Qual deve ser o nome da entidade do certificado para o novo certificado SSL do AD FS?**

    O Azure AD Connect verificará se o nome da entidade do certificado/nome da entidade alternativo contém o nome do serviço de federação. Por exemplo, se o nome do seu serviço de Federação for fs.contoso.com, o nome da entidade alternativo/nome da entidade deverá ser fs.contoso.com.  Certificados curinga também são aceitos. 

* **Por que as credenciais estão sendo solicitadas novamente na página do servidor WAP?**

    Se as credenciais fornecidas para conexão com servidores do AD FS não tiverem o privilégio para gerenciar também os servidores WAP, o Azure AD Connect solicitará credenciais que tenham privilégio administrativo nos servidores WAP.

* **O servidor é mostrado como offline, o que devo fazer?**

    O Azure AD Connect não poderá executar nenhuma operação se o servidor estiver offline. Se o servidor é parte do farm do AD FS, verifique a conectividade ao servidor e depois de ter resolvido o problema selecione o ícone de atualização para atualizar o status no assistente. Se o servidor fazia parte do farm anteriormente mas agora não existe mais, clique em Remover para excluí-lo da lista de servidores que o Azure AD Connect mantém.  Remover o servidor da lista no Azure AD Connect não altera a configuração do AD FS em si e, se você estiver usando o AD FS 2016 ou superior, o servidor permanecerá nas definições de configuração e será mostrado novamente na próxima execução da tarefa.

* **Posso atualizar um subconjunto dos meus servidores do farm com o novo certificado SSL?**

    Sim. Você também pode executar a tarefa "Atualizar certificado SSL" novamente para atualizar os servidores restantes. Na página "Selecionar servidores para atualização de certificado SSL", você pode classificar a lista de servidores na "Data de expiração do SSL" para acessar facilmente os servidores que ainda não foram atualizados. 

* **Eu removi o servidor na execução anterior, mas ele ainda está sendo mostrado como offline e listado na página de servidores do AD FS. Por que o servidor offline ainda está lá mesmo após a remoção?**

    Remover o servidor da lista no Azure AD Connect não o remove na configuração do AD FS. O Azure AD Connect faz referência ao AD FS (2016 ou superior) também para quaisquer informações sobre o farm e se o servidor ainda estiver presente na configuração do AD FS, ele será colocado novamente na lista.  

## <a name="next-steps"></a>Próximas etapas

[Azure AD Connect e federação](active-directory-aadconnectfed-whatis.md)
[Gerenciamento e personalização dos Serviços de Federação do Active Directory com o Azure AD Connect](active-directory-aadconnect-federation-management.md)

