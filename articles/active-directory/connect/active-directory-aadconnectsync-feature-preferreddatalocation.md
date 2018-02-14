---
title: "Sincronização do Azure AD Connect: Configurar o local de dados preferencial para recursos de replicação geográfica no Office 365 | Microsoft Docs"
description: "Descreve como colocar os recursos de usuário do Office 365 perto do usuário com a sincronização do Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: billmath
ms.openlocfilehash: 021f009e66e57665a2252646b210f0e6dc55d33c
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-ad-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Sincronização do Azure AD Connect: Configura o local de dados preferencial para recursos do Office 365
O objetivo deste tópico é orientar você como configurar o PreferredDataLocation na sincronização do Azure AD. Quando um cliente usa recursos de replicação geográfica no Office 365, este atributo é usado para designar a localização geográfica de dados do Office 365 do usuário. Os termos **região** e **Área geográfica** são usados de maneira intercambiável.

> [!IMPORTANT]
> A replicação geográfica está em versão prévia no momento. Se você gostaria de participar do programa de versão prévia, entre em contato com o seu representante da Microsoft.
>
>

## <a name="enable-synchronization-of-preferreddatalocation"></a>Habilitar a sincronização de PreferredDataLocation
Por padrão, os recursos do Office 365 para seus usuários estão localizados na mesma área geográfica que o locatário do Azure AD. Por exemplo, se seu locatário estiver localizado na América do Norte, as caixas de correio de usuários do Exchange também estarão localizadas na América do Norte. Para uma organização multinacional isso pode não ser o ideal. Ao configurar o atributo preferredDataLocation, a área geográfica do usuário poderá ser definida.

Ao configurar esse atributo, você colocar os recursos do usuário do Office 365, como o OneDrive e a caixa de correio, na mesma área geográfica que o usuário e ainda ter um locatário para toda a organização.

> [!IMPORTANT]
> Para se qualificar para replicação geográfica, você deve ter pelo menos 5000 estações na sua assinatura do Office 365
>
>

Uma lista de todas as Áreas geográficas para o Office 365 pode ser encontrada em [onde os seus dados estão localizados](https://aka.ms/datamaps).

As áreas geográficas no Office 365, disponíveis para replicação Multigeográfica, são:

| Localização geográfica | Valor de preferredDataLocation |
| --- | --- |
| Pacífico Asiático | APC |
| Austrália | AUS |
| Canadá | CAN |
| União Europeia | EUR |
| Índia | IND |
| Japão | JPN |
| Coreia do Sul | KOR |
| Reino Unido | GBR |
| Estados Unidos | NAM |

* Se uma área geográfica não estiver listada nesta tabela, por exemplo, América do Sul, ela não poderá ser usada para replicação Multigeográfica.
* As áreas geográficas Índia e Coreia do Sul estão disponíveis somente para clientes com endereços de cobrança e licenças compradas nessas áreas.
* Nem todas as cargas de trabalho do Office 365 são compatíveis com o uso da configuração de área geográfica do usuário.

O Azure AD Connect dá suporte à sincronização do atributo **PreferredDataLocation** para objetos de **Usuário** nas versões 1.1.524.0 e posteriores. Mais especificamente, as seguintes alterações foram introduzidas:

* O esquema do tipo de objeto **Usuário** no Conector do Azure AD é estendido para incluir o atributo PreferredDataLocation, que é do tipo cadeia de caracteres de valor único.
* O esquema do tipo de objeto **Pessoa** no Metaverso é estendido para incluir o atributo PreferredDataLocation, que é do tipo cadeia de caracteres e é de valor único.

Por padrão, o atributo PreferredDataLocation não está habilitado para sincronização. Esse recurso destina-se a organizações maiores e nem todos podem se beneficiar dele. Você também deve identificar um atributo para manter a área geográfica do Office 365 para seus usuários, porque não há nenhum atributo PreferredDataLocation no Active Directory local. Isso vai ser diferente para cada organização.

> [!IMPORTANT]
> Atualmente, o Azure AD permite que o atributo PreferredDataLocation nos objetos de Usuário sincronizados e nos objetos de Usuário da nuvem sejam configurados diretamente usando o PowerShell do Azure AD. Depois de habilitar a sincronização do atributo PreferredDataLocation, você deve parar de usar o PowerShell do Azure AD para configurar o atributo em **objetos de Usuário sincronizados**, uma vez que o Azure AD Connect os substituirá com base em valores de atributo de origem no Active Directory local.

> [!IMPORTANT]
> Desde 1º de setembro de 2017 o Azure AD não permite mais que o atributo PreferredDataLocation, em **objetos Usuário sincronizados**, seja configurado diretamente usando o PowerShell do Azure AD. Para configurar o atributo PreferredLocation em objetos Usuário sincronizados, use o Azure AD Connect.

Antes de habilitar a sincronização do atributo PreferredDataLocation, você deve:

* Primeiro, decidir qual atributo do Active Directory local será usado como atributo de origem. Ele deve ser do tipo **cadeia de caracteres de valor único**. Nas etapas abaixo um dos extensionAttributes é usado.
* Se você já tiver configurado o atributo PreferredDataLocation anteriormente em objetos Usuário sincronizados existentes no Azure AD usando o PowerShell do Azure AD, faça **backport** dos valores de atributo para os objetos Usuário no Active Directory local.

    > [!IMPORTANT]
    > Caso você não faça o backport dos valores de atributo para os objetos Usuário correspondentes no Active Directory local, o Azure AD Connect removerá os valores de atributo existentes no Azure AD quando a sincronização para o atributo PreferredDataLocation for habilitada.

* É recomendável configurar o atributo de origem em pelo menos dois objetos de Usuário do AD locais agora, que poderão ser usados para verificação posterior.

As etapas para habilitar a sincronização do atributo PreferredDataLocation podem ser resumidas como:

1. Desabilitar o agendador de sincronização e verificar se não há nenhuma sincronização em andamento
2. Adicionar o atributo de origem para o esquema de conector do ADDS local
3. Adicionar o PreferredDataLocation ao esquema do conector do Azure AD
4. Criar uma regra de sincronização de entrada para fazer o valor de atributo fluir do Active Directory local
5. Criar uma regra de sincronização de saída para fazer o valor de atributo fluir para o Azure AD
6. Executar o ciclo de Sincronização Completa
7. Habilitar o agendador de sincronização
8. Verifique o resultado

> [!NOTE]
> O restante desta seção aborda essas etapas em detalhes. Elas são descritas no contexto de uma implantação do Azure AD com topologia de floresta única e sem regras de sincronização personalizadas. Se você tiver uma topologia de várias florestas, regras de sincronização personalizadas configuradas ou possui um servidor de teste, você precisa ajustar as etapas apropriadamente.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Etapa 1: desabilitar o agendador de sincronização e verificar se não há nenhuma sincronização em andamento
Garanta que não haja nenhuma sincronização enquanto você estiver atualizando as regras de sincronização para evitar a exportação de alterações indesejadas ao Azure AD. Para desabilitar o agendador de sincronização interno:

1. Inicie uma sessão do PowerShell no servidor do Azure AD Connect.
2. Desabilite a sincronização agendada executando o cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Inicie o **Synchronization Service Manager** acessando **INICIAR** > **Serviço de Sincronização**.
4. Acesse a guia **Operações** e confirme se não há nenhuma operação cujo status seja *em andamento*.

![Synchronization Service Manager – verificar se não há nenhuma operação em andamento](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema"></a>Etapa 2: Adicionar o atributo de origem para o esquema do conector do ADDS local
Nem todos os atributos do AD são importados para o Espaço do AD Connector local. Se você tiver optado por usar um atributo não sincronizado por padrão, você precisará importá-lo. Para adicionar o atributo de origem à lista de atributos importados:

1. Acesse a guia **Conectores** no Synchronization Service Manager.
2. Clique com o botão direito do mouse no **Conector do AD local** e selecione **Propriedades**.
3. Na caixa de diálogo pop-up, vá para a guia **Selecionar atributos**.
4. Verifique se o atributo de origem que você selecionou está marcado na lista de atributos. Se você não vir o atributo, clique na caixa de seleção "Mostrar tudo".
5. Clique em **OK** para salvar.

![Adicionar atributo de origem ao esquema do AD Connector local](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Etapa 3: Adicionar PreferredDataLocation ao esquema de conector do Azure AD
Por padrão, o atributo PreferredDataLocation não é importado no espaço conector do Azure AD. Para adicionar o atributo PreferredDataLocation à lista de atributos importados:

1. Acesse a guia **Conectores** no Synchronization Service Manager.
2. Clique com botão direito do mouse em **Conector do Azure AD** e selecione **Propriedades**.
3. Na caixa de diálogo pop-up, vá para a guia **Selecionar atributos**.
4. Selecione o atributo preferredDataLocation na lista de atributos.
5. Clique em **OK** para salvar.

![Adicionar atributo de origem ao esquema do Azure AD Connector](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Etapa 4: criar uma regra de sincronização de entrada para fazer o valor de atributo fluir do Active Directory local
A regra de sincronização de entrada permite que o valor do atributo flua do atributo de origem do Active Directory local para o Metaverso:

1. Inicie o **Editor de Regras de Sincronização** acessando **INICIAR** > **Editor de Regras de Sincronização**.
2. Defina o filtro de pesquisa **Direção** como **Entrada**.
3. Clique no botão **Adicionar nova regra** para criar uma nova regra de entrada.
4. Na guia **Descrição**, forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | --- | --- | --- |
    | NOME | *Fornecer um nome* | Por exemplo, *"Entrada do AD – PreferredDataLocation do usuário"* |
    | DESCRIÇÃO | *Forneça uma descrição personalizada* |  |
    | Sistema Conectado | *Selecionar o AD connector local* |  |
    | Tipo de Objeto do Sistema Conectado | **Usuário** |  |
    | Tipo de Objeto de Metaverso | **Pessoa** |  |
    | Tipo de link | **Join** |  |
    | Precedência | *Escolha um número entre 1 e 99* | 1 a 99 são reservados para regras de sincronização personalizadas. Não selecione um valor que seja usado por outra regra de sincronização. |

5. Mantenha o **Filtro de escopo** vazio para incluir todos os objetos. Talvez seja necessário ajustar o filtro de escopo de acordo com sua implantação do Azure AD Connect.
6. Acesse a **guia Transformação** e implemente a seguinte regra de transformação:

    | Tipo de Fluxo | Atributo de Destino | Fonte | Aplicar Uma Vez | Tipo de mesclagem |
    | --- | --- | --- | --- | --- |
    |Direta | PreferredDataLocation | Selecione o atributo de origem | Desmarcado | Atualizar |

7. Clique em **Adicionar** para criar a regra de entrada.

![Criar regra de sincronização de entrada](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Etapa 5: criar uma regra de sincronização de saída para fazer o valor de atributo fluir para o Azure AD
A regra de sincronização de saída permite que o valor do atributo flua do Metaverso para o atributo PreferredDataLocation no Azure AD:

1. Acesse o Editor de **Regras de Sincronização**.
2. Defina o filtro de pesquisa **Direção** como **Saída**.
3. Clique no botão **Adicionar nova regra**.
4. Na guia **Descrição**, forneça a seguinte configuração:

    | Atributo | Valor | Detalhes |
    | ----- | ------ | --- |
    | NOME | *Fornecer um nome* | Por exemplo, "Saída para AAD – PreferredDataLocation do usuário" |
    | DESCRIÇÃO | *Fornecer uma descrição* ||
    | Sistema Conectado | *Selecione o AAD connector* ||
    | Tipo de Objeto do Sistema Conectado | Usuário ||
    | Tipo de Objeto de Metaverso | **Pessoa** ||
    | Tipo de link | **Join** ||
    | Precedência | *Escolha um número entre 1 e 99* | 1 a 99 são reservados para regras de sincronização personalizadas. Não selecione um valor que seja usado por outra regra de sincronização. |

5. Acesse a guia **Filtro de escopo** e adicione um **único grupo de filtro de escopo com as duas cláusulas a seguir**:

    | Atributo | Operador | Valor |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Usuário |
    | cloudMastered | NOTEQUAL | True |

    Filtro de escopo determina a quais objetos do Azure AD essa regra de sincronização de saída é aplicada. Neste exemplo, usamos o mesmo filtro de escopo da regra de sincronização OOB de "Saída para AD – identidade do usuário". Impede que a regra de sincronização seja aplicada aos objetos de Usuário que não estão sincronizados do Active Directory local. Talvez seja necessário ajustar o filtro de escopo de acordo com sua implantação do Azure AD Connect.

6. Acesse a guia **Transformação** e implemente a seguinte regra de transformação:

    | Tipo de Fluxo | Atributo de Destino | Fonte | Aplicar Uma Vez | Tipo de mesclagem |
    | --- | --- | --- | --- | --- |
    | Direta | PreferredDataLocation | PreferredDataLocation | Desmarcado | Atualizar |

7. Feche **Adicionar** para criar a regra de saída.

![Criar regra de sincronização de saída](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>Etapa 6: executar o ciclo de Sincronização Completa
Em geral, um ciclo completo de sincronização completo é necessário, já que adicionamos novos atributos ao esquema do AD e do Azure AD Connector e introduzimos regras de sincronização personalizadas. É recomendável verificar as alterações antes de exportá-los para o Azure AD. Você pode usar as etapas a seguir para verificar as alterações enquanto executa manualmente as etapas que formam um ciclo completo de sincronização.

1. Execute a etapa **Importação completa** no **AD Connector local**:

   1. Acesse a guia **Operações** no Synchronization Service Manager.
   2. Clique com o botão direito do mouse no **AD Connector local** e selecione **Executar...**.
   3. Na caixa de diálogo pop-up, selecione **Importação Completa** e clique em **OK**.
   4. Aguarde a conclusão da operação.

    > [!NOTE]
    > Você poderá ignorar a Importação Completa no AD Connector local se o atributo de origem já estiver incluído na lista de atributos importados. Em outras palavras, você não precisou fazer nenhuma alteração durante [Etapa 2: adicionar os atributos de origem ao esquema do AD Connector local](#step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema).

2. Execute a etapa de **Importação completa** no **Azure AD Connector**:

   1. Clique com o botão direito do mouse em **Azure AD Connector** e selecione **Executar...**
   2. Na caixa de diálogo pop-up, selecione **Importação Completa** e clique em **OK**.
   3. Aguarde a conclusão da operação.

3. Verifique se a regra de sincronização muda em um objeto de Usuário existente:

O atributo de origem do Active Directory local e de PreferredDataLocation do Azure AD foi importado no respectivo espaço conector. Antes de continuar com a etapa de Sincronização Completa, é recomendável fazer uma **Visualização** em um objeto Usuário existente no espaço conector do AD local. O objeto escolhido deve ter o atributo de origem preenchido. Uma **Visualização** bem-sucedida com o PreferredDataLocation preenchido no Metaverso é um bom indicador que você configurou as regras de sincronização corretamente. Para obter informações sobre como fazer uma **Visualização**, consulte a seção [Verificar a alteração](active-directory-aadconnectsync-change-the-configuration.md#verify-the-change).

4. Execute a etapa **Sincronização completa** no **AD Connector local**:

   1. Clique com o botão direito do mouse no **AD Connector local** e selecione **Executar...**.
   2. Na caixa de diálogo pop-up, selecione **Sincronização Completa** e clique em **OK**.
   3. Aguarde a conclusão da operação.

5. Verifique **Exportações Pendentes** para o Azure AD:

   1. Clique com o botão direito do mouse no **Conector do Azure AD** e selecione **Pesquisar Espaço Conector**.
   2. Na caixa de diálogo pop-up Pesquisar Espaço Conector:

      1. Defina o **Escopo** como **Exportação Pendente**.
      2. Marque todas as três caixas de seleção, incluindo **Adicionar, Modificar e Excluir**.
      3. Clique no botão **Pesquisar** para obter a lista de objetos com alterações a serem exportados. Para examinar as alterações para um determinado objeto, clique duas vezes nele.
      4. Verifique se as alterações são esperadas.

6. Executar a etapa **Exportar** no **Azure AD Connector**

   1. Clique com o botão direito do mouse em **Conector do Azure AD** e selecione **Executar...**.
   2. Na caixa de diálogo pop-up Executar Conector, selecione a etapa **Exportar** e clique em **OK**.
   3. Aguarde a Exportação para o Azure AD ser concluída.

> [!NOTE]
> Você pode observar que as etapas não incluem a etapa de Sincronização Completa no conector do Azure AD e a Exportação no conector do Azure AD. As etapas não são obrigatórias, já que os valores de atributo estão fluindo do Active Directory local para o Azure AD apenas.

## <a name="step-7-re-enable-sync-scheduler"></a>Etapa 7: reabilitar o agendador de sincronização
Reabilite o agendador de sincronização interno:

1. Inicie uma sessão do PowerShell.
2. Habilite a sincronização agendada novamente executando o cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>Etapa 8: Verificar o resultado
Agora é hora de verificar a configuração e ativá-la para seus usuários.

1. Adicione a área geográfica ao atributo selecionado em um usuário. A lista de áreas geográficas disponíveis pode ser encontrada [nessa tabela](#enable-synchronization-of-preferreddatalocation).  
![Atributo AD adicionado a um usuário](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Aguarde até que o atributo seja sincronizado com o Azure AD.
3. Usando o PowerShell do Exchange Online, verifique se a região da caixa de correio foi definida corretamente.  
![Região de caixa de correio definida em um usuário no Exchange Online](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Supondo que o locatário foi marcado para ser capaz de usar esse recurso, a caixa de correio será movida para a área geográfica correta. Isso pode ser verificado ao examinar o nome do servidor em que a caixa de correio está localizada.
4. Para verificar se essa configuração foi eficaz em várias caixas de correio, use o script de [Galeria do Technet](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e). Esse script também tem uma lista de todos os prefixos dos servidores dos datacenters do Office 365 e em quais áreas geográficas eles estão localizados. Ele pode ser usado como uma referência na etapa anterior para verificar o local da caixa de correio.

## <a name="next-steps"></a>Próximas etapas

**Saiba mais sobre replicação geográfica no Office 365:**

* Sessões sobre replicação geográfica no Ignite: https://aka.ms/MultiGeoIgnite
* Replicação georáfica no OneDrive: https://aka.ms/OneDriveMultiGeo
* Replicação geográfica no SharePoint Online: https://aka.ms/SharePointMultiGeo

**Saiba mais sobre o modelo de configuração no mecanismo de sincronização:**

* Leia mais sobre o modelo de configuração em [Noções básicas do provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* Leia mais sobre a linguagem de expressão em [Noções básicas sobre expressões de provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Tópicos de visão geral**

* [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)
