<p align="center"><img src="media/logo.png" width="128" alt="Nomichi logo"></p>

<h1 align="center">Nomichi 野道</h1>

<p align="center"><i>Learn Japanese the way it is written: only the kanji you know, everything else in hiragana.</i></p>

**Nomichi** (野道, "a path through the fields") is a Flutter app for learning Japanese, for iOS and Android. A single **profile of the kanji you know** drives every screen: you see a kanji in a text only once you have studied it, and everything else stays in hiragana with furigana. The guided path, the reviews and the handwriting practice all follow that profile. It works offline, and the handwriting checks run on the device. The look follows the language you study: for Japanese, washi paper, bamboo and a landscape with Mount Fuji on the home screen.

> 🇮🇹 *Nomichi (野道, "il sentiero fra i campi"): un'app per imparare il giapponese che mostra solo i kanji che conosci e scrive il resto in hiragana. 40 lezioni a dialoghi su tutto il vocabolario N5, con traduzione letterale e naturale e rōmaji ovunque; grammatica sempre consultabile, esercizi da completare scrivendo, scrittura col dito corretta tratto per tratto, ripasso a intervalli e conversazione con l'IA del telefono. Funziona offline.*

<p align="center">
  <img src="media/writing.gif" width="270" alt="Writing 日 with a finger: stroke-order animation, tracing, then writing from memory with a stroke-order mistake caught">
</p>

<p align="center">
  <img src="media/screen2.jpg" width="200" alt="Lesson 1 at level N5: N5 kanji with furigana, 会社員 and 田中 in highlighted hiragana">
  <img src="media/screen3.jpg" width="200" alt="The same lesson at level N4: 会社員 and 田中 now appear in kanji">
</p>
<p align="center"><sub>The same dialogue at level N5 and at level N4. Words whose kanji you don't know yet get a soft yellow highlight, and tapping them shows the original.</sub></p>

<p align="center">
  <img src="media/screen1.jpg" width="160" alt="Home: the app name in a Japanese landscape, next lesson, kanji profile, conversation and grammar">
  <img src="media/screen9.jpg" width="160" alt="Lesson 14 with the N5 profile: kanji with furigana, rōmaji, literal and natural translation">
  <img src="media/screen7.jpg" width="160" alt="Fill-in exercise: the missing particle typed in rōmaji and checked">
  <img src="media/screen8.jpg" width="160" alt="N5 grammar guide with search, rōmaji on every Japanese word">
  <img src="media/screen4.jpg" width="160" alt="The 79 N5 kanji grouped by the lesson that introduces them">
  <img src="media/screen5.jpg" width="160" alt="Stroke order of 日, with a button to hear its readings">
  <img src="media/screen6.jpg" width="160" alt="Hiragana table">
</p>

<p align="center">▶️ <a href="media/demo.mp4"><b>Watch the 20-second handwriting demo</b></a></p>

---

## ✨ Features

- **Texts that follow your level**: you pick a JLPT level (N5, N4, N3) and every text shows only the kanji of that level, plus any kanji you have studied in the app. The check is done by code on every sentence, so a text never contains a kanji above your profile.
- **40 N5 lessons covering the whole N5 vocabulary** (717 words, measured by a script against the JLPT lists): each dialogue line has furigana, rōmaji, a word-by-word literal translation and a natural Italian translation, and each can be hidden. Grammar notes come with examples, and every Japanese word in them carries its rōmaji. Polite forms come first (*dewa arimasen*), spoken and casual forms later; texts grow longer lesson after lesson, up to a diary written in plain form.
- **Grammar guide, always at hand**: every grammar point of the course on one page, searchable in Italian, Japanese or rōmaji ("te kudasai", "passato", "から").
- **Fill-in exercises**: at the end of each lesson, ten sentences with a missing word or particle to type in rōmaji, kana or kanji. Long vowels are forgiven (*tokyo* counts for *Tōkyō*), and the solution shows reading, rōmaji and audio.
- **Conversation with Hana**, on-device AI (Apple Foundation Models on iPhone): talk about a topic at your level or freely; Hana's replies are shown with your kanji profile, furigana and rōmaji.
- **Handwriting with the finger, checked stroke by stroke**: for every kana and kanji you watch the stroke order, trace over the model, then write from memory. The app tells you what went wrong: "stroke 3 goes the other way", "this is stroke 3, stroke 2 comes first". After two mistakes on the same stroke it shows a hint.
- **All the kana**: the 46 basic hiragana and katakana, voiced and semi-voiced sounds (が, ぱ), combinations (きゃ, しゅ), small っ and the long-vowel mark ー. There are pronunciation notes where Italian speakers usually stumble (し, つ, ふ, ら).
- **The 79 N5 kanji**: each one has its Italian meaning, its main on/kun readings in kana and rōmaji, and example words with furigana. They are introduced inside the lesson that first uses them.
- **Spaced repetition**: words, dialogue lines, kana and kanji go into an SM-2-style review. Reviews that are overdue come before new cards, and a session holds at most 20 cards. Writing cards are graded automatically from your mistakes.
- **"Second wave"**: five lessons later, an old lesson comes back in reverse. You see the Italian and say it in Japanese, and any line you miss goes straight back into today's review.
- **Audio** from the phone's Japanese voices, with a slow mode, for single lines or the whole dialogue. Single words and kanji are read from their kana reading, so 何 is never read as *ka*.
- **Offline and private**: lessons, stroke data and handwriting checks are all on the device. There is no account and no tracking.

## 🛠 Tech stack

| Area | Technologies |
|---|---|
| App | Flutter / Dart, Material 3, custom painting for furigana and the writing pad |
| Language processing (build time) | Python, MeCab via fugashi + UniDic: word splitting, per-kanji readings, Hepburn rōmaji |
| Data | KANJIDIC (meanings, readings), JLPT kanji lists, KanjiVG stroke order (SVG → point sequences) |
| Storage | shared_preferences for profile, progress and review state, offline-first |
| Audio | Platform text-to-speech (AVSpeechSynthesizer, Android TTS) |
| On-device AI | Apple Foundation Models via a Swift platform channel, guided generation; iOS tokenizer for the replies |
| Quality | Unit tests on every real lesson, kana and kanji (kanji limits, rōmaji, stroke recognition, scheduling) |

## 🧠 Engineering highlights

- **Level adaptation that can't leak**: lessons are written once in full Japanese. At build time each word is split into kanji with their own readings (食[た]べ物[もの]). At runtime the app replaces only the kanji outside your profile, and proper names are never left half-converted (no た中). A test runs every sentence of every lesson at every level and fails if a single unknown kanji gets through.
- **Handwriting recognition without ML**: each stroke is resampled to 24 equidistant points and compared with the KanjiVG reference by position, shape, direction and order. If you write the whole character a little off-centre, the offset is measured on the strokes already accepted and taken into account, so you aren't penalised. The thresholds were tuned against all 151 kana and 79 kanji. That exposed a real bug: two parallel strokes close together (き, 三, 電) could be accepted in the wrong order. Now a stroke that matches a later one much better is flagged.
- **Readings that a parser gets wrong**: UniDic reads お母さん as *ohaha-san* and splits 一万円. The pipeline uses MeCab's segmentation only when its reading agrees with the curated one. Otherwise it aligns the curated reading kanji by kanji, and the rōmaji comes out with Hepburn long vowels (*Tōkyō*, *okāsan*, *gakkō*).
- **Numbers and counters**: 三本 is *sanbon*, 六本 *roppon*, 二十日 *hatsuka*, 二人 *futari*, 何本 *nanbon*. A dedicated pass rebuilds number + counter readings with every sound change, because a parser reads the counter without looking at the number.
- **Vocabulary coverage as a test**: a script compares all lesson text with the JLPT word lists (dictionary forms, compounds, kana spellings) and reports what is still missing; N5 is at 717/717.
- **Rōmaji is enforced**: every Japanese fragment inside the Italian grammar explanations is annotated automatically (は → は (wa)). A test fails if a single one is missing.
- **Typographic details**: furigana keeps its space even when hidden, so lines don't jump. Closing punctuation never starts a line (kinsoku), and stroke numbers don't overlap when two strokes share a starting point (日).

## 📱 Status

Nomichi is in active development. It runs on iOS and Android, with the full kana, the 79 N5 kanji, all 40 N5 lessons, the grammar guide and the exercises. Conversation with on-device AI works on iPhone; Android support (Gemini Nano or an online model) comes next, followed by the N4 and N3 courses. Publication on the App Store and Google Play is planned.

## 🙏 Data credits

- Stroke order: [KanjiVG](http://kanjivg.tagaini.net) © Ulrich Apel, CC BY-SA 3.0
- Kanji meanings and readings: [KANJIDIC](http://www.edrdg.org/wiki/index.php/KANJIDIC_Project), EDRDG, CC BY-SA 4.0
- JLPT levels and vocabulary lists: [Jonathan Waller's JLPT Resources](http://www.tanos.co.uk/jlpt/), CC BY
- Morphological analysis: MeCab with the UniDic dictionary

---

🔒 *The source code is private. It is available on request for recruiters and collaborators.*
