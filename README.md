# travis-shared-configuration

Репозиторий для хранения общих конфигураций travis.

Для подключения конфигураций пропишите в файл .travis.yml:
import: yoomoney-gradle-plugins/travis-shared-configuration:<name>@main

Для тестирования возможно указать feature ветку вместо @main.
Подробнее о shared конфигурациях: https://docs.travis-ci.com/user/build-config-imports/#importing-configs-from-private-repositories

## Описание конфигураций:
### build-and-publish-plugin.yml
Конфигурация создана для сборки, релиза и публикации gradle-plugin. 
Конфигурация объявляет stage для сборки проекта для всех веток, а также stage для выпуска релиза для ветки master.
Используемые при релизе таски объявляются при подключении https://github.com/yoomoney-gradle-plugins/artifact-release-plugin.
Для использования конфигурации необходимо дополнительно настроить travis для проекта по ссылке 
https://travis-ci.com/github/<account>/<repo>/settings:

1) Генерируем пару ssh ключей для git пользователя, от имени которого будет выпускаться релиз. Публичную часть ключа необходимо 
   загрузить в gitHub. 
   Приватную часть ключа необходимо зашифровать:
   openssl aes-256-cbc -k GIT_PRIVATE_KEY_PASS -in git_key -out git_key.enc
   
   git_key.enc добавляем в репозиторий. Важно: git_key не добавляем в репозиторий и обозначем в .gitignore.

2) Прописать Environment Variables. Переменные не добавлены в файл конфигурации, т.к. при таком варианте использования сейчас 
   невозможно задавать переменные для конкретных веток, но в целях безопасности рекомендуется задавать их только для master ветки.
   Список необходимых переменных:
   а) Для загрузки артефакта в bintray (выпуск релиза, обязательная переменная):
      BINTRAY_API_KEY
      BINTRAY_PASSPHRASE
      BINTRAY_USER
   b) Для работы с git (применяется при выпуске релиза, обязательная переменная):
      GIT_KEY_PASSPHRASE
      GIT_PRIVATE_KEY_PASS
   c) Для работы с github (применяется при выпуске релиза, добавление ссылку на pull-request в changelog, необзятельная переменная):
      GITHUB_TOKEN
   d) Для публикации gradle-plugin на https://plugins.gradle.org:
      GRADLE_PUBLISH_KEY
      GRADLE_PUBLISH_SECRET
   
3) Добавить ssh key в разделе "SSH Key". Данный ключ будет использоваться при checkout репозитория. Важно: пользователь, которому 
   принадлежит ключ должен совпадать с пользователем, ключ которого добавляется в репозиторий.