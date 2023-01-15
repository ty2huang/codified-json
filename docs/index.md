# Introducing Codified-JSON

*Codified-JSON* is a new style of rewriting *JSON* for transfering payloads as smaller sizes between software applications.

When *JSON* data has many *JSON objects* with the same keys, it can often be rewritten (as *JSON*) into a more compact style such that the keys are only specified once. This style is what we call *Codified-JSON*.

Don't know what *JSON* is? Visit the [official *JSON* website](https://www.json.org/json-en.html){:target="_blank"} for more details. The sections below assume strong understanding of *JSON*.

## File Extension

*Codified-JSON* files should have the extension `.codf.json`.

Why end in `.json`? Because *Codified-JSON* is still *JSON*! You can still use your favorite tools to color-code or format your *Codified-JSON* files, just like any other *JSON* file.

If the *Codified-JSON* data is compressed to a stream of bytes using [GNU zip](https://www.gnu.org/software/gzip/){:target="_blank"} and written to a file, the file should have the extension `.codf.json.gz`.

## Documentation

All *Codified-JSON* data is a *JSON object* with two keys: 

* "s" (for "structures" or "schemas") 
* "b" (for "body").

The "s" value is a *JSON array* of "structures". Each "structure" is also a *JSON array* of strings in alphabetical order representing a list of keys a *JSON object* in the original *JSON* data (which we'll rephrase as "original *JSON object*").

The "b" value contains the "translated copy" of the *JSON* data. The "translated copy" looks very much like the original *JSON* data except each *JSON object* gets condensed into a new *JSON object* with two keys:

* "i" (for "index")
* "v" (for "values")

The "i" value is an integer representing the (zero-based) index of the "structures" array for the "structure" that contain the exact keys used for the original *JSON object*.

The "v" value is a *JSON array* containing the values of the original *JSON object* in the same order that the keys are listed in for the corresponding "structure".

Other elements in the original *JSON* data that are not *JSON objects* remain the same. Also, when writing *Codified-JSON* as string, we generally avoid including whitespaces to reduce the size of the payload.

## Examples

Below is an example of translating a simple *JSON object* into *Codified-JSON*.

JSON:
```json
{"apple": "a", "clementine": 4, "banana": true}
```

Codified-JSON:
```json
{"s":[["apple","banana","clementine"]],"b":{"i":0,"v":["a",4,true]}}
```

Notice that the *Codified-JSON* for the example above is actually longer than the original *JSON* data. *Codified-JSON* tends to be more useful when the original *JSON* data contain multiple *JSON objects* with the exact same keys. This makes *Codified-JSON* more scalable. Below shows an example where the *Codified-JSON* reduces the size of the *JSON* data without losing any information in the data.

JSON (length of 214 characters or 170 characters without whitespaces):
```json
[
    {"apple": "a", "clementine": 4, "banana": "e"}, 
    {"clementine": 4, "banana": 5, "apple": 34}, 
    {"banana": 4.5, "apple": false, "clementine": 4}, 
    {"clementine": 4, "apple": null, "banana": true}
]
```

Codified-JSON (length of 142 characters):
```json
{"s":[["apple","banana","clementine"]],"b":[{"i":0,"v":["a","e",4]},{"i":0,"v":[34,5,4]},{"i":0,"v":[false,4.5,4]},{"i":0,"v":[null,true,4]}]}
```

To make the *Codified-JSON* even more compact, the string can be compressed/decompressed into a stream of bytes using [GNU zip](https://www.gnu.org/software/gzip/){:target="_blank"}.

## Resources

* The official supported libraries for *Codified-JSON* in various programming languages:
    * Java - TBA
    * JavaScript - TBA
    * Python - [Github](https://github.com/ty2huang/codified-json-python-library) | PyPI | Docs
