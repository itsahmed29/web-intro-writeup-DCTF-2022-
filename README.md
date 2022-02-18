# web-intro writeup [DCTF 2022]

Hello friends, in this article we gonna take a look at the challenge web-intro from the DCTF 2022.

`Challenge description : Are you an admin?`

We are given an address to connect to the website : `35.246.158.241:31589`.
When entering the website we see this messages saying `Access Denied`


![Untitled](https://user-images.githubusercontent.com/55143192/154682416-75910091-87da-4bb7-a6ae-2270bae44b9b.png)

Looking at the source code we can’t see anything. But if we look at the cookies we can see our session.

![Untitled2](https://user-images.githubusercontent.com/55143192/154682466-fa96058a-0432-4ff2-8f0d-1cd7dad655d1.png)

Taking the value to jwt.io to decode it , we get the following :

![Untitled3](https://user-images.githubusercontent.com/55143192/154682516-1ef59ba5-647b-40e4-aec1-1a8f271333d5.png)

`{"logged_in": false}`

As we can see We are dealing with `Flask Session Cookies` , so I googled some “flask cheat sheet hacktricks” I got this amazing article which cover the Flask Session Cookies
https://book.hacktricks.xyz/pentesting/pentesting-web/flask .
Following this article , first thing we gonna do is decoding the cookie using the following command `flask-unsign --decode --cookie 'eyJsb2dnZWRfaW4iOmZhbHNlfQ.Yg9geQ.s8MKSRemMQyS5S60QTS0lY0Xg0o’` by doing that We get the following output ( like jwt.io earlier )
![Untitled4](https://user-images.githubusercontent.com/55143192/154682876-71b4251e-c84c-4fe6-aefe-39cb1ad76390.png)

Now we need to bruteforce the secret which was used to sign the original cookie and for this we gonna use this command :
`flask-unsign --unsign --cookie < cookie.txt` where `cookie.txt` is the file containing our cookie. [ We can also use this command : `flask-unsign --unsign --server <target>` ] 

![Untitled5](https://user-images.githubusercontent.com/55143192/154683028-690ea2cb-6527-4723-9e40-0a75c21bb6dc.png)

Quickly we get our secret key which is `password` . Now all we need to do is signing the cookie with the secret `password` but we need to change the `{"logged_in": false}` to `{"logged_in": True}` .

So the command will be : `flask-unsign --sign --cookie "{'logged_in': True}" --secret 'password'`

After doing that we get our new cookie.

![Untitled7](https://user-images.githubusercontent.com/55143192/154683085-956d94b2-39ab-40be-8369-0abbd83dd680.png)

Am using a cool chrome extension called `EditThisCookie` . Simply paste the value there and hit the green thingy.

![Untitled8](https://user-images.githubusercontent.com/55143192/154683179-ff942d2b-1d75-4af6-abe2-ba5700493e8e.png)

Refresh the page and theeereeeee We go!! We got our flag 

![Untitled9](https://user-images.githubusercontent.com/55143192/154684162-60aa982a-bab4-4b56-8c12-ce6fd95eadea.png)

`flag : CTF{66bf8ba5c3ee2bd230f5cc2de57c1f09f471de8833eae3ff7566da21eb141eb7}`

I hope You enjoyed this writeup :)

PS : The challenge is available in the Educational Archive here : 
https://app.cyberedu.ro/challenges/93550ce0-8a54-11ec-b670-134e64dab450/

