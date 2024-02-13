#### Creating container secrets for Semaphore:
```
head -c32 /dev/urandom | base64 -w 0 | podman secret create semaphore-db-pass -
head -c32 /dev/urandom | base64 -w 0 | podman secret create semaphore-access-key-encryption -
read -s -p "Password to encode > " TEMPPASS ; echo -n $TEMPPASS | podman secret create semaphore-admin-password - ; echo "" ; unset TEMPPASS
```