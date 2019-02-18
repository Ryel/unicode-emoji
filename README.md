# Unicode::Emoji [![[version]](https://badge.fury.io/rb/unicode-emoji.svg)](https://badge.fury.io/rb/unicode-emoji)  [![[travis]](https://travis-ci.org/janlelis/unicode-emoji.svg)](https://travis-ci.org/janlelis/unicode-emoji)

A small Ruby library which provides Unicode Emoji data and regexes.

Also includes a categorized list of recommended Emoji.

Emoji version: **11.0**

Supported Rubies: **2.6**, **2.5**, **2.4**, **2.3**

If you are stuck on an older Ruby version, checkout the latest [0.9 version](https://rubygems.org/gems/unicode-emoji/versions/0.9.3) of this gem.

## Gemfile

```ruby
gem "unicode-emoji"
```

## Usage

### Regex

Six Emoji regexes are included, which are compiled out of various Emoji Unicode data.

```ruby
require "unicode/emoji"

string = "String which contains all kinds of emoji:

- Singleton Emoji: 😴
- Textual singleton Emoji with Emoji variation: ▶️
- Emoji with skin tone modifier: 🛌🏽
- Region flag: 🇵🇹
- Sub-Region flag: 🏴󠁧󠁢󠁳󠁣󠁴󠁿
- Keycap sequence: 2️⃣
- Sequence using ZWJ (zero width joiner): 🤾🏽‍♀️

"

string.scan(Unicode::Emoji::REGEX) # => ["😴", "▶️", "🛌🏽", "🇵🇹", "🏴󠁧󠁢󠁳󠁣󠁴󠁿", "2️⃣", "🤾🏽‍♀️"]
```

Regex                         | Description | Example Matches | Example Non-Matches
------------------------------|-------------|-----------------|--------------------
`Unicode::Emoji::REGEX`       | **Use this if unsure!** Matches (non-textual) singleton Emoji (except for singleton components, like a skin tone modifier without base Emoji) and all kind of *recommended* Emoji sequences | `😴`, `▶️`, `🛌🏽`, `🇵🇹`, `2️⃣`, `🏴󠁧󠁢󠁳󠁣󠁴󠁿`, `🤾🏽‍♀️` | `😴︎`, `▶`, `🏻`, `🇵🇵`, `🏴󠁧󠁢󠁡󠁧󠁢󠁿`, `🤠‍🤢`
`Unicode::Emoji::REGEX_VALID` | Matches (non-textual) singleton Emoji (except for singleton components, like a skin tone modifier without base Emoji) and all kind of *valid* Emoji sequences | `😴`, `▶️`, `🛌🏽`, `🇵🇹`, `2️⃣`, `🏴󠁧󠁢󠁳󠁣󠁴󠁿`, `🏴󠁧󠁢󠁡󠁧󠁢󠁿`, `🤾🏽‍♀️`, `🤠‍🤢` | `😴︎`, `▶`, `🏻`, `🇵🇵`
`Unicode::Emoji::REGEX_WELL_FORMED` | Matches (non-textual) singleton Emoji (except for singleton components, like a skin tone modifier without base Emoji) and all kind of *well-formed* Emoji sequences | `😴`, `▶️`, `🛌🏽`, `🇵🇹`, `2️⃣`, `🏴󠁧󠁢󠁳󠁣󠁴󠁿`, `🏴󠁧󠁢󠁡󠁧󠁢󠁿`, `🤾🏽‍♀️`, `🤠‍🤢`,  `🇵🇵` | `😴︎`, `▶`, `🏻`
`Unicode::Emoji::REGEX_BASIC` | Matches (non-textual) singleton Emoji (except for singleton components, like a skin tone modifier without base Emoji), but no sequences at all | `😴`, `▶️` | `😴︎`, `▶`, `🏻`, `🛌🏽`, `🇵🇹`, `🇵🇵`,`2️⃣`, `🏴󠁧󠁢󠁳󠁣󠁴󠁿`, `🏴󠁧󠁢󠁡󠁧󠁢󠁿`, `🤾🏽‍♀️`, `🤠‍🤢`
`Unicode::Emoji::REGEX_TEXT`  | Matches only textual singleton Emoji (except for singleton components, like digit 1) | `😴︎`, `▶` | `😴`, `▶️`, `🏻`, `🛌🏽`, `🇵🇹`, `🇵🇵`,`2️⃣`, `🏴󠁧󠁢󠁳󠁣󠁴󠁿`, `🏴󠁧󠁢󠁡󠁧󠁢󠁿`, `🤾🏽‍♀️`, `🤠‍🤢`
`Unicode::Emoji::REGEX_ANY`   | Matches any Emoji-related codepoint (but no variation selectors, tags, or zero-width joiners). Please not that this will match Emoji-parts rather than complete Emoji, for example, single digits! | `😴`, `▶`, `🏻`, `🛌`, `🏽`, `🇵`, `🇹`, `2`, `🏴`, `🤾`, `♀`, `🤠`, `🤢` | -


#### Picking the Right Emoji Regex

- Usually you just want `REGEX` (RGI set)
- If you want broader matching (e.g. more sub-regions), choose `REGEX_VALID`
- If you even want to match for invalid sequences, too, use `REGEX_WELL_FORMED`

Please see [the standard](http://www.unicode.org/reports/tr51/#Emoji_Sets) for details.

Property | `REGEX` (RGI / Recommended) | `REGEX_VALID` (Valid) | `REGEX_WELL_FORMED` (Well-formed)
---------|-----------------------------|-----------------------|----------------------------------
Region "🇵🇹"                    | Yes | Yes | Yes
Region "🇵🇵"                   | No  | No  | Yes
Tag Sequence "🏴󠁧󠁢󠁳󠁣󠁴󠁿"              | Yes | Yes | Yes
Tag Sequence "🏴󠁧󠁢󠁡󠁧󠁢󠁿"              | No  | Yes | Yes
Tag Sequence "😴󠁧󠁢󠁡󠁡󠁡󠁿"              | No  | No  | Yes
ZWJ Sequence "🤾🏽‍♀️"           | Yes | Yes | Yes
ZWJ Sequence "🤠‍🤢"            | No  | Yes | Yes

More info about valid vs. recommended Emoji in this [blog article on Emojipedia](http://blog.emojipedia.org/unicode-behind-the-curtain/).

### List

Use `Unicode::Emoji::LIST` or the list method to get a grouped (and ordered) list of Emoji:

```ruby
Unicode::Emoji.list.keys
# => ["Smileys & People", "Animals & Nature", "Food & Drink", "Travel & Places", "Activities", "Objects", "Symbols", "Flags"]

Unicode::Emoji.list("Food & Drink").keys
# => ["food-fruit", "food-vegetable", "food-prepared", "food-asian", "food-sweet", "drink", "dishware"]

Unicode::Emoji.list("Food & Drink", "food-asian")
=> ["🍱", "🍘", "🍙", "🍚", "🍛", "🍜", "🍝", "🍠", "🍢", "🍣", "🍤", "🍥", "🍡", "\u{1F95F}", "\u{1F960}", "\u{1F961}"]
```

Please note that categories might change with future versions of the Emoji standard. This gem will issue warnings when attemting to retrieve old categories using the `#list` method.

A markdown file with all recommended Emoji can be found [in this gist](https://gist.github.com/janlelis/72f9be1f0ecca07372c64cf13894b801).

### Properties

Allows you to access the codepoint data form Unicode's [emoji-data.txt](http://unicode.org/Public/emoji/11.0/emoji-data.txt) file:

```ruby
require "unicode/emoji"

Unicode::Emoji.properties "☝" # => ["Emoji", "Emoji_Modifier_Base"]
```

## Also See

- [Unicode® Technical Standard #51](http://www.unicode.org/reports/tr51/proposed.html)
- [Emoji data](http://unicode.org/Public/emoji/11.0/)
- [Emoji categories](http://unicode.org/emoji/charts/emoji-ordering.html)
- Ruby gem which displays [Emoji sequence names](https://github.com/janlelis/unicode-sequence_name)
- Part of [unicode-x](https://github.com/janlelis/unicode-x)

## MIT

- Copyright (C) 2017, 2018 Jan Lelis <http://janlelis.com>. Released under the MIT license.
- Unicode data: http://www.unicode.org/copyright.html#Exhibit1
