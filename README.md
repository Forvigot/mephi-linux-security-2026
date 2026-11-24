# mephi-linux-security-2026

Уникальный номер: **M265323**

ОС: РЕД ОС 8.0.3 Education (контейнер). Сохранность настроек проверена перезапуском.

| Раздел | Что сделано | Как | Результат |
|---|---|---|---|
| 1.1 | Пользователь `user1`, UID 1234, группа `students`, смена пароля раз в 90 дней | `useradd -u 1234 -G students`, `chage -M 90`, `chmod 700 /home/user1` | `passwd`, `shadow`, `group`, `stat.out` |
| 2.1 | Поиск файлов с set-UID | `find / -type f -perm -4000 -ls` | `suid_files.out` |
| 2.2 | Процессы с EUID 0 и обычным RUID | `passwd` от user1 во время `ps -eo pid,ppid,ruid,euid,...` с фильтром `awk` | `priv_procs.out` |
| 3 | Чтение `/etc/shadow` через set-UID | копия `cat`: `/home/user1/mycat`, `root:students`, права 4750 | `stat.out` |
| 4 | Смена владельца файла через привилегию | копия `chown`: `/home/user1/mychown`, `setcap cap_chown=ep` | `stat.out`, `getcap.out` |
| 5 | `user1` может менять системное время | правило в `/etc/sudoers`: `date -s`, `timedatectl set-time` | `sudoers` |
| 6 | Артефакты | `history`, `stat`, `getcap`, скриншот | `history.out`, `mephi-screenshot.png` |

До настройки те же операции от `user1` завершаются отказом: `cat /etc/shadow` — Permission denied, `chown root` — Operation not permitted.

В разделах 3 и 4 группа `students` и права без доступа для остальных ограничивают круг тех, кто может запустить утилиты. Владелец меняется до `setcap`, так как `chown` сбрасывает привилегии файла.
