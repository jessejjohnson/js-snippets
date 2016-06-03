#Useful JavaScript Snippets

## Resolve jQuery
### Loads jQuery if it's not already loaded and encapsulates it in the callback so it doesn't conflict with a pre-existing jQuery on the page. It also checks that a minimum version is available or else loads a known version -- in this case, v1.3. It sends a boolean value to the callback (my widget) on whether or not jQuery was loaded in case there are any triggers needed to be made. And only after jQuery is loaded does it call my widget, passing jQuery into it.
    NewGuid: function (nsString) {
        function s4() {
            return Math.floor((1 + Math.random()) * 0x10000)
                .toString(16)
                .substring(1);
        }
        return s4() + s4() + '-' + s4() + '-' + s4() + '-' + s4() + '-' + s4() + s4() + s4();
    }
		
## New Namespace
    CreateNamespace: function (nsString) {
        var ns;
        if (nsString) {
            ns = nsString.split(".").reduce(function (pv, cv) { return pv[cv] = pv[cv] || {}; }, exports);
        }
        return ns;
    }

## New GUID
    NewGuid: function (nsString) {
        function s4() {
            return Math.floor((1 + Math.random()) * 0x10000)
                .toString(16)
                .substring(1);
        }
        return s4() + s4() + '-' + s4() + '-' + s4() + '-' + s4() + '-' + s4() + s4() + s4();
    }
		
