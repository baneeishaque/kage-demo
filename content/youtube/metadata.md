# YouTube upload metadata

Copy-paste metadata per locale. Durations are the rendered video lengths (subtitle timeline
plus the 1.4 s tail). Chapter timestamps follow the English timeline; other locales shift by
a few seconds — the chapter *labels* stay valid, only the numbers need adjusting if you
upload a non-English cut as the primary video.

Full metadata (title + description + tags) is provided for the ten uploads with the widest
reach; the remaining 27 locales get a compact table with localized titles and durations.
Every locale's subtitles are in `subtitles/`, and every narrated locale's audio track is in
the master MKV (`build/video/master-multiaudio.mkv`).

## Thumbnail

Text overlay on a dark slide: **"One host. Eleven sealed steps."** — pair with the chain
screenshot (`shot-chain.png`) or the sealed terminal (`shot-terminal.png`) so the seal line
is visible in the thumbnail itself.

***

## English (`en`) — 2:56

### Title

```text
One host, eleven sealed steps: DFIR triage with a chain of custody
```

### Description

```text
Kage is a local-first DFIR triage console for Windows hosts: eleven orchestrated steps —
workspace, Defender exclusion, tooling, collection, system context, Sigma rules, timeline,
analysis, optional YARA scan, indicator enrichment, and an AI-assisted summary — run as one
sealed chain with a SHA-256 seal on every completed step.

This video walks through what it is, how the chain and scoring work, what the synthetic demo
proves, and the trade-offs the project documents honestly. The repository even narrates its
own walkthrough: narration in 20 languages, subtitles in 37.

Tool: https://github.com/karim852/Kage-DFIR-toolkit (MIT, by k0r1m)
Demo: https://demo-kumo-kage.vercel.app/
Kit + docs: https://github.com/baneeishaque-ompventure/kage-demo

Chapters:
0:00 One host. Eleven sealed steps
0:14 Triage is ten tools and a shoebox
0:28 A console, not another collector
0:41 Eleven steps, sealed and replayable
0:59 A score you can argue with
1:17 Chain of custody by default
1:31 What the event logs never say
1:47 Rehearse before you touch a host
1:56 Run it headless, from anywhere
2:07 The trade-offs, honestly
2:25 What it adds up to
2:43 Point it at a suspect host

What the chain proves:
- Eleven steps with exact IDs and labels; the first four are the required base
- Every completed step sealed with SHA-256, declaring what it covers
- Four capped scoring axes: severity 45, kill chain 25, reputation 20, corroboration 10
- Confidence kept separate from severity — how many independent sources agree
- Demo mode: a synthetic intrusion buried under 420 noise events, touches nothing
- Headless operation over SSH on the suspect host, loopback-only by design

Stack: Python, FastAPI, PowerShell, Hayabusa, Sigma, THOR Lite.
License: MIT. This kit is an independent community resource, not affiliated with the tool's
author.
```

### Tags

```text
dfir, incident response, triage, digital forensics, windows, powershell, sigma, yara,
thor lite, hayabusa, chain of custody, cybersecurity, blue team, threat hunting, fastapi,
python
```

***

## Hindi (`hi`) — 4:05

### Title

```text
एक होस्ट, ग्यारह सीलबंद कदम: चेन ऑफ कस्टडी के साथ DFIR ट्रायाज
```

### Description

```text
Kage विंडोज़ होस्ट के लिए एक लोकल-फर्स्ट DFIR ट्रायाज कंसोल है: ग्यारह ऑर्केस्ट्रेटेड चरण —
वर्कस्पेस, Defender अपवाद, टूलिंग, संग्रह, सिस्टम संदर्भ, Sigma नियम, टाइमलाइन, विश्लेषण,
वैकल्पिक YARA स्कैन, इंडिकेटर एनरिचमेंट और AI-सहायता प्राप्त सारांश — एक सीलबंद शृंखला के
रूप में, हर पूर्ण चरण पर SHA-256 सील के साथ।

यह वीडियो बताता है कि यह क्या है, शृंखला और स्कोरिंग कैसे काम करते हैं, सिंथेटिक डेमो क्या
साबित करता है, और प्रोजेक्ट किन समझौतों को ईमानदारी से दर्ज करता है। रिपॉज़िटरी अपनी
वॉकथ्रू खुद सुनाती भी है: 20 भाषाओं में नैरेशन, 37 में सबटाइटल।

टूल: https://github.com/karim852/Kage-DFIR-toolkit (MIT, k0r1m)
डेमो: https://demo-kumo-kage.vercel.app/
किट + डॉक्स: https://github.com/baneeishaque-ompventure/kage-demo

अध्याय (अंग्रेज़ी टाइमलाइन):
0:00 एक होस्ट। ग्यारह सीलबंद चरण
0:14 ट्रायाज: दस औज़ार, एक जूते का डिब्बा
0:28 एक कंसोल, एक और कलेक्टर नहीं
0:41 ग्यारह चरण, सीलबंद और दोहराने योग्य
0:59 ऐसा स्कोर जिस पर बहस कर सकें
1:17 चेन ऑफ कस्टडी, डिफ़ॉल्ट रूप से
1:31 जो इवेंट लॉग कभी नहीं बताते
1:47 होस्ट छूने से पहले अभ्यास करें
1:56 हेडलेस चलाएँ, कहीं से भी
2:07 समझौते, ईमानदारी से
2:25 इसका कुल जोड़ क्या है
2:43 किसी संदिग्ध होस्ट पर चलाएँ

स्टैक: Python, FastAPI, PowerShell, Hayabusa, Sigma, THOR Lite.
लाइसेंस: MIT. यह किट एक स्वतंत्र सामुदायिक संसाधन है, टूल के लेखक से संबद्ध नहीं।
```

### Tags

```text
dfir, इंसिडेंट रिस्पॉन्स, फोरेंसिक्स, विंडोज़, साइबर सुरक्षा, sigma, yara, chain of custody,
blue team, python
```

***

## Arabic (`ar`) — 5:21

### Title

```text
مضيف واحد، إحدى عشرة خطوة مختومة: فرز DFIR مع سلسلة حيازة
```

### Description

```text
Kage طرفية فرز DFIR محلية أولاً لمضيفات Windows: إحدى عشرة خطوة منسّقة — مساحة العمل،
استثناء Defender، الأدوات، الجمع، سياق النظام، قواعد Sigma، الخط الزمني، التحليل، فحص
YARA اختياري، إثراء المؤشرات، وملخص بمساعدة الذكاء الاصطناعي — تعمل كسلسلة مختومة واحدة
مع ختم SHA-256 على كل خطوة مكتملة.

يشرح هذا الفيديو ما هو، وكيف تعمل السلسلة والتقييم، وما يثبته العرض الاصطناعي،
والمقايضات التي يوثقها المشروع بصدق. المستودع يروي جولته بنفسه أيضًا: سرد بعشرين لغة
وترجمات بسبع وثلاثين.

الأداة: https://github.com/karim852/Kage-DFIR-toolkit (MIT، k0r1m)
العرض: https://demo-kumo-kage.vercel.app/
الحزمة والوثائق: https://github.com/baneeishaque-ompventure/kage-demo

الفصول (الخط الزمني الإنجليزي):
0:00 مضيف واحد. إحدى عشرة خطوة مختومة
0:14 الفرز يعني عشر أدوات وصندوقًا
0:28 طرفية واحدة، لا جامع آخر
0:41 إحدى عشرة خطوة، مختومة وقابلة لإعادة التشغيل
0:59 درجة يمكن الجدال حولها
1:17 سلسلة الحيازة، افتراضيًا
1:31 ما لا تقوله سجلات الأحداث أبدًا
1:47 تدرّب قبل لمس المضيف
1:56 شغّله بلا واجهة، من أي مكان
2:07 المقايضات، بصدق
2:25 إلى ماذا يصل الأمر
2:43 وجّهه إلى مضيف مشتبه به

التقنيات: Python, FastAPI, PowerShell, Hayabusa, Sigma, THOR Lite.
الترخيص: MIT. هذه الحزمة مورد مجتمعي مستقل وغير تابعة لمؤلف الأداة.
```

### Tags

```text
dfir, الاستجابة للحوادث, التحقيق الجنائي الرقمي, ويندوز, الأمن السيبراني, sigma, yara,
الفريق الأزرق
```

***

## Spanish (`es`) — 3:30

### Title

```text
Un host, once pasos sellados: triaje DFIR con cadena de custodia
```

### Description

```text
Kage es una consola de triaje DFIR local para hosts Windows: once pasos orquestados —
espacio de trabajo, exclusión de Defender, herramientas, recolección, contexto del sistema,
reglas Sigma, línea de tiempo, análisis, escaneo YARA opcional, enriquecimiento de
indicadores y un resumen asistido por IA — ejecutados como una cadena sellada con un sello
SHA-256 en cada paso completado.

Este vídeo explica qué es, cómo funcionan la cadena y la puntuación, qué demuestra la demo
sintética y los compromisos que el proyecto documenta con honestidad. El repositorio además
narra su propio recorrido: narración en 20 idiomas y subtítulos en 37.

Herramienta: https://github.com/karim852/Kage-DFIR-toolkit (MIT, k0r1m)
Demo: https://demo-kumo-kage.vercel.app/
Kit y docs: https://github.com/baneeishaque-ompventure/kage-demo

Capítulos (línea de tiempo en inglés):
0:00 Un host. Once pasos sellados
0:14 Triaje significa diez herramientas y una caja
0:28 Una consola, no otro recolector
0:41 Once pasos, sellados y repetibles
0:59 Una puntuación discutible
1:17 Cadena de custodia, por defecto
1:31 Lo que los registros nunca cuentan
1:47 Practica antes de tocar el host
1:56 Ejecútalo sin interfaz, desde cualquier lugar
2:07 Compromisos, con honestidad
2:25 En qué acaba todo
2:43 Ejecútalo en un host sospechoso

Stack: Python, FastAPI, PowerShell, Hayabusa, Sigma, THOR Lite.
Licencia: MIT. Este kit es un recurso comunitario independiente, no afiliado al autor.
```

### Tags

```text
dfir, respuesta a incidentes, forense digital, windows, ciberseguridad, sigma, yara,
equipo azul
```

***

## Portuguese (`pt`) — 4:23

### Title

```text
Um host, onze passos selados: triagem DFIR com cadeia de custódia
```

### Description

```text
Kage é um console de triagem DFIR local para hosts Windows: onze passos orquestrados —
espaço de trabalho, exclusão do Defender, ferramentas, coleta, contexto do sistema, regras
Sigma, linha do tempo, análise, varredura YARA opcional, enriquecimento de indicadores e um
resumo assistido por IA — executados como uma cadeia selada com selo SHA-256 em cada passo
concluído.

Este vídeo explica o que é, como funcionam a cadeia e a pontuação, o que a demo sintética
comprova e os trade-offs que o projeto documenta com honestidade. O repositório ainda narra
a própria jornada: narração em 20 idiomas e legendas em 37.

Ferramenta: https://github.com/karim852/Kage-DFIR-toolkit (MIT, k0r1m)
Demo: https://demo-kumo-kage.vercel.app/
Kit e docs: https://github.com/baneeishaque-ompventure/kage-demo

Capítulos (linha do tempo em inglês):
0:00 Um host. Onze passos selados
0:14 Triagem significa dez ferramentas e uma caixa
0:28 Um console, não mais um coletor
0:41 Onze passos, selados e reexecutáveis
0:59 Uma pontuação discutível
1:17 Cadeia de custódia, por padrão
1:31 O que os logs nunca contam
1:47 Pratique antes de tocar no host
1:56 Execute sem interface, de qualquer lugar
2:07 Os trade-offs, com honestidade
2:25 No que dá tudo isso
2:43 Execute em um host suspeito

Stack: Python, FastAPI, PowerShell, Hayabusa, Sigma, THOR Lite.
Licença: MIT. Este kit é um recurso comunitário independente, sem afiliação com o autor.
```

### Tags

```text
dfir, resposta a incidentes, forense digital, windows, cibersegurança, sigma, yara, blue team
```

***

## Russian (`ru`) — 4:07

### Title

```text
Один хост, одиннадцать запечатанных шагов: DFIR-триаж с цепочкой сохранности
```

### Description

```text
Kage — локальная консоль DFIR-триажа для Windows-хостов: одиннадцать шагов оркестрации —
рабочая папка, исключение в Defender, инструменты, сбор, контекст системы, правила Sigma,
таймлайн, анализ, опциональный YARA-скан, обогащение индикаторов и сводка с помощью ИИ —
единая запечатанная цепочка с печатью SHA-256 на каждом завершённом шаге.

В видео: что это такое, как устроены цепочка и оценка, что доказывает синтетическая
демонстрация, и компромиссы, которые проект честно документирует. Репозиторий ещё и сам
озвучивает свою экскурсию: озвучка на 20 языках, субтитры на 37.

Инструмент: https://github.com/karim852/Kage-DFIR-toolkit (MIT, k0r1m)
Демо: https://demo-kumo-kage.vercel.app/
Набор и документация: https://github.com/baneeishaque-ompventure/kage-demo

Главы (английский таймлайн):
0:00 Один хост. Одиннадцать запечатанных шагов
0:14 Триаж — это десять инструментов и один ящик
0:28 Одна консоль, а не ещё один сборщик
0:41 Одиннадцать шагов, запечатанных и повторяемых
0:59 Балл, о котором можно спорить
1:17 Цепочка сохранности, по умолчанию
1:31 То, о чём журналы никогда не расскажут
1:47 Тренировка до прикосновения к хосту
1:56 Запуск без интерфейса, откуда угодно
2:07 Компромиссы, честно
2:25 Что получается в итоге
2:43 Запустите на подозрительном хосте

Стек: Python, FastAPI, PowerShell, Hayabusa, Sigma, THOR Lite.
Лицензия: MIT. Этот набор — независимый общественный ресурс, не связанный с автором
инструмента.
```

### Tags

```text
dfir, реагирование на инциденты, цифровая криминалистика, windows, кибербезопасность,
sigma, yara, blue team
```

***

## Japanese (`ja`) — 4:48

### Title

```text
1台のホスト、11の封印されたステップ：証拠の連鎖を備えたDFIRトリアージ
```

### Description

```text
Kage は Windows ホスト向けのローカル優先 DFIR トリアージ・コンソールです。11のオーケスト
レーションされたステップ — ワークスペース、Defender 除外、ツール、収集、システム文脈、
Sigma ルール、タイムライン、分析、任意の YARA スキャン、指標のエンリッチ、AI 支援の要約
— を1本の封印されたチェーンとして実行し、完了した各ステップに SHA-256 の封印を付けます。

この動画では、仕組み、チェーンとスコアリング、合成デモが証明するもの、そしてプロジェクト
が正直に記録するトレードオフを解説します。リポジトリ自体もこのウォークスルーを語ります。
20言語のナレーションと37言語の字幕付き。

ツール: https://github.com/karim852/Kage-DFIR-toolkit (MIT, k0r1m)
デモ: https://demo-kumo-kage.vercel.app/
キットとドキュメント: https://github.com/baneeishaque-ompventure/kage-demo

チャプター（英語タイムライン）:
0:00 1台のホスト。11の封印されたステップ
0:14 トリアージとは10のツールと1つの箱
0:28 1つのコンソール、もう1つの収集ツールではない
0:41 11のステップ、封印され、再実行できる
0:59 議論できるスコア
1:17 チェーン・オブ・カストディを標準で
1:31 イベントログが決して語らないこと
1:47 ホストに触れる前に練習
1:56 どこからでもヘッドレスで実行
2:07 トレードオフを正直に
2:25 最終的に何が得られるか
2:43 疑わしいホストで実行を

スタック: Python, FastAPI, PowerShell, Hayabusa, Sigma, THOR Lite.
ライセンス: MIT。このキットは独立したコミュニティ資料であり、ツール作者とは無関係です。
```

### Tags

```text
dfir, インシデントレスポンス, フォレンジック, windows, サイバーセキュリティ, sigma, yara,
ブルーチーム
```

***

## German (`de`) — 4:16

### Title

```text
Ein Host, elf versiegelte Schritte: DFIR-Triage mit Chain of Custody
```

### Description

```text
Kage ist eine lokale DFIR-Triage-Konsole für Windows-Hosts: elf orchestrierte Schritte —
Arbeitsbereich, Defender-Ausnahme, Tooling, Erfassung, Systemkontext, Sigma-Regeln, Timeline,
Analyse, optionaler YARA-Scan, Indikator-Anreicherung und eine KI-gestützte Zusammenfassung —
als eine versiegelte Kette mit SHA-256-Siegel auf jedem abgeschlossenen Schritt.

Das Video erklärt, was es ist, wie Kette und Bewertung funktionieren, was die synthetische
Demo beweist und welche Kompromisse das Projekt ehrlich dokumentiert. Das Repository erzählt
seinen eigenen Walkthrough gleich mit: Narration in 20 Sprachen, Untertitel in 37.

Tool: https://github.com/karim852/Kage-DFIR-toolkit (MIT, k0r1m)
Demo: https://demo-kumo-kage.vercel.app/
Kit und Doku: https://github.com/baneeishaque-ompventure/kage-demo

Kapitel (englische Timeline):
0:00 Ein Host. Elf versiegelte Schritte
0:14 Triage heißt zehn Tools und eine Kiste
0:28 Eine Konsole, kein weiterer Sammler
0:41 Elf Schritte, versiegelt und wiederholbar
0:59 Ein Score, über den man streiten kann
1:17 Chain of Custody, standardmäßig
1:31 Was Ereignisprotokolle nie erzählen
1:47 Üben, bevor der Host berührt wird
1:56 Headless ausführen, von überall
2:07 Kompromisse, ehrlich
2:25 Was am Ende herauskommt
2:43 Auf einem verdächtigen Host ausführen

Stack: Python, FastAPI, PowerShell, Hayabusa, Sigma, THOR Lite.
Lizenz: MIT. Dieses Kit ist eine unabhängige Community-Ressource, nicht mit dem Autor
verbunden.
```

### Tags

```text
dfir, incident response, forensik, windows, cybersicherheit, sigma, yara, blue team
```

***

## French (`fr`) — 3:57

### Title

```text
Un hôte, onze étapes scellées : triage DFIR avec chaîne de possession
```

### Description

```text
Kage est une console de triage DFIR locale pour les hôtes Windows : onze étapes orchestrées —
espace de travail, exclusion Defender, outillage, collecte, contexte système, règles Sigma,
chronologie, analyse, scan YARA optionnel, enrichissement des indicateurs et une synthèse
assistée par IA — exécutées comme une chaîne scellée avec un sceau SHA-256 sur chaque étape
terminée.

Cette vidéo explique ce que c'est, comment fonctionnent la chaîne et le score, ce que prouve
la démo synthétique, et les compromis que le projet documente honnêtement. Le dépôt narre
aussi sa propre visite : narration en 20 langues, sous-titres en 37.

Outil : https://github.com/karim852/Kage-DFIR-toolkit (MIT, k0r1m)
Démo : https://demo-kumo-kage.vercel.app/
Kit et docs : https://github.com/baneeishaque-ompventure/kage-demo

Chapitres (chronologie anglaise) :
0:00 Un hôte. Onze étapes scellées
0:14 Le triage, c'est dix outils et une caisse
0:28 Une console, pas un collecteur de plus
0:41 Onze étapes, scellées et rejouables
0:59 Un score discutable
1:17 Chaîne de possession, par défaut
1:31 Ce que les journaux ne disent jamais
1:47 S'entraîner avant de toucher l'hôte
1:56 Exécutez sans interface, de partout
2:07 Les compromis, honnêtement
2:25 Ce qu'on obtient au final
2:43 À exécuter sur un hôte suspect

Stack : Python, FastAPI, PowerShell, Hayabusa, Sigma, THOR Lite.
Licence : MIT. Ce kit est une ressource communautaire indépendante, non affiliée à l'auteur.
```

### Tags

```text
dfir, réponse à incident, forensic, windows, cybersécurité, sigma, yara, blue team
```

***

## Chinese (`zh`) — 4:26

### Title

```text
一台主机，十一个封存步骤：带证据链的 DFIR 应急响应
```

### Description

```text
Kage 是面向 Windows 主机的本地优先 DFIR 应急响应控制台：十一个编排步骤——工作区、
Defender 排除、工具定位、证据采集、系统上下文、Sigma 规则、时间线、分析、可选 YARA
扫描、指标富化和 AI 辅助摘要——作为一条封存的链条运行，每个完成的步骤都带有 SHA-256
封存。

本视频讲解它是什么、链条与评分如何工作、合成演示证明了什么，以及项目坦诚记录的取舍。
这个仓库甚至自己讲述它的走查：二十种语言的旁白和三十七种语言的字幕。

工具：https://github.com/karim852/Kage-DFIR-toolkit（MIT，k0r1m）
演示：https://demo-kumo-kage.vercel.app/
素材与文档：https://github.com/baneeishaque-ompventure/kage-demo

章节（英文时间轴）：
0:00 一台主机。十一个封存步骤
0:14 应急响应就是十件工具加一个箱子
0:28 一个控制台，而不是又一个采集器
0:41 十一个步骤，封存且可重跑
0:59 经得起争论的评分
1:17 默认自带证据链
1:31 事件日志永远不会告诉你的事
1:47 在触碰主机之前先演练
1:56 无界面运行，随处可用
2:07 坦诚面对取舍
2:25 最终得到什么
2:43 在可疑主机上运行

技术栈：Python、FastAPI、PowerShell、Hayabusa、Sigma、THOR Lite。
许可：MIT。本素材是独立的社区资源，与工具作者无隶属关系。
```

### Tags

```text
dfir, 应急响应, 数字取证, windows, 网络安全, sigma, yara, 蓝队
```

***

## Compact locales (27)

Titles below are each locale's own slide-1 title from `translations/<locale>.json`; append
`— Kage DFIR triage` when uploading. For descriptions, reuse the English description with a
translated first line; the repo URL, stack line, and chapter labels carry over. "Subtitles
only" means the video has no narration track (no system voice ships for that language).

| Locale | Language | Duration | Suggested title | Audio |
| ------ | -------- | -------- | --------------- | ----- |
| `as` | অসমীয়া | 3:25 | এক হষ্ট। এঘাৰটা ছিল কৰা পদক্ষেপ। | subtitles only |
| `bn` | বাংলা | 4:16 | একটি হোস্ট। এগারোটি সিলমোহর করা ধাপ। | narrated |
| `brx` | बड़ो | 3:27 | मोनसे होस्ट। जी मोनसे सिल खालामनाय खोन्दो। | subtitles only |
| `doi` | डोगरी | 4:19 | इक होस्ट। इक्कारह सील कीते गेदे कदम। | subtitles only |
| `gu` | ગુજરાતી | 3:52 | એક હોસ્ટ. અગિયાર સીલબંધ પગલાં. | subtitles only |
| `kn` | ಕನ್ನಡ | 4:27 | ಒಂದು ಹೋಸ್ಟ್. ಹನ್ನೊಂದು ಸೀಲ್ ಮಾಡಿದ ಹಂತಗಳು. | narrated |
| `kok` | कोंकणी | 3:26 | एक होस्ट. इकरा सील केल्लीं पावलां. | subtitles only |
| `ks` | کٲشُر | 4:15 | اَکھ ہوسٹ۔ کاہنہ سِیل کٔرمٕژ قدم۔ | subtitles only |
| `mai` | मैथिली | 4:08 | एक होस्ट। एगारह सील कएल चरण। | subtitles only |
| `ml` | മലയാളം | 3:03 | ഒരു ഹോസ്റ്റ്. പതിനൊന്ന് സീൽ ചെയ്ത ഘട്ടങ്ങൾ. | subtitles only |
| `mni` | মৈতৈলোন্ | 3:30 | অমা হোস্ট। চামরুক সিল তৌবা মরোল। | subtitles only |
| `mr` | मराठी | 3:31 | एक होस्ट. अकरा सीलबंद पायऱ्या. | subtitles only |
| `ne` | नेपाली | 3:22 | एउटा होस्ट। एघार सिल गरिएका चरण। | subtitles only |
| `or` | ଓଡ଼ିଆ | 3:30 | ଏକ ହୋଷ୍ଟ। ଏଗାରଟି ସିଲ୍ ହୋଇଥିବା ପଦକ୍ଷେପ। | subtitles only |
| `pa` | ਪੰਜਾਬੀ | 4:11 | ਇੱਕ ਹੋਸਟ। ਗਿਆਰਾਂ ਸੀਲਬੰਦ ਕਦਮ। | subtitles only |
| `sa` | संस्कृतम् | 2:58 | एकः होस्ट्। एकादश मुद्रिताः पदाः। | subtitles only |
| `sat` | ᱥᱟᱱᱛᱟᱲᱤ | 4:05 | ᱢᱤᱫ ᱦᱳᱥᱴ᱾ ᱜᱮᱞ ᱢᱤᱫ ᱥᱤᱞ ᱠᱟᱱ ᱫᱷᱟᱯ᱾ | subtitles only |
| `sd` | سنڌي | 4:24 | هڪ هوسٽ. يارنهن سيل ٿيل قدم. | subtitles only |
| `ta` | தமிழ் | 4:12 | ஒரு ஹோஸ்ட். பதினொரு முத்திரையிடப்பட்ட படிகள். | narrated |
| `te` | తెలుగు | 4:22 | ఒక హోస్ట్. పదకొండు సీలు చేసిన దశలు. | narrated |
| `ur` | اردو | 4:40 | ایک ہوسٹ۔ گیارہ سیل بند مراحل۔ | subtitles only |
| `id` | Bahasa Indonesia | 4:32 | Satu host. Sebelas langkah tersegel. | narrated |
| `it` | Italiano | 3:47 | Un host. Undici passi sigillati. | narrated |
| `ko` | 한국어 | 4:17 | 호스트 하나. 봉인된 11단계. | narrated |
| `th` | ไทย | 4:46 | โฮสต์เดียว สิบเอ็ดขั้นตอนที่ปิดผนึก | narrated |
| `tr` | Türkçe | 4:13 | Tek host. On bir mühürlü adım. | narrated |
| `vi` | Tiếng Việt | 4:18 | Một máy chủ. Mười một bước niêm phong. | narrated |

## Playlist suggestion

Create one playlist — **"Kage DFIR triage — full walkthrough"** — and start it with the
English cut, then the major global narrations (`hi`, `ar`, `es`, `pt`, `ru`, `ja`, `de`,
`fr`, `zh`), then the remaining narrated locales, and finally the subtitle-only locales as
unlisted variants for search coverage. Add the English video as the playlist thumbnail
source and the tool + kit repository links in the playlist description.
