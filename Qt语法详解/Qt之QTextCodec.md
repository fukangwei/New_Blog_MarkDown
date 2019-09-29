---
title: Qt之QTextCodec
categories: Qt语法详解
abbrlink: 917bcf6a
date: 2019-01-26 12:39:17
---
&emsp;&emsp;The `QTextCodec` class provides conversions between text encodings. The header file is `QTextCodec`. **Note**: All functions in this class are reentrant, except for `setCodecForTr()`, `setCodecForCStrings()`, and `~QTextCodec()`, which are nonreentrant.

### Public Functions

Return                      | Function
----------------------------|---------
`virtual QList<QByteArray>` | `aliases() const`
`bool`                      | `canEncode(QChar ch) const`
`bool`                      | `canEncode(const QString & s) const`
`QByteArray`                | `fromUnicode(const QString & str) const`
`QByteArray`                | `fromUnicode(const QChar * input, int number, ConverterState * state = 0) const`
`QTextDecoder *`            | `makeDecoder() const`
`QTextDecoder *`            | `makeDecoder(ConversionFlags flags) const`
`QTextEncoder *`            | `makeEncoder() const`
`QTextEncoder *`            | `makeEncoder(ConversionFlags flags) const`
`virtual int`               | `mibEnum() const = 0`
`virtual QByteArray`        | `name() const = 0`
`QString`                   | `toUnicode(const QByteArray & a) const`
`QString`                   | `toUnicode(const char * input, int size, ConverterState * state = 0) const`
`QString`                   | `toUnicode(const char * chars) const`

### Static Public Members

Return              | Function
--------------------|----------
`QList<QByteArray>` | `availableCodecs()`
`QList<int>`        | `availableMibs()`
`QTextCodec *`      | `codecForCStrings()`
`QTextCodec *`      | `codecForHtml(const QByteArray & ba, QTextCodec * defaultCodec)`
`QTextCodec *`      | `codecForHtml(const QByteArray & ba)`
`QTextCodec *`      | `codecForLocale()`
`QTextCodec *`      | `codecForMib(int mib)`
`QTextCodec *`      | `codecForName(const QByteArray & name)`
`QTextCodec *`      | `codecForName(const char * name)`
`QTextCodec *`      | `codecForTr()`
`QTextCodec *`      | `codecForUtfText(const QByteArray & ba, QTextCodec * defaultCodec)`
`QTextCodec *`      | `codecForUtfText(const QByteArray & ba)`
`void`              | `setCodecForCStrings(QTextCodec * codec)`
`void`              | `setCodecForLocale(QTextCodec * c)`
`void`              | `setCodecForTr(QTextCodec * c)`

### Protected Functions

Return               | Function
---------------------|----------
                     | `QTextCodec()`
`virtual`            | `QTextCodec()`
`virtual QByteArray` | `convertFromUnicode(const QChar * input, int number, ConverterState * state) const = 0`
`virtual QString`    | `convertToUnicode(const char * chars, int len, ConverterState * state) const = 0`

### Detailed Description

&emsp;&emsp;The `QTextCodec` class provides conversions between text encodings. `Qt` uses Unicode to store, draw and manipulate strings. In many situations you may wish to deal with data that uses a different encoding. For example, most `Japanese` documents are still stored in `Shift-JIS` or `ISO 2022-JP`, while Russian users often have their documents in `KOI8-R` or `Windows-1251`. `Qt` provides a set of `QTextCodec` classes to help with converting `non-Unicode` formats to and from `Unicode`. You can also create your own codec classes.
&emsp;&emsp;The supported encodings are: `Apple Roman`; `Big5`; `Big5-HKSCS`; `CP949`; `EUC-JP`; `EUC-KR`; `GB18030-0`; `IBM 850`; `IBM 866`; `IBM 874`; `ISO 2022-JP`; `ISO 8859-1 to 10`; `ISO 8859-13 to 16`; `Iscii-Bng`, `Dev`, `Gjr`, `Knd`, `Mlm`, `Ori`, `Pnj`, `Tlg`, and `Tml`; `JIS X 0201`; `JIS X 0208`; `KOI8-R`; `KOI8-U`; `MuleLao-1`; `ROMAN8`; `Shift-JIS`; `TIS-620`; `TSCII`; `UTF-8`; `UTF-16`; `UTF-16BE`; `UTF-16LE`; `UTF-32`; `UTF-32BE`; `UTF-32LE`; `Windows-1250 to 1258`; `WINSAMI2`.
&emsp;&emsp;`QTextCodecs` can be used as follows to convert some locally encoded string to `Unicode`. Suppose you have some string encoded in `Russian KOI8-R` encoding, and want to convert it to `Unicode`. The simple way to do it is like this:

``` cpp
QByteArray encodedString = "...";
QTextCodec *codec = QTextCodec::codecForName ( "KOI8-R" );
QString string = codec->toUnicode ( encodedString );
```

After this, string holds the text converted to `Unicode`. Converting a string from `Unicode` to the local encoding is just as easy:

``` cpp
QString string = "...";
QTextCodec *codec = QTextCodec::codecForName ( "KOI8-R" );
QByteArray encodedString = codec->fromUnicode ( string );
```

&emsp;&emsp;To read or write files in various encodings, use `QTextStream` and its `setCodec()` function. See the `Codecs` example for an application of `QTextCodec` to file `I/O`.
&emsp;&emsp;Some care must be taken when trying to convert the data in chunks, for example, when receiving it over a network. In such cases it is possible that a `multi-byte` character will be split over two chunks. At best this might result in the loss of a character and at worst cause the entire conversion to fail.
&emsp;&emsp;The approach to use in these situations is to create a `QTextDecoder` object for the codec and use this `QTextDecoder` for the whole decoding process, as shown below:

``` cpp
QTextCodec *codec = QTextCodec::codecForName ( "Shift-JIS" );
QTextDecoder *decoder = codec->makeDecoder();
​
QString string;
​
while ( new_data_available() ) {
    QByteArray chunk = get_new_data();
    string += decoder->toUnicode ( chunk );
}
​
delete decoder;
```

The `QTextDecoder` object maintains state between chunks and therefore works correctly even if a `multi-byte` character is split between chunks.

### Creating Your Own Codec Class

&emsp;&emsp;Support for new text encodings can be added to `Qt` by creating `QTextCodec` subclasses.
&emsp;&emsp;The pure virtual functions describe the encoder to the system and the coder is used as required in the different text file formats supported by `QTextStream`, and under `X11`, for the `locale-specific` character input and output.
&emsp;&emsp;To add support for another encoding to `Qt`, make a subclass of `QTextCodec` and implement the functions listed in the table below.

Function               | Description
-----------------------|-------------
`name()`               | Returns the official name for the encoding. If the encoding is listed in the `IANA` `character-sets` encoding file, the name should be the preferred `MIME` name for the encoding.
`aliases()`            | Returns a list of alternative names for the encoding. `QTextCodec` provides a default implementation that returns an empty list. For example, `ISO-8859-1` has `latin1`, `CP819`, `IBM819`, and `iso-ir-100` as aliases.
`mibEnum()`            | Return the `MIB` enum for the encoding if it is listed in the `IANA` `character-sets` encoding file.
`convertToUnicode()`   | Converts an `8-bit` character string to `Unicode`.
`convertFromUnicode()` | Converts a `Unicode` string to an `8-bit` character string.

You may find it more convenient to make your codec class available as a plugin.

### Member Type Documentation

- enum `QTextCodec::ConversionFlag`:

Constant                           | Value        | Description
-----------------------------------|--------------|------------
`QTextCodec::DefaultConversion`    | `0`          | No flag is set.
`QTextCodec::ConvertInvalidToNull` | `0x80000000` | If this flag is set, each invalid input character is output as a null character.
`QTextCodec::IgnoreHeader`         | `0x1`        | Ignore any `Unicode` `byte-order` mark and don't generate any.

The `ConversionFlags` type is a typedef for `QFlags<ConversionFlag>`. It stores an `OR` combination of `ConversionFlag` values.

### Member Function Documentation

- `QTextCodec::QTextCodec() [protected]`: Constructs a `QTextCodec`, and gives it the highest precedence. The `QTextCodec` should always be constructed on the heap (i.e. with new). `Qt` takes ownership and will delete it when the application terminates.
- `QTextCodec::~QTextCodec() [virtual protected]`: Destroys the `QTextCodec`. Note that you should not delete codecs yourself: once created they become `Qt's` responsibility. **Warning**: This function is not reentrant.
- `QList<QByteArray> QTextCodec::aliases() const [virtual]`: Subclasses can return a number of aliases for the codec in question. Standard aliases for codecs can be found in the `IANA` `character-sets` encoding file.
- `QList<QByteArray> QTextCodec::availableCodecs() [static]`: Returns the list of all available codecs, by name. Call `QTextCodec::codecForName()` to obtain the `QTextCodec` for the name. The list may contain many mentions of the same codec if the codec has aliases.
- `QList<int> QTextCodec::availableMibs() [static]`: Returns the list of `MIBs` for all available codecs. Call `QTextCodec::codecForMib()` to obtain the `QTextCodec` for the `MIB`.
- `bool QTextCodec::canEncode(QChar ch) const`: Returns `true` if the Unicode character `ch` can be fully encoded with this codec; otherwise returns `false`.
- `bool QTextCodec::canEncode(const QString & s) const`: This is an overloaded function. `s` contains the string being tested for `encode-ability`.
- `QTextCodec * QTextCodec::codecForCStrings() [static]`: Returns the codec used by `QString` to convert to and from `const char *` and `QByteArrays`. If this function returns `0` (the default), `QString` assumes `Latin-1`.
- `QTextCodec * QTextCodec::codecForHtml(const QByteArray & ba, QTextCodec * defaultCodec) [static]`: Tries to detect the encoding of the provided snippet of `HTML` in the given byte array, `ba`, by checking the `BOM (Byte Order Mark`) and the `content-type` meta header and returns a `QTextCodec` instance that is capable of decoding the html to unicode. If the codec cannot be detected from the content provided, `defaultCodec` is returned.
- `QTextCodec * QTextCodec::codecForHtml(const QByteArray & ba) [static]`: This is an overloaded function. Tries to detect the encoding of the provided snippet of `HTML` in the given byte array, `ba`, by checking the `BOM (Byte Order Mark)` and the `content-type` meta header and returns a `QTextCodec` instance that is capable of decoding the html to unicode. If the codec cannot be detected, this overload returns a `Latin-1` `QTextCodec`.
- `QTextCodec * QTextCodec::codecForLocale() [static]`: Returns a pointer to the codec most suitable for this locale. On `Windows`, the codec will be based on a system locale. On `Unix` systems, starting with `Qt 4.2`, the codec will be using the iconv library. Note that in both cases the codec's name will be `System`.
- `QTextCodec * QTextCodec::codecForMib(int mib) [static]`: Returns the `QTextCodec` which matches the `MIBenum` `mib`.
- `QTextCodec * QTextCodec::codecForName(const QByteArray & name) [static]`: Searches all installed `QTextCodec` objects and returns the one which best matches `name`; the match is `case-insensitive`.
- `QTextCodec * QTextCodec::codecForName(const char * name) [static]`: Searches all installed `QTextCodec` objects and returns the one which best matches `name`; the match is `case-insensitive`.
- `QTextCodec * QTextCodec::codecForTr() [static]`: Returns the codec used by `QObject::tr()` on its argument. If this function returns `0` (the default), `tr()` assumes `Latin-1`.
- `QTextCodec * QTextCodec::codecForUtfText(const QByteArray & ba, QTextCodec * defaultCodec) [static]`: Tries to detect the encoding of the provided snippet `ba` by using the `BOM (Byte Order Mark)` and returns a `QTextCodec` instance that is capable of decoding the text to unicode. If the codec cannot be detected from the content provided, `defaultCodec` is returned.
- `QTextCodec * QTextCodec::codecForUtfText(const QByteArray & ba) [static]`: This is an overloaded function. Tries to detect the encoding of the provided snippet `ba` by using the `BOM (Byte Order Mark)` and returns a `QTextCodec` instance that is capable of decoding the text to unicode. If the codec cannot be detected, this overload returns a `Latin-1` `QTextCodec`.
- `QByteArray QTextCodec::convertFromUnicode(const QChar * input, int number, ConverterState * state) const [pure virtual protected]`: `QTextCodec` subclasses must reimplement this function. Converts the first `number` of characters from the `input` array from `Unicode` to the encoding of the subclass, and returns the result in a `QByteArray`. `state` can be `0` in which case the conversion is stateless and default conversion rules should be used. If `state` is not `0`, the codec should save the state after the conversion in `state`, and adjust the `remainingChars` and `invalidChars` members of the struct.
- `QString QTextCodec::convertToUnicode(const char * chars, int len, ConverterState * state) const [pure virtual protected]`: `QTextCodec` subclasses must reimplement this function. Converts the first `len` characters of `chars` from the encoding of the subclass to `Unicode`, and returns the result in a `QString`. `state` can be `0`, in which case the conversion is stateless and default conversion rules should be used. If `state` is not `0`, the codec should save the state after the conversion in `state`, and adjust the `remainingChars` and `invalidChars` members of the struct.
- `QByteArray QTextCodec::fromUnicode(const QString & str) const`: Converts `str` from `Unicode` to the encoding of this codec, and returns the result in a `QByteArray`.
- `QByteArray QTextCodec::fromUnicode(const QChar * input, int number, ConverterState * state = 0) const`: Converts the first `number` of characters from the `input` array from `Unicode` to the encoding of this codec, and returns the result in a `QByteArray`. The `state` of the convertor used is updated.
- `QTextDecoder * QTextCodec::makeDecoder() const`: Creates a `QTextDecoder` which stores enough state to decode chunks of `char *` data to create chunks of `Unicode` data. The caller is responsible for deleting the returned object.
- `QTextDecoder * QTextCodec::makeDecoder(ConversionFlags flags) const`: Creates a `QTextDecoder` with a specified `flags` to decode chunks of `char *` data to create chunks of `Unicode` data. The caller is responsible for deleting the returned object.
- `QTextEncoder * QTextCodec::makeEncoder() const`: Creates a `QTextEncoder` which stores enough state to encode chunks of `Unicode` data as `char *` data. The caller is responsible for deleting the returned object.
- `QTextEncoder * QTextCodec::makeEncoder(ConversionFlags flags) const`: Creates a `QTextEncoder` with a specified `flags` to encode chunks of `Unicode` data as `char *` data. The caller is responsible for deleting the returned object.
- `int QTextCodec::mibEnum() const [pure virtual]`: Subclasses of `QTextCodec` must reimplement this function. It returns the `MIBenum`. It is important that each `QTextCodec` subclass returns the correct unique value for this function.
- `QByteArray QTextCodec::name() const [pure virtual]`: `QTextCodec` subclasses must reimplement this function. It returns the name of the encoding supported by the subclass. If the codec is registered as a character set in the `IANA` `character-sets` encoding file this method should return the preferred mime name for the codec if defined, otherwise its name.
- `void QTextCodec::setCodecForCStrings(QTextCodec * codec) [static]`: Sets the `codec` used by `QString` to convert to and from `const char *` and `QByteArrays`. If the `codec` is `0` (the default), `QString` assumes `Latin-1`. **Warning**: Some codecs do not preserve the characters in the `ASCII` range (`0x00` to `0x7F`). For example, the `Japanese` `Shift-JIS` encoding maps the backslash character (`0x5A`) to the `Yen` character. To avoid undesirable `side-effects`, we recommend avoiding such codecs with `setCodecsForCString()`. **Warning**: This function is not reentrant.
- `void QTextCodec::setCodecForLocale(QTextCodec * c) [static]`: Set the codec to `c`; this will be returned by `codecForLocale()`. If `c` is a null pointer, the codec is reset to the default. This might be needed for some applications that want to use their own mechanism for setting the locale.
- `void QTextCodec::setCodecForTr(QTextCodec * c) [static]`: Sets the codec used by `QObject::tr()` on its argument to `c`. If `c` is `0` (the default), `tr()` assumes `Latin-1`. If the literal quoted text in the program is not in the `Latin-1` encoding, this function can be used to set the appropriate encoding. For example, software developed by `Korean` programmers might use `eucKR` for all the text in the program, in which case the `main()` function might look like this:

``` cpp
int main ( int argc, char *argv[] ) {
    QApplication app ( argc, argv );
    QTextCodec::setCodecForTr ( QTextCodec::codecForName ( "eucKR" ) );
    /* ... */
}
```

Note that this is not the way to select the encoding that the user has chosen. For example, to convert an application containing literal `English` strings to `Korean`, all that is needed is for the `English` strings to be passed through `tr()` and for translation files to be loaded. **Warning**: This function is not reentrant.

- `QString QTextCodec::toUnicode(const QByteArray & a) const`: Converts `a` from the encoding of this codec to `Unicode`, and returns the result in a `QString`.
- `QString QTextCodec::toUnicode(const char * input, int size, ConverterState * state = 0) const`: Converts the first `size` characters from the `input` from the encoding of this codec to `Unicode`, and returns the result in a `QString`. The `state` of the convertor used is updated.
- `QString QTextCodec::toUnicode(const char * chars) const`: This is an overloaded function. `chars` contains the source characters.