## Useful JavaScript Snippets

### Resolve jQuery
Loads jQuery if it's not already loaded and encapsulates it in the callback so it doesn't conflict with a pre-existing jQuery on the page. It also checks that a minimum version is available or else loads a known version -- in this case, v1.3. It sends a boolean value to the callback (my widget) on whether or not jQuery was loaded in case there are any triggers needed to be made. And only after jQuery is loaded does it call my widget, passing jQuery into it.
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
})(window, document, "1.3", function($, jquery_loaded) {

    // Code to run once loaded

});
```

### Create Namespace
```js
function CreateNamespace(nsString) {
    var ns;
    if (nsString) {
        ns = nsString.split(".").reduce(function (pv, cv) { return pv[cv] = pv[cv] || {}; }, exports);
    }
    return ns;
}
```

### Create GUID
```js
function NewGuid(nsString) {
        function s4() {
            return Math.floor((1 + Math.random()) * 0x10000)
                .toString(16)
                .substring(1);
        }
        return s4() + s4() + '-' + s4() + '-' + s4() + '-' + s4() + '-' + s4() + s4() + s4();
    }
```

### Get Query String Variable	
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