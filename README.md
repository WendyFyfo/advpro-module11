# advpro-module11


## Reflection on Hello Minikube
1.  Compare the application logs before and after you exposed it as a Service.

> Before exposing as a service
![Before expose](/imgs/image.png)



> After exposing as a service
![After expose](/imgs/image%20copy.png)

Pada log setelah expose sebagai service, terdapat tambahan beberapa log berupa request ke endpoint service hello-node dan HTTP method dari setiap request yang dibuat ke endpoint tersebut.

2. Notice that there are two versions of `kubectl get` invocation during this tutorial section. The first does not have any option, while the latter has `-n` option with value set to `kube-system`. What is the purpose of the `-n` option and why did the output not list the pods/services that you explicitly created?

opsi -n pada kubectlget menunjuk ke namespace yang berfungsi untuk mengelompokkan dan memisahkan resource dalam satu cluster. Secara default, command kubectl get pods akan menampilkan pod yang berada di namespace default. Namun, jika ingin melihat pod yang berada di namespace lain, contohnya kube-system yang sudah dijalankan sebelumnya, Maka kita bisa menggunakan opsi -n  diikuti dengan nama namespace-nya yaitu `kube-system`.

contoh = `kubectl get pods, services -n kube-system`

## Reflection on Rolling Update & Kubernetes Manifest File
1. What is the difference between Rolling Update and Recreate deployment strategy?

Strategi Rolling Update memperbarui secara bertahap dengan cara membuat pod baru dengan versi aplikasi terbaru terlebih dahulu. Setelah pod baru siap menggantikan pod lama, pod lama akan dihentikan.  

Recreate deployment strategy menghentikan semua pod lama sebelum membuat pod baru. selama proses ini, tidak ada pod yang dijalankan, sehingga tidak akan ada service yang bisa diakses untuk sementara.


2. Try deploying the Spring Petclinic REST using Recreate deployment strategy and document
your attempt.

langkah 1. membuat recreate deployment.yaml
```yaml
kubectl create deployment recreate-spring-petclinic-rest 
  --image=springcommunity/recreate-spring-petclinic-rest 
  --dry-run=client -o yaml > recreate-deployment.yaml
```

langkah 2. Memodifikasi strategi deployment menjadi Recreate dalam file yaml
```yaml
    ...
        strategy:
            type: Recreate
    ...
```
[recreate-deployment.yaml](/recreate-deployment.yaml)

langkah 3 apply deployment ke cluster kubernetes dan periksa status pods
```yaml
kubectl apply -f recreate-deployment.yaml
kubectl get deployments
kubectl get pods
```
![Apply Recreate Deployment](/imgs/image%20copy%202.png)

langkah 4. Expose deployment sebagai service dengan tipe LoadBalancer
```yaml
kubectl expose deployment recreate-spring-petclinic-rest --type=LoadBalancer --port=9966
kubectl get service recreate-spring-petclinic-rest
```
![Expose Recreate Deployment](/imgs/image%20copy%203.png)


3. Prepare different manifest files for executing Recreate deployment strategy.
membuat recreate-service.yaml
```yaml
kubectl get services/recreate-spring-petclinic-rest -o yaml > recreate-service.yaml
```
[recreate-service.yaml](/recreate-service.yaml)

4. What do you think are the benefits of using Kubernetes manifest files? Recall your experience
in deploying the app manually and compare it to your experience when deploying the same app
by applying the manifest files (i.e., invoking `kubectl apply -f` command) to the cluster.

Menggunakan kubernetes manifest file berguna karena kita dapat menyimpan konfigurasi aplikasi dalam file yang dapat kita modifikasi dan dapat kita pakai ulang. Tidak perlu menulis banyak command pada CLI, cukup membuat file YAML sekali dan menjalankan `kubectl apply -f ...` ketika perlu mendeploy atau mengupdate aplikasi.

Selain itu, dari percobaan pada no.2 di atas, manifest juga memudahkan saya menentukan strategi khusus untuk deployment.

5. (Optional) Do the same tutorial steps, but on a managed Kubernetes cluster (e.g., GCP). You
need to provision a Kubernetes cluster on Google Cloud Platform. Then, re-run the tutorial steps
(Hello Minikube and Rolling Update) on the remote cluster. Document your attempt and highlight
the differences and any issues you encountered.