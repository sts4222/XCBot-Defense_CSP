# F5XC Bot Defense Standard with CSP nonce


Content Security Policy (CSP) is an added layer of security that helps to detect and mitigate certain types of attacks. By using CSP, the browser is instructed to do or not to do specific actions. The concept of CSP is designed to be fully backward compatible (i.e. browsers that do not support CSP will ignore it) and it i.e. supports a directive to control external and internal sources (scripts, styles, etc.). 

CSP nonce is a way to verify, how scripts (especially dynamic scripts) and sources should be handled in the browser. The nonce by itself is a "one-time" (per request) and randomized value, which is only known by the server / app and only scripts which have this nonce included (i.e. in the source definition) will be executed.

<br />

example:

without nonce: 

`<script type="text/javascript" src="/script.js"></script>`

with nonce: 

`<script nonce=SKCAMqPkuwf0mAao9uHjwMfUt05HbcXRwrPB3xOtEmhm5tN7bc7VOFNHxwYSo9JfrNDHoAUkSRTEs55eYLPzNXR02fmEsaHxWO2cHunJCvSYhzgYvSQrEcmXlZAr2X7k type="text/javascript" src="/script.js"></script>`

<br /> 
In additon, the CSP HTTP Response Header must include the current nonce value, so that the browser can compare against it.

<br />
example: 

(HTTP header) Content-Security-Policy:

 `... nonce=SKCAMqPkuwf0mAao9uHjwMfUt05HbcXRwrPB3xOtEmhm5tN7bc7VOFNHxwYSo9JfrNDHoAUkSRTEs55eYLPzNXR02fmEsaHxWO2cHunJCvSYhzgYvSQrEcmXlZAr2X7k ...`

<br />

---
## Bot Defense Standard (BIG-IP) with CSP nonce
<br />

To enable F5XC Bot Defense Standard together with CSP nonce, the iApp needs to be adjusted with the following commands. 

<br />

**_NOTE:_** This integration is for the iApp version 3.04b only.

**_NOTE:_** The iApp can be downloaded from the app definition within the F5XC portal 

<br /> 

example:

![](/images/image-01.png)

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

