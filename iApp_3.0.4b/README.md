> **Disclaimer: The changes below will change the officially supported iApp version to provide the best user experience (integrated iRules). However, be aware that ALL changes are on your own risk.**

<br />

**_NOTE:_** This integration is for the iApp version 3.04b only.

**_NOTE:_** The iApp can be downloaded from the app definition within the F5XC portal 

<br /> 

example:

![example](/images/image-01.png)

<br />

---
## Add this code to template file. 

---
### Verify if CSP is enabled and if nonce is used.

* Open the iApp template with any editor 
* add / change the following code at line: 2078

<br />

        set nonce 1
        if {[HTTP::header exists "Content-Security-Policy"]} {
                set nonce [findstr [HTTP::header Content-Security-Policy] \'nonce 7 \']
        } elseif {[HTTP::header exists "Content-Security-Policy-Report-Only"]} {
                set nonce [findstr [HTTP::header Content-Security-Policy-Report-Only] \'nonce 7 \']
        } else {
        return
        }

<br />

![example](/images/image-02.png)

<br />

---
### insert the nonce value (if present) into the existing JS_snippet

* add / change the code at line: 2235 (after adding the first code snippet above)

        if {$nonce != 1} {
                set map_find "<script"
                set map_replace "<script nonce=$nonce"
                regsub -nocase -all $map_find "${I1_js_io_snippet}" $map_replace js_snippet
        } else {
        set js_snippet $I1_js_io_snippet
        }

* add / change the code at line: 2246 (after adding the code snippet above)

        if {$nonce != 1} {
                regsub -nocase -all $map_find "${I1_js_io_snippet}${I1_js_tel_snippet}" $map_replace js_snippet
        } else {
        set js_snippet ${I1_js_io_snippet}${I1_js_tel_snippet}
        }

<br />

![example](/images/image-03.png)

<br />

* add / change the code at line: 2274 (after adding the code snippet above)

        unset nonce

<br />

![example](/images/image-04.png)

<br />

---
### Save and import the template into your BIG-IP and use it / replace the old one.  

