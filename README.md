# cloudlogin

Утилита для получения и обновления пользовательского токена на основе персональных ключей доступа Cloud.ru.
Необходима при подключении к кластеру Evolution Managed Kubernetes.

При работе с kubectl утилита cloudlogin будет автоматически запрашивать и обновлять токен для подключения к кластеру. 
Если токен валидный, пользователь получит доступ к кластеру. 
При просроченом токене cloudlogin перевыпустит его.

## Установка и настройка
 
1. Скачайте cloudlogin для вашей операционной системы.

1. Настройте cloudlogin для выполнения.
 
   **Linux или macOS**
   
   1. Распакуйте загруженный архив:
   
      ```shell
	  tar -xzf <archive_name>
	  ```
	  
	  Где `<archive_name>` — название загруженного архива. 
	  Например, `cloudlogin_Linux_amd64.tar.gz`.
   
   
   1. Переместите файл cloudlogin в директорию /usr/local/bin:
   
	  ```shell
	  sudo mv cloudlogin /usr/local/bin
	  ```	  
   
   **Windows**
   
   1. Распакуйте загруженный архив в директорию cloudlogin.
   
   1. Переместите директорию cloudlogin в C:\Program Files.

   1. Добавьте путь к cloudlogin в переменную окружения PATH.
   
   
1. Проверьте работу утилиты, выполнив:

   ```shell
   cloudlogin help
   ```
   
   Если утилита установлена правильно, будет доступна справочная информация о работе с ней.
   
## Настройка kubeconfig

1. [Получите kubeconfig вашего кластера](https://cloud.ru/ru/docs/kubernetes-evolution/ug/topics/guides__cluster__download-kubeconfig.html).

1. [Создайте персональный ключ доступа](https://cloud.ru/ru/docs/console_api/ug/topics/guides__api_key.html), если еще не создавали.

1. Пропишите в kubeconfig персональный ключ доступа одним из способов:

   **Способ 1**

   Последовательно выполните:
   
   ```shell
   export CLOUDRU_KEY_ID=<Key_ID>
   export CLOUDRU_SECRET_ID=<Key_Secret>
   ```
	  
   Где `<Key_ID>` и `<Key_Secret>` — персональные ключи доступа.

   **Способ 2**
   
   Откройте конфигурационный файл kubeconfig и заполните значения для `CLOUDRU_KEY_ID` и `CLOUDRU_SECRET_ID`.
   
   ```yaml  
   env:
    - name: CLOUDRU_KEY_ID
      value: "<Key_ID>"
    - name: CLOUDRU_SECRET_ID
      value: "<Key_Secret>"
   ```
	  
   Где `<Key_ID>` и `<Key_Secret>` — персональные ключи доступа.

   Конфигурационный файл kubeconfig готов к использованию.
	  
1. Проверьте подключение:

   ```shell
   kubectl cluster-info
   ``` 	  

При вызове kubectl для `CLOUDRU_KEY_ID` и `CLOUDRU_SECRET_ID` будет получен токен, который кешируется в файл `$HOME/.cloudru/.token`. 

## Посмотреть пользовательский токен

Если требуется посметреть токен, в командной строке выполните:

```shell
cloudlogin get-token
```


## Конвертирование kubeconfig

С помощью cloudlogin можно конвертировать ранее загруженный kubeconfig в правильный формат.

В командной строке выполните:

```shell
cloudlogin convert-kubeconfig --kubeconfig <kubeconfig_path> --username <user> --iam-url <iam_url>
```   

Где:

- `--kubeconfig <kubeconfig_path>` — путь к kubeconfig.

  Опциональный параметр. По умолчанию — `--kubeconfig $HOME/.kube/config`.

- `--username <user>` — идентификатор пользователя, для которого необходимо преобразовать kubeconfig.

   Чтобы посмотреть идентификатор пользователя <user>, выполните:
   
   ```shell
   kubectl config get-users
   ```

   Можно не указывать, если kubeconfig предназначен только для одного пользователя.

- `--iam-url <iam_url>` — эндпоинт для получения токенов доступа.

   Опциональный параметр. По умолчанию — `--iam-url https://id.cloud.ru/auth/system/openid/token`.

Далее необходимо настроить kubeconfig.
