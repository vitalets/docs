### Получите токен сервисного аккаунта {{ k8s }} для аутентификации в {{ GL }} {#k8s-get-token}

{% note info %}

Обратите внимание, сервисный аккаунт {{ k8s }} — это не [сервисный аккаунт {{ iam-full-name }}](../../iam/concepts/users/service-accounts.md). Подробнее см. в [документации {{ managed-k8s-name }}](../../managed-kubernetes/concepts/index.md#service-accounts).

{% endnote %}

Чтобы получить токен сервисного аккаунта {{ k8s }}:
1. Настройте локальное окружение на работу с созданным кластером {{ k8s }}:

   ```bash
   {{ yc-k8s }} cluster get-credentials <идентификатор или имя кластера> --external
   ```

1. Сохраните спецификацию для создания сервисного аккаунта {{ k8s }} в YAML-файл `gitlab-admin-service-account.yaml`:

   ```yaml
   ---
   apiVersion: v1
   kind: ServiceAccount
   metadata:
     name: gitlab-admin
     namespace: kube-system
   ---
   apiVersion: rbac.authorization.k8s.io/v1beta1
   kind: ClusterRoleBinding
   metadata:
     name: gitlab-admin
   roleRef:
     apiGroup: rbac.authorization.k8s.io
     kind: ClusterRole
     name: cluster-admin
   subjects:
   - kind: ServiceAccount
     name: gitlab-admin
     namespace: kube-system
   ```

1. Создайте сервисный аккаунт:

   ```bash
   kubectl apply -f gitlab-admin-service-account.yaml
   ```

1. Узнайте токен сервисного аккаунта:

   ```bash
   kubectl -n kube-system get secrets -o json | \
   jq -r '.items[] | select(.metadata.name | startswith("gitlab-admin")) | .data.token' | \
   base64 --decode
   ```

1. Сохраните полученный токен — он понадобится для следующих шагов.