---
title: "Azure AD Connect: alterar uma configuração na sincronização do Azure AD Connect | Microsoft Docs"
description: "Explica como fazer uma alteração da configuração na sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 7c237bfb42fdd2ffdfface1a12ab21c51d2504bb
ms.openlocfilehash: b327671b12bf6e2ce040ef6e6b0a58a0fead22b4


---
# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Sincronização do Azure AD Connect: como fazer uma alteração na configuração padrão
O objetivo deste tópico é explicar como fazer alterações na configuração padrão na sincronização do Azure AD Connect. Ele fornece etapas para alguns cenários comuns. Com esse conhecimento, você deve ser capaz de fazer algumas alterações simples em sua própria configuração com base em suas próprias regras de negócios.

## <a name="synchronization-rules-editor"></a>Editor de Regras de Sincronização
O Editor de Regras de Sincronização é usado para exibir e alterar a configuração padrão. Você o encontrará no Menu Iniciar no grupo **Azure Connect AD** .  
![Menu Iniciar com o Editor de Regras de Sincronização](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Ao abri-lo, você verá as regras padrão prontas para uso.

![Editor de Regras de Sincronização](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navegando no editor
Os menus suspensos na parte superior do editor permitem localizar rapidamente uma determinada regra. Por exemplo, se você quisesse ver as regras onde o atributo proxyAddresses está incluído, alteraria os menus suspensos para o seguinte:   
![Filtragem de SRE](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
para redefinir a filtragem e carregar uma nova configuração, pressione **F5** no teclado.

À direita superior, você tem um botão **Adicionar nova regra**. Este botão é usado para criar sua própria regra personalizada.

Na parte inferior, você tem botões para atuar em uma regra de sincronização selecionada. **Editar** e **Excluir** fazem o que você espera. **Exportar** gera um script do PowerShell para recriar a regra de sincronização. Esse procedimento permite mover uma regra de sincronização de um servidor para outro.

## <a name="create-your-first-custom-rule"></a>Criar sua primeira regra personalizada
A alteração mais comum é fazer mudanças nos fluxos do atributo. Os dados no diretório de origem podem não estar como no Azure AD. No exemplo nesta seção, você deseja verificar se o nome fornecido de um usuário está sempre com o **Formato apropriado**.

### <a name="disable-the-scheduler"></a>Desabilitar o agendador
O [Agendador](active-directory-aadconnectsync-feature-scheduler.md) é executado a cada 30 minutos por padrão. Você deseja verificar se ele não está iniciando enquanto está fazendo alterações e solucionando os problemas das novas regras. Para desabilitar temporariamente o agendador, inicie o PowerShell e execute `Set-ADSyncScheduler -SyncCycleEnabled $false`

![Desabilitar o agendador](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Criar a regra
1. Clique em **Adicionar nova regra**.
2. Na página **Descrição**, insira o seguinte:  
   ![Filtragem das regras de entrada](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * Nome: dê um nome descritivo à regra.
   * Descrição: algum esclarecimento para que outra pessoa possa entender para que serve a regra.
   * Sistema conectado: o sistema no qual o objeto pode ser encontrado. Neste caso, selecionamos o Active Directory Connector.
   * Tipo de Sistema Conectado/Objeto do Metaverso: selecione **Usuário** e **Pessoa**, respectivamente.
   * Tipo de Link: altere esse valor para **Ingressar**.
   * Precedência: forneça um valor que seja exclusivo no sistema. Um valor numérico mais baixo indica uma precedência mais alta.
   * Marcação: deixe em branco. As regras integradas da Microsoft devem ter essa caixa preenchida com um valor.
3. Na página **Filtro de escopo**, insira **givenName ISNOTNULL**.  
   ![Filtrar escopo das regras de entrada](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   Esta seção é usada para definir em quais objetos a regra deve ser aplicada. Se deixada em branco, a regra se aplicaria a todos os objetos de usuário. Mas isso incluiria as salas de conferência, contas de serviço e outros objetos de usuário que não são de pessoas.
4. Em **Regras de junção**, deixe a caixa vazia.
5. Na página **Transformações**, altere o FlowType para **Expression**. Selecione o Atributo de Destino **giveName** e, em Origem, digite `PCase([givenName])`.
   ![Transformações das regras de entrada](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   O mecanismo de sincronização diferencia as letras maiúsculas das minúsculas no nome da função e no nome do atributo. Se você digitar algo errado, verá um aviso quando adicionar a regra. O editor permite que você salve e continue, portanto, é necessário reabrir a regra e corrigi-la.
6. Clique em **Adicionar** para salvar a regra.

A nova regra personalizada deve ficar visível com as outras regras de sincronização no sistema.

### <a name="verify-the-change"></a>Verificar a alteração
Com essa nova alteração, você deseja verificar se ela está funcionando conforme o esperado e não está gerando erros. Dependendo do número de objetos que você tem, há duas maneiras diferentes de realizar esta etapa.

1. Executar uma sincronização completa em todos os objetos
2. Executar uma visualização e uma sincronização completa em um único objeto

Inicie o **Serviço de Sincronização** no menu Iniciar. As etapas nesta seção estão todas nessa ferramenta.

1. **Sincronização completa em todos os objetos**  
   Selecione **Conectores** na parte superior. Identifique o Conector no qual você fez uma alteração na seção anterior, neste caso, os Serviços de Domínio do Active Directory, e selecione-o. Selecione **Executar** em Ações e selecione **Sincronização Completa** e **OK**.
   ![Sincronização completa](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   Os objetos estão atualizados no metaverso. Agora você deseja ver o objeto no metaverso.
2. **Visualização e sincronização completa em um único objeto**  
   Selecione **Conectores** na parte superior. Identifique o Conector no qual você fez uma alteração na seção anterior, neste caso, os Serviços de Domínio do Active Directory, e selecione-o. Selecione **Pesquisar Espaço do Conector**. Use o escopo para localizar um objeto que você deseja usar para testar a alteração. Selecione o objeto e clique em **Visualizar**. Na nova tela, selecione **Confirmar Visualização**.  
   ![Commit preview](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   Agora, a alteração está confirmada no metaverso.

**Ver o objeto no metaverso**  
Agora, você deseja escolher alguns objetos de exemplo para verificar se o valor é o esperado e se a regra foi aplicada. Selecione **Pesquisar Metaverso** na parte superior. Adicione qualquer filtro necessário para localizar os objetos relevantes. No resultado da pesquisa, abra um objeto. Veja os valores do atributo e verifique também na coluna **Regras de Sincronização** se a regra aplicada está conforme o esperado.  
![Metaverse search](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Habilitar o agendador
Se tudo estiver como o esperado, você poderá habilitar o agendador novamente. No PowerShell, execute `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Outras alterações comuns no fluxo de atributos
A seção anterior descreveu como fazer alterações em um fluxo de atributos. Nesta seção, são fornecidos alguns exemplos adicionais. As etapas para criar a regra de sincronização estão abreviadas, mas você pode encontrar as etapas completas na seção anterior.

### <a name="use-another-attribute-than-the-default"></a>Usar outro atributo diferente do padrão
Na Fabrikam, há uma floresta na qual o alfabeto local é usado para o nome dado, sobrenome e nome de exibição. A representação de caracteres latinos desses atributos pode ser encontrada nos atributos de extensão. Ao compilar a lista de endereços global no AD do Azure e no Office 365, a organização deseja que esses atributos sejam usados.

Com uma configuração padrão, um objeto da floresta local tem esta aparência:   
![Fluxo de atributos 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Para criar uma regra com outros fluxos de atributos, faça o seguinte:

* Inicie o **Editor da Regra de Sincronização** no menu Iniciar.
* Com a **Entrada** ainda selecionada à esquerda, clique no botão **Adicionar nova regra**.
* Dê à regra um nome e uma descrição. Selecione o Active Directory local e os tipos de objetos relevantes. Em **Tipo de Link**, selecione **Junção**. Para a precedência, escolha um número que não seja usado por outra regra. As regras prontas para uso começam com 100, então, o valor 50 pode ser usado neste exemplo.
  ![Fluxo de atributos 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
* Deixe o escopo vazio (ou seja, deve ser aplicada a todos os objetos de usuário na floresta).
* Deixe as regras de junção vazias (ou seja, permita que a regra pronta para uso lide com quaisquer junções).
* Em Transformações, crie os seguintes fluxos:   
  ![Fluxo de atributos 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
* Clique em **Adicionar** para salvar a regra.
* Vá para o **Synchronization Service Manager**. Em **Conectores**, selecione o Conector ao qual adicionamos a regra. Selecione **Executar** e **Sincronização Completa**. Uma Sincronização Completa recalcula todos os objetos usando as regras atuais.

Este é o resultado para o mesmo objeto com essa regra personalizada:   
![Fluxo de atributos 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Comprimento de atributos
Atributos de cadeia de caracteres são definidos por padrão para serem indexáveis e seu comprimento máximo é 448 caracteres. Se você estiver trabalhando com atributos da cadeia de caracteres que podem conter mais, inclua o seguinte no fluxo de atributos:   
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>Alterando o userPrincipalSuffix
O atributo userPrincipalName no Active Directory não é sempre conhecido pelos usuários e pode não ser adequado como a ID de logon. O assistente de instalação da sincronização do Azure AD Connect permite escolher um atributo diferente, por exemplo, o email. Mas, em alguns casos, o atributo deve ser calculado. Por exemplo, a empresa Contoso tem dois diretórios do AD do Azure, um para a produção e outro para o teste. Eles querem que os usuários em seu locatário de teste usem outro sufixo na ID de logon.  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

Nesta expressão, pegamos tudo à esquerda do primeiro @-sign (Word) e concatenamos com uma cadeia de caracteres fixa.

### <a name="convert-a-multi-value-to-a-single-value"></a>Converter um número de vários valores em um único valor
Alguns atributos no Active Directory são compostos de vários valores no esquema, mesmo que pareçam de valor único nos Usuários e computadores do Active Directory. Um exemplo é o atributo de descrição.  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

Nesta expressão, caso o atributo tenha um valor, pegue o primeiro item (Item) no atributo, remova espaços à direita e à esquerda (Trim, cortar) e, em seguida, mantenha os primeiros 448 caracteres (Left, à esquerda) na cadeia de caracteres.

### <a name="do-not-flow-an-attribute"></a>Não faça um atributo fluir
Para obter informações sobre o cenário para esta seção, confira [Controlar o processo de fluxo de atributos](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Há duas maneiras de não fazer um atributo fluir. A primeira está disponível no assistente de instalação e permite que você [remova os atributos selecionados](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Essa opção funciona se você nunca tiver sincronizado o atributo antes. No entanto, se você começou a sincronizar esse atributo e removê-lo mais tarde com esse recurso, o mecanismo de sincronização deixará de gerenciar o atributo e os valores existentes serão deixados no AD do Azure.

Se você quiser remover o valor de um atributo e assegurar que ele não fluirá no futuro, será necessário criar uma regra personalizada.

Na Fabrikam, percebemos que alguns dos atributos que sincronizamos para a nuvem não deveriam estar lá. Queremos removê-los do AD do Azure.  
![Atributos de Extensão Ruins](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

* Crie uma nova Regra de Sincronização de entrada e preencha a descrição ![Descrições](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
* Crie fluxos de atributo do tipo **Expression** e com a fonte **AuthoritativeNull**. O literal **AuthoritativeNull** indica que o valor deve ser vazio na MV mesmo se uma regra de sincronização de precedência inferior tentar preencher o valor.
  ![Transformação dos Atributos de Extensão](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
* Salve a Regra de Sincronização. Inicie o **Serviço de Sincronização**, localize o Conector, selecione **Executar** e **Sincronização Completa**. Esta etapa recalculará todos os fluxos de atributo.
* Verifique se as alterações pretendidas estão prestes a ser exportadas pesquisando o espaço conector.
  ![Exclusão em etapas](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Criar regras com o PowerShell
Usando o editor de regra de sincronização funciona bem quando você tem apenas algumas alterações a serem feitas. Se você precisar fazer muitas alterações, o PowerShell pode ser uma opção melhor. Alguns recursos avançados só estão disponíveis com o PowerShell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Obter o script do PowerShell para uma regra pronta para uso
Para ver o PowerShell script que criou uma regra de caixa de saída, selecione a regra no editor de regras de sincronização e clique em **Exportar**. Esta ação lhe dá o script do PowerShell que criou a regra.

### <a name="advanced-precedence"></a>Precedência avançada
As regras de sincronização prontas para uso começam com um valor de precedência 100. Se você tiver várias florestas e precisar fazer muitas alterações personalizadas, então as 99 regras de sincronização poderão não ser suficientes.

Você pode instruir o Mecanismo de Sincronização que deseja regras adicionais inseridas antes das regras prontas para uso. Para obter esse comportamento, siga estas etapas:

1. Marque a primeira regra de sincronização pronta para uso (essa regra é a **In from AD-User Join**) no editor de regras de sincronização e selecione **Exportar**. Copie o valor do Identificador SR.  
![PowerShell antes da alteração](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. Crie nova regra de sincronização. Você pode usar o editor de regras de sincronização para criá-lo. Exporte a regra para um script do PowerShell.
3. Na propriedade **PrecedenceBefore**, insira o valor do identificador da regra pronta para uso. Definir o **precedência** para **0**. Verifique se o atributo do identificador é exclusivo e você não estiver reutilizando um GUID de outra regra. Verifique também se a propriedade **ImmutableTag** não foi definida; essa propriedade deve ser definida apenas para uma regra pronta para uso. Salve o script do PowerShell e executá-lo. O resultado é que a regra personalizada recebe o valor de precedência 100 e todas as outras regras prontas para uso são incrementadas.  
![PowerShell após a alteração](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

Você pode ter várias regras de sincronização personalizadas usando o mesmo **PrecedenceBefore** valor quando necessário.

## <a name="next-steps"></a>Próximas etapas
* Leia mais sobre o modelo de configuração em [Noções básicas do provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Leia mais sobre a linguagem de expressão em [Noções básicas sobre expressões de provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Tópicos de visão geral**

* [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)



<!--HONumber=Feb17_HO1-->


