# nordmart-gitops

GitOps config will contain Tenant Applications, ArgoCD Apps, Tekton Pipelines.

### Get Started
Add following folders to the repo.

- **For Each Application**
This folder will contain application helm chart which will be updated with newly built image whenever main of application repo is updated. 
- **For Tekton Manifests**
This folder will contain the pipeline to be run for application.
- **For ArgoCD Apps**
The folder contains argocd applications for all applications & pipelines.
### Sample Setup
1. Lets clone our gitops repo that we created in the previous section 1b.

        # git clone https://gitlab.com/<group-name>/<gitops-repo>
        git clone https://gitlab.com/workshop-exercise/nordmart-gitops

2. Add folder for our apps we cloned in previous section 1b. Run following commands:

        # cd <gitops-repo-name>
        cd nordmart-gitops

        # mkdir <app-name>
        mkdir stakater-nordmart-review-ui
        touch stakater-nordmart-review-ui/.gitkeep

        mkdir stakater-nordmart-review-ui
        touch stakater-nordmart-review/.gitkeep

3. Lets create a tekton pipelines folder and it will contain our manifests for pipeline for our nordmart review and nordmart review ui applications.

        # mkdir <tekton-manifests-dir-name>
        mkdir tekton-pipeline
        touch tekton-pipeline/.gitkeep

4. Now lets create argocd apps folder that will point to our application directory and make sure that resource manifest inside these are deployed and synced in the cluster

        # mkdir <argocd-apps-dir-name>
        mkdir argocd-apps
        touch argocd-apps/.gitkeep

    4.a For each folder present at root level (application folders,tekton-pipeline) , create an argocd application inside this folder. This will create an application inside argocd which will make sure the manifests in these folder are deployed and synced inside our cluster

        apiVersion: argoproj.io/v1alpha1
        kind: Application
        metadata:
        name: <YOUR_TENANT_NAME>-<FOLDER_NAME>
        namespace: openshift-gitops
        spec:
        destination:
            namespace: <YOUR_TENANT_NAME>
            server: 'https://kubernetes.default.svc'
        project: <YOUR_TENANT_NAME>
        source:
            path: <FOLDER_IN_GITOPS_REPO>
            repoURL: <GITOPS_REPO_URL>
            targetRevision: HEAD
        syncPolicy:
            automated:
            prune: true
            selfHeal: true

    4.b Using the above template, we will create 3 new files with our two nordmart applications and 1 tekton pipeline application.  
    Manifest1:

        apiVersion: argoproj.io/v1alpha1
        kind: Application
        metadata:
        name: workshop-stakater-nordmart-review-ui
        namespace: openshift-gitops
        spec:
        destination:
            namespace: workshop
            server: 'https://kubernetes.default.svc'
        project: gabbar
        source:
            path: stakater-nordmart-review-ui
            repoURL: https://gitlab.com/workshop-exercise/nordmart-gitops
            targetRevision: HEAD
        syncPolicy:
            automated:
            prune: true
            selfHeal: true

    Manifest2:

        apiVersion: argoproj.io/v1alpha1
        kind: Application
        metadata:
        name: workshop-stakater-nordmart-review
        namespace: openshift-gitops
        spec:
        destination:
            namespace: workshop
            server: 'https://kubernetes.default.svc'
        project: gabbar
        source:
            path: stakater-nordmart-review
            repoURL: https://gitlab.com/workshop-exercise/nordmart-gitops
            targetRevision: HEAD
        syncPolicy:
            automated:
            prune: true
            selfHeal: true

    Manifest3:

        apiVersion: argoproj.io/v1alpha1
        kind: Application
        metadata:
        name: workshop-tekton-pipeline
        namespace: openshift-gitops
        spec:
        destination:
            namespace: workshop
            server: 'https://kubernetes.default.svc'
        project: gabbar
        source:
            path: tekton-pipeline
            repoURL: https://gitlab.com/workshop-exercise/nordmart-gitops
            targetRevision: HEAD
        syncPolicy:
            automated:
            prune: true
            selfHeal: true

    4c. Argocd apps folder contains three files with the above manifests specified
5. Finally push these changes to the remote 

        git push origin main
        or 
        git push origin master