Настройка Pre-Commit для Автоматической Проверки PHP-Кода

Для поддержания качества кода и соблюдения стандартов в проекте можно настроить хуки pre-commit. Они автоматически проверяют и исправляют код перед каждым коммитом. Вот как это настроить для phpcs, phpstan и PHP CS Fixer, используя Docker и конфигурацию pre-commit.

Если pre-commit еще не установлен, установите его командой:

```bash
pip install pre-commit
или
brew install pre-commit
или
sudo apt install pre-commit
```
Создайте в корне проекта директорию .pre-commit(лучше что бы исключить его их гита, локально или глобально для всего гита на компьютере). Склонировать туда проект.
Если вам необходимо прогонять определеную папку проекта, то это можно изменить вот в этих местах файла
files: ^(app|src)/.*\.php

-   PHP CS Fixer: Автоматически исправляет стиль кода перед проверкой стандартов.
-   PHP CodeSniffer: Проверяет код на соответствие стандарту PSR-12 после исправлений.
-   PHPStan: Выполняет статический анализ, используя базовое состояние (baseline), чтобы игнорировать ранее зафиксированные ошибки.
-   PHP Lint: Проверка синтаксиса для предотвращения грубых ошибок.
-  Psalm: Выполняет статический анализ кода.
Оставить можно любой этап или отредактировать его.
Если вам на какой то момент нужно выключить все шаги, можно закоментировать всё и оставить такое
```yaml
repos:  
  - repo: local  
    hooks:
		- id: Hooks start  
		  name: Hooks start  
		  entry: bash -c 'echo Hooks start"'  
		  language: system
```

Установите хуки командой:
```bash
pre-commit install
```
Так как мы создали отдельную директорию под наш pre-commit,  необходимо открыть файл .git/hooks/pre-commit, необходимо поправить подключение файл  `.pre-commit-config.yaml`

Теперь, перед каждым коммитом, pre-commit автоматически выполнит проверку вашего PHP-кода. Это помогает поддерживать чистоту и соответствие стандартам вашего проекта!

### PhpStan
Для phpstan можно воспользоваться следующем базовым файлом конфигурации.

Немного пояснений. Если вы будете использовать baseline в своем проекте, первый запуск необходимо выполнить раскоментить соответствующую строку и закоментить строку без генерации этого файла.

Файл basleline будет полезным если у вас уже есть много написанного кода и вы поставили phpstan в уже существующий проект. 
Данный файл зафиксирует все имеющиеся ошибки в файл и будет прокускать их в дальнейшем.
Так же необходимо раскоменнтировать соответствующее подключение этого файл в конфигурации.
**phpstan.neon**
```neon
includes: 
	# Раскомментировать если используется
    #- phpstan-baseline.neon  
parameters:  
    level: 6  
  
    paths:  
       - src  
       - tests  
    excludePaths:  
    reportUnmatchedIgnoredErrors: false  
	ignoreErrors:
```
### Psalm
Для psalm вы можете использовать следующий базовый конфиг
**psalm.xml**
```xml
<?xml version="1.0"?>  
<psalm  
        errorLevel="2"  
        findUnusedVariablesAndParams="true"  
        resolveFromConfigFile="true"  
        useDocblockPropertyTypes="true"  
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
        xmlns="https://getpsalm.org/schema/config"  
        xsi:schemaLocation="https://getpsalm.org/schema/config vendor/vimeo/psalm/config.xsd"  
>  
    <projectFiles>
		<directory name="../app" />  
        <ignoreFiles>
	        <directory name="../vendor" />  
        </ignoreFiles>
    </projectFiles>
    <forbiddenFunctions>
	    <function name="dd"/>  
        <function name="dump"/>  
        <function name="eval"/>  
        <function name="print_r"/>  
    </forbiddenFunctions></psalm>
```

### Phpcs
Для phpcs вы можете использовать следующий базовый конфиг
**.phpcs.xml**
```xml
<?xml version="1.0"?>
<ruleset name="MyProject">
    <rule ref="PSR12"/>
    <arg name="encoding" value="utf-8"/>
    <arg name="tab-width" value="4"/>

    <rule ref= "Generic.Files.LineLength">
        <properties>
            <property name="lineLimit" value="800"/>
            <property name="absoluteLineLimit" value="1200"/>
        </properties>
    </rule>
</ruleset>
```
