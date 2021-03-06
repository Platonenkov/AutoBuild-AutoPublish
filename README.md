## Как настроить автоматическу публикацию проекта

#### 1. В проекте перейдите на вкладку "Actions"

#### 2. Нажмиет кнопку "Set up this workflow"
![Demo](https://github.com/Platonenkov/AutoBuild-AutoPublish/blob/main/Source/1.jpg)

#### 3. Настройте файл конфигурации или скопируйте из моего [шаблона](https://github.com/Platonenkov/AutoBuild-AutoPublish/blob/main/dotnet.yml)

```C
 # платформа проекта
name: .NET
          # условия выполнения 
on:
  push:                  # при публикацмм в ветку
    branches: [ master ]
  pull_request:          # при пул-реквесте в ветку
    branches: [ master ]
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Setup .NET
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 5.0.x
                                # восстановить зависимости
    - name: Restore dependencies 
      run: dotnet restore      
                                # собрать проект      
    - name: Build               
      run: dotnet build --no-restore
      
                                # провести тестирование      
    - name: Test                
      run: dotnet test --no-build --verbosity normal
      
                                # собрать проект релиза и упаковать в пакет      
    - name: Packing
      run: |                    
          dotnet build OpenXmlEx/OpenXmlEx.csproj -c Release
          dotnet pack OpenXmlEx/OpenXmlEx.csproj -c Release
      
                                # опубликовать пакет в личном репозитории GitHub     
    - name: Push package
      run: dotnet nuget push "**/*.nupkg" --skip-duplicate --source https://nuget.pkg.github.com/{"RepositoryOwener"}/index.json -k ${{secrets.GITHUB_TOKEN}}       
                                # опубликовать пакет в личном репозитории NuGet   
    - name: Publishing
      run: dotnet nuget push "**/*.nupkg" -k ${{ secrets.NuGetApiKey }} --skip-duplicate -s https://api.nuget.org/v3/index.json
```

#### 3.1 Для проектов Windows иправьте секцию
```
jobs:
  build:
    runs-on: windows-latest
    name: Update package

    steps:
      - name: Checkout repository
        uses: actions/checkout@master
      
      - name: Use .NET 5.0.x
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: 5.0.x
```
#### 4. 37-38 строка для публикации пакета на GitHub, 40-41 для публикации на NuGet

#### 4. Обязательно замените {"ProjectName"} - путь к папке проекта который публикуете

#### 5. Обязательно замените {"RepositoryOwener"} - Ваш профиль в GitHub

### Настройка для публикации на Nuget

#### 1. Откройте меню профиля на сайте NuGet и перейдите во вкладку Api Keys
![Demo](https://github.com/Platonenkov/AutoBuild-AutoPublish/blob/main/Source/2.jpg)

#### 2. Нажмите кнопку "Create"
![Demo](https://github.com/Platonenkov/AutoBuild-AutoPublish/blob/main/Source/3.jpg)

#### 3. Настройте токен

![Demo](https://github.com/Platonenkov/AutoBuild-AutoPublish/blob/main/Source/4.jpg)

1) Установите имя токена (любое как хочется"
2) Установите срок действия токена
3) Выберите собственника от чьего имени будете работать
4) Настройте права доступа (у меня настроено как публикация новых пакетов и версий)
5) укажите какие на какие проекты будет действовать ключ или как я поставьте ###"*"
6) Скопируйте сгенерированный Токен

#### 4. Вернитесь на страницу проекта в GitHub, Откройте вкладку "Settings" -> "Secrets" -> "New repository secret" 
![Demo](https://github.com/Platonenkov/AutoBuild-AutoPublish/blob/main/Source/5.jpg)

#### 5. Дайте имя токену, вставьте созданный на Nuget токен в поле и нажмите "Add secrets"
![Demo](https://github.com/Platonenkov/AutoBuild-AutoPublish/blob/main/Source/6.jpg)


### Если все настроено, при публикации в ветку "master" будет происходить публикация проекта

## Как проверить этапы

#### при коммите появится информация о результе

желтый кружок в процессе и галочка когда процесс прошёл успешно
![Demo](https://github.com/Platonenkov/AutoBuild-AutoPublish/blob/main/Source/7.jpg)

#### нажмите на него чтобы увидеть детали
![Demo](https://github.com/Platonenkov/AutoBuild-AutoPublish/blob/main/Source/8.jpg)

#### Осмотрите подробности если были ошибки
![Demo](https://github.com/Platonenkov/AutoBuild-AutoPublish/blob/main/Source/9.jpg)

То есть теперь что бы внести изменения, надо:
1. Создать новую ветвь из master
2. Внести изменения
3. Сделать пулл-реквест в master
4. Сделать ревью и принять пуллреквест
5. Подготовить релиз. Для этого в файле .csptoj изменить версию, добавить описание релиза
6. Создать пулл-реквест из dev в master. В названии пулл-реквеста указать версию и краткое описание. В описании пулл-реквеста добавить описание внесённых изменений
7. Принять пулл-реквест
