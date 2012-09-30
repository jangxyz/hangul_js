
Hangul.js
---------

Javascript libary to work with Hangul Unicode. Designed to work for both HANGUL LETTER(a.k.a 2-bul) and HANGUL CHOSEONG families(a.k.a 3-bul) in harmony.

## Functions

* Helper functions: `ord`, `chr`
* Test functions: `isHangul3Choseong`, `isHangul3Jungseong`, `isHangul3Jongseong`, `isHangulDot`, `isHangul2Letter`, `isHangul2Consonant`, `isHangul2Vowel`, `isHangulSyllable`, `isHangul`, `hasJongseong`
* Convert: `consonantToChoseong`, `consonantToJongseong`, `vowelToJungseong`, `choseongToConsonant`, `jongseongToConsonant`, `jungseongToVowel`
* Merge & Split: `merge3`, `merge`, `split`
* Concatenation: `attachIfHangul`


## Examples

```javascript
> var h = require('./hangul');
> var ord = h.ord,
      chr = h.chr;
```

### Unicode
```javascript
> ord('한')
54620
> ord('ㅎ')
12622

> chr(54620)                // 'HANGUL SYLLABLE HAN'
'한'
> chr(12622)                // 'HANGUL LETTER HIEUH'
'ㅎ'
> chr(4370)                 // 'HANGUL CHOSEONG HIEUH'
'ㅎ'
> chr(12622) == chr(4370)   // not the same
false
```

### Test
You may test any kind of Hangul, or 2-bul family and 3-bul family, respectively.

```javascript
> h.isHangul('한글')
true
> h.isHangulSyllable('한')
true

// 2-bul (consonant and vowel)
> h.isHangul2Letter('ㅎ')
true
> h.isHangul2Vowel('ㅏ')
true
> h.isHangul2Consonant('ㄴ')
true

// 3-bul (choseong, jungseong and jongseong)
> h.isHangul3Choseong('ㅎ')  
false
> h.isHangul3Choseong(chr(4370))
true
```

A special method to check if letter has a jongseong.
```javascript
> h.hasJongseong('한')
true
```

### Conversion
Convert between 2-bul family and 3-bul family. Note some letters are on one-side only, and may not be converted.
```javascript
> chr(12622)    // 'HANGUL LETTER HIEUH'
'ㅎ'
> chr(4370)     // 'HANGUL CHOSEONG HIEUH'
'ㅎ'
> ord(h.choseongToConsonant(chr(4370)))
12622
```

### Merge & Split
Split a single letter into choseong, jungseong and jongseong.
```javascript
> split = h.split('한')
[ 'ᄒ', 'ᅡ', 'ᆫ' ]
> h.isHangul3Choseong(split[0])
true
> h.isHangul2Consonant(split[0])
false
```

And merge back to a single letter. You may also use 2-bul families too.
```javascript
> h.merge(split[0], split[1], split[2])
'한'
> h.merge('ㄱ', 'ㅡ', 'ㄹ')
'글'
> h.merge('한', '글', '짱')     // taking parts from each syllables
'흥'
```


### Attach
Concatenating two strings, but merging Hangul parts.
```javascript
> h.attachIfHangul('하', 'ㄴ')
'한'
> h.attachIfHangul('한', 'ㄱ')
'한ㄱ'
> h.attachIfHangul('한ㄱ', 'ㅡ')
'한그'

> d = h.attachIfHangul('도깨비', 'ㅂ')
'도깨빕'
> h.attachIfHangul(h.attachIfHangul(d, 'ㅜ'), 'ㄹ')
'도깨비불'

> var text = 'ㅎㅏㄴㄱㅡㄹ ㄷㅏ ㄲㅐㅈㅣㅁ ㅠㅠ'
> var sentence = '';
> for(var i=0; i<text.length; i++) 
    sentence = h.attachIfHangul(sentence, text[i])
> sentence
'한글 다 깨짐 ㅠㅠ'
```
Note that attaching a vowel to Hangul syllable that has jongseong takes the jongseong away from the front letter and makes it as a choseong of the second letter. This is known as `도깨비불 현상', and only happens on 2-bul family because you cannot determine whether a given consonant is a choseong or jongseong.


```javascript
> var h1 = chr(4370)    // 'HANGUL CHOSEONG HIEUH'
> var a2 = chr(4449)    // 'HANGUL JUNGSEONG A'
> var n3 = chr(4523)    // 'HANGUL JONGSEONG NIEUN'
> h.attachIfHangul('하', n3) 
'한'
> h.attachIfHangul(a2, h1)
'하'
> h.attachIfHangul('하', h1)
'하ᄒ'

> var b1 = chr(4359)    // 'HANGUL CHOSEONG PIEUP'
> var u2 = chr(4462)    // 'HANGUL JUNGSEONG U'
> var b3 = chr(4536)    // 'HANGUL CHOSEONG PIEUP'
> d = h.attachIfHangul('도깨비', b1)
'도깨비ᄇ'
> h.attachIfHangul(d, u2)
'도깨비부'

> d = h.attachIfHangul('도깨비', b3)
'도깨빕'
> h.attachIfHangul(d, u2)
'도깨빕ㅜ'

```
The `도깨비불 현상' does not appear on 3-bul family.


