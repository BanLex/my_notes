# Команды Terraform
[Назад](https://github.com/BanLex/my_notes/blob/main/terraform/readme.md)

`terraform fmt` - это команда приводит ваши конфигурационные файлы к такому формату как это видит HashiCorp(по типу pep8 для python).
Если запускаете на github, то команда бесполезна. Рабочие процессы github не сохраняют изменённые файлы в репозиторий.

`terraform init` - обязятельная команда при выполнении изменений инфраструктуры. Имеет смысл её использовать в таком виде на локальном компьютере. На github удобнее использовать её с опцией `-backend-config` подключающей backend.
`terraform init -backend-config=backend.tfbackend`
backend.tfbackend - имя файла с описанием подключения к backend.

`terraform validate` - проверяет синтаксис.

`terraform plan` - проверяет, что из описанного в конфигурации можно развернуть.

`terraform apply` - разворачивает инфраструктуру. При этом он сперва спросит делать или нет, что бы он не спрашивал можно её запускать совместно с опцией `--auto-approve`.
`terraform apply --auto-approve`

`terraform destroy` - удаление инфрастуктуры. При этом он сперва спросит делать или нет, что бы он не спрашивал можно её запускать совместно с опцией `--auto-approve`.
`terraform destroy --auto-approve`
