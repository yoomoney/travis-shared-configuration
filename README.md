# travis-shared-configuration

Репозиторий для хранения общих конфигураций travis.

Для подключения конфигураций пропишите в файл .travis.yml:
import: yoomoney-gradle-plugins/travis-shared-configuration:<name>@master

Для тестирования возможно указать feature ветку вместо @master.
Подробнее о shared конфигурациях: https://docs.travis-ci.com/user/build-config-imports/#importing-configs-from-private-repositories

Описание конфигураций:
build-and-publish-plugin.yml: создана для сборки, релиза и публикации gradle-plugin. 
Используемые при релизе таски объявляются при подключении https://github.com/yoomoney-gradle-plugins/artifact-release-plugin.