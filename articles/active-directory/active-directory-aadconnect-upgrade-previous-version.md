<properties
   pageTitle="Azure AD Connect: atualização de uma versão anterior | Microsoft Azure"
   description="Explica os diferentes métodos para atualizar para a versão mais recente do Azure Active Directory Connect, incluindo a atualização in-loco e a migração swing."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="06/27/2016"
   ms.author="andkjell"/>

# Azure AD Connect: atualização de uma versão anterior para a mais recente
Este tópico descreve os diferentes métodos que você pode usar para atualizar sua instalação do Azure AD Connect para a versão mais recente. Recomendamos que você se mantenha atualizado com as versões do Azure AD Connect.

Se você quiser atualizar a partir do DirSync, confira [Atualizar a partir da ferramenta de sincronização do Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

Há algumas estratégias diferentes para atualizar o Azure AD Connect.

Método | Descrição
--- | ---
[Atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) | Para clientes com uma instalação expressa, esse é o método mais simples.
[Atualização in-loco](#in-place-upgrade) | Se você tiver apenas um servidor, atualize a instalação in-loco no mesmo servidor.
[Migração swing](#swing-migration) | Com dois servidores, é possível preparar um dos servidores com a nova versão e alterar o servidor ativo quando você estiver pronto.

Para obter as permissões necessárias, confira [permissões necessárias para a atualização](active-directory-aadconnect-accounts-permissions.md#upgrade).

## Atualização in-loco
Uma atualização in-loco funcionará para mudar do Azure AD Sync ou do Azure AD Connect. Ela não funcionará para o DirSync ou para uma solução com o FIM + Azure AD Connector.

Esse será o método preferencial se você tiver um único servidor e menos de cerca de 100.000 objetos. Após a atualização, uma importação e uma sincronização completas ocorrerão se houver alterações nas regras de sincronização prontas para uso. Isso garantirá que a nova configuração seja aplicada a todos os objetos existentes no sistema. Isso pode levar algumas horas, dependendo do número de objetos no escopo do mecanismo de sincronização. A sincronização delta normal agendada (por padrão a cada 30 minutos) será suspensa mas a sincronização de senha continuará. Você pode considerar fazer a atualização in-loco durante um final de semana. Se não houver nenhuma alteração na configuração pronta para uso com a nova versão do Azure AD Connect, uma importação/sincronização delta normal será iniciada.

![Atualização in-loco](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Se você tiver feito alterações nas regras de sincronização prontas, elas serão definidas para a configuração padrão na atualização. Para certificar-se de que sua configuração seja mantida entre as atualizações, verifique se as alterações foram feitas como descrito em [Práticas recomendadas para alterar a configuração padrão](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## Migração swing
Se você tiver uma implantação complexa ou muitos objetos, talvez seja impossível fazer uma atualização in-loco do sistema dinâmico. Para alguns clientes, isso poderia levar vários dias e, durante esse tempo, nenhuma alteração delta seria processada.

Em vez do método recomendado, use uma migração swing. Para esse método, você precisará de (pelo menos) dois servidores, um ativo e um de preparo. O servidor ativo (linhas azuis sólidas na imagem abaixo) será responsável pela carga ativa. O servidor de preparo (linhas tracejadas roxas na imagem abaixo) será preparado com a nova versão e quando estiver totalmente pronto, ficará ativo. O servidor ativo anterior, agora com a versão antiga instalada, se tornará o servidor de preparo e será atualizado.

Os dois servidores podem usar versões diferentes. Por exemplo, o servidor ativo que você planeja desativar pode usar o Azure AD Sync e o novo servidor de preparo pode usar o Azure AD Connect.

![Servidor de preparo](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Observação: foi observado que alguns clientes preferem ter três ou quatro servidores para isso. Como o servidor de preparo está sendo atualizado, durante esse tempo você não terá um servidor de backup no caso de uma [recuperação de desastre](active-directory-aadconnectsync-operations.md#disaster-recovery). Com um máximo de quatro servidores, pode ser preparado um novo conjunto de servidores principais/em espera com a nova versão, garantindo que sempre haverá um servidor de teste pronto para assumir o controle.

Estas etapas também funcionarão para mudar do Azure AD Sync ou de uma solução com o FIM + Azure AD Connector. Estas etapas não funcionam para o DirSync, mas o mesmo método de migração swing (também chamado de implantação paralela) com as etapas para o DirSync pode ser encontrado em [Atualizando a sincronização do Azure Active Directory (DirSync) com o Azure AD Connect](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### Etapas da migração swing

1. Se você usar o Azure AD Connect nos dois servidores, certifique-se de que o servidor ativo e o servidor de preparo estejam usando a mesma versão antes de iniciar a atualização. Assim, será mais fácil comparar as diferenças mais tarde. Se você estiver atualizando do Azure AD Sync, esses servidores terão versões diferentes.
2. Se você tiver feito alguma configuração personalizada e o servidor de preparo não a tiver, siga as etapas em [Mover a configuração personalizada do servidor ativo para o de preparo](#move-custom-configuration-from-active-to-staging-server).
3. Se estiver atualizando de uma versão anterior do Azure AD Connect, atualize o servidor de preparo para a versão mais recente. Se estiver movendo do Azure AD Sync, instale o Azure AD Connect em seu servidor de preparo.
4. Permita que o mecanismo de sincronização execute a importação completa e a sincronização completa em seu servidor de preparo.
5. Verifique se a nova configuração não causou alterações inesperadas usando as etapas descritas em **Verificar** em [Verificar a configuração de um servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Se algo não estiver como esperado, corrija, execute a importação e a sincronização e verifique até os dados parecerem estar bons. Estas etapas podem ser encontradas no tópico vinculado.
6. Altere o servidor de preparo para que ele passe a ser o servidor ativo. Esta é a etapa final de **alterar o servidor ativo** em [Verificar a configuração de um servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Se estiver atualizando do Azure AD Connect, atualize o servidor que está no modo de preparo para a versão mais recente. Siga as mesmas etapas anteriores para atualizar os dados e a configuração. Se estiver movendo do Azure AD Sync, agora você poderá desativar e encerrar o servidor antigo.

### Mover configuração personalizada do servidor ativo para o servidor de preparo
Se você tiver feito alterações de configuração no servidor ativo, precisará garantir que as mesmas alterações sejam aplicadas ao servidor de preparo.

As regras de sincronização personalizadas criadas por você podem ser movidas com o PowerShell. Outras alterações devem ser aplicadas da mesma maneira em ambos os sistemas e não podem ser migradas.

O que você deve garantir que seja configurado da mesma maneira em ambos os servidores:

- A conexão às mesmas florestas.
- Qualquer filtragem de domínio OU de unidade organizacional
- Os mesmos recursos opcionais, como a sincronização de senha e o write-back de senha.

**Mover regras de sincronização** Para mover uma regra de sincronização personalizada, faça o seguinte:

1. Abra o **Editor de Regras de Sincronização** em seu servidor ativo.
2. Selecione a regra personalizada. Clique em **Exportar**. Isso abrirá uma janela do bloco de notas. Salve o arquivo temporário com uma extensão PS1. Isso o transformará em um script do PowerShell. Copie o arquivo ps1 para o servidor de preparo. ![Exportação da Regra de Sincronização](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. O GUID do Conector será diferente no servidor de preparo. Para obter o GUID, inicie o **Editor de Regras de Sincronização**, escolha uma das regras prontas que representam o mesmo sistema conectado e clique em **Exportar**. Substitua o GUID em seu arquivo PS1 com o GUID do servidor de preparo.
4. Em um prompt do PowerShell, execute o arquivo PS1. Isso criará a regra de sincronização personalizada no servidor de preparo.
5. Se você tiver várias regras personalizadas, repita para todas as regras personalizadas.

## Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0629_2016-->