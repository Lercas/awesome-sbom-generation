# Генерация SBOM с помощью плагинов CycloneDX

Этот репозиторий предоставляет руководство и примеры для генерации Software Bill of Materials (SBOM) для различных языков программирования и менеджеров пакетов.

## Оглавление

- [Введение](#введение)
- [Установка](#установка)
  - [Python (pip)](#python-pip)
  - [Java-(Maven)](#java-maven)
  - [.NET-(NuGet)](#net-nuget)
  - [Go](#go)
  - [JavaScript (npm)](#javascript-npm)
- [Использование](#использование)
  - [Python (pip)](#python-pip-1)
  - [Java (Maven)](#java-maven-1)
  - [.NET (NuGet)](#net-nuget-1)
  - [Go](#go-1)
  - [JavaScript (npm)](#javascript-npm-1)
- [Интеграция в CI/CD](#интеграция-в-cicd)
  - [GitLab CI/CD](#gitlab-cicd)
    - [GO](#go-2)
    - [JavaScript (npm)](#javascript-npm-2)
    - [.NET (nuget)](#net-nuget)
    - [Python (pip)](#python-pip-2)
    - [Java (maven)](#java-maven-2)

## Введение

CycloneDX - это легковесный стандарт SBOM, предназначенный для использования в контексте безопасности приложений и анализа компонентов цепочки поставок. В этом руководстве показано, как использовать плагины CycloneDX для генерации SBOM в различных программных средах.

## Установка

### Python (pip)

1. Создайте виртуальное окружение в корневой директории вашего проекта:
```bash
python -m venv venv
```

2. Активируйте виртуальное окружение:

3. Установите `cyclonedx-bom` в виртуальное окружение:
```bash
pip install cyclonedx-py
```
Если не хотите заморачиваться, то ставите `cyclonedx-py`в свою окружение, а уже потом запускать сканирование на venv
### Java (Maven)

1. Добавьте плагин CycloneDX Maven в ваш `pom.xml`:
```xml
    <plugin>
        <groupId>org.cyclonedx</groupId>
        <artifactId>cyclonedx-maven-plugin</artifactId>
        <version>2.7.11</version>
        <executions>
            <execution>
                <goals>
                    <goal>makeAggregateBom</goal>
                </goals>
            </execution>
        </executions>
    </plugin>
```

Либо можете установить его отдельно используя:
```bash
mvn org.apache.maven.plugins:maven-dependency-plugin:2.6:get -Dartifact=org.cyclonedx:cyclonedx-maven-plugin:2.7.11
```

### .NET (NuGet)

1. Установите CycloneDX для .NET:
```bash
dotnet tool install --ignore-failed-sources --global CycloneDX -v n
```

### Go

1. Установите `cyclonedx-gomod` с помощью команды `go install`:
```bash
go install github.com/CycloneDX/cyclonedx-gomod/cmd/cyclonedx-gomod@latest
```

2. Убедитесь, что `GOPATH/bin` добавлен в переменную среды `PATH`, чтобы вы могли запускать `cyclonedx-gomod` из любой директории:
```bash
export PATH=$PATH:$(go env GOPATH)/bin
```

### JavaScript (npm)

1. Установите CycloneDX для npm:
```bash
npm install --global @cyclonedx/cyclonedx-npm
```

## Использование

### Python (pip)

#### Основные команды

`cyclonedx-py` поддерживает несколько команд для генерации SBOM. Ниже представлены основные команды и примеры их использования для генерации SBOM в формате JSON для pip.

##### Генерация SBOM для проекта в venv

1. Убедитесь, что виртуальное окружение активировано:

2. Выполните команду для генерации SBOM:
```bash
cyclonedx-py environment --short-PURLs --of json "$VIRTUAL_ENV" -o sbom.json # Либо через ".../some/path/.venv"
```

##### Генерация SBOM для проекта для requirements

```bash
cyclonedx-py requirements  --short-PURLs --of json -o sbom.json .../path/to/requirements.txt
```


### Java (Maven)

1. Сгенерируйте SBOM:
```bash
mvn -f .../path/to/pom.xml org.cyclonedx:cyclonedx-maven-plugin:makeAggregateBom -DoutputFormat=json -DoutputName=result_maven -DoutputDirectory=.../path/to/output
```

### .NET (NuGet)

#### Основные команды

`CycloneDX CLI` поддерживает несколько команд для генерации SBOM. Ниже представлены основные команды и примеры их использования для генерации SBOM в формате JSON.

##### Генерация SBOM для проекта

Эта команда используется для генерации SBOM для проекта .NET. Результат сохраняется в формате JSON.

1. Перейдите в корневую директорию вашего проекта:
```bash
cd /path/to/your/dotnet/project
```

2. Выполните команду для генерации SBOM:
```bash
dotnet CycloneDX /path/to/project -o /output/path -fn sbom.json -j -rs 
```

`-rs` - для использования с одним файлом проекта, он будет рекурсивно сканировать ссылки на проект в предоставленном файле проекта 

### Go

### Основные команды

`cyclonedx-gomod` поддерживает несколько команд для генерации SBOM. Ниже представлены основные команды и примеры их использования для генерации SBOM в формате JSON.

#### Генерация SBOM для приложений

Эта команда используется для генерации SBOM для приложений. Результат сохраняется в формате JSON.

1. Перейдите в корневую директорию вашего проекта:
```bash
cd /path/to/your/go/project
```

2. Выполните команду для генерации SBOM:
```bash
cyclonedx-gomod app -output sbom.json -json -licenses
```

#### Генерация SBOM для бинарных файлов

Эта команда используется для генерации SBOM для бинарных файлов. Результат сохраняется в формате JSON.

1. Перейдите в директорию с бинарными файлами вашего проекта:
```bash
cd /path/to/your/go/project/bin
```

2. Выполните команду для генерации SBOM:
```bash
cyclonedx-gomod bin -output sbom.json -json -licenses
```

#### Генерация SBOM для модулей

Эта команда используется для генерации SBOM для модулей Go, определенных в файле `go.mod`. Результат сохраняется в формате JSON.

1. Перейдите в корневую директорию вашего проекта, где находится файл `go.mod`:
```bash
cd /path/to/your/go/project
```

2. Выполните команду для генерации SBOM:
```bash
cyclonedx-gomod mod -output sbom.json -json -licenses
```

### JavaScript (npm)


`@cyclonedx/cyclonedx-npm` поддерживает несколько команд для генерации SBOM. Ниже представлены основные команды и примеры их использования для генерации SBOM в формате JSON.

#### Генерация SBOM для проекта

Эта команда используется для генерации SBOM для проекта npm. Результат сохраняется в формате JSON.

1. Перейдите в корневую директорию вашего проекта:
```bash
cd /path/to/your/npm/project
```
Либо далее укажите прямой путь к манифесту (package-manifest) package.json

2. Выполните команду для генерации SBOM:
```bash
cyclonedx-npm --output sbom.json --format json --short-PURLs package.json
```

## Интеграция в CI/CD

### GitLab CI/CD
Не забываем заранее всё установить в docker или туда, где ваш runner
#### GO

Добавьте следующий блок в ваш `.gitlab-ci.yml`:

```yaml
stages:
  - sbom

sbom_generation:
  stage: sbom
  script:
    - go install github.com/CycloneDX/cyclonedx-gomod/cmd/cyclonedx-gomod@latest
    - export PATH=$PATH:$(go env GOPATH)/bin
    - cyclonedx-gomod mod -o sbom.json -f json
  artifacts:
    paths:
      - sbom.json
```

#### JavaScript (npm)

Добавьте следующий блок в ваш `.gitlab-ci.yml`:

```yaml
stages:
  - sbom

sbom_generation:
  stage: sbom
  script:
    - npm install -g @cyclonedx/cyclonedx-npm
    - npm config set registry <$REG> # Если у вас есть свой реджистри
    - cyclonedx-npm --output-file sbom.json --ignore-npm-errors
  artifacts:
    paths:
      - sbom.json
```

#### .NET (nuget) 

```yaml
stages:
  - sbom

sbom_generation:
  stage: sbom
  script:
    - dotnet tool install --global CycloneDX
    - export PATH=$PATH:~/.dotnet/tools
    - dotnet CycloneDX /path/to/project -o /output/path -fn sbom.json -j -rs
  artifacts:
    paths:
      - sbom.json
```

#### Python (pip)

```yaml
stages:
  - sbom

sbom_generation:
  stage: sbom
  script:
    - cyclonedx-py environment --short-PURLs --of json "$VIRTUAL_ENV" -o sbom.json
  artifacts:
    paths:
      - sbom.json
```

#### Java (maven)

```yaml
stages:
  - sbom

sbom_generation:
  stage: sbom
  script:
    - mvn -f .../path/to/pom.xml org.cyclonedx:cyclonedx-maven-plugin:makeAggregateBom -DoutputFormat=json -DoutputName=result_maven -DoutputDirectory=.../path/to/output
  artifacts:
    paths:
      - sbom.json
```

# To Do 
- [ ] Добавить С++ 
- [ ] Добавить PHP
- [ ] Добавить Kotlin (Gradle)
- [ ] Ruby, etc...
- [ ] Сделать нормальную разметку
- [ ] Сделать перевод
- [ ] Разбить по папкам