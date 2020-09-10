# cheese-quizz

A fun cheese quizz deployed on OpenShift and illustrating cloud native technologies like Quarkus, Istio Service Mesh, CodeReady Workspaces, Strimzi Kafka Operator, Fuse Online/Syndesis, Tekton pipelines and ArgoCD.

[![Contribute](https://www.eclipse.org/che/contribute.svg)](https://codeready-crw.apps.ocp4.itix.fr/f?url=https://github.com/nmasse-itix/cheese-quizz)

## Setup

Please initialize and configure following components in this order:

* A `cheese-quizz` project for holding your project component
    * Add this label: `oc label namespace/cheese-quizz istio.io/member-of=istio-system`
    * Add this label: `oc label namespace/cheese-quizz kiali.io/member-of=istio-system`

* AMQ Streams operator deployed cluster wide
    * Create a `Kafka` CR into `cheese-quizz` letting the default properties

* Istio Service Mesh deployed with `basic-install` on `istio-system` project
    * Also deploy a `ServiceMeshMemberRoll` into `istio-system` referencing `cheese-quizz` project as member
    * Take care of removing `LimitRanges` into `cheese-quizz` project

* Knative Serving deployed cluster wide
    * Create a `KnativeServing` CR into `knative-serving` project, adding `image-registry.openshift-image-registry.svc:5000` into `registriesSkippingTagResolving` property

* Fuse Online operator deployed into `fuse-online` project
** Create a `SyndesisCRD` CR, calling it `syndesis`

* CodeReady Workspaces deployed onto `workspaces` project with:
** `quay.io/lbroudoux/che-plugin-registry:master` as the `pluginRegistryImage`
** `true` for `tlsSupport`
** `CHE_INFRA_KUBERNETES_PVC_WAIT__BOUND: 'false'` as `server.customCheProperties`

```yml
apiVersion: org.eclipse.che/v1
kind: CheCluster
metadata:
  name: codeready-workspaces
  namespace: workspaces
spec:
  server:
    cheImageTag: ''
    cheFlavor: codeready
    devfileRegistryImage: ''
    pluginRegistryImage: 'quay.io/lbroudoux/che-plugin-registry:master'
    tlsSupport: true
    selfSignedCert: false
    customCheProperties:
      CHE_INFRA_KUBERNETES_PVC_WAIT__BOUND: 'false'
  database:
    externalDb: false
    chePostgresHostName: ''
    chePostgresPort: ''
    chePostgresUser: ''
    chePostgresPassword: ''
    chePostgresDb: ''
  auth:
    openShiftoAuth: true
    identityProviderImage: ''
    externalIdentityProvider: false
    identityProviderURL: ''
    identityProviderRealm: ''
    identityProviderClientId: ''
  storage:
    pvcStrategy: per-workspace
    pvcClaimSize: 1Gi
    preCreateSubPaths: true
```



$ oc -n argocd apply -f https://raw.githubusercontent.com/argoproj/argo-cd/v1.4.2/manifests/install.yaml


{"email":"david.clauvel@gmail.com","username":"David Clauvel","cheese":"Cheddar"}
