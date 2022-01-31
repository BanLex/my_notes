# terraform
[Назад](https://github.com/BanLex/my_notes/blob/main/terraform/content_config.md)

> **Ссылки**
> [Яндекс. Начало работы с Terraform](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-quickstart)
> 
> [Яндекс. Загрузка состояний Terraform в Object Storage](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-state-storage)
> 
> [HashiCorp. Yandex.Cloud Provider](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs)

***
В этом блоке описываем где сохранять состояние инфраструктуры. И подключаем провайдера. В данном случае яндекс.

Блок backend хранит в себе настройки подключения к хранилищу, здесь описываются приватные данные.

В своём примере яндекс показывает такую форму блока:
```
terraform {
  required_providers {
    yandex = {
      source = "yandex-cloud/yandex"
    }
  }

  backend "s3" {
    endpoint   = "storage.yandexcloud.net"
    bucket     = "<имя бакета>"
    region     = "us-east-1"
    key        = "<путь к файлу состояния в бакете>/<имя файла состояния>.tfstate"
    access_key = "<идентификатор статического ключа>"
    secret_key = "<секретный ключ>"

    skip_region_validation      = true
    skip_credentials_validation = true
  }
}
```


Она допустима при использвании на локальном сервере. Но её лучше не применять на github по следующим причинам:
1. здесь содержиться конфиденциальная информация которую нельзя сохранять в открытом виде. Необходимо использовать secrets github.
2. вложенный блок backend не может получить доступ к локальным переменным.

Я использую другой подход.

[terraform.tf](https://github.com/BanLex/example_webapp/blob/main/yandex-cloud-terraform/terraform.tf)
```
terraform {
  backend "s3" {}
  required_providers {
    yandex = {
      source = "yandex-cloud/yandex"
    }
  }
}
```

В отличии от описания яндекса здесь блок backend "s3" пустой. Описание содержимое блока будет подключено отдельным файлом `backend.tfbackend`.
Для этого необходимо добавить опцию `-backend-config=backend.tfbackend` к команде init. В этот файл динамически допишу secrets во время выполнения workflow. Этот файл содержит настройки:

[backend.tfbackend](https://github.com/BanLex/example_webapp/blob/main/yandex-cloud-terraform/backend.tfbackend)

    endpoint   = "storage.yandexcloud.net"
    bucket     = "banlex.terraform"
    region     = "us-east-1"
    key        = "tester.tfstate"
    skip_region_validation      = true
    skip_credentials_validation = true

endpoint, region, skip_region_validation, skip_credentials_validation - оставляем по рекомендациям яндекса.

**bucket = "<имя бакета>"**
[Консоль](https://console.cloud.yandex.ru/) --> Object Storage --> Бакеты --> Создать бакет --> Заполняем --> Создать бакет --> Заходим в бакет. Здесь можно создать нужные каталоги.
**key = "<путь к файлу состояния в бакете>/<имя файла состояния>.tfstate"**
Указываем имя файла. Terraform создаст его. 
**access_key = "<идентификатор статического ключа>"**
**secret_key = "<секретный ключ>"**

[Инструкция как их получить](https://cloud.yandex.ru/docs/iam/operations/sa/create-access-key)

[Как создать сервисный аккаунт](https://cloud.yandex.ru/docs/iam/operations/sa/create)

Эти два ключа добавляю динамически из workflow в файл [backend.tfbackend](https://github.com/BanLex/example_webapp/blob/main/yandex-cloud-terraform/backend.tfbackend).
Для это перехожу в настройки репозитория github.
Settings --> Secrets --> Actions --> New repository secret
Использую названия **YC_ACCESS_KEY** (идентификатор ключа) и **YC_SECRET_KEY** (секретный ключ).

В workflow добавляю два блока:
```
env:
      access_key: "${{secrets.YC_ACCESS_KEY}}"
      secret_key: "${{secrets.YC_SECRET_KEY}}"
```
```
      - name: insert baket keys
        run: |
          cd yandex-cloud-terraform
          echo '  access_key = "${{ access_key }}"'  >> backend.tfbackend 
          echo '  secret_key = "${{ secret_key }}"'  >> backend.tfbackend
```
[Пример](https://github.com/BanLex/example_webapp/blob/main/.github/workflows/Deploy%20infrastructure%20with%20Terraform.yml)
