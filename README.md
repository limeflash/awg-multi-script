# AWG Multi Script

Универсальный менеджер **AmneziaWG** для быстрого развёртывания VPN на VPS одной командой.

## Быстрый старт

```bash
curl -s https://raw.githubusercontent.com/pumbaX/awg-multi-script/main/awg.sh -o /tmp/awg.sh
sudo bash /tmp/awg.sh
```

Описание
AWG Multi Script — интерактивный bash-менеджер для установки, настройки и управления AmneziaWG VPN-сервером. Поддерживает все версии протокола AmneziaWG от 1.0 до 2.0, автоматически генерирует обфускационные параметры, включает встроенный генератор мимикрии на основе реальных доменов и позволяет управлять клиентами без ручного редактирования конфигов.

Возможности
Один скрипт — установка, генерация, управление клиентами в едином интерфейсе

Все версии протокола — WireGuard, AWG 1.0, AWG 1.5, AWG 2.0 на выбор

Автогенерация параметров обфускации — Jc (3-7), Jmin (64), Jmax (576-1024), S1-S4, H1-H4 (диапазоны для AWG 2.0)

Генератор мимикрии — 5 профилей на основе AmneziaWG Architect:

QUIC Initial (HTTP/3) — наиболее надёжный в 2026

QUIC 0-RTT (Early Data) — быстрый старт

TLS 1.3 Client Hello — HTTPS (наибольшая совместимость)

DTLS 1.3 (WebRTC/STUN) — видеозвонки

SIP (VoIP) — телефонные звонки

I1 имитация трафика — получение реального QUIC-пакета через API для выбранного домена

Управление клиентами — добавление, просмотр статистики (↑ выгрузка / ↓ загрузка), QR-коды

Автоопределение версии — при добавлении клиента параметры читаются из конфига сервера

NAT + FORWARD — автоматическая настройка iptables с сохранением через hook

UFW — автоматическое открытие портов с защитой SSH

Статистика — отображение трафика клиентов в МБ/ГБ

Backup — автоматический бэкап конфига перед пересозданием сервера

Проверка доменов — встроенный ping-тест доступности доменов из пулов мимикрии

Полное удаление — очистка пакетов, конфигов, правил firewall одной командой

Требования
Ubuntu 22.04 / 24.04

KVM-виртуализация (не OpenVZ/LXC)

Публичный IPv4

Root-доступ

Поддерживаемые версии протокола
Версия	Jc/Jmin/Jmax	S1/S2	S3/S4	H1-H4	I1-I5
WireGuard	✗	✗	✗	✗	✗
AWG 1.0	✅ (Jc ≥4)	✅	✗	одиночные	✗
AWG 1.5	✅	✅	✗	одиночные	✅ (только клиент)
AWG 2.0	✅	✅	✅	диапазоны	✅ (сервер+клиент)
Меню
text
╔══════════════════════════════════════════════╗
║        AmneziaWG Manager v4.0                ║
║     С генератором мимикрии (QUIC/TLS/DTLS)   ║
╚══════════════════════════════════════════════╝
  IP сервера : 1.2.3.4
  Порт       : 443
  Интерфейс  : активен
  Клиентов   : 3

  1) Установка зависимостей и AmneziaWG
  2) Создать сервер + первый клиент (с мимикрией)
  3) Добавить клиента
  4) Показать клиентов
  5) Показать QR клиента
  6) Перезапустить awg0
  7) Удалить всё
  8) Проверить домены из пулов (ping)
  0) Выход
Генератор мимикрии — выбор профиля
При создании сервера или добавлении клиента доступны профили:

№	Профиль	Назначение
1	QUIC Initial	HTTP/3, CDN — наиболее надёжный в 2026
2	QUIC 0-RTT	Early Data, быстрый старт
3	TLS 1.3 Client Hello	HTTPS (наибольшая совместимость)
4	DTLS 1.3	WebRTC/STUN (видеозвонки)
5	SIP	VoIP (телефонные звонки)
6	Случайный домен	Из любого пула
7	Ручной ввод	Любой домен через API
8	Без имитации	Только обфускация
После выбора профиля скрипт автоматически:

Выбирает случайный домен из проверенного пула

Запрашивает свежий I1 через API (реальный QUIC/TLS/DTLS пакет)

Встраивает его в конфиг

I1 — получение реального пакета
Скрипт использует API https://junk.web2core.workers.dev/signature?domain=... для получения актуального QUIC-пакета. Это позволяет имитировать трафик популярных сервисов (Yandex, VK, Microsoft, GitHub и др.).

Доменные пулы (более 50 проверенных хостов)
QUIC Initial (HTTP/3):

text
yandex.net, yastatic.net, vk.com, mycdn.me, mail.ru, ozon.ru,
wildberries.ru, wbstatic.net, sber.ru, tbank.ru, gosuslugi.ru,
gcore.com, fastly.net, cloudfront.net, microsoft.com, icloud.com,
github.com, cdn.jsdelivr.net, wikipedia.org, dropbox.com,
steamstatic.com, spotify.com, akamaiedge.net, msedge.net
TLS 1.3 Client Hello (HTTPS):

text
yandex.ru, vk.com, mail.ru, ozon.ru, wildberries.ru, sberbank.ru,
tbank.ru, gosuslugi.ru, kaspersky.ru, github.com, gitlab.com,
stackoverflow.com, microsoft.com, apple.com, amazon.com,
cloudflare.com, jetbrains.com, docker.com
DTLS (WebRTC/STUN):

text
stun.yandex.net, stun.vk.com, stun.mail.ru, stun.sber.ru,
stun.stunprotocol.org, meet.jit.si, stun.services.mozilla.com,
stun.zoiper.com, stun.counterpath.com, stun.sipgate.net
SIP (VoIP):

text
sip.beeline.ru, sip.mts.ru, sip.megafon.ru, sip.rostelecom.ru,
sip.yandex.ru, sip.vk.com, sip.mail.ru, sip.sipnet.ru,
sip.zadarma.com, sip.iptel.org, sip.linphone.org, sip.3cx.com
Импорт на клиенте
Используй AmneziaVPN:

Сканирование QR-кода из терминала

Импорт файла .conf через «Добавить туннель → Из файла»

Скачать: amnezia.org/downloads

Файлы
text
/etc/amnezia/amneziawg/awg0.conf   — серверный конфиг
/root/client1_awg2.conf            — первый клиент
/root/<name>_awg2.conf             — дополнительные клиенты
/var/log/awg-manager.log           — лог операций
Примечания
Для профилей QUIC/TLS рекомендуется использовать порт 443 для максимальной маскировки

MTU 1380 даёт лучшую совместимость с мобильными сетями

Jc = 3-7 обеспечивает оптимальный баланс между обфускацией и скоростью

При добавлении клиента можно скопировать I1 с сервера или сгенерировать новый

Скрипт автоматически проверяет формат I1 и исправляет распространённые ошибки

Лицензия
MIT Licensе
