---

title: "Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory | Microsoft Docs"
description: "Mais cenários de licenciamento baseado em grupo do Azure Active Directory"
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: a7240c52e9351a60e3cf577d8112862c7dc8d913
ms.lasthandoff: 03/14/2017


---

# <a name="scenarios-limitations-and-known-issues-with-using-groups-to-manage-licensing-in-azure-active-directory"></a>Cenários, limitações e problemas conhecidos com o uso de grupos para gerenciar o licenciamento no Azure Active Directory

Use as informações e exemplos a seguir para obter uma compreensão mais avançada do licenciamento baseado em grupo do Azure AD (Azure Active Directory).

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Usar o licenciamento baseado em grupo com grupos dinâmicos

Você pode usar o licenciamento baseado em grupo com qualquer grupo de segurança, o que significa que ele pode ser combinado a grupos dinâmicos do Azure AD. Os grupos dinâmicos executam regras em relação a atributos de objeto de usuário para adicionar e remover usuários automaticamente dos grupos.

Por exemplo, você pode criar vários grupos dinâmicos, um para cada conjunto de produtos que você deseja atribuir aos usuários. Cada grupo contém uma regra que observa um atributo específico em um usuário, descrevendo qual conjunto de licenças ele deve receber. Você pode atribuir o atributo local e sincronizá-lo com o Azure AD ou pode gerenciar o atributo diretamente na nuvem.

Quando você especifica o atributo, o usuário é adicionado a um ou mais desses grupos dinâmicos de licenciamento. Licenças são atribuídas ao usuário logo depois disso. Quando o atributo é removido, o usuário deixa os grupos e as licenças são removidas.

### <a name="example"></a>Exemplo

Considere uma solução de gerenciamento de identidade local que decida quais usuários devem ter acesso a quais serviços Web da Microsoft. Usa **extensionAttribute1** para armazenar um valor de cadeia de caracteres que representa as licenças que o usuário deve ter. O Azure AD Connect é sincronizado com o Azure AD.

Para este exemplo, o objetivo é distribuir licenças Office 365 Enterprise E5 e Enterprise Mobility + Security para os usuários. No Azure AD, crie dois grupos dinâmicos, uma para cada produto. Isso ocorre porque alguns usuários talvez precisem de um dos produtos, mas não do outro. Especifique as configurações de licença e de regra de associação dinâmica em cada grupo. Veja como ficam:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: serviços de base

![Captura de tela de serviços de base do Office 365 Enterprise E5](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: usuários licenciados

![Captura de tela de usuários licenciados do Enterprise Mobility + Security](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

Para este exemplo, modifique um usuário e defina extensionAttribute1 com o valor de `EMS;E5_baseservices;`. Isso ocorre porque você deseja que esse usuário tenha ambas as licenças. Observe que você pode fazer essa modificação no local. Depois que a alteração é sincronizada com a nuvem, o usuário é adicionado automaticamente a ambos os grupos, e as licenças são atribuídas.

![Captura de tela mostrando como definir extensionAttribute1 do usuário](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modify-a-dynamic-group-membership-rule"></a>Modificar uma regra de associação de grupo dinâmica

Tenha cuidado ao modificar uma regra de associação de um grupo existente. Quando uma regra é alterada, todos os usuários são removidos do grupo. A regra é avaliada, e os usuários são adicionados ao grupo com base nas novas condições.

Se o grupo tiver licenças atribuídas, todos os usuários terão essas licenças removidas durante o processo. Novas licenças são aplicadas somente depois que a nova regra é avaliada e os usuários são adicionados novamente. Isso significa que os usuários podem enfrentar perda de serviço ou, em alguns casos, perda de dados.

É melhor não alterar a regra de associação em um grupo usado para atribuição de licença. Em vez disso, crie um novo grupo com a nova regra e especifique as mesmas configurações de licença do grupo original. Aguarde até que os membros tenham sido adicionados e as licenças tenham sido aplicadas a todos os usuários. Só então você pode excluir o grupo original. Essa abordagem garante uma transição segura e em estágios para a nova regra de associação sem qualquer perda de acesso ou dados para os usuários.


## <a name="multiple-groups-and-multiple-licenses"></a>Vários grupos e várias licenças

Um usuário pode ser membro de vários grupos com licenças. Estas são algumas coisas que você deve considerar:

- Várias licenças para o mesmo produto podem se sobrepor e resultam na aplicação de todos os serviços habilitados ao usuário. O exemplo a seguir mostra dois grupos de licenciamento: *serviços de base E3* contém os serviços de base para implantar em primeiro lugar, para todos os usuários. E *serviços estendidos E3* contém serviços adicionais (Sway e Planejador) para implantação apenas para alguns usuários. Observe que o Yammer permanece desabilitado para implantação futura. Neste exemplo, o usuário foi adicionado aos dois grupos:

  ![Captura de tela de serviços habilitados](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  Como resultado, o usuário tem sete dos 12 serviços no produto habilitados, enquanto usa apenas uma licença para esse produto.

- A seleção da licença *E3* mostra mais detalhes, incluindo informações sobre quais grupos causaram a habilitação de quais serviços para o usuário.

  ![Captura de tela de serviços habilitados por grupo](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>As licenças diretas coexistem com as licenças de grupo

Quando um usuário herda uma licença de um grupo, não é possível remover ou modificar essa atribuição de licença diretamente no objeto do usuário. Em vez disso, qualquer alteração deve ser feita no grupo e, em seguida, propagada pelo sistema a todos os usuários.

No entanto, é possível, atribuir a mesma licença do produto diretamente ao usuário, além da licença herdada. Isso pode ser usado, por exemplo, para habilitar serviços adicionais do produto apenas para um usuário, sem afetar outros usuários.

Licenças atribuídas diretamente podem ser removidas, mas isso não afetará a licença herdada. Por exemplo, considere o seguinte usuário, que herda de uma licença do Office 365 Enterprise E3 de um grupo.

1. Inicialmente, o usuário herda a licença somente do grupo *Serviços básicos E3*. Isso habilita os quatro planos de serviço, conforme mostrado na imagem a seguir.

  ![Captura de tela de serviços habilitados para grupo E3](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. Clicando em **Atribuir**, você pode atribuir uma licença E3 diretamente ao usuário. Nesse caso, você desabilitará todos os planos de serviço, exceto o Yammer Enterprise.

  ![Captura de tela de como atribuir uma licença diretamente a um usuário](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. Como resultado, o usuário ainda usa apenas uma licença do produto E3. Porém, a atribuição direta habilita o serviço do Yammer Enterprise somente para esse usuário. A imagem a seguir mostra quais serviços são habilitados pela associação de grupo em comparação com a atribuição direta.

  ![Captura de tela de atribuição herdada versus direta](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Quando você usa a atribuição direta, são permitidas as seguintes operações:

  a. O Yammer Enterprise pode ser desativado diretamente no objeto do usuário. Observe que a alternância **Ativar/Desativar** está habilitada para este serviço, em vez de outras alternâncias de serviço. Isso ocorre porque esse serviço é habilitado diretamente no usuário e, assim, pode ser modificado.

  b. Outros serviços também podem ser habilitados como parte da licença atribuída diretamente.

  c. O botão **Remover** pode ser usado para remover a licença direta do usuário. Veja que agora o usuário tem apenas a licença de grupo herdada, e somente os serviços originais permanecem habilitados.

    ![Captura de tela mostrando como remover a atribuição direta](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="usage-location"></a>Local de uso

Alguns serviços da Microsoft não estão disponíveis em todos os locais. Para que uma licença possa ser atribuída a um usuário, o administrador precisa especificar a propriedade **Local de uso** para o usuário. Isso pode ser feito no [portal do Azure](https://portal.azure.com), em **Usuário** &gt; **Perfil** &gt; **Configurações**.

Para a atribuição de licenças de grupo, qualquer usuário sem um local de uso especificado herdará o local do diretório. Se você tiver usuários em locais diferentes, certifique-se de refletir isso corretamente nos objetos do usuário antes de adicionar usuários a grupos de licenças.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Usar o PowerShell para ver quem tem licenças herdadas e diretas

Durante o período de visualização de uma versão do Azure AD, o PowerShell não pode ser usado para controlar totalmente atribuições de licenças de grupo. No entanto, ele pode ser usado para descobrir informações básicas sobre o estado do usuário e determinar se as licenças são herdadas de um grupo ou atribuídas diretamente. O exemplo de código a seguir mostra como um administrador pode produzir um relatório básico sobre atribuições de licença.

1. Execute o cmdlet `connect-msolservice` para autenticar e conectar-se ao locatário.

2. `Get-MsolAccountSku` pode ser usado para descobrir todas as licenças de produto provisionadas no locatário.

  ![Captura de tela do cmdlet Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Neste exemplo, você deseja saber quais usuários têm a licença do Enterprise Mobility + Security atribuída diretamente, de um grupo ou ambos. Você pode usar o script do PowerShell a seguir.
  
  ```
  \# Returns TRUE if the user has the license assigned directly
  function UserHasLicenseAssignedDirectly
  {
      Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
      foreach($license in $user.Licenses)
      {
          \# we look for the specific license SKU in all licenses assigned to the user
          if ($license.AccountSkuId -ieq $skuId)
          {
              \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              \# This could be a group object or a user object (contrary to what the name suggests)
              \# If the collection is empty, this means the license is assigned directly. This is the case for users who have never been licensed via groups in the past
              if ($license.GroupsAssigningLicense.Count -eq 0)
              {
                  return $true
              }
              \# If the collection contains the ID of the user object, this means the license is assigned directly
              \# Note: the license may also be assigned through one or more groups in addition to being assigned directly
              foreach ($assignmentSource in $license.GroupsAssigningLicense)
              {
                  if ($assignmentSource -ieq $user.ObjectId)
                  {
                      return $true
                  }
              }
              return $false
          }
      }
      return $false
  }
  \# Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
     foreach($license in $user.Licenses
     {
        \# we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
          \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
          \# This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
          {
                  \# If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  \# Note: the license may also be assigned directly in addition to being inherited
                  if ($assignmentSource -ine $user.ObjectId)

            {
                      return $true
            }
          }
              return $false
        }
      }
      return $false
  }
  ```

4. O restante do script obtém todos os usuários e executa essas funções em cada usuário. Em seguida, formata a saída em uma tabela.
    
  ```
  \# the license SKU we are interested in
  $skuId = "reseller-account:EMS"
  \# find all users that have the SKU license assigned
  Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
      ObjectId, `
      @{Name="SkuId";Expression={$skuId}}, `
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
  ```

5. A saída do script completo aparece da seguinte forma:

  ![Captura de tela da saída do script do PowerShell](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

Se você usa o licenciamento baseado em grupo, convém se familiarizar com a lista de limitações e problemas conhecidos a seguir.

- No momento, o licenciamento baseado em grupo não oferece suporte a "grupos aninhados" (grupos que contêm outros grupos). Se você aplicar uma licença a um grupo aninhado, somente os membros imediatos de primeiro nível do grupo terão as licenças aplicadas.

- Uma ou mais das licenças não puderam ser modificada porque são herdadas de uma associação de grupo. Para exibir ou modificar licenças com base em grupo, acesse o portal de administração do Azure.

- No momento, o [Portal de Administração do Office 365](https://portal.office.com ) não oferece suporte ao licenciamento baseado em grupo. Se um usuário herdar uma licença de um grupo, essa licença aparecerá no portal de administração do Office como uma licença de usuário comum. Se você tentar modificar essa licença (por exemplo, para desabilitar um serviço na licença) ou tentar removê-la, o portal retornará uma mensagem de erro. Licenças herdadas de grupos não podem ser modificadas diretamente em um usuário.

- Quando um usuário é removido de um grupo e perde a licença, os planos de serviço dessa licença (por exemplo, Exchange Online ou SharePoint Online) são definidos como um estado "suspenso". Os planos de serviço não são definidos com um estado final desabilitado. Isso é uma precaução para evitar a remoção acidental de dados do usuário se um administrador cometer um erro no gerenciamento de associação do grupo.

- Quando as licenças são atribuídas ou modificadas para um grupo muito grande (por exemplo, 100.000 usuários), isso pode afetar o desempenho. Especificamente, o volume de alterações geradas pela automação do Azure AD pode afetar negativamente o desempenho da sincronização de diretório entre o Azure AD e sistemas locais. Isso pode causar atrasos na sincronização de diretório em seu ambiente.

- A automação de gerenciamento de licença não reage automaticamente a todos os tipos de alteração no ambiente. Por exemplo, você pode ficar sem licenças, colocando alguns usuários em um estado de erro. Para liberar a contagem de estações disponíveis, você pode remover algumas licenças atribuídas diretamente de outros usuários. No entanto, o sistema não reage automaticamente a essa alteração e corrige os usuários nesse estado de erro.

  Como solução alternativa para esses tipos de limitações, acesse a folha **Grupo** no Azure AD e clique em **Reprocessar**. Isso processa todos os usuários nesse grupo e resolve os estados de erro, se possível.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre outros cenários de gerenciamento de licenças por meio de licenciamento baseado em grupo, confira:

* [O que é o licenciamento baseado em grupo no Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Atribuição de licenças a um grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)

