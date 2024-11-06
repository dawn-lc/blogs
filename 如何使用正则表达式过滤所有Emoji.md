```
    const emojiSeq = String.raw`(?:\p{Emoji}\uFE0F\u20E3?|\p{Emoji_Modifier_Base}\p{Emoji_Modifier}?|\p{Emoji_Presentation})`;
    const emojiSTags = String.raw`\u{E0061}-\u{E007A}`;
    const emojiRegex = new RegExp(String.raw`[\u{1F1E6}-\u{1F1FF}]{2}|\u{1F3F4}[${emojiSTags}]{2}[\u{E0030}-\u{E0039}${emojiSTags}]{1,3}\u{E007F}|${emojiSeq}(?:\u200D${emojiSeq})*`, 'gu');

    String.prototype.replaceEmojis = function (replace) {
        return this.replaceAll(emojiRegex, replace ?? '');
    }
```
使用方法：
```
"包含Emoji的字符串".replaceEmojis("替换Emoji的字符串");
```
出处：
https://github.com/slevithan/emoji-regex-xs
