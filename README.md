# Resources

##  Load jQuery

Loads jQuery, if it's not already loaded, and encapsulates within a callback to avoid conflicting with any pre-existing version(s).
Ensures that a minimum version of jQuery is available otherwise loads a known version -- in this case, v1.7.
Passes a boolean value to the callback (jquery_loaded) on whether or not jQuery was loaded to assit with triggering events after load.

```js
(function(window, document, version, callback) {
    var j, d;
    var loaded = false;
    if (!(j = window.jQuery) || version > j.fn.jquery || callback(j, loaded)) {
        var script = document.createElement("script");
        script.type = "text/javascript";
        script.src = "/media/jquery.js";
        script.onload = script.onreadystatechange = function() {
            if (!loaded && (!(d = this.readyState) || d == "loaded" || d == "complete")) {
                callback((j = window.jQuery).noConflict(1), loaded = true);
                j(script).remove();
            }
        };
        (document.getElementsByTagName("head")[0] || document.documentElement).appendChild(script);
    }
})(window, document, "1.7", function($, jquery_loaded) {

    // Code to run once loaded

});
```

## Load javascript

```js
function loadScript(src, onLoad) {
    var script_tag = document.createElement('script');
    script_tag.setAttribute("type", "text/javascript");
    script_tag.setAttribute("src", src);

    if (script_tag.readyState) {
        script_tag.onreadystatechange = function () {
            if (this.readyState == 'complete' || this.readyState == 'loaded') {
                onLoad();
            }
        };
    } else {
        script_tag.onload = onLoad;
    }
    (document.getElementsByTagName("head")[0] || document.documentElement).appendChild(script_tag);
}
```

## Load stylesheet

```js
function loadCss(href) {
    var link_tag = document.createElement('link');
    link_tag.setAttribute("type", "text/css");
    link_tag.setAttribute("rel", "stylesheet");
    link_tag.setAttribute("href", href);
    (document.getElementsByTagName("head")[0] || document.documentElement).appendChild(link_tag);
}
```

# Identifiers

## Create GUID

```js
function createGuid(nsString) {
        function s4() {
            return Math.floor((1 + Math.random()) * 0x10000)
                .toString(16)
                .substring(1);
        }
        return s4() + s4() + '-' + s4() + '-' + s4() + '-' + s4() + '-' + s4() + s4() + s4();
    }
```

## Create unique id

```js
var createUniqueId = function() {
    var text = "id-",
        possible = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789",
        length = 5;
    for(var i = 0; i < length; i++) {
        text += possible.charAt(Math.floor(Math.random() * possible.length));
    }
    return document.getElementById(text) ? makeUniqueId() : text;
};
```

## Objects

## Get object keys

```js
var keys = Object.keys || function(obj) {
    if (obj !== Object(obj)) throw new TypeError('Invalid object');
    var keys = [];
    for (var key in obj) if (has(obj, key)) keys[keys.length] = key;
    return keys;
};
```

## Extend an object

```js
var extend = function(obj) {
    each(slice.call(arguments, 1), function(source) {
        for (var prop in source) {
            obj[prop] = source[prop];
        }
    });
    return obj;
};
```

## Check if object is array

```js
var isArray = Array.isArray || function(obj) {
    return Object.prototype.toString.call(obj) == '[object Array]';
};
```

## Get index of object in array

```js
if(!Array.prototype.indexOf) {
    Array.prototype.indexOf = function(obj, start) {
        for (var i = (start || 0), j = this.length; i < j; i++) {
            if (this[i] === obj) { return i; }
        }
        return -1;
    };
}
```

## Check if object has property

```js
var has = function(obj, key) {
    return Object.hasOwnProperty.call(obj, key);
};
```

# Querystring

## Get query string value

```js
function GetQueryParamByName(name, url) {
    if (!url) url = window.location.href;
    name = name.replace(/[\[\]]/g, "\\$&");
    var regex = new RegExp("[?&]" + name + "(=([^&#]*)|&|#|$)"),
        results = regex.exec(url);
    if (!results) return null;
    if (!results[2]) return '';
    return decodeURIComponent(results[2].replace(/\+/g, " "));
}
```

## Query string to object

Taken from [jquery-bbq by Ben Alman](https://github.com/cowboy/jquery-bbq/blob/master/jquery.ba-bbq.js)

```js
var deparam = function(params, coerce) {
    var obj = {},
        coerce_types = { 'true': !0, 'false': !1, 'null': null };

    // Iterate over all name=value pairs.
    each(params.replace(/\+/g, ' ').split('&'), function(v, j) {
        var param = v.split('='),
            key = decodeURIComponent(param[0]),
            val,
            cur = obj,
            i = 0,

            // If key is more complex than 'foo', like 'a[]' or 'a[b][c]', split it
            // into its component parts.
            keys = key.split(']['),
            keys_last = keys.length - 1;

        // If the first keys part contains [ and the last ends with ], then []
        // are correctly balanced.
        if (/\[/.test(keys[0]) && /\]$/.test(keys[keys_last])) {
            // Remove the trailing ] from the last keys part.
            keys[keys_last] = keys[keys_last].replace(/\]$/, '');

            // Split first keys part into two parts on the [ and add them back onto
            // the beginning of the keys array.
            keys = keys.shift().split('[').concat(keys);

            keys_last = keys.length - 1;
        } else {
            // Basic 'foo' style key.
            keys_last = 0;
        }

        // Are we dealing with a name=value pair, or just a name?
        if (param.length === 2) {
            val = decodeURIComponent(param[1]);

            // Coerce values.
            if (coerce) {
                val = val && !isNaN(val) ? +val // number
                    : val === 'undefined' ? undefined // undefined
                    : coerce_types[val] !== undefined ? coerce_types[val] // true, false, null
                    : val; // string
            }

            if (keys_last) {
                // Complex key, build deep object structure based on a few rules:
                // * The 'cur' pointer starts at the object top-level.
                // * [] = array push (n is set to array length), [n] = array if n is 
                //   numeric, otherwise object.
                // * If at the last keys part, set the value.
                // * For each keys part, if the current level is undefined create an
                //   object or array based on the type of the next keys part.
                // * Move the 'cur' pointer to the next level.
                // * Rinse & repeat.
                for (; i <= keys_last; i++) {
                    key = keys[i] === '' ? cur.length : keys[i];
                    cur = cur[key] = i < keys_last ? cur[key] || (keys[i + 1] && isNaN(keys[i + 1]) ? {} : []) : val;
                }

            } else {
                // Simple key, even simpler rules, since only scalars and shallow
                // arrays are allowed.

                if (isArray(obj[key])) {
                    // val is already an array, so push on the next value.
                    obj[key].push(val);

                } else if (obj[key] !== undefined) {
                    // val isn't an array, but since a second value has been specified,
                    // convert val into an array.
                    obj[key] = [obj[key], val];

                } else {
                    // val is a scalar.
                    obj[key] = val;
                }
            }

        } else if (key) {
            // No value was defined, so set something meaningful.
            obj[key] = coerce ? undefined : '';
        }
    });

    return obj;
};
```

# Selectors

## Get elements by class name

```js
var getElementsByClassName = function(className, tag, elm) {
    if (document.getElementsByClassName) {
        getElementsByClassName = function(className, tag, elm) {
            elm = elm || document;
            var elements = elm.getElementsByClassName(className),
                nodeName = (tag) ? new RegExp("\\b" + tag + "\\b", "i") : null,
                returnElements = [],
                current;
            for (var i = 0, il = elements.length; i < il; i += 1) {
                current = elements[i];
                if (!nodeName || nodeName.test(current.nodeName)) {
                    returnElements.push(current);
                }
            }
            return returnElements;
        };
    } else if (document.evaluate) {
        getElementsByClassName = function(className, tag, elm) {
            tag = tag || "*";
            elm = elm || document;
            var classes = className.split(" "),
                classesToCheck = "",
                xhtmlNamespace = "http://www.w3.org/1999/xhtml",
                namespaceResolver = (document.documentElement.namespaceURI === xhtmlNamespace) ? xhtmlNamespace : null,
                returnElements = [],
                elements,
                node;
            for (var j = 0, jl = classes.length; j < jl; j += 1) {
                classesToCheck += "[contains(concat(' ', @class, ' '), ' " + classes[j] + " ')]";
            }
            try {
                elements = document.evaluate(".//" + tag + classesToCheck, elm, namespaceResolver, 0, null);
            } catch (e) {
                elements = document.evaluate(".//" + tag + classesToCheck, elm, null, 0, null);
            }
            while ((node = elements.iterateNext())) {
                returnElements.push(node);
            }
            return returnElements;
        };
    } else {
        getElementsByClassName = function(className, tag, elm) {
            tag = tag || "*";
            elm = elm || document;
            var classes = className.split(" "),
                classesToCheck = [],
                elements = (tag === "*" && elm.all) ? elm.all : elm.getElementsByTagName(tag),
                current,
                returnElements = [],
                match;
            for (var k = 0, kl = classes.length; k < kl; k += 1) {
                classesToCheck.push(new RegExp("(^|\\s)" + classes[k] + "(\\s|$)"));
            }
            for (var l = 0, ll = elements.length; l < ll; l += 1) {
                current = elements[l];
                match = false;
                for (var m = 0, ml = classesToCheck.length; m < ml; m += 1) {
                    match = classesToCheck[m].test(current.className);
                    if (!match) {
                        break;
                    }
                }
                if (match) {
                    returnElements.push(current);
                }
            }
            return returnElements;
        };
    }
    return getElementsByClassName(className, tag, elm);
};
```

# Miscellaneous

## Create namespace

```js
function CreateNamespace(nsString) {
    var ns;
    if (nsString) {
        ns = nsString.split(".").reduce(function (pv, cv) { return pv[cv] = pv[cv] || {}; }, exports);
    }
    return ns;
}
```

## Iterate over elements

```js
var each = function(obj, iterator, context) {
    if (obj == null) return;
    if (Array.prototype.forEach && obj.forEach === Array.prototype.forEach) {
        obj.forEach(iterator, context);
    } else if (obj.length === +obj.length) {
        for (var i = 0, l = obj.length; i < l; i++) {
            if (i in obj && iterator.call(context, obj[i], i, obj) === breaker) return;
        }
    } else {
        for (var key in obj) {
            if (has(obj, key)) {
                if (iterator.call(context, obj[key], key, obj) === breaker) return;
            }
        }
    }
};
```

