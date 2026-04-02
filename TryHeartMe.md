# ♡ TryHeartme ₊˚
Actually had so much fun solving this one ദ്ദി ༎ຶ‿༎ຶ )

# Challenge Info
Platform: <img src="https://img.shields.io/badge/TryHackMe-pink?style=flat&logo=TryHackMe&logoColor=white" />

Difficulty: Easy 

<img src="images/chief.png"/>

Chief had spoken, so lets find this Valenflag (ㅅ´ ˘ `)

# Getting Into it
After opening the given URL, the valentines shop appeared

<img src="images/shop.png"/>

..... I have a feeling this might be easy

I then, navigated to a product page to analyze whether the URL endpoint changes dynamically, and as expected, It did

<img src="images/roses.png"/>

<i>/product/rose-bouquet</i> for the Rose bouquet, this confirmed my suspicion, The Joker was me at that time ^

# Not Getting it
Of course my first instinct was to modify the URL to /product/valenflag ( thinking it might be an IDOR vuln ), expecting it to work and call it a day. However, I was met with a 404 error.

<img src="images/urly.png"/>

Safe to say, I was quickly humbled. But this is where the fun began, a challenge ദ്ദി ˉ͈̀꒳ˉ͈́ )✧

# Diving In
So I pulled back, I suspected that the valenflag might not be visible to guest users, so I followed the application’s intended flow, creating an account and logging in.

<img src="images/account.png"/>

**After logging in, I noticed:**

♡ An account was successfully created, with the email displayed in the interface.

♡ No account identifier was present in the URL.

♡ The account balance was 0 credits.

♡ Purchasing items requires credits.

♡ Top-ups are disabled, preventing legitimate credit acquisition.

<i>Noticing my role was ‘user’, I suspected that a higher-privileged role, such as ‘admin’, might exist.</i>

# Access Denied
Using Gobuster, I discovered an **/admin** endpoint. However, attempting to access it resulted in a 403 Forbidden response. The page clearly existed… I just wasn’t allowed in, so its time to break in ᗜ⩊ᗜ

<img src="images/buster.png"/>

# "Breaking In"
After encountering the restricted /admin endpoint, I attempted to use ffuf to uncover potential administrative accounts by testing different wordlists and filters. However, this approach led nowhere.

<img src="images/radar.png"/>

Time to pivot, using Burp Suite, I analyzed the application requests. While intercepting requests, I spotted a session cookie **(tryheartme_jwt)**, and that immediately caught my attention. It suggested that authentication and user state were likely being handled through it

<img src="images/cookiez.png"/>

# Decoding the Session Token
Since the cookie seemed to be encoded , I attempted to decode it and it revealed values like, **role** and **credits**. I then modified the values, role to admin and added some credits, then re-encoded it and tried using it by intercepting requests in Burp Suite. However, the application did not accept the modified token, Instead it logged me out

<img src="images/encode.png"/>
