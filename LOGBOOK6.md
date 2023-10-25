## This week's CTF

- Upon arriving at the website, we were greeted by an input box and an id to our request. After testing a request with a random string, we then went to the _admin_ webpage and saw that the buttons were disabled and there was a link to our justification. Using inspect element, we could see that the disabled buttons represented two different forms with two different action links, one for approval, the other one for disapproval. Focusing on the approval link, we saw that it followed the structure of `ctf-fsi.fe.up:5005/request/{id of request}/approve`. Going back to the user input webpage, we tested the XSS capabilities by typing in `<script>alert('hello')</script>` and after we submited the request the alert popped up, indicating that the user input wasn't controlled or encoded and XSS was possible.

- Following that discovery, we decided to move on to developing the CSRF script. Since the admin would open the justification webpage to verify it and we can control what is in that webpage, we saw a possibility for CSRF. We decided to first try to use javascript to just redirect the admin to the `approve` webpage by inputing `<script> window.location.replace("http://ctf-fsi.fe.up.pt:5005/request/{id of request}/approve")</script>` but we saw that it raised a `Method Not Allowed` error. We then concluded that the website may be expecting a POST request for that approve link, just like the approval form on the admin webpage. So, we decided to try the following:
```html
<form action="http://ctf-fsi.fe.up.pt:5005/request/{id_of_request}/approve" method="POST" id="csrf_form">
    <input type="hidden" name="hello">
</form>

<script>document.getElementById("csrf_form").submit()</script>
```

- This way, when the admin entered the justification webpage, the form with a correct approval link would automatically be submited, and since the admin was previously logged in, his SESSION token were in the browser's cookies, so the form would be submited without any issues because it would think the admin submited it.

- There was a problem, when we used this justification, we would be automatically redirected to the approve webpage and we would get a `Forbidden` error. We found a workarround, after waiting a couple of seconds for the admin to fall for out _trap_, we decided to load into the webpage `ctf-fsi.fe.up.pt:5004/request/{id_of_request}` (the justification page), and we stopped the redirect by clinking the `X` in the refresh symbol of our browser, allowing us to stay there and see the flag revealed to us.

- This was the flag we got for the id f748e071d63b1a20d029f6d634af24c5 : flag{f748e071d63b1a20d029f6d634af24c5} 

- Later, we found a workaround for this problem. It consists in changing the input form so it submits in another tab. This way we are able to stay in `http://ctf-fsi.fe.up.pt:5004/request/{id_of_request}` and see the flag. This would be the new input:


```html
<form action="http://ctf-fsi.fe.up.pt:5005/request/{id_of_request}/approve" method="POST" id="csrf_form" target="_blank">
    <input type="hidden" name="hello">
</form>

<script>document.getElementById("csrf_form").submit()</script>
```
