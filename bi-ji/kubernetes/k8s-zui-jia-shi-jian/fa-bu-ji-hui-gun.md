# 发布及回滚

```text
Manage the rollout of a resource.
 Valid resource types include:

  *  deployments
  *  daemonsets
  *  statefulsets

Examples:
  # Rollback to the previous deployment
  kubectl rollout undo deployment/abc

  # Check the rollout status of a daemonset
  kubectl rollout status daemonset/foo

Available Commands:
  history     View rollout history
  pause       Mark the provided resource as paused
  restart     Restart a resource
  resume      Resume a paused resource
  status      Show the status of the rollout
  undo        Undo a previous rollout
```

## **打补丁**

**kubectl patch** --help

```text
  # Update a container's image; spec.containers[*].name is required because it's a merge key.
  kubectl patch pod valid-pod -p '{"spec":{"containers":[{"name":"kubernetes-serve-hostname","image":"new image"}]}}'
kubectl patch (-f FILENAME | TYPE NAME) -p PATCH [options]
```

## **直接更新镜像**

kubectl set image --help `kubectl set image deploy bw-custom bw-custom=192.168.0.96:8082/bw-custom:v7.12.0 -n tbw-pre` `kubectl get rs -n tbw-pre -o wide`

## **回滚**默认回退到上一个版本

```text
  # Rollback to the previous deployment
  kubectl rollout undo deploy/bw-custom
  # Rollback to daemonset revision 3
  kubectl rollout undo daemonset/abc --to-revision=3
  # Rollback to the previous deployment with dry-run
  kubectl rollout undo --dry-run=true deployment/abc
```

