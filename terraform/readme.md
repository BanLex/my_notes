# Terraform
[Назад на главную](https://github.com/BanLex/my_notes/tree/main/README.md)

Заметки и полезные ссылки по настройке Terraform. На примере подключения к yandex cloud.

> **Ссылки**
> 
> [Яндекс. Начало работы с Terraform](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-quickstart)
> 
> [Яндекс. Загрузка состояний Terraform в Object Storage](https://cloud.yandex.ru/docs/solutions/infrastructure-management/terraform-state-storage)
> 
> [HashiCorp. Yandex.Cloud Provider](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs)

## Содержание
[ **Установка Terraform** ](https://github.com/BanLex/my_notes/blob/main/terraform/setup_terraform.md)


## Разворачивание инфраструктуры

`terraform fmt` - это команда приводит ваши конфигурационные файлы к такому формату как это видит HashiCorp. Если запускаете на github, то команда бесполезна. Рабочие процессы github не сохраняют изменённые файлы в репозиторий.
`terraform init` - обязятельная команда при выполнении изменений инфраструктуры. Имеет смысл её использовать в таком виде на локальном компьютере. На github удобнее использовать её с опцией подключающей backend. `terraform init -backend-config=backend.tfbackend` - иммено в таком виде я её использую.
`terraform validate` - проверяет синтаксис.
`terraform plan` - проверяет, что из описанного в конфигурации можно развернуть.
`terraform apply` - разворачивает инфраструктуру. При этом он сперва спросит делать или нет, что бы он не спрашивал можно её запускать совместно с опцией `--auto-approve`.

Я на github использую следующую последовательность команд:
```
terraform init -backend-config=backend.tfbackend
terraform validate
terraform plan
terraform apply --auto-approve
```
[Пример](https://github.com/BanLex/example_webapp/blob/main/.github/workflows/Deploy%20infrastructure%20with%20Terraform.yml)

## Удаление инфраструктуры

Для удаления инфрастуктуры служит команда ```terraform destroy```
Но при использовании на github одной командой не обойтись. Потому что нет сохраненного текущего состояния.

На github использую следующие команды:
```
terraform init -backend-config=backend.tfbackend
terraform destroy  --auto-approve
```
[Пример](https://github.com/BanLex/example_webapp/blob/main/.github/workflows/Delete%20infrastructure%20with%20Terraform.yml)
## Файлы конфигурации
Конфигурация храниться в файлах с расширением tf. Например: main.tf.
Для Terraform не важно название файлов. Он считывает конфигурацию из всех файлов *.tf в текущем каталоге, поэтому различные конфигурации инфраструктуры следует хранить в отдельных каталогах. 
Для удобства различные ресурсы лучше хранить в отдельных файлах, а не одном длинном конфигурационном файле.

[Пример](https://github.com/BanLex/example_webapp/tree/main/yandex-cloud-terraform)

Конфигурация описывается блоками:
**locals** - переменные. Не обязательный, их может несколько.
**terraform** - настройки терраформ, обязательный блок
**provider "имя"** - поставщик услуг.


### блок terraform
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

Здесь мы описываем где сохранять состояние инфраструктуры.
И подключаем провайдера. В данном случае яндекс.
Блок backend хранит в себе настройки подключения к хранилищу, здесь описываются приватные данные.
Оставляю его пустым, т.к.:
1. Запускаю разворачивание инфраструктуры раннером github. Файл храниться в общем доступе. Эти данные буду динамически подставлять и secrets github.
2. Вложенные блоки не видят переменные в блоках locals.
Содержимое данного блока подключаю из файла [backend.tfbackend](https://github.com/BanLex/example_webapp/blob/main/yandex-cloud-terraform/backend.tfbackend) опцией  -backend-config=backend.tfbackend к команде init. В этот файл динамически допишу secrets во время выполнения workflow.
Этот файл содержит настройки:

[backend.tfbackend](https://github.com/BanLex/example_webapp/blob/main/yandex-cloud-terraform/backend.tfbackend)
```
    endpoint   = "storage.yandexcloud.net"
    bucket     = "banlex.terraform"
    region     = "us-east-1"
    key        = "tester.tfstate"
    skip_region_validation      = true
    skip_credentials_validation = true
```
endpoint, region, skip_region_validation, skip_credentials_validation - оставляем по рекомендациям яндекса.

**bucket = "<имя бакета>"**
[Консоль](https://console.cloud.yandex.ru/) --> Object Storage --> Бакеты --> Создать бакет --> Заполняем --> Создать бакет --> Заходим в бакет.
Здесь можно создать нужные каталоги.
**key = "<путь к файлу состояния в бакете>/<имя файла состояния>.tfstate"**
Указываем имя файла. Terraform создаст его.
**access_key = "<идентификатор статического ключа>"
secret_key = "<секретный ключ>"**
[Инструкция как их получить](https://cloud.yandex.ru/docs/iam/operations/sa/create-access-key)
[Как создать сервисный аккаунт](https://cloud.yandex.ru/docs/iam/operations/sa/create)
Эти два ключа добавляю динамически из workflow в файл [backend.tfbackend](https://github.com/BanLex/example_webapp/blob/main/yandex-cloud-terraform/backend.tfbackend).
Для это перехожу в настройки репозитория.
Settings --> Secrets --> Actions --> New repository secret
Использую названия YC_ACCESS_KEY (идентификатор ключа) и YC_SECRET_KEY (секретный ключ).
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

### блок provider
[provider.tf](https://github.com/BanLex/example_webapp/blob/main/yandex-cloud-terraform/provider.tf)
```
provider "yandex" {
  token     = "${ local.oauth }"
  cloud_id  = "${ local.cloud }"
  folder_id = "${ local.folder }"
  zone      = "ru-central1-a"
}
```
Здесь указываются настройки подключения к месту развертывания инфраструктуры.
**token** - [можно получить по инструкции OAuth-токен](https://cloud.yandex.ru/docs/iam/concepts/authorization/oauth-token)
Добавляем его в secrets. Называю YC_OAUTH.
**cloud_id** и **folder_id** - [Можно взять из консоли](https://console.cloud.yandex.ru/) верхний облако, ниже папка.
Добавляю в secrets YC_CLOUD и YC_FOLDER.

Этот блок свободно получает доступ к переменным. Файл locals.tf генерирую в wokflow.
Для этого в раздел env добавляю:
```
      oauth: "${{secrets.YC_OAUTH}}"
      cloud: "${{secrets.YC_CLOUD}}"
      folder: "${{secrets.YC_FOLDER}}"
```
Файл создаю шагом
```
      - name: create locals file
        run: |
          cd yandex-cloud-terraform
          echo 'locals {'' > locals.tf
          echo '  oauth  = "${{ oauth }}"'  >> locals.tf 
          echo '  cloud  = "${{ cloud }}"'  >> locals.tf 
          echo '  folder = "${{ folder }}"'  >> locals.tf 
          echo '}'  >> locals.tf
          cat locals.tf
```
### блок resurce

