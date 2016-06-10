## Useful JavaScript Snippets

### Resolve jQuery
Loads jQuery if it's not already loaded and encapsulates it in the callback so it doesn't conflict with a pre-existing jQuery on the page. It also checks that a minimum version is available or else loads a known version -- in this case, v1.3. It sends a boolean value to the callback (my widget) on whether or not jQuery was loaded in case there are any triggers needed to be made. And only after jQuery is loaded does it call my widget, passing jQuery into it.

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
            
### Create Namespace
    CreateNamespace: function (nsString) {
        var ns;
        if (nsString) {
            ns = nsString.split(".").reduce(function (pv, cv) { return pv[cv] = pv[cv] || {}; }, exports);
        }
        return ns;
    }

### Create GUID
    NewGuid: function (nsString) {
        function s4() {
            return Math.floor((1 + Math.random()) * 0x10000)
                .toString(16)
                .substring(1);
        }
        return s4() + s4() + '-' + s4() + '-' + s4() + '-' + s4() + '-' + s4() + s4() + s4();
    }
		
### Parse Query String
    function getUrlVars(the_widget) {
        var vars = [], hash;
        var hashes = the_widget.attr("src").split('?')[1].split('&');
        for(var i = 0; i < hashes.length; i++)
        {
            hash = hashes[i].split('=');
            vars.push(hash[0]);
            vars[hash[0]] = hash[1];
        }
        return vars;
    }
