---
title: "OpenShift em tarefas de pós-implantação do Azure | Microsoft Docs"
description: Tarefas adicionais para depois que um cluster OpenShift for implantado.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 77c4719b5cee7f5736d73ee10cf6abf12229ea11
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2017
---
# <a name="post-deployment-tasks"></a>Tarefas de pós-implantação

Depois de implantar um cluster OpenShift, você pode configurar itens adicionais. Este artigo aborda as seguintes tarefas:

- Como configurar o logon único entre o Active Directory do Azure (Azure AD)
- Como configurar o Operations Management Suite para monitorar OpenShift
- Como configurar métricas e logs

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Configurar o Logon Único usando o Azure Active Directory

Para usar o Azure Active Directory para autenticação, primeiro você precisa criar um registro de aplicativo do Azure AD. Esse processo envolve duas etapas: o registro do aplicativo de criação e configuração de permissões.

### <a name="create-an-app-registration"></a>Criar o Registro do aplicativo

Essas etapas usam a CLI do Azure para criar o Registro do aplicativo e a GUI (Portal) para definir as permissões. Para criar o Registro do aplicativo, são necessárias cinco informações:

- Nome de exibição: nome de registro do aplicativo (ex: OCPAzureAD)
- Home Page: URL do console do OpenShift (ex: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- Identificador URI: URL do console do OpenShift (ex: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- URL de resposta: URL mestra pública e o nome de registro do aplicativo (ex: https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD)
- Senha: senha de segurança (use uma senha forte)

O exemplo a seguir criará um Registro de aplicativo usando as informações acima:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

Se o comando for bem-sucedido, você obterá uma saída JSON semelhante a esta:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD"
  ]
}
```

Anote a propriedade appId retornada do comando para uma etapa posterior.

No Portal do Azure:

1.  Selecione **Azure Active Directory** > **Registro de aplicativo**.
2.  Pesquise o Registro do seu aplicativo (ex: OCPAzureAD).
3.  Nos resultados, clique no Registro do aplicativo.
4.  Em **Configurações**, selecione **Permissões necessárias**.
5.  Em **Permissões necessárias**, selecione **Adicionar**.

  ![Registro do Aplicativo](media/openshift-post-deployment/app-registration.png)

6.  Clique em Etapa 1: selecionar a API e, em seguida, clique em **Microsoft Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Na parte inferior, selecione **Selecionar**.

  ![Registro de aplicativo Selecionar API](media/openshift-post-deployment/app-registration-select-api.png)

7.  Na Etapa 2: selecionar as permissões, selecione **Entrar e ler o perfil do usuário** em **Permissões delegadas** e clique em **Selecionar**.

  ![Registro de aplicativo Acesso](media/openshift-post-deployment/app-registration-access.png)

8.  Selecione **Concluído**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Configurar o OpenShift para autenticação do Azure AD

Para configurar o OpenShift para usar o Azure AD como um provedor de autenticação, o arquivo /etc/origin/master/master-config.yaml precisa ser editado em todos os nós mestres.

A Id do locatário pode ser encontrada usando o seguinte comando da CLI:

```azurecli
az account show
```

No arquivo yaml, localize as seguintes linhas:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Insira as seguintes linhas imediatamente após as linhas acima:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

A Id do locatário pode ser encontrada usando o seguinte comando da CLI: ```az account show```

Reinicie os serviços do OpenShift Master em todos os nós mestres:

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**OpenShift Container Platform com vários mestres**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**OpenShift Container Platform com um único mestre**

```bash
sudo systemctl restart atomic-openshift-master
```

No Console do OpenShift, agora você verá duas opções para autenticação – htpasswd_auth e [Registro de Aplicativo].

## <a name="monitor-openshift-with-operations-management-suite"></a>Monitorar OpenShift com o Operations Management Suite

Para monitorar o OpenShift com o OMS, você pode usar uma de duas opções: instalação do Agente do OMS no host da VM ou o OMS Container. Este artigo fornece instruções sobre como implantar o OMS Container.

## <a name="create-an-openshift-project-for-operations-management-suite-and-set-user-access"></a>Criar um projeto do OpenShift para o OMS e definir o acesso do usuário

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-a-daemon-set-yaml-file"></a>Criar um arquivo yaml do conjunto de daemon

Crie um arquivo chamado ocp-omsagent.yml:

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: oms
spec:
  selector:
    matchLabels:
      name: omsagent
  template:
    metadata:
      labels:
        name: omsagent
        agentVersion: 1.4.0-45
        dockerProviderVersion: 10.0.0-25
    spec:
      nodeSelector:
        zone: default
      serviceAccount: omsagent
      containers:
      - image: "microsoft/oms"
        imagePullPolicy: Always
        name: omsagent
        securityContext:
          privileged: true
        ports:
        - containerPort: 25225
          protocol: TCP
        - containerPort: 25224
          protocol: UDP
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        livenessProbe:
          exec:
            command:
              - /bin/bash
              - -c
              - ps -ef | grep omsagent | grep -v "grep"
          initialDelaySeconds: 60
          periodSeconds: 60
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
      - name: omsagent-secret
        secret:
         secretName: omsagent-secret
````

## <a name="create-a-secret-yaml-file"></a>Criar um arquivo yaml de segredo

Para criar o arquivo yaml de segredo, você precisará de duas informações: a ID do OMS Workspace e a chave compartilhada do OMS Workspace. 

Exemplo de arquivo ocp-secret.yml: 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

Substituir wsid_data com ID de espaço de trabalho do OMS de Base64 codificada. Substituirkey_data com ID de espaço de trabalho do OMS de Base64 codificada.

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>Criar o segredo e o conjunto de daemon

Implantar o arquivo de segredo:

```bash
oc create -f ocp-secret.yml
```

Implantar o conjunto de daemon de Agente do OMS:

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>Configurar métricas e logs

O modelo do Azure Resource Manager do OpenShift Container Platform fornece parâmetros de entrada para habilitar as métricas e logs. Já o OpenShift Container Platform Marketplace Offer e o modelo do Gerenciador de Recursos do OpenShift Origin não.

Se o modelo do Gerenciador de Recursos do OCP foi usado, mas as métricas e logs não foram habilitados no momento da instalação ou a oferta do Marketplace do OCP foi usada, eles poderão ser habilitados posteriormente. Se você usar o modelo do Gerenciador de Recursos do OpenShift Origin, é necessário realizar algumas tarefas previamente.

### <a name="openshift-origin-template-pre-work"></a>Tarefas prévias do modelo do OpenShift Origin

1. Execute o SSH para o primeiro nó mestre usando a porta 2200.

   Exemplo:

   ```bash
   ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
   ```

2. Editar o arquivo /etc/ansible/hosts e adicione as seguintes linhas depois da Seção de Provedor de Identidade (# Enable HTPasswdPasswordIdentityProvider):

   ```yaml
   # Setup metrics
   openshift_hosted_metrics_deploy=false
   openshift_metrics_cassandra_storage_type=dynamic
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"type":"infra"}
   openshift_metrics_cassandra_nodeselector={"type":"infra"}
   openshift_metrics_heapster_nodeselector={"type":"infra"}
   openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

   # Setup logging
   openshift_hosted_logging_deploy=false
   openshift_hosted_logging_storage_kind=dynamic
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"type":"infra"}
   openshift_logging_kibana_nodeselector={"type":"infra"}
   openshift_logging_curator_nodeselector={"type":"infra"}
   openshift_master_logging_public_url=https://kibana.$ROUTING
   ```

3. Substitua $ROUTING pela cadeia de caracteres usada na opção openshift_master_default_subdomain no mesmo arquivo /etc/ansible/hosts.

### <a name="azure-cloud-provider-in-use"></a>Azure Cloud Provider em uso

No primeiro nó mestre (Origin) ou nó de bastião (OCP), execute o SSH usando as credenciais fornecidas durante a implantação. Emita o seguinte comando:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure Cloud Provider não usado

No primeiro nó mestre (Origin) ou nó de bastião (OCP), execute o SSH usando as credenciais fornecidas durante a implantação. Emita o seguinte comando:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [Introdução ao OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html)
