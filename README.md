# DDNS over CloudFlare
Реализация сисетмы динамических доменных имён через API CloudFlare.  
DDNS получает ваш ip адрес через [www.cloudflare.com/cdn-cgi/trace](https://www.cloudflare.com/cdn-cgi/trace)

## Начало работы
Запустите собранный .jar файл через команду `java -jar file.jar`. При первом запуске код сгенерирует 
файл `config.json`, имеющий следующее содержание:
```json
{
  "zone_id": "<zone_id>",
  "hostname": "example.com",
  "period": 30,
  "proxied": true,
  "record_type": "A",
  "token": "<token>"
}
``` 
`zone_id` – id зоны CloudFlare  
`hostname` – Имя DNS записи, которая будет создана и обновляться  
`period` – Период в секундах, с которым будет проверяться текущий ip адрес  
`proxied` – Определяет, будут ли реквесты на хостнейм проксироваться через CloudFlare  
`record_type` – Тип DNS записи
`token` – Bearer [токен CloudFlare](https://dash.cloudflare.com/profile/api-tokens). Должен иметь права на чтение и изменение DNS записей  

## Принцип работы
При запуске код проверяет наличие DNS записи в CloudFlare по переданным в `config.json` параметрам.
Если запись не найдена, то создает новую с текущим ip адресом. После этого входит в бесконечный цикл, где каждые `period` секунд
проверяет текущий ip адрес, и если он изменился – обновляет его в CloudFlare.

## Важные замечания
**Внимание! Не рассматривайте этот проект всерьёз и используйте в продакшене на свой страх и риск!**  
Распространение DNS записей может занимать значительный промежуток времени. Так как обновление записей на опережение невозможно,
будьте готовы к отставанию актуальности данных DNS на ~10 минут.  
Для корректной работы, ваш провайдер интернет услуг (ISP) должен поддерживать NAT через динамические ip адреса, в противном случае
динамический DNS не будет иметь смысла.  
На данный момент проект не умет отличать ipv4 от ipv6, поэтому внезапное его изменение на другой тип может привести к сбою в работе.

---
by AndcoolSystems, June 21, 2024.