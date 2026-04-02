# ♡ TryHeartme ₊˚
Actually had so much fun solving this one ദ്ദി ༎ຶ‿༎ຶ )

# Challenge Info
Platform: <img src="https://img.shields.io/badge/TryHackMe-pink?style=flat&logo=TryHackMe&logoColor=white" />

Difficulty: Easy 

<img src="../images/chief.png"/>

Chief had spoken, so lets find this Valenflag (ㅅ´ ˘ `)

# 🌹 Getting Into it
After opening the given URL, the valentines shop appeared

<img src="../images/shop.png"/>

..... I have a feeling this might be easy

I then, navigated to a product page to analyze whether the URL endpoint changes dynamically, and as expected, It did

<img src="../images/roses.png"/>

<i>/product/rose-bouquet</i> for the Rose bouquet, this confirmed my suspicion, The Joker was me at that time ^

# 🥀 Not Getting it
Of course my first instinct was to modify the URL to /product/valenflag ( thinking it might be an IDOR vuln ), expecting it to work and call it a day. However, I was met with a 404 error.

<img src="../images/urly.png"/>

Safe to say, I was quickly humbled. But this is where the fun began, a challenge ദ്ദി ˉ͈̀꒳ˉ͈́ )✧

# 🏹 Diving In
So I pulled back, I suspected that the valenflag might not be visible to guest users, so I followed the application’s intended flow, creating an account and logging in.

<img src="../images/account.png"/>

**After logging in, I noticed:**

♡ An account was successfully created, with the email displayed in the interface.

♡ No account identifier was present in the URL.

♡ The account balance was 0 credits.

♡ Purchasing items requires credits.

♡ Top-ups are disabled, preventing legitimate credit acquisition.

<i>Noticing my role was ‘user’, I suspected that a higher-privileged role, such as ‘admin’, might exist.</i>

# 💔 Access Denied
Using Gobuster, I discovered an **/admin** endpoint. However, attempting to access it resulted in a 403 Forbidden response. The page clearly existed… I just wasn’t allowed in, so its time to break in ᗜ⩊ᗜ

<img src="../images/buster.png"/>

# "Breaking In"
After encountering the restricted /admin endpoint, I attempted to use ffuf to uncover potential administrative accounts by testing different wordlists and filters. However, this approach led nowhere.

<img src="../images/radar.png"/>

Time to pivot, using Burp Suite, I analyzed the application requests. While intercepting requests, I spotted a session cookie **(tryheartme_jwt)**, and that immediately caught my attention. It suggested that authentication and user state were likely being handled through it

<img src="../images/cookiez.png"/>

# 🍪 Decoding the Session Token
Since the cookie seemed to be encoded , I attempted to decode it and it revealed values like, **role** and **credits**. I then modified the values, role to admin and added some credits, then re-encoded it and tried using it by intercepting requests in Burp Suite.

<img src="../images/encode.png"/>

 However, the application did not accept the modified token, Instead it logged me out

 
# ACTUALLY Decoding the Session Token ᯓ🍪
At this point, I took a step back and looked more closely at the token structure, tryheartme_**jwt** I didn’t initially realize how significant JWTs were, but I decided to look it up. That’s when everything started to make sense.

 <img src="../images/jwt.png"/>

Now that I'm aware that JWT is significant and not just a random array of letters, I repeated the previous step using a JWT decoder, I could clearly see the token’s structure and its payload now, including fields like email, role, and credits.
I then modified the payload by setting the role to admin and increasing the credits, then re-encoded the token.


 <img src="../images/jwt2.png"/>

 with that we got our new modified token

 # 🧸 Privilege Escalation via JWT Manipulation
 Testing the Modified Token, we finally got admin, and also 10 million credits (∩˃ᴗ˂∩)✧, also an **admin** button that was particularly compelling to investigate

 <img src="../images/admined.png"/>

Continuing, I tried accessing the /admin page, but was redirected back to the error page. Checking Burp Suite, I noticed that the cookie had been changed back to its original values, with the role set to user and credits at 0.

 <img src="../images/user.png"/>


# 💌 Final admin access
I re-intercepted the request and applied the modified cookie, and there it is, the admin portal and the Valenflag

 <img src="../images/portall.png"/>

<b> I noticed that the token was refreshed at each stage of the buying process, so I had to intercept and replace the cookie in every request to keep my elevated privileges, 

from the admin portal > Valenflag > Buy > and finally the Receipt <i> (Where the flag is) </i></b>

 <img src="../images/valenflagged.png"/>

 And with that, the challenge was complete (˶' ꒳ '˶)

 # Conclusion ‧₊
This challenge demonstrated how relying on client-side tokens without proper validation can lead to complete privilege escalation

 <img src="../images/donttrust.png"/>

 # Timelapse :3





