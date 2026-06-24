# mihomo-rules

Кастомный список доменов для селективного VPN-роутинга через [mihomo](https://github.com/MetaCubeX/mihomo) (Clash.Meta).

**`custom.txt`** — домены, которые гео-блокируют российские IP (или DPI-троттлятся)
и которых **нет** в публичных авто-обновляемых списках:
[no-russia-hosts](https://github.com/dartraiden/no-russia-hosts),
[antifilter](https://antifilter.download/),
[itdoginfo/allow-domains](https://github.com/itdoginfo/allow-domains),
[RussiaFancyLists](https://github.com/Noktomezo/RussiaFancyLists),
[Re-filter-lists](https://github.com/1andrevich/Re-filter-lists).
В основном — GitHub-инфраструктура, ИИ-сервисы и точечно полезное.

## Формат

`+.домен` — суффикс-матч (ловит и поддомены). Формат mihomo `behavior: domain`.
Суффикс важен: гео-блокеры на shared-CDN утекают поддоменами, если в списке только apex.

## Подключение в mihomo

```yaml
rule-providers:
  custom:
    type: http
    behavior: domain
    format: text
    proxy: PROXY        # github-raw из РФ троттлится — тянем через ноду
    url: "https://raw.githubusercontent.com/gentslava/mihomo-rules/main/custom.txt"
    path: ./ruleset/custom.txt
    interval: 86400
```

В правилах: `- RULE-SET,custom,PROXY`, и в `dns.fake-ip-filter`: `- "rule-set:custom"`.

## Как добавить домен

Допиши строку `+.новый-домен.com` и закоммить — mihomo подтянет по `interval`
(или дёрни refresh: `PUT /providers/rules/custom`).

## Вклад в сообщество

Подтверждённые гео-блокеры, релевантные широкой аудитории, отправляются апстрим в
[no-russia-hosts](https://github.com/dartraiden/no-russia-hosts) (например
[PR #51](https://github.com/dartraiden/no-russia-hosts/pull/51)). После merge
они уезжают в авто-список и убираются отсюда — чтобы не держать дубли.
