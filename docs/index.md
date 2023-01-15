# Introducing Codified-JSON

*Codified-JSON* is a new representation of *JSON* for transfering payloads as smaller sizes between software applications.

When *JSON* data has many *JSON objects* with the same keys, it can often be rewritten (as *JSON*) into a more compact representation such that the keys are only specified once. This representation is what we call *Codified-JSON*.

Don't know what *JSON* is? Visit the [official *JSON* website](https://www.json.org/json-en.html){:target="_blank"} for more details. The sections below assume strong understanding of *JSON*.

## File Extension

*Codified-JSON* files should have the extension `.codf.json`.

Why end in `.json`? Because *Codified-JSON* is still *JSON*! You can still use your favorite tools to color-code or format your *Codified-JSON* files, just like any other *JSON* file.

If the *Codified-JSON* data is compressed to a stream of bytes using [GNU zip](https://www.gnu.org/software/gzip/){:target="_blank"} and written to a file, the file should have the extension `.codf.json.gz`.

## Specifications

All *Codified-JSON* data is a *JSON object* with two keys: 

* "s" (for "structures" or "schemas") 
* "b" (for "body")

The value for "s" is usually a *JSON object* with zero-based indices as keys and "structures" as values. Each "structure" is a *JSON array* of strings in alphabetical order representing the full list of keys found in at least one *JSON object* in the original *JSON* data (which will be called "original *JSON object*" going forward). 

The value for "s" can also be a *JSON array* of the structures, ordered by the indices from the *JSON object* representation.

The value for "b" or "body" contains the actual "content" of the *JSON* data. The "content" looks very much like the original *JSON* data. 

Putting the "structures" and "body" together, the *Codified-JSON* data would look something like this:
```json
{"s":{"0":["key1","key2",...],"1":["keyA","keyB",...],...},"b":<body>}
```

Or like this:
```json
{"s":[["key1","key2",...],["keyA","keyB",...],...],"b":<body>}
```

The only difference in the body and the original JSON data is that each original *JSON object* (except empty ones) get converted into a new *JSON object* with two keys:

* "i" (for "index")
* "v" (for "values") except with some differences (which will be discussed later).

The "i" value is an integer representing the index of the structures array for the structure that contain the exact keys used for the original *JSON object*.

The "v" value is a *JSON array* containing the values of the original *JSON object* in the same order that the keys are listed in for the corresponding "structure".

This means that the *Codified-JSON* data will always look something like this:
```json
{"i":<some integer value>,"v":[<value1>,<value2>,...]}
```

Other elements in the original *JSON* data that are not *JSON objects* remain the same. Empty *JSON Objects* also remain the same, and they are the only original *JSON Objects* that don't get injected "i" and "v" keys. When writing *Codified-JSON* as a string, whitespaces are generally avoided to reduce the size of the payload.

Note that although using *JSON array* for the structures is allowed, it can make it difficult to trace the structure corresponding to the "i" value of a *JSON Objects*. So although it can marginally improve compression, using *JSON array* is not recommended for large structures arrays ("s" values) as it may sacrifice interpretability.

## Examples

Below is an example of translating a simple *JSON object* into *Codified-JSON*.

JSON:
```json
{"apple": "a", "clementine": 4, "banana": true}
```

Codified-JSON:
```json
{"s":{"0":["apple","banana","clementine"]},"b":{"i":0,"v":["a",4,true]}}
```

Notice that the *Codified-JSON* for the example above is actually longer than the original *JSON* data. *Codified-JSON* tends to be more useful when the original *JSON* data contain multiple *JSON objects* with the exact same keys. This makes *Codified-JSON* more scalable. Below shows an example where the *Codified-JSON* reduces the size of the *JSON* data without losing any information.

JSON (length of 214 characters or 170 characters without whitespaces):
```json
[
    {"apple": "a", "clementine": 4, "banana": "e"}, 
    {"clementine": 4, "banana": 5, "apple": 34}, 
    {"banana": 4.5, "apple": false, "clementine": 4}, 
    {"clementine": 4, "apple": null, "banana": true}
]
```

Codified-JSON (length of 146 characters):
```json
{"s":{"0":["apple","banana","clementine"]},"b":[{"i":0,"v":["a","e",4]},{"i":0,"v":[34,5,4]},{"i":0,"v":[false,4.5,4]},{"i":0,"v":[null,true,4]}]}
```

To make the *Codified-JSON* even more compact, the string can be compressed/decompressed into a stream of bytes using [GNU zip](https://www.gnu.org/software/gzip/){:target="_blank"}.

## Resources

* The official supported libraries for *Codified-JSON* in various programming languages:
    * Java - TBA
    * JavaScript - TBA
    * Python - [Github](https://github.com/ty2huang/codified-json-python-library) | PyPI | Docs
