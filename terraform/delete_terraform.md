# Удаление инфраструктуры
[Назад](https://github.com/BanLex/my_notes/blob/main/terraform/readme.md)

**Следующая посследовательность команд на github**:
```
terraform init -backend-config=backend.tfbackend
terraform destroy  --auto-approve
```
[Пример](https://github.com/BanLex/example_webapp/blob/main/.github/workflows/Delete%20infrastructure%20with%20Terraform.yml)

**Следующая посследовательность команд для запуска на сервере(рабочей станции)**:
```
terraform init -backend-config=backend.tfbackend
terraform destroy  --auto-approve
```
Опции ` -backend-config=backend.tfbackend` и ` --auto-approve` в данном случае не обязательны.
Команда `terraform init` так же не обязательна.
