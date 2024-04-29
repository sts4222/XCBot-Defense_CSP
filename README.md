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

To enable F5XC Bot Defense Standard together with CSP nonce, the iApp needs to be adjusted with the following commands, based on the iApp version. 

**_NOTE:_** Click on the folder for the iApp version you want to add the changes.