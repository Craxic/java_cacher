# java_cacher
Wraps any java method in another method that caches its result

## Intro
This small program will take any Java class and alter any number of its
functions to cache their result for their given input.
This was made to reduce the number of native calls required in a SWIG Java
wrapper when you know the result of a function will never change.

## Requirements
This requires that you install **CRAXIC**'s fork of "plyj" that you can find 
[here](https://github.com/Craxic/plyj). Make sure that you have no previous 
version of "plyj" installed. To install, `sudo python setup.py install`.

## Example
I.E, for some const array class that may look similar to this
```
class ConstArray {
    public String name() {
        return SwigJNI.ConstArray_name(this);
    }
    public int count() {
        return SwigJNI.ConstArray_count(this);
    }
    public Item get(int index) {
        return SwigJNI.ConstArray_get(this, index);
    }
}
```
Then we want something like this:
```
class ConstArray {
    private boolean isNameCached = false;
    private String nameCached = null;
    public String name() {
        if (!isNameCached) {
            nameCached = SwigJNI.ConstArray_name(this);
            isNameCached = true;
        }
        return nameCached;
    }
    private Item[] array1 = null;
    public int count() {
        if (array1 == null) {
            array1 = new Item[SwigJNI.ConstArray_count(this)];
        }
        return array1.length;
    }
    public Item get(int index) {
        count()
        if (array1[index] == null) {
            array1[index] = SwigJNI.ConstArray_get(this, index);
        }
        return array1[index];
    }
}
```
The input function_list_file looks like this:
```
cache ConstArray name
cache_array_no_nulls ConstArray count get
```