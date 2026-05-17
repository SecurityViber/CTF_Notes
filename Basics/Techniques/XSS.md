
## Types of XSS

Stored (Persistent XSS)
- The most critical type of XSS, which occurs when user input is stored on the back-end database and then displayed upon retrieval 
Reflected (Non-Pesistent) XSS
- Occurs when user input is displayed on the page after being processed by the backend server, but without being stored 
DOM-based XSS
- Another Non-Persistent XSS type that occurs when user input is directly shown in the browser and is completely processed on the client-side, without reaching the back-end server 

## Detection 

Can be done using Vulnerability Scanners such as 
- Nessus
- Burp Pro
- ZAP

Other Open Source tools which can Help you are 
- XSS Strike
- Brute XSS 
- XSSer

## Where to find payloads
- PayloadAllTheThings

## Defacement Elements 
Usually when there is a way to have a stored XSS, we can persistently deface the Website. For this the following elements are usually used:
- documetn.body.style.background
- document.body.background
- document.title
- DOM.innerHTML