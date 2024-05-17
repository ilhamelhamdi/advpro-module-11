# advpro-module-11

Nama : Ilham Abdillah Alhamdi <br>
NPM : 2206081194 <br>
Kelas : Advance Programming A <br>

## Reflection on Hello Minikube

1. Question 1

  > Compare the application logs before and after you exposed it as a Service. Try to open the app several times while the proxy into the Service is running. What do you see in the logs? Does the number of logs increase each time you open the app?

  Pada hasil logs yang diperoleh dari command `kubectl logs <PODS_NAME>`, terlihat informasi mengenai dijalankannya service HTTP dan UDP di awal, serta beberapa log terkait HTTP request yang mengakses aplikasi. 

  ![](./assets/images/before-after-hit-logs.png)

  Gambar di atas adalah snapshot dari logs pada pod di waktu yang berbeda. Log pertama pertama adalah initial condition. Sedangkan log kedua adalah kondisi setelah dilakukan 5 kali hit/akses aplikasi. Terlihat terdapat perbedaan jumlah record log untuk HTTP request GET, sebanyak 10 record. 


2. Question 2

  > Notice that there are two versions of `kubectl get` invocation during this tutorial section. The first does not have any option, while the latter has `-n` option with value set to `kube-system`. What is the purpose of the `-n` option and why did the output not list the pods/services that you explicitly created?

  Opsi `-n` pada perintah `kubectl get` digunakan untuk menentukan _namespace_ untuk mencari _resource_.

  Di Kubernetes, _namespace_ adalah cara untuk membagi resource kluster antara beberapa pengguna atau tim. _Namespace_ ini pada dasarnya adalah _scoope_ untuk resource dan bisa digunakan untuk memberikan cakupan pada beberapa nama. Ini adalah cara untuk membagi resource kluster antara berbagai penggunaan.

  `kubectl get services`: <br>
  Perintah ini tanpa opsi -n akan menampilkan daftar layanan di _namespace_ default.

  `kubectl get pods,services -n kube-system`:<br>
   Perintah ini dengan opsi `-n kube-system` akan menampilkan daftar pod dan layanan di namespace `kube-system`. Namespace `kube-system` adalah namespace khusus yang dicadangkan untuk pod sistem yang menjalankan Kubernetes itu sendiri. Pada tutorial ini, perintah ini digunakan untuk memverifikasi apakah `service/metrics-server` telah dijalankan atau tidak (yang mana terdapat pada namespace `kube-system`).


## Reflection on Rolling

1. Question 1

> What is the difference between Rolling Update and Recreate deployment strategy?

Strategi Rolling update merupakan strategi update secara incremental. Artinya, ketika kita melakukan update pada sebuah deployment, _deployment controller_ akan meng-update masing-masing _pod_ satu per satu secara incremental. Pod yang baru akan dibuat pada node yang masih memiliki terdapat ruang (resource). Kubernetes akan menunggu hingga semua pods baru itu dijalankan, kemudian Kubernetes akan menghapus pods yang lama. Hal ini tentu akan memungkinkan pengalaman _zero downtime_ pada saat mengakses server.

Adapun strategi Recreate Deployment mengambil langkah yang berbeda. Saat akan dilakukan update, Kubernetes akan mematikan pods dari deployment sebelumnya. Kemudian, Kubernetes menggantikannya dengan pods deployment yang baru. Tentunya ini akan menyebabkan downtime pada server. Strategi ini cocok untuk deployment dengan _major update_.

2. Question 2
>Try deploying the Spring Petclinic REST using Recreate deployment strategy and document your attempt

Strategi Recreate deployment sederhananya dapat kita lakukan dengan menghapus deployment dan membuatnya lagi. Untuk menghapus deployment kita dapat menjalankan command `kubectl delete deployment <Deployment_name>`. Sedangkan untuk menjalankannya kembali, cukup menjalankan `kubectl apply -f <manifest_file.yaml>`

Namun jika kita ingin men-set nya di pods controller, kita dapat menjalankan command berikut
`kubectl patch deployment <Deployment_name> -p '{"spec": {"strategy": {"type": "Recreate"}}}'`.
Dengan begini, ketika kita mengupdate deployment, misal dengan mengubah image, Kubernetes akan melakukan strategi ini.


3. Question 3
> Prepare different manifest files for executing Recreate deployment strategy.

Manifest file untuk mengeksekusi Recreate deployment strategy terdapat dalam deployment-recreate.yaml

4. Question 4
> What do you think are the benefits of using Kubernetes manifest files? Recall your experience in deploying the app manually and compare it to your experience when deploying the same app by applying the manifest files (i.e., invoking `kubectl apply -f` command) to the cluster.

Dengan menggunakan file manifest, kita dapat melakukan deployement dengan lebih cepat, tanpa perlu melakukan konfiguras/instalasi secara manual. Hal ini karena Kubernetes akan menjalankan steps-steps deployment secara otomatis.
