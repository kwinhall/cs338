# Cookies and cross-site scripting (XSS)

By Kendra Winhall

## Part 1: Cookies

* a. Go to FDF and use your browser's Inspector to take a look at your cookies for cs338.jeffondich.com. Are there cookies for that domain? What are their names and values?
    * Yes, there's a cookie for cs338.jeffondich.com. Its name is `theme` and its value is `default`.
* b. Using the "Theme" menu on the FDF page, change your theme to red or blue. Look at your cookies for cs338.jeffondich.com again. Did they change?
    * Yes, I changed the theme to red, and the `theme` cookie's value changed to `red`.
* c. Do the previous two steps (examining cookies and changing the theme) using Burpsuite. What "Cookie:" and "Set-Cookie:" HTTP headers do you see? Do you see the same cookie values as you did with the Inspector?
    * For part (a), the browser sends an HTTP GET request for `/fdf/` and the server's response includes the header `Set-Cookie: theme=default; Expires=Thu, 23 Jan 2025 23:35:58 GMT; Path=/`. The subsequent HTTP GET requests sent by my browser include the header `Cookie: theme=default`.
    * For part (b), the browser sends an HTTP GET request for `/fdf/?theme=red`. The server's response includes the header `Set-Cookie: theme=red; Expires=Thu, 23 Jan 2025 23:42:15 GMT; Path=/`. The subsequent HTTP GEt requests sent by my browser includethe header `Cookie: theme=red`.
    * The cookie values are the same as they were for the Inspector.
* d. Quit your browser, relaunch it, and go back to the FDF. Is your red or blue theme (wherever you last left it) still selected?
    * Yes.
* e. How is the current theme transmitted between the browser and the FDF server?
    * The browser stores the data about the theme in a cookie. When the browser sends an HTTP request to the FDF server, it includes the data about the theme under the HTTP header `Cookie`.
* f. When you change the theme, how is the change transmitted between the browser and the FDF server?
    * After the browser sends an HTTP request to the FDF server, the FDF server's response includes the data about the new theme color under the HTTP header `Set-Cookie`.
* g. How could you use your browser's Inspector to change the FDF theme without using the FDF's Theme menu?
    * I go to the `Storage` tab in the Inspector to view the cookies. I double click on the `value` field of the theme cookie and type in `blue`. When I reload the webpage, the theme is now blue.
* h. How could you use Burpsuite's Proxy tool to change the FDF theme without using the FDF's Theme menu?
    * I enable the `Intercept` option and go to to the webpage. When my browser is about to send the HTTP GET request, I go to the `Cookie` header and change `theme=default` to `theme=blue`. Then I press the `Forward` button. When I look at the webpage, the theme is now blue.
* i. Where does your OS (the OS where you're running your browser and Burpsuite, that is) store cookies? (This will require some internet searching, most likely.)
    * On my Mac, I navigate to `Library/Application Support/Firefox/Profiles/<my profile string>.default`. This folder contains a database called `cookies.sqlite`, which stores cookies.

## Part 2: Cross-Site Scripting (XSS)

* a. Provide a diagram and/or a step-by-step description of the nature and timing of Moriarty's attack on users of the FDF. Note that some of the relevant actions may happen long before other actions.
    * First evil post
        * Moriarty logs in.
        * Moriarty writes a post consisting of source code that includes `<span>` tags. 
        * Moriarty presses the "Submit Query" button.
        * The browser sends an HTTP POST request to the server. The request contains the title and the body of Moriarty's post, including the `<span>` tags. 
        * The server stores the data and displays the title of the post. Importantly, the server does not sanitize Moriarty's input.
        * An undetermined amount of time passes.
        * When a user clicks on Moriarty's post, the browser sends an HTTP GET request. 
        * The server sends a 200 OK response, which contains the post title, content, and source code.
        * The browser shows the post title, content, and source code to the user.
        * The browser automatically executes Moriarty's source code, turning the text red.
    * Second evil post
        * Moriarty logs in.
        * Moriarty writes a post consisting of source code that includes `<script>` tags and the JavaScript `alert` function. 
        * Moriarty presses the "Submit Query" button.
        * The browser sends an HTTP POST request to the server. The request contains the title and the body of Moriarty's post, including the `<script>` tags. 
        * The server stores the data and displays the title of the post. Importantly, the server does not sanitize Moriarty's input.
        * An undetermined amount of time passes.
        * When a user clicks on Moriarty's post, the browser sends an HTTP GET request. 
        * The server sends a 200 OK response, which contains the post title, content, and source code.
        * The browser shows the post title, content, and source code to the user.
        * The browser automatically executes Moriarty's source code, which makes an alert pop up on screen.

* b. Describe an XSS attack that is more virulent than Moriarty's "turn something red" and "pop up a message" attacks. Think about what kinds of things the Javascript might have access to via Alice's browser when Alice views the attacker's post.
    * One attack could involve accessing or changing the values of the cookies. For instance, in one of my posts, I reset the `session` cookie to automatically log out a user. If this was a website for something more important, being logged out every time you click on a certain thing could be a real problem. In addition, the attacker could go one step further and actually access and save the user's `session` cookie. This gives the attacker the ability to log in as that user without even knowing their username or password. 

* c. Do it again: describe a second attack that is more virulent than Moriarty's, but that's substantially different from your first idea.
    * Another attack could involve redirecting the user to a malicious website that imitates the original website. This is what I did in one of my posts. I created a homepage and login page that look identical to Jeff's site. I deployed my site using Cloudflare Pages. Then, I created a post on Jeff's site that automatically redirects the user to my malicious site's login page. I put the script in my title, so the user doesn't even have to click on my post; when you go to Jeff's FDF homepage, it automatically redirects to my page. Once the user logs in, I tell them that they've been tricked and I display their email and password. If the user doesn't look at the URL, they could input their credentials to my malicious website.

* d. What techniques can the server or the browser use to prevent what Moriarty is doing?
    * The server could immediately encode user input in HTML before processing it or saving it in the database. This would turn `<` into `&lt;` and `>` into `&gt;`, so any JavaScript code that a user puts in their post title or body won't execute.
    * The server could just block any code in `<script>` blocks from executing.
    * The browser and server could implement the Content Security Policy. The person managing the website could specifiy which domains are trusted. Then the browser would ignore inline scripts (like the ones in malicious posts) and only execute JavaScript code located in files from a trusted domain.
