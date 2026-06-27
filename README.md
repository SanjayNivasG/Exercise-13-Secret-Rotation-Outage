# Exercise 13 – Secret Rotation Outage

## Overview

This exercise demonstrates a common Kubernetes production issue where an application fails authentication after a secret rotation.

After the secret was rotated in Secret Manager, the Kubernetes Secret was not updated. As a result, the application continued using the old credentials and returned **401 Unauthorized**.

---

## Incident

**Error**

```
401 Unauthorized
```

**Application Logs**

```
Token validation failed
```

**Kubernetes Secret**

```
kubectl get secret payment-secret
```

The secret had not been updated even after the credential rotation.

---

## Root Cause

The secret was successfully rotated in the external Secret Manager, but the updated value was not synchronized to Kubernetes.

The application continued using the old secret, causing authentication to fail.

---

## Investigation

1. Checked the Kubernetes Secret.
2. Verified that the secret was outdated.
3. Compared the expected secret with the Kubernetes Secret.
4. Confirmed that the application was using the old credentials.

---

## Resolution

- Deleted the outdated Kubernetes Secret.
- Created a new Secret with the updated token.
- Restarted the application deployment to load the new Secret.
- Verified that the application could authenticate successfully.

---

## Commands Used

```bash
kubectl get secret payment-secret -n payment

kubectl describe secret payment-secret -n payment

kubectl get secret payment-secret -n payment -o yaml

kubectl delete secret payment-secret -n payment

kubectl create secret generic payment-secret \
--from-literal=TOKEN=new-token \
-n payment

kubectl rollout restart deployment payment-service -n payment
```

---

## Screenshots

- Secret Created
- Secret Details
- Secret YAML
- Updated Secret

---

## Key Learning

- Secret rotation alone is not enough.
- Kubernetes Secrets must also be updated.
- Applications must reload the updated Secret.
- Secret synchronization should be automated to avoid authentication failures.

---

## Outcome

The outdated Kubernetes Secret was replaced with the latest secret, and the application was able to authenticate successfully.
