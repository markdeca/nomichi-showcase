<p align="center"><img src="media/logo.png" width="128" alt="Nomichi logo"></p>

<h1 align="center">Nomichi 野道</h1>

<p align="center"><i>Learn Japanese the way it is written: only the kanji you know, everything else in hiragana.</i></p>

**Nomichi** (野道, "a path through the fields") is a Flutter app for learning Japanese, for iOS and Android. A single **profile of the kanji you know** drives every screen: you see a kanji in a text only once you have studied it, and everything else stays in hiragana with furigana. The guided path, the reviews and the handwriting practice all follow that profile. It works offline, and the handwriting checks run on the device.

> 🇮🇹 *Nomichi (野道, "il sentiero fra i campi"): un'app per imparare il giapponese che mostra solo i kanji che conosci e scrive il resto in hiragana. Lezioni a dialoghi con traduzione letterale e naturale, rōmaji ovunque, scrittura col dito corretta tratto per tratto, ripasso a intervalli. Funziona offline.*

<p align="center">
  <img src="media/writing.gif" width="270" alt="Writing 日 with a finger: stroke-order animation, tracing, then writing from memory with a stroke-order mistake caught">
</p>

<p align="center">
  <img src="media/screen2.jpg" width="200" alt="Lesson 1 before studying any kanji: everything in hiragana">
  <img src="media/screen3.jpg" width="200" alt="The same lesson after studying 人 生 学 中: those kanji now appear, with furigana">
</p>
<p align="center"><sub>The same dialogue before and after studying the lesson's kanji. Words with kanji you don't know yet are underlined, and tapping them shows the original.</sub></p>

<p align="center">
  <img src="media/screen1.jpg" width="160" alt="The guided path with progress, kanji profile and writing sections">
  <img src="media/screen4.jpg" width="160" alt="The 79 N5 kanji grouped by the lesson that introduces them">
  <img src="media/screen5.jpg" width="160" alt="Writing 学 from memory">
  <img src="media/screen6.jpg" width="160" alt="Hiragana table">
  <img src="media/screen7.jpg" width="160" alt="Tracing あ: a stroke drawn in the wrong direction is caught">
  <img src="media/screen8.jpg" width="160" alt="Review card: write a in hiragana, graded automatically">
  <img src="media/screen9.jpg" width="160" alt="Grammar notes with rōmaji on every Japanese word">
</p>

<p align="center">▶️ <a href="media/demo.mp4"><b>Watch the 20-second handwriting demo</b></a></p>

---

## ✨ Features

- **A kanji profile instead of a "level"**: by default the app shows only the kanji you have studied in it ("My kanji"). You can also pick a JLPT level (N5, N4, N3) and see every kanji of that level. The check is done by code on every sentence, so a text never contains a kanji above your profile.
- **Guided path of dialogue lessons**: each line has furigana, rōmaji, a word-by-word literal translation and a natural Italian translation, and each can be hidden. Grammar notes come with examples, and every Japanese word in them carries its rōmaji. Polite forms come first (*dewa arimasen*), spoken and casual forms later.
- **Handwriting with the finger, checked stroke by stroke**: for every kana and kanji you watch the stroke order, trace over the model, then write from memory. The app tells you what went wrong: "stroke 3 goes the other way", "this is stroke 3, stroke 2 comes first". After two mistakes on the same stroke it shows a hint.
- **All the kana**: the 46 basic hiragana and katakana, voiced and semi-voiced sounds (が, ぱ), combinations (きゃ, しゅ), small っ and the long-vowel mark ー. There are pronunciation notes where Italian speakers usually stumble (し, つ, ふ, ら).
- **The 79 N5 kanji**: each one has its Italian meaning, its main on/kun readings in kana and rōmaji, and example words with furigana. They are introduced inside the lesson that first uses them.
- **Spaced repetition**: words, dialogue lines, kana and kanji go into an SM-2-style review. Reviews that are overdue come before new cards, and a session holds at most 20 cards. Writing cards are graded automatically from your mistakes.
- **"Second wave"**: five lessons later, an old lesson comes back in reverse. You see the Italian and say it in Japanese, and any line you miss goes straight back into today's review.
- **Audio** from the phone's Japanese voices, with a slow mode, for single lines or the whole dialogue.
- **Offline and private**: lessons, stroke data and handwriting checks are all on the device. There is no account and no tracking.

## 🛠 Tech stack

| Area | Technologies |
|---|---|
| App | Flutter / Dart, Material 3, custom painting for furigana and the writing pad |
| Language processing (build time) | Python, MeCab via fugashi + UniDic: word splitting, per-kanji readings, Hepburn rōmaji |
| Data | KANJIDIC (meanings, readings), JLPT kanji lists, KanjiVG stroke order (SVG → point sequences) |
| Storage | shared_preferences for profile, progress and review state, offline-first |
| Audio | Platform text-to-speech (AVSpeechSynthesizer, Android TTS) |
| Quality | Unit tests on every real lesson, kana and kanji (kanji limits, rōmaji, stroke recognition, scheduling) |

## 🧠 Engineering highlights

- **Level adaptation that can't leak**: lessons are written once in full Japanese. At build time each word is split into kanji with their own readings (食[た]べ物[もの]). At runtime the app replaces only the kanji outside your profile, and proper names are never left half-converted (no た中). A test runs every sentence of every lesson at every level and fails if a single unknown kanji gets through.
- **Handwriting recognition without ML**: each stroke is resampled to 24 equidistant points and compared with the KanjiVG reference by position, shape, direction and order. If you write the whole character a little off-centre, the offset is measured on the strokes already accepted and taken into account, so you aren't penalised. The thresholds were tuned against all 151 kana and 79 kanji. That exposed a real bug: two parallel strokes close together (き, 三, 電) could be accepted in the wrong order. Now a stroke that matches a later one much better is flagged.
- **Readings that a parser gets wrong**: UniDic reads お母さん as *ohaha-san* and splits 一万円. The pipeline uses MeCab's segmentation only when its reading agrees with the curated one. Otherwise it aligns the curated reading kanji by kanji, and the rōmaji comes out with Hepburn long vowels (*Tōkyō*, *okāsan*, *gakkō*).
- **Rōmaji is enforced**: every Japanese fragment inside the Italian grammar explanations is annotated automatically (は → は (wa)). A test fails if a single one is missing.
- **Typographic details**: furigana keeps its space even when hidden, so lines don't jump. Closing punctuation never starts a line (kinsoku), and stroke numbers don't overlap when two strokes share a starting point (日).

## 📱 Status

Nomichi is in active development. It runs on iOS and Android, and comes with the full kana, the 79 N5 kanji and the first 10 N5 lessons. Next comes conversation with on-device AI (Apple Foundation Models, Gemini Nano), with replies held to the kanji profile, then the rest of N5. Publication on the App Store and Google Play is planned.

## 🙏 Data credits

- Stroke order: [KanjiVG](http://kanjivg.tagaini.net) © Ulrich Apel, CC BY-SA 3.0
- Kanji meanings and readings: [KANJIDIC](http://www.edrdg.org/wiki/index.php/KANJIDIC_Project), EDRDG, CC BY-SA 4.0
- JLPT levels: [Jonathan Waller's JLPT Resources](http://www.tanos.co.uk/jlpt/), CC BY
- Morphological analysis: MeCab with the UniDic dictionary

---

🔒 *The source code is private. It is available on request for recruiters and collaborators.*
