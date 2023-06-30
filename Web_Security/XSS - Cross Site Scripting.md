When an application insecurely processes some user provided input, the attacker can inject malicious code or markup into the input. This is called Cross Site Scripting.

The attacker can now executre their JS in the potentially logged-in staate of their victim. This can be used to conduct different types of attacks

1. Steal cookies to hijack victims session
2. Manipulate page. Inject a password confirmation page to leak users password.
3. Depending upon application attacker can interact with the application as the victim user.

XSS can be categorized into two dimensions:
1. Location of the vulnerable code (server or client side).
2. Whether attack payload is permanently stored (Persistent XSS) or extracted from ex. URL every time a user visits (Reflected XSS).

XSS is possible when attacker-controllable data is mixed into the HTML or JS code that makes up the application.