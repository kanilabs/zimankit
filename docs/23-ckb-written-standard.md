<!-- nav:top -->
[← Sentence structure](22-sentence-structure.md) · [⌂ Contents](00-index.md)
<!-- nav:top -->

# The Central Kurdish written standard

**On this page:** [Letters and sounds](#letters-and-sounds) · [Ezafe and the three ی](#ezafe-and-the-three-ی) · [Verbs: the third person](#verbs-the-third-person) · [Word spacing](#word-spacing) · [Agent clitic placement](#agent-clitic-placement) · [Punctuation](#punctuation) · [Where the sources disagree](#where-the-sources-disagree) · [Not implemented yet](#not-implemented-yet)

This chapter records the Central Kurdish spelling and grammar rules that zimankit is checked against, how much of each is implemented, and where the authorities disagree. The sources are listed in [References](21-references.md). Coverage of them is uneven: the spelling standard and the verb chapters were read closely, while several school books and handouts were read only in part.

Two ideas run through it:

- **Corpus evidence decides what zimankit recognises, and the standard decides what it recommends.** News text prefers some non-standard forms (`وەکو` over `وەکوو`, `ڕۆژئاوا` over `ڕۆژاوا`), so frequency cannot choose the standard form. Those forms stay in the lexicon, and the validator flags them where it can.
- **Solid compound verbs are not errors.** The standard prefers a space, but the Academy is reported to allow solid forms and they are common. Accept both.

## Letters and sounds

| rule | write | not | zimankit |
|---|---|---|---|
| Kurdish code points | `ک` U+06A9, `ی` U+06CC, `ە` U+06D5 | `ك ي ة` | normalised |
| `ە` and `ه` are different letters | `سەر` | `سهر`, `سه ر` | normalised where safe |
| `ڕ` and `ر` are different letters, and every initial r is `ڕ` | `ڕاست`, `کوڕ` | `راست` | initial `ر` normalised; elsewhere needs the lexicon |
| `ئ` before an initial vowel | `ئاو`, `ئەو` | `او`, `ەو` | normalised |
| `ئ` drops inside compounds | `ڕۆژاوا`, `گوڵاو`, `ماڵاوایی` | `ڕۆژئاوا`, `گوڵئاو` | `validate_word` for the listed words; common forms stay recognised |
| `وو` is never initial | `وشە`, `ورچ` | `ووشە`, `وورچ` | `validate_word` |
| final û after a consonant is `وو` | `چوو`, `مێژوو`, `وەکوو` | `چو`, `وەکو` | `validate_word` |
| medial û is `وو` | `کەرکووک`, `حوکوومەت` | `کەرکوک` | `validate_word` for the listed words |
| plain î is one `ی` | `تیر`, `کوردی` | `تییر`, `کوردیی` as the bare word | not flagged; needs the lexicon |
| `یی` for î after a vowel | `کۆتایی`, `سنەیی` | `کۆتای` | generated correctly by the morphology |
| `یە` is written solid | `سپییە`, `چییە`, `نییە`, `مامۆستایە` | `سپی یە`, `سپی‌یە` | ZWNJ form normalised |
| a ی-final word keeps its ی before `یە` | `سپییە`, `کوردییە` | `سپیە`, `کوردیە` | `validate_word(word, known)` with a lexicon; the builder lists `سپیە` as a variant when both spellings occur |
| ی as y is not doubled | `ژیان` | `ژییان` | `validate_word` |
| the `ت` stays | `دەستەسڕ`, `ماستاوچی` | `دەسەسڕ` | not flagged |
| Latin values | `wişe`, `mirdin`, `stran`, `mindall` | | `to_latin` |

There are 37 sounds and 34 letters. The bizroke `i` is a real short vowel that the Arabic-based alphabet does not write. `برا` is `bira`, in Kurmanji too, even though one syllable table prints it as `bra`.

## Ezafe and the three ی

The ezafe `ی` is written even after a word that ends in `ی` or `یی`:

| the word ends in | form | example |
|---|---|---|
| no `ی` | `ی` | `کوردی سنە` "the Kurd of Sanandaj" |
| `ی` | `یی` | `کوردیی سنە` "the Kurdish of Sanandaj", `ڕۆژی جیهانیی ژن` |
| `یی` | `ییی` | `سنەییی دڵسۆز`, `ڕەشاییی چاو` |

`decline` generates these and `normalize` now keeps them. One author reports that most people reject three `ی`, so zimankit does not warn against `یی` here.

## Verbs: the third person

The third person of a transitive past whose stem ends in `ی` needs a second `ی`, and intransitive verbs do not. zimankit generates these correctly:

| verb type | forms |
|---|---|
| transitive | `نووسیم`, `نووسیت`, `نووسیی`, `نووسییان` |
| intransitive | `فڕیم`, `فڕییت`, `فڕی`, `فڕیین` |

The passive adds `د` after a root in `ر` or `ڕ`: `دەخوێنرێت`, but `دەکڕدرێت`, `دەنێردرێت`.

## Word spacing

| rule | write | not | zimankit |
|---|---|---|---|
| compound words are solid | `دڵپاک`, `مارماسی`, `گوڵەگەنم`, `دەموچاو`, `شارەوشار` | `دڵ پاک` | not checked |
| adjective after ە is separate | `کچە جوانەکە`, `شەوە گەرمەکە` | `کچەجوانەکە` | not checked |
| compound infinitives are solid | `نانکردن`, `شەڕکردن`, `پێکهێنان` | `نان کردن` | not checked |
| conjugated compounds split | `نان دەکەم`, `نانم کرد`, `نانم کردووە` | `نانمکرد` (see below) | read when split; pages show it split |
| preverb, present is solid | `هەڵدەگرم` | `هەڵ دەگرم` | generated |
| `دەر`, `پێک`, `تێک`, `ڕێک` are separate in every tense | `دەر دەچم`, `دەر چووم`, `پێک دێن`, `تێک دەچم` | `دەردەچم` (see below) | generated; solid accepted |
| preverb, split around a clitic | `هەڵم گرت`, `ڕای گەیاند` | `ڕای‌گەیاند` | read when split; pages show it split |
| four parts | `پێ لێ هەڵگرتن`, `پێی لێ هەڵگرت` | | not implemented |
| infinitive or finite verb | `ڕێککەوتن` (noun), `ڕێک کەوتن` (verb) | | not checked |
| conjunction `و` is separate | `من و تۆ` | `من‌و تۆ` | ZWNJ form normalised |
| prepositions are separate, postpositions attach | `لە ماڵدا`, `لە کرماشانەوە`, `بە ئاسمانەوە` | `لەماڵدا`, `لە ماڵ دا` | not checked |
| `دا` as a postposition attaches, even before a copula | `من لە ماڵدام`, `لە ماڵدایە` | `لە ماڵ دایە` | analysed |
| `دا` as the verb "gave" is separate | `نامەکەم دا بە سۆران` | | handled |
| `لە` joins in fixed forms | `لەگەڵ`, `لەسەر`, `لەژێر`, `لێرە`, `لەوێ` | `لە گەڵ` | not checked |
| `بێ` attached means "-less", separate means "without" | `بێکار` "unemployed", `بێ کار ژیان` | | analysed |
| comparative attaches, the word "another" is separate | `جوانتر`, `جوانترین`; `شەوێکی تر` | | analysed |
| `پێ تێ لێ` are separate, and attach to a clitic | `پارەم پێ نەما`, `دەستم لێ دا`; `پێم وت`, `لێم سەند` | `پارەم پێنەما` | analysed |
| manner `بە` attaches, preposition `بە` is separate | `بەخێرایی`, `بەهێواشی`; `بە ئۆتۆمبیل` | `بە خێرایی` | not analysed |
| compound numbers are separate | `بیست و یەک`, `دوو سەد`, `سێ میلیۆن` | `بیستویەک` | not checked |
| number + noun as a new word is solid | `دووشەممە`, `یەکشاخ`, `چوارپێ` | | lexicon |
| `ئەم` + time word is solid, unless the noun has an ending | `ئەمڕۆ`, `ئەمساڵ`; `ئەم جارە` | `ئەمجارە` | not checked |

Other preferred spellings: `بەرانبەر` (not `بەرامبەر`), `لێبووردن`, `مشتومڕ`, `کشتوکاڵ`, `ئەلفبێ`, `پێم وایە`, `دەست خۆش`, `تەمەندرێژ`, and `دە-` in preference to `ئە-` (`دەڕۆم`).

From Hashemi's ڕێنووس pages, not checked by zimankit: split `لانی کەم`, `هەموو جارێ`, `هەر وەک`, `شەو شاد`, `وام زانی`; solid `بەپێی`, `بەدوای`, `دڵناسک`, and the nouns `لەدایکبوون`, `چوونەژوورەوە`; `لێوئاڵ` keeps its `ئ` because the stress falls there; `هاتوچۆ` is preferred and `هاتووچۆ` accepted; `لەسەر` is solid, and split only when `سەر` means "head" (`بەردێکی لە سەری دا`).

## Agent clitic placement

The grade 12 handout and ڕێزمانی وێنەیی agree on this rule:

1. With a stated object, the clitic goes on the object, whatever the verb type: `نانم کرد`.
2. Otherwise it goes on the first part of a compound or preverb verb, in every tense and polarity: `ڕامان خست`, `ڕامان نەخست`, `ڕامان دەخست`, `ڕامان خستووە`.
3. On a simple verb it follows `نە` or `دە`, sits inside the perfect, or ends the verb.

zimankit follows all three rules. It still recognises the older shapes `هەڵنەمگرت`, `هەڵدەمگرت` and `هەڵگرتوومە` as a variant.

## Punctuation

From Hashemi's punctuation page:

- A mark attaches to the word before it and is followed by a space: `بەڵێ، دێم.`
- Use the Arabic-script marks `،` `؛` `؟`. `normalize` maps `,` `;` `?` to them.
- Do not stack a question mark and a full stop (`؟.`). Headings take no full stop.
- Put a comma after a vocative, after `بەڵێ`/`نەخێر` and after an interjection: `کچینە، هەوڵ بدەن`, `ئازاد، کتێبەکە داخە.`
- Put a comma before `یان` when it joins clauses, but not before the last `و` of a list.
- An ellipsis is exactly three dots.

zimankit checks none of these except the mark mapping.

## Where the sources disagree

These are open, so zimankit should not change them silently. The interim choice is in the last column.

| point | positions | interim |
|---|---|---|
| preverb + clitic | split in the Academy pamphlets (2010: `هەڵم گرت`, `پێم گەیشت`, `وەرم گرت`); solid allowed according to Hashemi's FAQ, which is the only evidence for that allowance; both printed in school material | accept both, prefer split |
| compound preverb in the infinitive | solid in Hashemi (`پێکهاتن`); split in the 2010 pamphlet (`لێک دابڕان`, `پێکەوە هاتن`) | keep the infinitive solid |
| conjugated compounds, present | split in Hashemi and zkurd.org; solid in the grade 12 handout | as above |
| `تێکچوو` | split in zkurd.org; solid in the 2010 pamphlet | prefer split |
| `دەر` in finite forms | split in Hashemi's grammar (`دەر دەچێت`, `دەر چووم`); solid `دەردەچم` is common in print | generate split, accept solid |
| `ئەمجارە` | `ئەم جارە` in Hashemi; solid in the 2004 pamphlet | do not flag |
| `حەوت سەد`, `ئەلفبێ` | `حەوتسەد` in the 2010 pamphlet; `ئەلفوبێ` in the 2004 pamphlet | prefer Hashemi, warn only: `validate_word` names `حەوت سەد` and `ئەلفبێ`; the spaced `ئەلف و بێ` is not checked |
| clitic after `نە`/`دە` on a simple verb | spaced `نەم هێنا`, `دەمان سڕی` in school material; solid `دەمانخوێند`, and solid in the 2010 pamphlet (`دەمگرت`, `دەمانگرت`, `دەیانگرت`) | keep solid |
| plural of ە-final nouns | `قوتابخانان`, `پەنجەران`, `دەرگەیان` | recognise, not generated |
| definite after a vowel | by syllable count, both `چراکە` and `چرایەکە`, or by final letter | by final letter |
| negative past subjunctive with a clitic | not printed in the sources checked; `نەمخواردبا` follows the clitic rule, `نەخواردبام` is the alternative | generate the first, accept both |
| 3sg pluperfect + ەوە | `دۆزیبووەوە` or `هێنابوویەوە` | the first |
| imperative + ەوە | `بکەرەوە` or `بکەوە` | show `بکەوە`, accept both |
| `هاتن` negative | `نایەم`, `مەیە` or `نایێم`, `مەیێ` | show the common forms, accept both |
| `چوون` imperative | `بچۆ` in the sources; `بڕۆ` is also used | show `بچۆ`, accept both |
| passive after ر | `د` inserted, or `سپێررا`, `برا` regionally | keep `د` |
| `ماسییەکە` or `ماسێکە` | | generate the first, accept both |
| vocative | `ە` masculine, `ێ` feminine (`کچێ`, ڕێنووس و خاڵبەندی), `ینە` plural (`کچینە`, Hashemi's punctuation page), or `ە` for both | recognise all three, not generated |
| number + plural noun, `پیاوەکە هاتن` | common usage; no explicit rule found | `پیاوەکە هاتن` is a usage note, not an error |
| `هەبوون` | only `هەم`, `هەیە`, `هەبوو`, `دەبێت`, `دەبوو`, `دەبووایە` are in the pages read; the rest of the paradigm has no source | not added as a core verb |
| terminology | `هاوەڵناو`/`ئاوەڵناو`, `جێناو`/`ڕاناو`, `کار`/`کردار` | list both |
| ی after `دوا`, `مشک` as `mişk` or `mişik` | one example each, or regional | not changed |

## Not implemented yet

In the order they depend on each other:

1. **Parser:** circumpositions over several words with no verb, relative clauses with no کە, compound verbs outside the core list.
2. **Documents and lexicon:** the wordlist stores solid keys, and `standard_spelling` works on verbs from the paradigm, not on running text, so zimankit cannot yet rewrite a text into the split spelling.

Some transliterations are also still doubtful and have not been checked against a source, such as `rakridin` for `ڕاکردن` and `hellîgrit` for `هەڵیگرت`.

## Regional variation

Standard Central Kurdish is close to the speech of Sulaymaniyah but does not match any single subdialect (Ahmadi et al. 2024, the CORDI corpus). Their tables show where the subdialects differ from the standard. zimankit handles some of these differences:

| feature | Sulaymaniyah | Sanandaj | Erbil | Mahabad | in zimankit |
|---|---|---|---|---|---|
| progressive | `ئە-` | `ئە-` | `دە-` | `دە-` | both; `ئە-` gets `variant=dialect_ae` |
| particle | `-ەوە`, `-ەوانێ` | `-ەو` | `-ەوە` | `-ەوە` | all three; `particle_variant` |
| plural | `-ان` | `-گەل` | `-ان` | `-ان` | both |
| indefinite | `-ێک` | `-ێک` | `-ەک` | `-ێک` | both |
| oblique `-ی`/`-ێ` | no | no | yes | yes | not marked |
| past agent and object | `بردیانم` | `بردمیان` | `بردیانم` | `بردیانم` | standard order only |

Some words also differ: `ziman`/`ziwan` "language", `kewtin`/`keftin` "fall", `jinan`/`jingel` "women" (their Table 3). The sound changes they list, for example `دڵ` pronounced `dir` in Erbil, are not written, so they do not affect text processing.

<!-- nav:bottom -->
---
[← Sentence structure](22-sentence-structure.md) · [⌂ Contents](00-index.md)
<!-- nav:bottom -->
