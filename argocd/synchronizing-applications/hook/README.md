# Hooks in ArgoCD

Argo CD has the ability to set up different sync phases by allowing the user to utilize “hooks”. These injection points 
within the Application lifecycle enable additional automation, such as running scripts before, during, and/or after a sync 
has completed, to supplement applying the standard set of resources. You can also use hooks in the event a sync has 
failed for whatever reason. While hooks can be implemented as any Kubernetes object, they are usually Pods or Jobs.

There are 4 hooks that can be used in your Argo CD Sync process:

### PreSync
This phase occurs prior to the Sync phase. This is typically used for actions that need to occur before the Application 
is synced. A common use case is running a script that performs a schema update against a database.

#### Use cases:
- Run data migration / schema update
- Check prerequisites
- Backup data before deploying

### Sync
This is the “standard” or “default” phase for Argo CD and is executed once the PreSync phase has finished. 
This is typically used to aid the Argo CD Application deployment process in the event more complex activities within the Application
needs to occur.

#### Use cases:
- Handle complex deployment tasks.
- Apply additional configuration during deployment

### PostSync

This phase occurs after the Sync phase has been completed. This can be used to send a notification that the phase has 
been completed or to trigger a CI progress or continue a CI/CD workflow.

#### Use cases:
- Send notification (Slack, Email, Teams)
- Trigger CI/CD pipeline next
- Run smoke tests/ integration tests
- Update ticket/ issue tracking

### SyncFail

This is a special hook that is run only if a sync operation has failed. This is normally used for alerting or performing cleanup activities.

#### Use cases:
- Send alerts when a deployment fails
- Automatically perform rollbacks
- Clean up partially created resources

## Hook Deletion Policies

Resources that make use of Hooks can be deleted when a sync operation is performed
by using the argocd.argoproj.io/hook-delete-policy annotation. The following
hook deletion policies are available.

### HookSucceeded
The hook resource/object is deleted once it has successfully completed.
### HookFailed
The hook resource/object is deleted if the hook has failed.
### BeforeHookCreation
Any hook resource/object will be deleted before the new one is created.
