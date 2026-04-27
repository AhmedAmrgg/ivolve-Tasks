# 📦 Lab 12: Managing Configuration and Sensitive Data with ConfigMaps and Secrets

## 📌 Overview

In this lab, we learn how to manage application configuration and sensitive data in Kubernetes using:

* **ConfigMaps** → for non-sensitive data
* **Secrets** → for sensitive data (Base64 encoded)

---

## 🧾 Requirements

* Kubernetes Cluster (Minikube / K8s)
* kubectl installed and configured

---

## ⚙️ Step 1: Create ConfigMap

Store non-sensitive MySQL configuration:

* `DB_HOST` → MySQL service hostname
* `DB_USER` → Database username

### 📄 configmap.yaml

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-config
data:
  DB_HOST: mysql-service
  DB_USER: ivolve_user
```

### ▶️ Apply ConfigMap

```bash
kubectl apply -f configmap.yaml
```

---

## 🔐 Step 2: Encode Sensitive Data

Encode values using Base64:

```bash
echo -n "mypassword" | base64
echo -n "myrootpassword" | base64
```

Example output:

```
bXlwYXNzd29yZA==
bXlyb290cGFzc3dvcmQ=
```

---

## 🔑 Step 3: Create Secret

Store sensitive data:

* `DB_PASSWORD`
* `MYSQL_ROOT_PASSWORD`

### 📄 secret.yaml

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
type: Opaque
data:
  DB_PASSWORD: bXlwYXNzd29yZA==
  MYSQL_ROOT_PASSWORD: bXlyb290cGFzc3dvcmQ=
```

### ▶️ Apply Secret

```bash
kubectl apply -f secret.yaml
```

---

## 🔍 Step 4: Verify Resources

### Check ConfigMap

```bash
kubectl get configmap
kubectl describe configmap mysql-config
```

### Check Secret

```bash
kubectl get secret
kubectl describe secret mysql-secret
```

---

## 🔗 Step 5: Use in Pod

### 📄 pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
    - name: app
      image: nginx
      env:
        - name: DB_HOST
          valueFrom:
            configMapKeyRef:
              name: mysql-config
              key: DB_HOST

        - name: DB_USER
          valueFrom:
            configMapKeyRef:
              name: mysql-config
              key: DB_USER

        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: DB_PASSWORD

        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: MYSQL_ROOT_PASSWORD
```

### ▶️ Apply Pod

```bash
kubectl apply -f pod.yaml
```

---

## 🧠 Key Concepts

| Resource  | Purpose              | Security       |
| --------- | -------------------- | -------------- |
| ConfigMap | Non-sensitive config | Plain text     |
| Secret    | Sensitive data       | Base64 encoded |

---

## ⚠️ Notes

* Base64 is **encoding, not encryption**
* For production:

  * Enable Kubernetes Encryption at Rest
  * Use external secret managers

---

## ✅ Conclusion

You learned how to:

* Manage configuration using ConfigMaps
* Secure sensitive data using Secrets
* Inject both into Kubernetes Pods

---
