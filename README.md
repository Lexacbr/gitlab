# Домашнее задание к занятию "`Gitlab`" - `Савельев Алексей SYS-25`

---

### Задание 1
Что нужно сделать:
1. Установите себе jenkins по инструкции из лекции или любым другим способом из официальной документации. Использовать Docker в этом задании нежелательно.
2. Установите на машину с jenkins golang.
3. Используя свой аккаунт на GitHub, сделайте себе форк репозитория. В этом же репозитории находится дополнительный материал для выполнения ДЗ.
4. Создайте в jenkins Freestyle Project, подключите получившийся репозиторий к нему и произведите запуск тестов и сборку проекта `go test` . и `docker build` ..
В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

Что я делал:

1. Устанавливаю себе jenkins по инструкции из лекции.

![Кадр из презентации](https://github.com/Lexacbr/gitlab/blob/main/screenshots/jenkins.png)

2. Устанавливаю Go_lang по инструкции из Интернета
```bash
sudo snap install go --classic
```
![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/install_go.png)

  Добавляю в конец файла `/.profile` вот такую строку:
```bash
export PATH=$PATH:/usr/local/go/bin
```
  После этого проверяю, могу ли вы выполнять go_команды, запустив go version:
```bash
go version
```
![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/go_version.png)

  Теперь, когда Go установлен и пути для сервера заданы, надо попробовать создать `Hello, World!` приложение, чтобы убедиться, что Go работает. Сначала создаю новый каталог для рабочего пространства Go, в котором Go будет собирать свои файлы:
```bash
mkdir hello
```
  Перехожу в папку: 
```bash
cd hello
```
![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/mkdir.png)

  При импорте пакетов придется управлять зависимостями через собственный модуль кода. Делаю это, создав `go.mod` файл с помощью `go mod init` команды:
```bash
go mod init example/hello
```
   Затем создал `Hello, World!` файл Go. Я делал это в `nano` текстовом редакторе:
```bash
nano hello.go
```
   Добавил в файл следующий текст `hello.go`:
```golang
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```
Затем сохранил и закрыл файл, нажав CTRL+X, затем Y, а затем ENTER.

  Проверяю свой код, чтобы убедиться, что он печатает `Hello, World!` приветствие:
```bash
go run .
```
Должно быть что-то вроде этого:
```bash
Output
Hello, World!
```
![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/go_run.png)

   Пробую запустить `go build .` Запускать нужно из того же каталога, где хранится `hello.go` файл:
```bash
go build .
```
![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/go_build.png)

3. Сделал `fork`скачал домашнее задание с ссылкой к себе в репозиторий.

4. Всё делал по лекци, но проблемы всё же были:
   Начал с того, что настроил в Jenkins, как будет собираться мой проект. По-скольку Дженкинс был на ВМ через ВиртуалБокс,то я его настраивал вручную, а не через  Вагрант-файл, и Гоулэнг установался через `snap`. Из-за этого команды в Дженкинс немного изменились, а именно запуск бинарного файла "Гоу" был таким:
 ![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/snap.png) 
  
  Потом установил Нексус в докер-контенере,с пробросом 2-ух портов (8081 и 8082), который я запустил на хостовой машине, и настроил резолв из ВМ VirtualBox для соединения с Нексусом на хостовой машине.

  ![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/nex_inst.png)

  ![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/etc_hosts.png)

  ![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/ps_a.png)
  
  ![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/ping.png)

  После того как я убедился, что всё работает, я переключился на создание самого проекта в Дженкинс. Сразу скажу, что сборок было 13 и не сразу настроил всё как надо. Сначала я установил неправильную команду на тест

  ![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/err_go.png)

  Исправил путь до бинарного файла в проекте. Потом была проблемма с Докером

  ![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/err_dock.png)

  Тут я запутался в машинах, что на какой установленно, и понял потом, что на ВМ вообще не установлен Докер. Установил. Но проблемма не исчезла , т.к. устанавливал "руками" и не обратил внимание на Вагрант-файл, где было нужно произвести некоторые, важные настройки, а именно: нужно было создать на машине с Дженкинсом `json` файл с настройкой "insecure". Потом нужно рестaртовать докер сервис

![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/sec_dock.png)  
 
  ```bash
  sudo systemctl restatr docker 
  ```
  Потом я добавил Дженкинс в группу пользователей с Докером 
  ```bash
  sudo usermod -aG docker jenkins
  ```
  Потом поменял права на папку `/var/run/docker.sock`
  ```bash
  sudo chmod 666 /var/run/docker.sock
  ```
  После этого задача выполнилась без каких-либо проблем.

  ![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/jenk_b_ok.png)

  ![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/jenk_ok.png)

  ![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/nex_ok.png)

---

### Задание 2
Что нужно сделать:

1. Создайте новый проект pipeline.
2. Перепишите сборку из задания 1 на declarative в виде кода.
3. В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

Что я сделал:

1. Открыл в Дженкинс новый проект `Pipeline`

![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/pipline.png)

2. Вставил скрипт в диалоговое окно 

![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/past_script.png)

3. Получил вывод об удачной сборке

![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/ok_pipe.png)

4. Проверил в Нексусе наличие проекта в базе

![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/ok_nex.png)

---

### Задание 3
Что нужно сделать:

1. Установите на машину Nexus.
2. Создайте raw-hosted репозиторий.
3. Измените pipeline так, чтобы вместо Docker-образа собирался бинарный go-файл. Команду можно скопировать из Dockerfile.
4. Загрузите файл в репозиторий с помощью jenkins.

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.


1. Установил Nexus

![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/welc_nex.png)

2. Создал репозиторий

![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/nex_repo.png)

3. Изменил pipeline с помощью Dockerfile

![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/pipe_change.png)

4. Загрузил файл в репозиторий с помощью Jenkins.

![Скриншот](https://github.com/Lexacbr/gitlab/blob/main/screenshots/nex_ok_3.png)



