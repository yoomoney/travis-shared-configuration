# travis-shared-configuration

Репозиторий для хранения общих конфигураций travis.  
  
Для подключения конфигураций пропишите в файл .travis.yml:  
import: yoomoney/travis-shared-configuration:<name>@master  
  
Для тестирования возможно указать feature ветку вместо @master.  
Подробнее о shared конфигурациях: https://docs.travis-ci.com/user/build-config-imports/#importing-configs-from-private-repositories  


## Описание конфигураций:
### build-and-publish-plugin.yml
Конфигурация создана для сборки, релиза и публикации gradle-plugin.  
Конфигурация объявляет stage для сборки проекта для всех веток, а также stage для выпуска релиза для ветки master.  
Используемые при релизе таски объявляются при подключении https://github.com/yoomoney/artifact-release-plugin.  

Для fork проектов сборка осуществляется без публикации артефакта в репозиторий. Переменные окружения для сборки fork не нужны. 

Для того чтобы не запускать сборку для коммитов от релизного бота введено условие запуска: сообщение коммита не должно содержать [Gradle Release Plugin].  
Если необходимо запустить сборку в обход данного ограничения, это можно сделать следующим образом:  
1. В travis, в нужном репозитории, перейти в ```More options``` -> ```Trigger build```  
2. В поле ```CUSTOM COMMIT MESSAGE``` ввести любое сообщение, не содержащее [Gradle Release Plugin].

Для использования конфигурации необходимо дополнительно настроить travis для проекта по ссылке https://travis-ci.com/github/<account>/<repo>/settings:  
  
1. Генерируем пару ssh ключей для git пользователя, от имени которого будет выпускаться релиз. Публичную часть ключа необходимо
   загрузить в gitHub.  
   Приватную часть ключа необходимо зашифровать:  
   ```openssl base64 -A -in git_key```  
   Полученную строку добавить в переменную GIT_KEY.

1. Прописать Environment Variables. Для возможности прописывать переменные, нужно быть членом организации (достаточно прав member).  
   Переменные не добавлены в файл конфигурации, т.к. при таком варианте использования сейчас 
   невозможно задавать переменные для конкретных веток, но в целях безопасности рекомендуется задавать их только для master ветки.  
   Переменные прописываются в блоке "Environment Variables", при добавлении переменной не забыть указать ветку master в блоке BRANCH.  
   Список необходимых переменных:  
   * Для загрузки артефакта в bintray.  
      Обязательные переменные, релиз будет выпущен, но артефакт в bintray не загрузится:  
      ```BINTRAY_API_KEY```  
      ```BINTRAY_PASSPHRASE```  
      ```BINTRAY_USER```

   * Для работы с git.  
      Применяется при выпуске релиза, обязательные переменные, без них релиз выпущен не будет:  
     ```GIT_KEY_PASSPHRASE```  
     ```GIT_KEY```

   * Для работы с github.  
      Применяется при выпуске релиза, для добавления ссылки на pull-request в changelog, необязательная переменная,
      релиз выпустится успешно:  
      ```GITHUB_TOKEN```

   * Для публикации gradle-plugin на https://plugins.gradle.org.  
      Без переменных релиз будет выпущен, но плагин не будет загружен:  
      ```GRADLE_PUBLISH_KEY```  
      ```GRADLE_PUBLISH_SECRET```
     
   * Для подписи артефакта. Подпись необходима для публикации артефакта в https://oss.sonatype.org (MavenCentral).
      Без переменной релиз проекта, использующий публикацию в MavenCentral, выпущен не будет:  
      ```PUBLISH_GPG_KEY```  - в переменной необходимо задать вывод команды openssl base64 -A -in <private_gpg_key>
