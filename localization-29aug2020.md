

{ "name": "How we coded the localization system", "creator": "Hubz" }

Today I wanted to take a minute to talk about the internal workings of the localization system for this site, i think it's pretty cool.
We have a MySQL database, filled with strings (see image)
![](https://upload.hubza.co.uk/i/firefox_xTfmIoXnXV_2020-August-29.png)
I wrote down all the strings i could find on the site into this, and then got a bunch of friends to login to the panel to translate them.

At this point I needed an easy way to grab one of these strings, i made this simple php function:
```php
function grabtext($text, $var1 = 0, $var2 = 0){
    require dirname(__FILE__) . '/api/database.php';

    $lang = $_COOKIE["lang"];

    $sql = $mysqli_conection->query("SELECT * FROM lang WHERE name = '" . $text . "'");

    $failed = False;

    while ($res = $sql->fetch_assoc()) { 
        $response = $res[$lang];
        if($response == ""){
            $response = $res['english'];
            
        }
    }

    $response = str_replace("{0}", $var1, $response);
    $response = str_replace("{1}", $var2, $response);
  
    return $response;
}
```

I needed to be able to place variables into the text, so I have two "var"s which are optional.
I grab the user's requested language through the cookies, then try to grab the text from the database, placing in the requested language.
If the response is nothing, which would happen if the translation is incomplete, it'll default to english, then it'll replace the variables and return.

I placed this function in a php file called "generic.php", which also includes many other functions and loads the base css for the site, which is included everywhere.
Then i could just run it, here's an example:
```html
<a class="restr-text rt"><?php echo grabtext("restriction-reason", $reason); ?></a>
```

thought it'd be nice to show people some insight onto how our systems work, and also we needed another news post for the homepage test.
Thanks for reading
