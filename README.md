# Домашнее задание к занятию 1 «Введение в Ansible»

## Подготовка к выполнению

1. Установите Ansible версии 2.10 или выше.
2. Создайте свой публичный репозиторий на GitHub с произвольным именем.
3. Скачайте [Playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.

```bash
ansible --version
```

![image](https://github.com/user-attachments/assets/2dd4afd9-c066-4287-a345-d5199a9c19e9)
```bash
git remote -v
```
![image](https://github.com/user-attachments/assets/2211f062-8481-4b28-b569-ea7fb5e42ba7)


## Основная часть

1. Попробуйте запустить playbook на окружении из `test.yml`, зафиксируйте значение, которое имеет факт `some_fact` для указанного хоста при выполнении playbook.

   
   ```bash
   cd playbook/
   ansible-playbook -i inventory/test.yml site.yml
   ```
![image](https://github.com/user-attachments/assets/41a58c99-eeaa-4774-8760-76728a1dcb9f)


2. Найдите файл с переменными (group_vars), в котором задаётся найденное в первом пункте значение, и поменяйте его на `all default fact`.

![image](https://github.com/user-attachments/assets/17cee244-0863-42e0-a170-c219c2a0f41a)

3. Воспользуйтесь подготовленным (используется `docker`) или создайте собственное окружение для проведения дальнейших испытаний.

```bash
docker run -d --name ubuntu ubuntu tail -f /dev/null
docker run -d --name centos8 centos:8 tail -f /dev/null
```

```bash
ansible-playbook -i playbook/inventory/prod.yml playbook/site.yml
```


4. Проведите запуск playbook на окружении из `prod.yml`. Зафиксируйте полученные значения `some_fact` для каждого из `managed host`.

![image](https://github.com/user-attachments/assets/e58ce12b-d9e9-43f4-9e96-5d99977c145d)

5. Добавьте факты в `group_vars` каждой из групп хостов так, чтобы для `some_fact` получились значения: для `deb` — `deb default fact`, для `el` — `el default fact`.
6. Повторите запуск playbook на окружении `prod.yml`. Убедитесь, что выдаются корректные значения для всех хостов.

![image](https://github.com/user-attachments/assets/4af7c571-0b2b-4462-8dad-f5def4ff1253)

7. При помощи `ansible-vault` зашифруйте факты в `group_vars/deb` и `group_vars/el` с паролем `netology`.

```bash
ansible-vault encrypt /root/08-ansible-01-base-hw/playbook/group_vars/deb/examp.yml --vault-password-file <(echo netology)
ansible-vault encrypt /root/08-ansible-01-base-hw/playbook/group_vars/el/examp.yml --vault-password-file <(echo netology)
```
![image](https://github.com/user-attachments/assets/23f161da-f38a-4216-ba33-5151302c9d7f)

8. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь в работоспособности.

```bash
ansible-playbook -i playbook/inventory/prod.yml playbook/site.yml  --ask-vault-pass
```
![image](https://github.com/user-attachments/assets/091639ab-0a8a-4759-a28a-e94ad645ed72)

9. Посмотрите при помощи `ansible-doc` список плагинов для подключения. Выберите подходящий для работы на `control node`.

```bash
ansible-doc -t connection local
```
![image](https://github.com/user-attachments/assets/0a6243bf-eec5-435e-a8b2-e1b832600393)

10. В `prod.yml` добавьте новую группу хостов с именем  `local`, в ней разместите localhost с необходимым типом подключения.

#prod.yml
```yaml
---
el:
  hosts:
    centos8:
      ansible_connection: docker
deb:
  hosts:
    ubuntu:
      ansible_connection: docker
local:
  hosts:
    localhost:
      ansible_connection: local
```

11. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь, что факты `some_fact` для каждого из хостов определены из верных `group_vars`.

![image](https://github.com/user-attachments/assets/fa91386d-0dd5-4331-b3ff-ec0bfb18ca73)

12. Заполните `README.md` ответами на вопросы. Сделайте `git push` в ветку `master`. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым `playbook` и заполненным `README.md`.
13. Предоставьте скриншоты результатов запуска команд.

## Необязательная часть

1. При помощи `ansible-vault` расшифруйте все зашифрованные файлы с переменными.

```bash
ansible-vault decrypt /root/08-ansible-01-base-hw/playbook/group_vars/deb/examp.yml --vault-password-file <(echo netology)
ansible-vault decrypt /root/08-ansible-01-base-hw/playbook/group_vars/el/examp.yml --vault-password-file <(echo netology)
```
![image](https://github.com/user-attachments/assets/42ce9476-fba3-4fa7-bd56-aa163fe524cc)

2. Зашифруйте отдельное значение `PaSSw0rd` для переменной `some_fact` паролем `netology`. Добавьте полученное значение в `group_vars/all/exmp.yml`.

```bash
ansible-vault encrypt_string 'PaSSw0rd' --name 'some_fact' --vault-password-file <(echo netology)
```
![image](https://github.com/user-attachments/assets/c5621d45-51de-4101-93b0-0d6b8866d128)

3. Запустите `playbook`, убедитесь, что для нужных хостов применился новый `fact`.

```bash
ansible-playbook -i playbook/inventory/prod.yml playbook/site.yml  --ask-vault-pass
```
![image](https://github.com/user-attachments/assets/790fbce2-27bc-41b5-9432-852b003afb19)

4. Добавьте новую группу хостов `fedora`, самостоятельно придумайте для неё переменную. В качестве образа можно использовать [этот вариант](https://hub.docker.com/r/pycontribs/fedora).

```bash
ansible-playbook -i playbook/inventory/prod.yml playbook/site.yml  --ask-vault-pass
```
![image](https://github.com/user-attachments/assets/77f574a1-a7b3-4708-b2c1-f6dd8b7a347e)

5. Напишите скрипт на bash: автоматизируйте поднятие необходимых контейнеров, запуск ansible-playbook и остановку контейнеров.
6. Все изменения должны быть зафиксированы и отправлены в ваш личный репозиторий.

---

### Как оформить решение задания

Выполненное домашнее задание пришлите в виде ссылки на .md-файл в вашем репозитории.

---
