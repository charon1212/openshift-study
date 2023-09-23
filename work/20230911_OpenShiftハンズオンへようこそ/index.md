# OpenShiftハンズオンへようこそ

ハンズオンをやってみた感想や内容など

リンク: [OpenShiftハンズオンへようこそ](https://github.com/mayumi00/OpenShiftLearning/blob/main/README.md)

## Container編

### [container101-jp](https://play.instruqt.com/embed/openshift/tracks/container101-jp?token=em_cwrtT0g1S8AGiCbI&show_challenges=true)

(未実施)

### [container102-jp](https://play.instruqt.com/embed/openshift/tracks/container102-jp?token=em_YTfHS1Bmrj3dKD5R&show_challenges=true)

(未実施)

## OpenShift基礎編

### [Getting Started with OpenShift for Developers (Japanese)](https://play.instruqt.com/embed/openshift/tracks/developing-on-openshift-getting-started-jp?token=em_ejUY5shIu9GHyZJD&show_challenges=true)（はじめてみようOpenShift）（所要時間目安：15分〜30分）

- CUI:

```shell
# ログイン
$ oc login -u admin -p admin https://api.crc.testing:6443 --insecure-skip-tls-verify=true
# ユーザー確認
$ oc whoami
```

- GUI:
  - 右上のユーザー設定から言語を変更
  - Addメニューからプロジェクトの作成
  - Addメニューからイメージのビルドを実施してアプリケーションPodの作成
  - TopologyメニューからPodを選択し、Podのスケーリングを調整
  - TopologyメニューからRouteを確認し、アプリケーションにInternetアクセス

### [Login to an OpenShift cluster (Japanese)](https://play.instruqt.com/embed/openshift/tracks/logging-into-an-openshift-cluster-jp?token=em_5J6Y6rWmtHqwXuA9&show_challenges=true)（OpenShiftクラスター上でのユーザー切り替え）（所要時間目安：10分〜15分）

- CUI:

```shell
# WebコンソールのPodが使用可能であること確認
oc get pods -n openshift-console | grep console
# ログイン
oc login -u developer -p developer
# プロジェクト変更
oc project myproject
# アクセスできるプロジェクト一覧
oc get projects
# 現在のユーザ
oc whoami
# 現在のサーバー
oc whoami --show-server
# 現在のコンテキスト
oc whoami --show-context
# ユーザー作成(？)
oc login --username user1 --password user1
# 新規プロジェクト作成
oc new-project yourproject
# 閲覧権限の割り当て（？）
oc adm policy add-role-to-user view developer -n yourproject
# OpenShiftクラスタの一覧
oc config get-clusters
# 全てのコンテキストの一覧
oc config get-contexts
```

- GUI:
  - 「Project > "選択するプロジェクト" > Project access」から"Role Bindings"を選択し、ロールを付与

- memo:

```shell
[root@crc-lgph7-master-0 /]# oc get pods -n openshift-console
NAME                         READY   STATUS    RESTARTS   AGE
console-6cc669c9d4-vn58x     1/1     Running   0          9m28s
downloads-555c6b94f6-shp8k   1/1     Running   2          349d
[root@crc-lgph7-master-0 /]# oc get pods -n openshift-console | grep console
console-6cc669c9d4-vn58x     1/1     Running   0          5m17s

[root@crc-lgph7-master-0 /]# oc get routes console -n openshift-console
NAME      HOST/PORT                                                                   PATH   SERVICES   PORT    TERMINATION          WILDCARD
console   console-openshift-console.crc-lgph7-master-0.crc.aoaxidufsvog.instruqt.io          console    https   reencrypt/Redirect   None
[root@crc-lgph7-master-0 /]# oc get routes console -n openshift-console -o jsonpath='{"https://"}{.spec.host}{"\n"}'
https://console-openshift-console.crc-lgph7-master-0.crc.aoaxidufsvog.instruqt.io

[root@crc-lgph7-master-0 /]# oc get projects
NAME        DISPLAY NAME   STATUS
myproject   myproject      Active
```

### [Deploying Applications From Images (Japanese)](https://play.instruqt.com/embed/openshift/tracks/deploying-applications-from-images-jp?token=em_4er-ge2Y68HjE2Oc&show_challenges=true)（イメージを利用したアプリのデプロイ）（所要時間目安：10分〜15分）

(未実施)

### [Deploying Applications From Source (Japanese)](https://play.instruqt.com/embed/openshift/tracks/deploying-applications-from-source-jp?token=em_vPba4iC-zQwOtP7S&show_challenges=true)（ソースからのアプリのデプロイ）（所要時間目安：15分〜30分）

- CUI:

```shell
# 全kubernetesリソースのリソース名
oc get all -o name
# リソースの詳細確認
oc describe route/blog-django-py
# リソース一覧の取得（selectorでlabelsの絞り込み）
oc get all --selector app=blog-django-py -o name
# リソース削除
oc delete all --selector app=blog-django-py
# CUIからアプリケーションの立ち上げ
oc new-app python:latest~https://github.com/openshift-instruqt/blog-django-py
# 上記のビルドログを取得
oc logs bc/blog-django-py --follow
# プロジェクト内のアプリのステータス確認
oc status
# アプリケーションの公開（Route作成）
oc expose service/blog-django-py
# ビルドの進行状況をモニタリング（ctrl+Cで中断）
oc get builds --watch
# 手動でgitコードを引っ張ってきて、ビルドする
git clone https://github.com/openshift-instruqt/blog-django-py
cd blog-django-py
echo 'BLOG_BANNER_COLOR=blue' >> .s2i/environment # Podの環境変数を指定し、application内での表示色を赤から青に変更（違い確認のため）
oc start-build blog-django-py --from-dir=. --wait
# ビルドのキャンセル
oc cancel-build blog-django-py-5
```

- GUI:
  - Addメニューからプロジェクトを選択し、「Developer Catalogs > All Services」からpythonの"BuilderImage"を選択し、gitのpythonコードをデプロイ。
  - Topologyの詳細画面からBuilds欄の該当ビルドの"View logs"を押下し、ビルドログを参照する。
  - Buildsメニューからビルドしたいものを探し、その行の右側3点メニューから"Start build"を選択してビルドを実行。

- memo:

```shell
```

### [OpenShift Persistent Volume (Japanese)](https://play.instruqt.com/embed/openshift/tracks/openshift-persistent-volume-jp?token=em_ZLVCcR0uwZ5MRuvt_challenges=true)（OpenShiftの永続ストレージ）（所要時間目安：15分〜30分）

(未実施)

### [Manage Resource Objects (Japanese)](https://play.instruqt.com/embed/openshift/tracks/openshift-manage-resource-objects-jp?token=em_2P_-985m1wllVPOZ&show_challenges=true)（OpenShiftのリソースオブジェクト）（所要時間目安：15分〜30分）

- CUI:

```shell
# ログイン
oc login -u developer -p developer https://api.crc.testing:6443 --insecure-skip-tls-verify=true
# プロジェクト作成
oc new-project myproject
# アプリケーション作成
oc new-app quay.io/openshiftroadshow/parksmap:1.3.0 --name parksmap
# アプリケーションの公開
oc expose svc/parksmap
# 全件取得
oc get all
oc get all -o name
# OpenShiftのリソースオブジェクトの種類の一覧を取得する
oc api-resources
# リソースの詳細を取得する(下の二つはどちらも同じ意味)
oc describe route/parksmap
oc describe route parksmap
# 出力をJSONにする
oc get route/parksmap -o json
# 各リソースのフィールドの説明を見る
oc explain route.spec.host
# リソースを修正する(viエディタが起動)
oc edit route/parksmap
oc edit route/parksmap -o json # デフォルトはyaml。これでいくとjsonで編集できる。
# JSONからリソースを作成
oc create -f parksmap-fqdn.json
# コマンドで別Routeを作成
oc create route edge parksmap-fqdn2 --service parksmap --insecure-policy Allow --hostname www.otherexample.com
# リソースを置き換える
oc replace -f parksmap-fqdn.json
# リソースを置き換える その2
oc patch route/parksmap-fqdn --patch '{"spec":{"tls": {"insecureEdgeTerminationPolicy": "Allow"}}}'
# oc replaceの、updateinsert版。oc replaceは置き換え元がないとエラーになるが、applyは有ったら置き換えてなければ新規作成する。
oc apply -f "<JSON_OR_YAML_FILE>"
# ラベル検索する（selectorオプション）
oc get all -o name --selector app=parksmap
# ラベルを設定する
oc label service/parksmap web=true
# ラベルを削除する
oc label service/parksmap web-
# リソース削除
oc delete route/parksmap-fqdn
oc delete route --selector app=parksmap # ラベル指定
oc delete svc,route --selector app=parksmap # ,区切りで複数リソースを指定可能
oc delete all --selector app=parksmap # delete allで全てのリソースを削除。
oc delete all --all # 完全にすべてのリソースを削除する場合、--allオプションを指定。
```

- GUI:

- memo:

```shell
# 全件取得結果
root@container:~# oc get all
NAME                            READY   STATUS    RESTARTS   AGE
pod/parksmap-54476698b6-mwkhl   1/1     Running   0          2m56s

NAME               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
service/parksmap   ClusterIP   10.217.4.111   <none>        8080/TCP   2m57s

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/parksmap   1/1     1            1           2m57s

NAME                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/parksmap-54476698b6   1         1         1       2m56s
replicaset.apps/parksmap-7b46cfdfb7   0         0         0       2m57s

NAME                                      IMAGE REPOSITORY                                                                                            TAGS    UPDATED
imagestream.image.openshift.io/parksmap   default-route-openshift-image-registry.crc-lgph7-master-0.crc.nup0wizgehcm.instruqt.io/myproject/parksmap   1.3.0   2 minutes ago

NAME                                HOST/PORT                                                            PATH   SERVICES   PORT       TERMINATION   WILDCARD
route.route.openshift.io/parksmap   parksmap-myproject.crc-lgph7-master-0.crc.nup0wizgehcm.instruqt.io          parksmap   8080-tcp                 None
# リソースオブジェクトの種類一覧
root@container:~# oc api-resources
NAME                                  SHORTNAMES       APIVERSION                                    NAMESPACED   KIND
bindings                                               v1                                            true         Binding
componentstatuses                     cs               v1                                            false        ComponentStatus
configmaps                            cm               v1                                            true         ConfigMap
endpoints                             ep               v1                                            true         Endpoints
events                                ev               v1                                            true         Event
limitranges                           limits           v1                                            true         LimitRange
namespaces                            ns               v1                                            false        Namespace
nodes                                 no               v1                                            false        Node
persistentvolumeclaims                pvc              v1                                            true         PersistentVolumeClaim
persistentvolumes                     pv               v1                                            false        PersistentVolume
pods                                  po               v1                                            true         Pod
podtemplates                                           v1                                            true         PodTemplate
replicationcontrollers                rc               v1                                            true         ReplicationController
resourcequotas                        quota            v1                                            true         ResourceQuota
secrets                                                v1                                            true         Secret
serviceaccounts                       sa               v1                                            true         ServiceAccount
services                              svc              v1                                            true         Service
mutatingwebhookconfigurations                          admissionregistration.k8s.io/v1               false        MutatingWebhookConfiguration
validatingwebhookconfigurations                        admissionregistration.k8s.io/v1               false        ValidatingWebhookConfiguration
customresourcedefinitions             crd,crds         apiextensions.k8s.io/v1                       false        CustomResourceDefinition
apiservices                                            apiregistration.k8s.io/v1                     false        APIService
apirequestcounts                                       apiserver.openshift.io/v1                     false        APIRequestCount
controllerrevisions                                    apps/v1                                       true         ControllerRevision
daemonsets                            ds               apps/v1                                       true         DaemonSet
deployments                           deploy           apps/v1                                       true         Deployment
replicasets                           rs               apps/v1                                       true         ReplicaSet
statefulsets                          sts              apps/v1                                       true         StatefulSet
deploymentconfigs                     dc               apps.openshift.io/v1                          true         DeploymentConfig
tokenreviews                                           authentication.k8s.io/v1                      false        TokenReview
localsubjectaccessreviews                              authorization.k8s.io/v1                       true         LocalSubjectAccessReview
selfsubjectaccessreviews                               authorization.k8s.io/v1                       false        SelfSubjectAccessReview
selfsubjectrulesreviews                                authorization.k8s.io/v1                       false        SelfSubjectRulesReview
subjectaccessreviews                                   authorization.k8s.io/v1                       false        SubjectAccessReview
clusterrolebindings                                    authorization.openshift.io/v1                 false        ClusterRoleBinding
clusterroles                                           authorization.openshift.io/v1                 false        ClusterRole
localresourceaccessreviews                             authorization.openshift.io/v1                 true         LocalResourceAccessReview
localsubjectaccessreviews                              authorization.openshift.io/v1                 true         LocalSubjectAccessReview
resourceaccessreviews                                  authorization.openshift.io/v1                 false        ResourceAccessReview
rolebindingrestrictions                                authorization.openshift.io/v1                 true         RoleBindingRestriction
rolebindings                                           authorization.openshift.io/v1                 true         RoleBinding
roles                                                  authorization.openshift.io/v1                 true         Role
selfsubjectrulesreviews                                authorization.openshift.io/v1                 true         SelfSubjectRulesReview
subjectaccessreviews                                   authorization.openshift.io/v1                 false        SubjectAccessReview
subjectrulesreviews                                    authorization.openshift.io/v1                 true         SubjectRulesReview
horizontalpodautoscalers              hpa              autoscaling/v2                                true         HorizontalPodAutoscaler
clusterautoscalers                    ca               autoscaling.openshift.io/v1                   false        ClusterAutoscaler
machineautoscalers                    ma               autoscaling.openshift.io/v1beta1              true         MachineAutoscaler
cronjobs                              cj               batch/v1                                      true         CronJob
jobs                                                   batch/v1                                      true         Job
buildconfigs                          bc               build.openshift.io/v1                         true         BuildConfig
builds                                                 build.openshift.io/v1                         true         Build
certificatesigningrequests            csr              certificates.k8s.io/v1                        false        CertificateSigningRequest
credentialsrequests                                    cloudcredential.openshift.io/v1               true         CredentialsRequest
apiservers                                             config.openshift.io/v1                        false        APIServer
authentications                                        config.openshift.io/v1                        false        Authentication
builds                                                 config.openshift.io/v1                        false        Build
clusteroperators                      co               config.openshift.io/v1                        false        ClusterOperator
clusterversions                                        config.openshift.io/v1                        false        ClusterVersion
consoles                                               config.openshift.io/v1                        false        Console
dnses                                                  config.openshift.io/v1                        false        DNS
featuregates                                           config.openshift.io/v1                        false        FeatureGate
imagecontentpolicies                                   config.openshift.io/v1                        false        ImageContentPolicy
images                                                 config.openshift.io/v1                        false        Image
infrastructures                                        config.openshift.io/v1                        false        Infrastructure
ingresses                                              config.openshift.io/v1                        false        Ingress
networks                                               config.openshift.io/v1                        false        Network
nodes                                                  config.openshift.io/v1                        false        Node
oauths                                                 config.openshift.io/v1                        false        OAuth
operatorhubs                                           config.openshift.io/v1                        false        OperatorHub
projects                                               config.openshift.io/v1                        false        Project
proxies                                                config.openshift.io/v1                        false        Proxy
schedulers                                             config.openshift.io/v1                        false        Scheduler
consoleclidownloads                                    console.openshift.io/v1                       false        ConsoleCLIDownload
consoleexternalloglinks                                console.openshift.io/v1                       false        ConsoleExternalLogLink
consolelinks                                           console.openshift.io/v1                       false        ConsoleLink
consolenotifications                                   console.openshift.io/v1                       false        ConsoleNotification
consoleplugins                                         console.openshift.io/v1alpha1                 false        ConsolePlugin
consolequickstarts                                     console.openshift.io/v1                       false        ConsoleQuickStart
consoleyamlsamples                                     console.openshift.io/v1                       false        ConsoleYAMLSample
podnetworkconnectivitychecks                           controlplane.operator.openshift.io/v1alpha1   true         PodNetworkConnectivityCheck
leases                                                 coordination.k8s.io/v1                        true         Lease
endpointslices                                         discovery.k8s.io/v1                           true         EndpointSlice
events                                ev               events.k8s.io/v1                              true         Event
flowschemas                                            flowcontrol.apiserver.k8s.io/v1beta2          false        FlowSchema
prioritylevelconfigurations                            flowcontrol.apiserver.k8s.io/v1beta2          false        PriorityLevelConfiguration
helmchartrepositories                                  helm.openshift.io/v1beta1                     false        HelmChartRepository
projecthelmchartrepositories                           helm.openshift.io/v1beta1                     true         ProjectHelmChartRepository
images                                                 image.openshift.io/v1                         false        Image
imagesignatures                                        image.openshift.io/v1                         false        ImageSignature
imagestreamimages                     isimage          image.openshift.io/v1                         true         ImageStreamImage
imagestreamimports                                     image.openshift.io/v1                         true         ImageStreamImport
imagestreammappings                                    image.openshift.io/v1                         true         ImageStreamMapping
imagestreams                          is               image.openshift.io/v1                         true         ImageStream
imagestreamtags                       istag            image.openshift.io/v1                         true         ImageStreamTag
imagetags                             itag             image.openshift.io/v1                         true         ImageTag
configs                                                imageregistry.operator.openshift.io/v1        false        Config
imagepruners                                           imageregistry.operator.openshift.io/v1        false        ImagePruner
dnsrecords                                             ingress.operator.openshift.io/v1              true         DNSRecord
network-attachment-definitions        net-attach-def   k8s.cni.cncf.io/v1                            true         NetworkAttachmentDefinition
machinehealthchecks                   mhc,mhcs         machine.openshift.io/v1beta1                  true         MachineHealthCheck
machines                                               machine.openshift.io/v1beta1                  true         Machine
machinesets                                            machine.openshift.io/v1beta1                  true         MachineSet
containerruntimeconfigs               ctrcfg           machineconfiguration.openshift.io/v1          false        ContainerRuntimeConfig
controllerconfigs                                      machineconfiguration.openshift.io/v1          false        ControllerConfig
kubeletconfigs                                         machineconfiguration.openshift.io/v1          false        KubeletConfig
machineconfigpools                    mcp              machineconfiguration.openshift.io/v1          false        MachineConfigPool
machineconfigs                        mc               machineconfiguration.openshift.io/v1          false        MachineConfig
baremetalhosts                        bmh,bmhost       metal3.io/v1alpha1                            true         BareMetalHost
bmceventsubscriptions                 bes,bmcevent     metal3.io/v1alpha1                            true         BMCEventSubscription
firmwareschemas                                        metal3.io/v1alpha1                            true         FirmwareSchema
hostfirmwaresettings                  hfs              metal3.io/v1alpha1                            true         HostFirmwareSettings
preprovisioningimages                 ppimg            metal3.io/v1alpha1                            true         PreprovisioningImage
provisionings                                          metal3.io/v1alpha1                            false        Provisioning
storagestates                                          migration.k8s.io/v1alpha1                     false        StorageState
storageversionmigrations                               migration.k8s.io/v1alpha1                     false        StorageVersionMigration
alertmanagerconfigs                   amcfg            monitoring.coreos.com/v1beta1                 true         AlertmanagerConfig
alertmanagers                         am               monitoring.coreos.com/v1                      true         Alertmanager
podmonitors                           pmon             monitoring.coreos.com/v1                      true         PodMonitor
probes                                prb              monitoring.coreos.com/v1                      true         Probe
prometheuses                          prom             monitoring.coreos.com/v1                      true         Prometheus
prometheusrules                       promrule         monitoring.coreos.com/v1                      true         PrometheusRule
servicemonitors                       smon             monitoring.coreos.com/v1                      true         ServiceMonitor
thanosrulers                          ruler            monitoring.coreos.com/v1                      true         ThanosRuler
clusternetworks                                        network.openshift.io/v1                       false        ClusterNetwork
egressnetworkpolicies                                  network.openshift.io/v1                       true         EgressNetworkPolicy
hostsubnets                                            network.openshift.io/v1                       false        HostSubnet
netnamespaces                                          network.openshift.io/v1                       false        NetNamespace
egressrouters                                          network.operator.openshift.io/v1              true         EgressRouter
operatorpkis                                           network.operator.openshift.io/v1              true         OperatorPKI
ingressclasses                                         networking.k8s.io/v1                          false        IngressClass
ingresses                             ing              networking.k8s.io/v1                          true         Ingress
networkpolicies                       netpol           networking.k8s.io/v1                          true         NetworkPolicy
runtimeclasses                                         node.k8s.io/v1                                false        RuntimeClass
oauthaccesstokens                                      oauth.openshift.io/v1                         false        OAuthAccessToken
oauthauthorizetokens                                   oauth.openshift.io/v1                         false        OAuthAuthorizeToken
oauthclientauthorizations                              oauth.openshift.io/v1                         false        OAuthClientAuthorization
oauthclients                                           oauth.openshift.io/v1                         false        OAuthClient
tokenreviews                                           oauth.openshift.io/v1                         false        TokenReview
useroauthaccesstokens                                  oauth.openshift.io/v1                         false        UserOAuthAccessToken
authentications                                        operator.openshift.io/v1                      false        Authentication
cloudcredentials                                       operator.openshift.io/v1                      false        CloudCredential
clustercsidrivers                                      operator.openshift.io/v1                      false        ClusterCSIDriver
configs                                                operator.openshift.io/v1                      false        Config
consoles                                               operator.openshift.io/v1                      false        Console
csisnapshotcontrollers                                 operator.openshift.io/v1                      false        CSISnapshotController
dnses                                                  operator.openshift.io/v1                      false        DNS
etcds                                                  operator.openshift.io/v1                      false        Etcd
imagecontentsourcepolicies                             operator.openshift.io/v1alpha1                false        ImageContentSourcePolicy
ingresscontrollers                                     operator.openshift.io/v1                      true         IngressController
kubeapiservers                                         operator.openshift.io/v1                      false        KubeAPIServer
kubecontrollermanagers                                 operator.openshift.io/v1                      false        KubeControllerManager
kubeschedulers                                         operator.openshift.io/v1                      false        KubeScheduler
kubestorageversionmigrators                            operator.openshift.io/v1                      false        KubeStorageVersionMigrator
networks                                               operator.openshift.io/v1                      false        Network
openshiftapiservers                                    operator.openshift.io/v1                      false        OpenShiftAPIServer
openshiftcontrollermanagers                            operator.openshift.io/v1                      false        OpenShiftControllerManager
servicecas                                             operator.openshift.io/v1                      false        ServiceCA
storages                                               operator.openshift.io/v1                      false        Storage
catalogsources                        catsrc           operators.coreos.com/v1alpha1                 true         CatalogSource
clusterserviceversions                csv,csvs         operators.coreos.com/v1alpha1                 true         ClusterServiceVersion
installplans                          ip               operators.coreos.com/v1alpha1                 true         InstallPlan
olmconfigs                                             operators.coreos.com/v1                       false        OLMConfig
operatorconditions                    condition        operators.coreos.com/v2                       true         OperatorCondition
operatorgroups                        og               operators.coreos.com/v1                       true         OperatorGroup
operators                                              operators.coreos.com/v1                       false        Operator
subscriptions                         sub,subs         operators.coreos.com/v1alpha1                 true         Subscription
packagemanifests                                       packages.operators.coreos.com/v1              true         PackageManifest
performanceprofiles                                    performance.openshift.io/v2                   false        PerformanceProfile
poddisruptionbudgets                  pdb              policy/v1                                     true         PodDisruptionBudget
podsecuritypolicies                   psp              policy/v1beta1                                false        PodSecurityPolicy
projectrequests                                        project.openshift.io/v1                       false        ProjectRequest
projects                                               project.openshift.io/v1                       false        Project
appliedclusterresourcequotas                           quota.openshift.io/v1                         true         AppliedClusterResourceQuota
clusterresourcequotas                 clusterquota     quota.openshift.io/v1                         false        ClusterResourceQuota
clusterrolebindings                                    rbac.authorization.k8s.io/v1                  false        ClusterRoleBinding
clusterroles                                           rbac.authorization.k8s.io/v1                  false        ClusterRole
rolebindings                                           rbac.authorization.k8s.io/v1                  true         RoleBinding
roles                                                  rbac.authorization.k8s.io/v1                  true         Role
routes                                                 route.openshift.io/v1                         true         Route
configs                                                samples.operator.openshift.io/v1              false        Config
priorityclasses                       pc               scheduling.k8s.io/v1                          false        PriorityClass
rangeallocations                                       security.internal.openshift.io/v1             false        RangeAllocation
podsecuritypolicyreviews                               security.openshift.io/v1                      true         PodSecurityPolicyReview
podsecuritypolicyselfsubjectreviews                    security.openshift.io/v1                      true         PodSecurityPolicySelfSubjectReview
podsecuritypolicysubjectreviews                        security.openshift.io/v1                      true         PodSecurityPolicySubjectReview
rangeallocations                                       security.openshift.io/v1                      false        RangeAllocation
securitycontextconstraints            scc              security.openshift.io/v1                      false        SecurityContextConstraints
csidrivers                                             storage.k8s.io/v1                             false        CSIDriver
csinodes                                               storage.k8s.io/v1                             false        CSINode
csistoragecapacities                                   storage.k8s.io/v1                             true         CSIStorageCapacity
storageclasses                        sc               storage.k8s.io/v1                             false        StorageClass
volumeattachments                                      storage.k8s.io/v1                             false        VolumeAttachment
brokertemplateinstances                                template.openshift.io/v1                      false        BrokerTemplateInstance
processedtemplates                                     template.openshift.io/v1                      true         Template
templateinstances                                      template.openshift.io/v1                      true         TemplateInstance
templates                                              template.openshift.io/v1                      true         Template
profiles                                               tuned.openshift.io/v1                         true         Profile
tuneds                                                 tuned.openshift.io/v1                         true         Tuned
groups                                                 user.openshift.io/v1                          false        Group
identities                                             user.openshift.io/v1                          false        Identity
useridentitymappings                                   user.openshift.io/v1                          false        UserIdentityMapping
users                                                  user.openshift.io/v1                          false        User
ippools                                                whereabouts.cni.cncf.io/v1alpha1              true         IPPool
overlappingrangeipreservations                         whereabouts.cni.cncf.io/v1alpha1              true         OverlappingRangeIPReservation
# 全件削除
root@container:~# oc delete all
error: resource(s) were provided, but no name was specified
root@container:~# oc delete all --all
pod "parksmap-54476698b6-mwkhl" deleted
deployment.apps "parksmap" deleted
replicaset.apps "parksmap-54476698b6" deleted
replicaset.apps "parksmap-7b46cfdfb7" deleted
imagestream.image.openshift.io "parksmap" deleted
```

### [Using Port Forwarding (Japanese)](https://play.instruqt.com/embed/openshift/tracks/openshift-port-forwarding-jp?token=em_81k1Kb_XXhS83F6W&show_challenges=true)（ポートフォワーディングによるデータベースへのリモートアクセス）（所要時間目安：15分〜30分）

(未実施)

### [Transferring Files in and out of Containers (Japanese)](https://play.instruqt.com/embed/openshift/tracks/openshift-transferring-files-jp?token=em_uw1_ii0Sbu-uvwG-&show_challenges=true)（コンテナとローカルマシン間のファイルのコピー）（所要時間目安：15分〜30分）

(未実施)

## GitOps

### [Getting Started with ArgoCD and OpenShift GitOps Operator (Japanese)](https://play.instruqt.com/embed/openshift/tracks/gitops-getting-started-jp?token=em_eXQHuhFdwqfyZYka&show_challenges=true)（ArgoCDを使ってみよう）（所要時間目安：15分〜30分）

(未実施)

### [Working with Kustomize (Japanese)](https://play.instruqt.com/embed/openshift/tracks/gitops-kustomize-jp?token=em_xwRSqwHEZLz-4S1u&show_challenges=true)（所要時間目安：15分〜30分）

(未実施)

### [Working with Helm (Japanese)](https://play.instruqt.com/embed/openshift/tracks/gitops-helm-jp?token=em_0IilZzl5oFN6aht3&show_challenges=true)（所要時間目安：15分〜30分）

(未実施)

## Pipeline

### [Getting Started with OpenShift Pipelines (Japanese)](https://play.instruqt.com/embed/openshift/tracks/gitops-pipelines-jp?token=em_Iuuq9GMWhtmKCsnX&show_challenges=true)（tektonを使ってみよう）（所要時間目安：15分〜30分）

(未実施)

## Serverless

### [Getting Started with OpenShift Serverless (Japanese)](https://play.instruqt.com/embed/openshift/tracks/serverless-getting-started-jp?token=em_l5TuSKB7oPQwryBT&show_challenges=true)（所要時間目安：15分〜30分）

(未実施)

## Playgrounds OpenShift

### [Playgrounds OpenShift 4.12 (Japanese)](https://play.instruqt.com/embed/openshift/tracks/red-hat-openshift-playground-412-jp?token=em_76RNgIPdTf3tHjE1=true)（自由に使えるOpenShift環境）（1時間利用可能）
