
**_NOTE:_** This integration is for the iApp version 3.04b only.

**_NOTE:_** The iApp can be downloaded from the app definition within the F5XC portal 

<br /> 

example:

![example](../images/picture-01.png)

<br />

---
### Verify if CSP is enabled and if nonce is used.

Open the iApp template with any editor and the following code at line: 2078

<br />

`set nonce 1
    if {[HTTP::header exists "Content-Security-Policy"]} {
	    set nonce [findstr [HTTP::header Content-Security-Policy] \'nonce 7 \']
    } elseif {[HTTP::header exists "Content-Security-Policy-Report-Only"]} {
        set nonce [findstr [HTTP::header Content-Security-Policy-Report-Only] \'nonce 7 \']
    } else {
    return
    }`

<br />

---
### insert the nonce value (if present) into the existing JS_snippet

add / change the code at line: 2235 (after adding the first code snippet above)

`if {$nonce != 1} {
        set map_find "<script"
        set map_replace "<script nonce=$nonce"
        regsub -nocase -all $map_find "${I1_js_io_snippet}" $map_replace js_snippet
} else {
	set js_snippet $I1_js_io_snippet
}`


add / change the code at line: 2246 (after adding the code snippet above)

`if {$nonce != 1} {
        regsub -nocase -all $map_find "${I1_js_io_snippet}${I1_js_tel_snippet}" $map_replace js_snippet
} else {
        set js_snippet ${I1_js_io_snippet}${I1_js_tel_snippet}
}`

add / change the code at line: 2274 (after adding the code snippet above)

`unset nonce`

---
### Save and import the template into your BIG-IP and use it / replace the old one.  

