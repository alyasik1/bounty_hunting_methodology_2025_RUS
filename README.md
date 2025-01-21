<h1 align="center">Bug Bounty Hunting Методология RUS 2025</h1>


<div align="left">
  <h2>Добро пожаловать в <strong>Методологию Bug Bounty 2025 года</strong>!</h2>
  <p>Эта методология является базовым руководством, которое поможет вам начать путь в программе bug bounty. В ней изложены основные шаги для эффективного анализа вашей цели, но настоящая сложность заключается в выявлении уязвимостей с высоким импактом, используя собственные навыки и креативность. Методология будет регулярно обновляться по мере появления новых интересных техник для улучшения процесса тестирования.</p>
  <p>Данная методология является переводом методологии <a href="https://github.com/amrelsagaei/Bug-Bounty-Hunting-Methodology-2025" target="_blank">https://github.com/amrelsagaei/Bug-Bounty-Hunting-Methodology-2025</a></p>
  <p>Если вам помог мой перевод, буду рада любой поддержке:</p>
  <ul>
    <li><strong>Т-Банк</strong>: 2200 7007 0632 5700</li>
    <li><strong>USDT (TRC20)</strong>: TPfzsyjNmbXnpmNqoyVsUHMmj65Fgund2g</li>
    <li><strong>TON</strong>: UQBHZm5vM2m8xMRaFuvIrJOIYn2aagVK90dHUOJ6HOAFSVHl</li>
  </ul>
</div>



<br>
📜 Table of Contents

| Раздел | Описание |
|---------|-------------|
| 1. [Разведка](#1-reconnaissance-and-subdomain-enumeration) | 	Перечисление субдоменов и начальное сканирование |
| 2. [Обнаружение](#2-discovery-and-probing) | Пробинг HTTP и обнаружение активов |
| 3. [Перечисление](#3-advanced-enumeration-techniques) | 	Продвинутые методы и обнаружение параметров |
| 4. [Тестирование](#4-vulnerability-testing) | Оценка уязвимостей |
| 5. [Два глаза](#5-the-two-eye-approach) | Что это? |
| 6. [Создание POC) | Документирование и сбор доказательств |
| 7. [Отчетность](#7-reporting) | Финальное документирование |

---

<br>


## **1. Разведка и перечисление субдоменов**

### **1.1 Пассивное перечисление субдоменов**  
**🛠️Инструменты:** [Subfinder](https://github.com/projectdiscovery/subfinder), [Amass](https://github.com/OWASP/Amass), [CRTSH](https://crt.sh/), [Github-Search](https://github.com/gwen001/github-search)

**Subfinder**
```bash
subfinder -d target.com -silent -all -recursive -o subfinder_subs.txt
```

**Amass (пассивный режим)**
```bash
amass enum -passive -d target.com -o amass_passive_subs.txt
```

**Запрос CRT.sh**
```bash
curl -s "https://crt.sh/?q=%25.target.com&output=json" | jq -r '.[].name_value' | sed 's/\*\.//g' | anew crtsh_subs.txt
```

**Github Dorking**
```bash
github-subdomains -d target.com -t YOUR_GITHUB_TOKEN -o github_subs.txt
```

**Комбинация результатов**
```bash
cat *_subs.txt | sort -u | anew all_subs.txt
```

### **1.2 Активное перечисление субдоменов**

**🛠️Инструменты:** [MassDNS](https://github.com/blechschmidt/massdns), [Shuffledns](https://github.com/projectdiscovery/shuffledns), [DNSX](https://github.com/projectdiscovery/dnsx), [SubBrute](https://github.com/TheRook/subbrute), [FFuF](https://github.com/ffuf/ffuf)

**MassDNS**
```bash
massdns -r resolvers.txt -t A -o S -w massdns_results.txt wordlist.txt
```

**Shuffledns**
```bash
shuffledns -d target.com -list all_subs.txt -r resolvers.txt -o active_subs.txt
```

**Резолвинг через DNSX**
```bash
dnsx -l active_subs.txt -resp -o resolved_subs.txt
```

**SubBrute**
```bash
python3 subbrute.py target.com -w wordlist.txt -o brute_force_subs.txt
```

**FFuF для субдоменов**
```bash
ffuf -u https://FUZZ.target.com -w wordlist.txt -t 50 -mc 200,403 -o ffuf_subs.txt
```


```markdown
## **1. Разведка и перечисление субдоменов**

### **1.1 Пассивное перечисление субдоменов**  
**🛠️Инструменты:** [Subfinder](https://github.com/projectdiscovery/subfinder), [Amass](https://github.com/OWASP/Amass), [CRTSH](https://crt.sh/), [Github-Search](https://github.com/gwen001/github-search)

**Subfinder**
```bash
subfinder -d target.com -silent -all -recursive -o subfinder_subs.txt
```

**Amass (пассивный режим)**
```bash
amass enum -passive -d target.com -o amass_passive_subs.txt
```

**Запрос CRT.sh**
```bash
curl -s "https://crt.sh/?q=%25.target.com&output=json" | jq -r '.[].name_value' | sed 's/\*\.//g' | anew crtsh_subs.txt
```

**Github Dorking**
```bash
github-subdomains -d target.com -t YOUR_GITHUB_TOKEN -o github_subs.txt
```

**Комбинация результатов**
```bash
cat *_subs.txt | sort -u | anew all_subs.txt
```

### **1.2 Активное перечисление субдоменов**

**🛠️Инструменты:** [MassDNS](https://github.com/blechschmidt/massdns), [Shuffledns](https://github.com/projectdiscovery/shuffledns), [DNSX](https://github.com/projectdiscovery/dnsx), [SubBrute](https://github.com/TheRook/subbrute), [FFuF](https://github.com/ffuf/ffuf)

**MassDNS**
```bash
massdns -r resolvers.txt -t A -o S -w massdns_results.txt wordlist.txt
```

**Shuffledns**
```bash
shuffledns -d target.com -list all_subs.txt -r resolvers.txt -o active_subs.txt
```

**Резолвинг через DNSX**
```bash
dnsx -l active_subs.txt -resp -o resolved_subs.txt
```

**SubBrute**
```bash
python3 subbrute.py target.com -w wordlist.txt -o brute_force_subs.txt
```

**FFuF для субдоменов**
```bash
ffuf -u https://FUZZ.target.com -w wordlist.txt -t 50 -mc 200,403 -o ffuf_subs.txt
```

### **1.3 Обработка специфических (не wildcard) целей**

**🛠️Инструменты:** [GAU](https://github.com/lc/gau), [Waybackurls](https://github.com/tomnomnom/waybackurls), [Katana](https://github.com/projectdiscovery/katana), [Hakrawler](https://github.com/hakluke/hakrawler)

**GAU**
```bash
gau target.example.com | anew gau_results.txt
```

**Waybackurls**
```bash
waybackurls target.example.com | anew wayback_results.txt
```

**Katana**
```bash
katana -u target.example.com -silent -jc -o katana_results.txt
```

**Hakrawler**
```bash
echo "https://target.example.com" | hakrawler -depth 2 -plain -js -out hakrawler_results.txt
```

### **Дополнительные расширенные техники**

**🛠️Инструменты:** [CloudEnum](https://github.com/initstring/cloud_enum), [AWSBucketDump](https://github.com/jordanpotti/AWSBucketDump), [S3Scanner](https://github.com/sa7mon/S3Scanner)

**Обратный DNS**
```bash
dnsx -ptr -l resolved_subs.txt -resp-only -o reverse_dns.txt
```

**Перечисление ASN**
```bash
amass intel -asn <ASN_NUMBER> -o asn_results.txt
```

**Перечисление облачных активов**
```bash
cloud_enum -k target.com
```

**Валидация результатов**
```bash
cat all_subs.txt | httpx -silent -title -o live_subdomains.txt
```

---

<br>
```


## **2. Открытие и исследование**

### **2.1 Пробинг HTTP**

**🛠️Инструменты:** [httpx](https://github.com/projectdiscovery/httpx), [httprobe](https://github.com/tomnomnom/httprobe)

**Пробинг с использованием HTTPX**
```bash
httpx -l resolved_subs.txt -p 80,443,8080,8443 -silent -title -sc -ip -o live_websites.txt
```

**Кастомная фильтрация**
```bash
cat live_websites.txt | grep -i "login\|admin" | tee login_endpoints.txt
```

### **2.2 Анализ JavaScript**

**🛠️Инструменты:** [LinkFinder](https://github.com/GerbenJavado/LinkFinder), [subjs](https://github.com/lc/subjs), [JSFinder](https://github.com/Threezh1/JSFinder), [GF](https://github.com/tomnomnom/gf)

**Извлечение JS файлов**
```bash
cat live_websites.txt | waybackurls | grep "\.js" | anew js_files.txt
```

**Анализ с использованием LinkFinder**
```bash
python3 linkfinder.py -i js_files.txt -o js_endpoints.txt
```

**Поиск чувствительных паттернов**
```bash
cat js_files.txt | gf aws-keys | tee aws_keys.txt
cat js_files.txt | gf urls | tee sensitive_urls.txt
```

**Проверка API ключа**
```bash
curl -X GET "https://api.example.com/resource" -H "Authorization: Bearer <extracted_key>"
```

### **2.3 Расширенный Google Dorking**

**🛠️Инструменты:** [GitDorker](https://github.com/obheda12/GitDorker)

**Автоматизированный поиск с использованием Dorking**
```bash
python3 GitDorker.py -tf <github_token.txt> -q target.com -d dorks.txt -o git_dorks_output.txt
```

**Файлы администратора/входа**
```bash
site:*.example.com inurl:"*admin | login" | inurl:.php | .asp
```

**Конфигурационные файлы**
```bash
site:*.example.com ext:env | ext:yaml | ext:ini
```

**Публичные ключи**
```bash
site:*.example.com inurl:"id_rsa.pub" | inurl:".pem"
```
```

Теперь все команды и текст представлены в корректном формате Markdown.

Вот исправленный и отформатированный текст в Markdown:

```markdown
### **2.4 Открытие URL**

**🛠️Инструменты:** [Katana](https://github.com/projectdiscovery/katana), [Gospider](https://github.com/jaeles-project/gospider), [Hakrawler](https://github.com/hakluke/hakrawler)

**Краулинг с использованием Katana**
```bash
katana -list live_websites.txt -jc -o katana_urls.txt
```

**Gospider**
```bash
gospider -s "https://target.com" -d 2 -o gospider_output/
```

**Hakrawler**
```bash
echo "https://target.com" | hakrawler -depth 3 -plain -out hakrawler_results.txt
```

### **2.5 Перечень архивов**

**🛠️Инструменты:** [GAU](https://github.com/lc/gau), [Waybackurls](https://github.com/tomnomnom/waybackurls), [ParamSpider](https://github.com/devanshbatham/ParamSpider)

**Сбор URL из архивов**
```bash
gau --subs target.com | anew archived_urls.txt
waybackurls target.com | anew wayback_urls.txt
```

**Извлечение параметров**
```bash
cat archived_urls.txt | grep "=" | anew parameters.txt
```

---
```

Вот переведенный и отформатированный текст в Markdown:

```markdown
## **3. Расширенные методы перечисления**

### **3.1 Открытие параметров**

**🛠️Инструменты:** [Arjun](https://github.com/s0md3v/Arjun), [ParamSpider](https://github.com/devanshbatham/ParamSpider), [FFuF](https://github.com/ffuf/ffuf)

**Открытие параметров с использованием Arjun**
```bash
arjun -u "https://target.example.com" -m GET,POST --stable -o params.json
```

**Открытие веб-параметров с использованием ParamSpider**
```bash
python3 paramspider.py --domain target.com --exclude woff,css,js --output paramspider_output.txt
```

**Брутфорс параметров с использованием FFuF**
```bash
ffuf -u https://target.com/page.php?FUZZ=test -w /usr/share/wordlists/params.txt -o parameter_results.txt
```

### **3.2 Перечень облачных активов**

**🛠️Инструменты:** [CloudEnum](https://github.com/initstring/cloud_enum), [AWSBucketDump](https://github.com/jordanpotti/AWSBucketDump), [S3Scanner](https://github.com/sa7mon/S3Scanner)

**Перечень облачных бакетов**
```bash
cloud_enum -k target.com -b buckets.txt -o cloud_enum_results.txt
```

**Тест доступа к S3 бакету**
```bash
aws s3 ls s3://<bucket_name> --no-sign-request
```

**Дамп содержимого S3 бакета**
```bash
python3 AWSBucketDump.py -b target-bucket -o dumped_data/
```

### **3.3 Открытие контента**

**🛠️Инструменты:** [Feroxbuster](https://github.com/epi052/feroxbuster), [FFuF](https://github.com/ffuf/ffuf), [Dirsearch](https://github.com/maurosoria/dirsearch)

**Использование Feroxbuster**
```bash
feroxbuster -u https://target.com -w /usr/share/wordlists/common.txt -r -t 20 -o recursive_results.txt
```

**Использование Dirsearch**
```bash
dirsearch -u https://target.com -w /usr/share/wordlists/content_discovery.txt -e php,html,js,json -x 404 -o dirsearch_results.txt
```

**Рекурсивный поиск с использованием FFuF**
```bash
ffuf -u https://target.com/FUZZ -w /usr/share/wordlists/content_discovery.txt -mc 200,403 -recursion -recursion-depth 3 -o ffuf_results.txt
```

### **3.4 Перечень API**

**🛠️Инструменты:** [Kiterunner](https://github.com/assetnote/kiterunner), [Postman](https://www.postman.com/), [Burp Suite](https://portswigger.net/burp)

**Использование Kiterunner**
```bash
kr scan https://api.target.com -w /usr/share/kiterunner/routes-large.kite -o api_routes.txt
```

### **3.5 ASN Маппинг**

**🛠️Инструменты:** [Amass](https://github.com/OWASP/Amass), [Shodan](https://www.shodan.io/), [Censys](https://censys.io/)

**Поиск по ASN**
```bash
amass intel -asn <ASN_Number> -o asn_ips.txt
```

**Перечень с использованием Shodan**
```bash
shodan search "net:<ip_range>" --fields ip_str,port --limit 100
```

**Поиск активов с использованием Censys**
```bash
censys search "autonomous_system.asn:<ASN_Number>" -o censys_assets.txt
```

---
```


Вот переведенный и отформатированный текст в Markdown:

```markdown
## **4. Тестирование уязвимостей**

### **4.1 Уязвимости высокого приоритета**

**🐞Тестирование CSRF**
```bash
cat live_websites.txt | gf csrf | tee csrf_endpoints.txt
```

**🐞Тестирование LFI**
```bash
cat live_websites.txt | gf lfi | qsreplace "/etc/passwd" | xargs -I@ curl -s @ | grep "root:x:" > lfi_results.txt
```

**🐞Тестирование RCE**
```bash
curl -X POST -F "file=@exploit.php" https://target.com/upload
```

**🐞Тестирование SQLi**
```bash
ghauri -u "https://target.com?id=1" --dbs --batch
```

**🐞Поиск чувствительных данных**
```bash
cat js_files.txt | grep -Ei "key|token|auth|password" > sensitive_data.txt
```

**🐞Тест на открытые редиректы**
```bash
cat urls.txt | grep "=http" | qsreplace "https://evil.com" | xargs -I@ curl -I -s @ | grep "evil.com"
```

---

<br>

## **5. Подход "Два Глаза" 👀**
1. **Первый Глаз:** Сфокусируйтесь на тестировании каждого собранного поддомена, конечной точки или параметра на наличие распространенных уязвимостей.
2. **Второй Глаз:** Выявите «интересные» находки, такие как раскрытые учетные данные, забытые поддомены или панели администратора.

### **Действия:**
- Если уязвимость обнаружена, создайте доказательство концепции (POC) и протестируйте его воздействие.
- Если уязвимости не найдены, переходите к более глубокому тестированию уникальных поддоменов или конечных точек.

---

<br>

## **6. Создание доказательства концепции (POC)**

### **🎥Видео POC**

Демонстрируйте уязвимости в действии, используя инструменты для записи экрана, такие как Greenshot или OBS Studio.

### **📸Скриншоты POC**

Снимайте четкие скриншоты с аннотациями для объяснения каждого шага.

- **🛠️Инструмент:** Greenshot.

---
```


Вот переведенный и отформатированный текст в Markdown:

```markdown
## **7. Отчетность**

### **📝Структура отчета**

1. **Исполнительное резюме**
   - Объем тестирования
   - Время тестирования
   - Резюме ключевых находок
   - Оценка рисков

2. **Технические детали**
   - Название уязвимости
   - Оценка серьезности
   - Затронутые компоненты
   - Техническое описание
   - Шаги для воспроизведения
   - Анализ воздействия
   - Подтверждающие доказательства (POC)

3. **Ремедиация**
   - Подробные рекомендации
   - Шаги по смягчению рисков
   - Дополнительные меры безопасности
   - Ссылки и ресурсы

4. **Дополнительные материалы**
   - Видео демонстрации
   - Скриншоты и аннотации
   - Логи HTTP-запросов/ответов
   - Фрагменты кода
   - Хронология обнаружения

### **Лучшие практики**

- Пишите четкие и краткие описания
- Включайте подробные шаги для воспроизведения
- Предоставляйте практичные рекомендации по исправлению
- Подкрепляйте выводы доказательствами
- Используйте профессиональное форматирование
- Подчеркивайте бизнес-воздействие
- Включайте шаги для верификации

### **Формат отчета**

```markdown
# Отчет об уязвимости: [Название]

## Обзор
- Серьезность: [Критическая/Высокая/Средняя/Низкая]
- Оценка CVSS: [Оценка]
- Затронутый компонент: [Компонент]

## Описание
[Подробное техническое описание]

## Шаги для воспроизведения
1. [Шаг 1]
2. [Шаг 2]
3. [Шаг n...]

## Воздействие
[Бизнес- и техническое воздействие]

## Доказательства концепции
[Скриншоты, видео, код]

## Рекомендации
[Подробные рекомендации по исправлению]

## Ссылки
[CVE, CWE, связанные ресурсы]
```

---

<br>

## 📄 Лицензия
Все права защищены © 2025 **alyasik**.
```
