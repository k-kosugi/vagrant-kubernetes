# はじめに
vagrant up で Kuberenetes クラスターを作成します。

# 手順
1. git clone する
    ```bash
    $ git clone https://github.com/k-kosugi/vagrant-kubernetes.git
    ```
2. vagrat up する
    ```
    $ vagrant up
    ```
3. マスターにログインする
    ```
    $ vagrant ssh master-node
    ```
4. su - で root ユーザーになる
   パスワードは vagrant 
    ```
    $ su - 
    ```
5. kuberentes の状態を確認する
    ```
    $ kubectl get nodes 
    ```

