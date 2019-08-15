# Tokenization {#concept_354503 .concept}

Search index can tokenize words for queries. If the field type is set to text, you can set an additional tokenization parameter for this field to specify the method in which the text is tokenized. Tokenization cannot be set for fields of non-text types.

You can use MatchQuery and MatchPhraseQuery to query text data. TermQuery, TermsQuery, PrefixQuery, and WildcardQuery are also used in a few scenarios.

The following tokenization methods are supported:

## Methods {#section_pm0_w2g_chm .section}

 **Single-word tokenization** 

-   Name: single\_word
-   Applies to: all natural languages, such as Chinese, English, and Japanese
-   Parameter:
    -   caseSensitive: specifies whether this method is case-sensitive. The default value is false. False indicates that all English letters are converted to lowercase letters.
    -   delimitWord: specifies whether to tokenize alphanumeric characters. The default value is false.

English letters or numbers are tokenized based on spaces or punctuation, and English letters are converted to lowercase letters. For example, "Hang Zhou" is tokenized into "hang" and "zhou". You can use MatchQuery or MatchPhraseQuery to query data that contains "hang", "HANG", or "Hang". If you do not need the system to automatically convert English letters to lowercase letters, you can set the caseSensitive parameter to true.

Alphanumeric characters such as product models cannot be tokenized by this method because there are no spaces or punctuation between letters and numbers. For example, "IPhone6" remains "IPhone6" after tokenization. When querying by MatchQuery or MatchPhraseQuery, you can retrieve data only by specifying "iphone6" to query. You can set the delimitWord parameter to true to separate English letters from numbers. This way, "iphone6" is tokenzied into "iphone" and "6".

 **Delimiter tokenization** 

-   Name: split
-   Applies to: all natural languages, such as Chinese, English, and Japanese
-   Parameter:
    -   delimiter: The default delimiter is a space. You can set the delimiter to any character based on your needs.

Search index tokenizes words based on general dictionaries, but words from some special industries need to be tokenized based on their custom dictionaries. In this case, tokenization methods provided by search index cannot meet the needs of users.

Delimiter tokenization, or custom tokenization, can address this need. Users segment words in their own way and tokenize the segmented words with a specific delimiter. Then, the tokenized words are written to Table Store.

**Note:** When you create a search index, the delimiter set in the field for tokenization must be the same as that in the written data. Otherwise, data may not be retrieved.

 **Minimum semantic unit-based tokenization** 

-   Name: min\_word
-   Applies to: Chinese
-   Parameter:
    -   None

In addition to word-level tokenization, search index also provides semantic-level tokenization. By using this method, text is tokenized into minimum semantic units.

In most cases, this method can meet basic requirements in the full-text search scenario.

 **Maximum semantic unit-based tokenization** 

-   Name: max\_word
-   Applies to: Chinese
-   Parameter:
    -   None

Aside from the minimum semantic unit-based tokenization, the more complex maximum semantic unit-based tokenization is provided to obtain as many semantic units as possible. However, different semantic units may overlap. The total length of the tokenized words is greater than the length of the original text. The index fields are increased.

This method can generate more tokens and increase the probability of obtaining results. However, the index fields are greatly increased. MatchPhraseQuery also tokenizes words in the same way. This way, tokens may overlap and data may not be retrieved. Therefore, This tokenization method is more suitable for MatchQuery.

 **Fuzzy tokenization** 

-   Name: fuzzy
-   Applies to: all natural languages, such as Chinese, English, and Japanese
-   Parameter:
    -   minChars: specifies the minimum number of characters for a token. We recommend that you set this value to 2.
    -   maxChars: specifies the maximum number of characters for a token. We recommend that you set this value to a number smaller than or equal to 7.
-   Limits:
    -   A text field cannot exceed 32 characters in length. Only the first 32 characters of a text field is retained and the characters after the 32nd character are truncated and discarded.

Assume that you need to be able to quickly obtain results for short text, such as headlines, movie names, or book titles by using drop-down prompts. In this case, you can use fuzzy tokenization to tokenize text content into n-grams, whose lengths are between minChars and maxChars.

This method has minimal delay when obtaining results, but the index fields are increased greatly. Therefore, this tokenization method is suitable for short text.

## Comparison {#section_8nj_3wl_bz8 .section}

The following table compares the five tokenization methods.

| |Single-word tokenization|Delimiter tokenization|Minimum semantic unit-based tokenization|Maximum semantic unit-based tokenization|Fuzzy tokenization|
|--|------------------------|----------------------|----------------------------------------|----------------------------------------|------------------|
|Index expansion|Medium|Small|Small|Large|Huge|
|Relevance|Weak|Weak|Medium|Relatively strong|Relatively strong|
|Applicable language|All|All|Chinese|Chinese|All|
|Length limit|No|No|No|No|32 characters|
|Recall rate|High|Low|Low|Medium|Medium|

