# Argo CD Practice – 

## Topics Practiced

* Installed Argo CD on Kubernetes.
* Accessed the Argo CD Web UI through the browser.
* Logged in to Argo CD using the admin account.
* Created an Argo CD Application manually through the Web UI.
* Created an Argo CD Application using an `Application` YAML manifest.
* Connected Argo CD to a **private GitHub repository using SSH authentication**.
* Used an SSH repository URL:
  `git@github.com:<username>/<repository>.git`
* Configured the application to deploy manifests from a specific Git repository path.
* Deployed the application into the `dev` namespace.
* Enabled automatic synchronization.
* Enabled Argo CD **self-healing** with:

  ```yaml
  automated:
    selfHeal: true
    prune: true
  ```
* Created configuration drift by manually changing the Kubernetes resource.
* Observed Argo CD detect the difference between the Git desired state and the Kubernetes live state.
* Verified that Argo CD automatically reconciled the drift and restored the desired state.

## GitOps Flow Practiced

```text
Private GitHub Repository
          |
          | SSH
          v
       Argo CD
          |
          | Automatic Sync
          v
      Kubernetes
          |
          | Manual change
          v
        Drift
          |
          | Self Heal
          v
  Desired state restored
```

## Interview Explanation

> "Today I practiced Argo CD by installing it on Kubernetes and accessing its UI. I created applications both manually through the UI and using an Argo CD Application YAML file. I also connected Argo CD to a private GitHub repository using SSH authentication. I enabled automated synchronization with self-healing and tested configuration drift by manually changing a Kubernetes resource. Argo CD detected the drift and automatically reconciled the cluster back to the state defined in Git."

## Key Concepts Learned

**Application:** Argo CD resource that defines what Git repository/path should be deployed to which Kubernetes cluster and namespace.

**Sync:** The process of making the Kubernetes cluster match the desired state in Git.

**Automated Sync:** Argo CD automatically applies changes detected in Git.

**Self-Healing:** Argo CD automatically restores resources when the live Kubernetes state is changed manually and differs from Git.

**Prune:** Argo CD removes Kubernetes resources that were deleted from Git.

**Drift:** Difference between the desired state in Git and the actual live state in Kubernetes.
