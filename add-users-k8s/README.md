### Create a service/user account in Kubernetes and restrict it to one namespace using RBAC
run manifest file
Get the service account token that will be used to access Kubernetes from the dashboard or through the kubectl command line.
````
kubectl -n ${NAMESPACE} describe secret $(kubectl -n ${NAMESPACE} get secret | (grep demo-sa-secret |
| echo "$_") | awk '{print $1}') | grep token: | awk '{print $2}'\n
````
Get certificate details:
````
kubectl  -n ${NAMESPACE} get secret `kubectl -n ${NAMESPACE} get secret | (grep ${K8S_USER} |
| echo "$_") | awk '{print $1}'` -o "jsonpath={.data['ca\.crt']}"
````
Creating a kubectl configuration
If you want to set up kubectl with the credentials you received, it would look something like this:
````
$ cat .kube/config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJXRENCL3FBREFnRUNBZ0VBTUFvR0NDcUdTTTQ5QkFNQ01DTXhJVEFmQmdOVkJBTU1HR3N6Y3kxelpYSjIKWlhJdFkyRkFNVFUzTmpNd01qVXdNakFlRncweE9URXlNVFF3TlRRNE1qSmFGdzB5T1RFeU1URXdOVFE0TWpKYQpNQ014SVRBZkJnTlZCQU1NR0dzemN5MXpaWEoyWlhJdFkyRkFNVFUzTmpNd01qVXdNakJaTUJNR0J5cUdTTTQ5CkFnRUdDQ3FHU000OUF3RUhBMElBQlBiSmdKQ2w0elZsNFlaQUJ4dThnbFk1c2hEeDYwaVd6cXY0RU96MS93K24KKzJpMG5heUxuRTF1MjZmT1ZkY3dlMFdjUFJCb2J0M2ViNnNtYWRKQUhBV2pJekFoTUE0R0ExVWREd0VCL3dRRQpBd0lDcERBUEJnTlZIUk1CQWY4RUJUQURBUUgvTUFvR0NDcUdTTTQ5QkFNQ0Ewa0FNRVlDSVFEczN4YTArK0E4CktVd0w2NUZyR25vWW9sTWNUei81QnoxSmNJc292VkxncUFJaEFJV0xRUXpyWkpDem9TaVlEMFZvUXhlaXIwOUEKVWhnTDBucFlzRDVUUlVYKwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
    server: https://k3s-master01:6443
  name: mycluster


contexts:
- context:
    cluster: mycluster
    namespace: demo
    user: demo-user
  name: demo

current-context: demo
kind: Config
preferences: {}


users:
- name: demo-user
  user:
    token: eyJhbGciOiJSUzI1NiIsImtpZCI6IkRrUEFveUZGUGZZS0Q3Tzl5eVZpcFE5elFYZEI5SWZ6ZlVhYXFzLU04ZTQifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZW1vIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlbW8tdXNlci10b2tlbi1rOXFibCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJkZW1vLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJjOWRhNGVmOC1jNmQ5LTQ0NTEtYTQ5Ny02ODc1MjY1MzAwMzQiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6ZGVtbzpkZW1vLXVzZXIifQ.CnrAkziL_Qr8QNQCV_PDkXCi2H-4MoPUGoPVxjSWGZUTXd6V-a9_JKv6t5Vqhrh5vXNTkDSaR1BtLCpKYdXTyqY6CjbyI7gYYcA2M22nkCDjUiwDhxInlios29SAtoOAXq7rwg_cdgdA7XWAWEcWDtT1vRe5LLbXsnORuJ5BtYXynQXWjWjbcC6T9XqRL7iZX4VUk4YCAkX7N89OGzvyycUjjHzOne67qzqOawzjYqeSzHiXIXILwHk4KKhU8tdGG6shYF7niazdp6ZyssdQ24lQext9jzDeUZf3iXPJ_bvZUv4Jo0_eZjldi9WW0dgN5PXe5r-cD1nOJHE8sClBsg
    client-key-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJXRENCL3FBREFnRUNBZ0VBTUFvR0NDcUdTTTQ5QkFNQ01DTXhJVEFmQmdOVkJBTU1HR3N6Y3kxelpYSjIKWlhJdFkyRkFNVFUzTmpNd01qVXdNakFlRncweE9URXlNVFF3TlRRNE1qSmFGdzB5T1RFeU1URXdOVFE0TWpKYQpNQ014SVRBZkJnTlZCQU1NR0dzemN5MXpaWEoyWlhJdFkyRkFNVFUzTmpNd01qVXdNakJaTUJNR0J5cUdTTTQ5CkFnRUdDQ3FHU000OUF3RUhBMElBQlBiSmdKQ2w0elZsNFlaQUJ4dThnbFk1c2hEeDYwaVd6cXY0RU96MS93K24KKzJpMG5heUxuRTF1MjZmT1ZkY3dlMFdjUFJCb2J0M2ViNnNtYWRKQUhBV2pJekFoTUE0R0ExVWREd0VCL3dRRQpBd0lDcERBUEJnTlZIUk1CQWY4RUJUQURBUUgvTUFvR0NDcUdTTTQ5QkFNQ0Ewa0FNRVlDSVFEczN4YTArK0E4CktVd0w2NUZyR25vWW9sTWNUei81QnoxSmNJc292VkxncUFJaEFJV0xRUXpyWkpDem9TaVlEMFZvUXhlaXIwOUEKVWhnTDBucFlzRDVUUlVYKwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg== 
````
let's confirm it works:
````
$ kubectl get secrets
NAME                    TYPE                                  DATA   AGE
default-token-25lbj     kubernetes.io/service-account-token   3      55m
demo-user-token-k9qbl   kubernetes.io/service-account-token   3      50m

$ kubectl get nodes
Error from server (Forbidden): nodes is forbidden: User "system:serviceaccount:demo:demo-user" cannot list resource "nodes" in API group "" at the cluster scope
````
