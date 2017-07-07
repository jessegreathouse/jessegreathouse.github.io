# Stop using JSON. Start using HTML

## Stop engineering front-end code like this

Too often, I see front end applications, when rendered, look something [like this](https://jessegreathouse.github.io/stop-doing-this-with-JSON/stop-engineering-front-end-code-like-this.html):


```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Stop Engineering Front End Code Like This</title>
</head>
<body>

<script language='javascript'>
    JSDATA = {
        people: [{
            name: 'William',
            age: '23',
            address: '2201 Washington Blvd Houston, TX, 77022'
        }]
    };
</script>

<table id="contacts">
    <thead>
        <tr>
            <th>Name</th>
            <th>Age</th>
            <th>Address</th>
        </tr>
    </thead>
    <tbody>

    </tbody>
</table>

<script
        src="https://code.jquery.com/jquery-3.2.1.min.js"
        integrity="sha256-hwg4gsxgFZhOsEEamdOYGBf13FyQuiTwlAQgxVSNgt4="
        crossorigin="anonymous"></script>

<script language='javascript'>
    var contactsTbl = $('#contacts');

    // transformer
    function loadContactInfo(person, myTable) {
        myTable.find('tbody').append('<tr><td>' + person.name + '</td><td>' + person.age + '</td><td>' + person.address + '</td></tr>');
    }

    $(document).ready(function(){
        // parsing data
        for (var i=0; i < JSDATA.people.length; i++) {
            loadContactInfo(JSDATA.people[i], contactsTbl);
        }
    });
</script>

</body>
</html>

````

This is what we call, in the business, over-engineering. It’s arbitrarily using JSON as a data format, when the whole document is being produced in a perfectly acceptable data format: HTML. The choice of putting your data into JSON format, means that you will have also develop and maintain some kind of transformer for this data. For a simple webpage, this doesn't seem like a big deal. But when your web page sprawls into a giant site, it will require a lot of work to make sure data transformers are all maintained and working correctly, as your site's content evolves and changes.

## Instead, engineer front-end code like this

Instead of engineering your front end code, like the above example, do yourself a favor and engineer it [like this](https://jessegreathouse.github.io/stop-doing-this-with-JSON/instead-engineer-front-end-code-like-this.html):

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Instead Engineer Front End Code Like This</title>
</head>
<body>
<table id="contacts">
    <thead>
    <tr>
        <th>Name</th>
        <th>Age</th>
        <th>Address</th>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td>William</td>
        <td>23</td>
        <td>2201 Washington Blvd Houston, TX, 77022</td>
    </tr>
    </tbody>
</table>

<script language='javascript'>
    //...
</script>
</body>
</html>

````

## Stop self-informing UI data like this

I know what you’re thinking… there’s a REASON Why it’s being engineered like the first example, and it’s true … but it’s a bad reason.

Somewhere in your application there are other pages to render data like a REST APIs JSON response, so you can dynamically update this user interface. It sounds like a great idea, but it’s not. Why? Because, once again, it’s over engineering.

Somehwere in your application are endpoints like these here:
[Fred](https://jessegreathouse.github.io/stop-doing-this-with-JSON/users/fred/contact.json)
[Wilma](https://jessegreathouse.github.io/stop-doing-this-with-JSON/users/wilma/contact.json)
[Barney](https://jessegreathouse.github.io/stop-doing-this-with-JSON/users/barney/contact.json)
[Betty](https://jessegreathouse.github.io/stop-doing-this-with-JSON/users/betty/contact.json)

Your code is meant to allow the user to interact with the data via the UI and retrieve this information on demand. It's a reasonable expectation, but too often I see developers make bad a bad decision and do it [like this](https://jessegreathouse.github.io/stop-doing-this-with-JSON/stop-self-informing-ui-data-like-this.html):

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Stop Self Informing UI Data Like This</title>
</head>
<body>

<script language='javascript'>
    JSDATA = {
        people: [{
            name: 'William',
            age: '23',
            address: '2201 Washington Blvd Houston, TX, 77022'
        }]
    };
</script>

<table id="contacts">
    <thead>
    <tr>
        <th>Name</th>
        <th>Age</th>
        <th>Address</th>
    </tr>
    </thead>
    <tbody>

    </tbody>
</table>

<!-- I use buttons because links would serve no purpose -->
<button class="invite-btn" data-user="fred">Invite Fred</button>
<button class="invite-btn" data-user="wilma">Invite Wilma</button>
<button class="invite-btn" data-user="barney">Invite Barney</button>
<button class="invite-btn" data-user="betty">Invite Betty</button>

<script
        src="https://code.jquery.com/jquery-3.2.1.min.js"
        integrity="sha256-hwg4gsxgFZhOsEEamdOYGBf13FyQuiTwlAQgxVSNgt4="
        crossorigin="anonymous"></script>

<script language='javascript'>
    var contactsTbl = $('#contacts');

    // transformer
    function loadContactInfo(person, myTable) {
        myTable.find('tbody').append('<tr><td>' + person.name + '</td><td>' + person.age + '</td><td>' + person.address + '</td></tr>');
    }

    // method for knowing how to retrieve data
    function inviteFriend(name) {
        var url = 'users/' + name + '/contact.json';
        $.getJSON( url, function(person) {
            loadContactInfo(person, contactsTbl);
        });
    }

    $(document).ready(function(){
        // event handler
        $('.invite-btn').click(function(e){
            var btn = $(e.currentTarget);
            var name = btn.attr('data-user');
            inviteFriend(name);
            btn.remove();
        });

        // parsing data
        for (var i=0; i < JSDATA.people.length; i++) {
            loadContactInfo(JSDATA.people[i], contactsTbl);
        }
    });
</script>

</body>
</html>

````

The above code does accomplish what is intended, but at too high of a cost. In a simple example, it may seem easy to maintain a transformer for the data, and a function to arrange the data retrieval. But spread over the many pages and features of an immense website, these are the bits that get difficult to maintain. Every time a stakeholder wants to change some aspect of the site, these little bits need to have their jobs updated also. It gets very difficult to maintain the many places where these functions live, every time something changes.

Doing it this way, has another consequence that you may not have considered. If the client doesn't have JavaScript, they client cannot use your UI. I can hear the Guffaws now, a client not using JavaScript? We don't want those kind of users, I hear some saying... Well, that's your perogative, but the next time your site breaks because Chrome, Firefox, or Edge release a new version, don't say I didn't warn you.

## Instead, self-inform UI data like this

Instead of falling into the trap of the above example, I'm going to suggest something that might sound wrong to many people, but I want you to really think it over. We use JSON because it's a data transfer format. It's not data, it's not the end result, it's an inbetween format. This is, no doubt, important for countless reasons, but I suggest to you that it's not necessary for your UI. Rather than having the data endpoints above rendered in JSON, I suggest you render them with plain HTML:
[Fred](https://jessegreathouse.github.io/stop-doing-this-with-JSON/users/fred/contact.html)
[Wilma](https://jessegreathouse.github.io/stop-doing-this-with-JSON/users/wilma/contact.html)
[Barney](https://jessegreathouse.github.io/stop-doing-this-with-JSON/users/barney/contact.html)
[Betty](https://jessegreathouse.github.io/stop-doing-this-with-JSON/users/betty/contact.html)

HTML is a data format, in the same way JSON is. Relax, lower your heart rate, and just take it what I'm saying. There's no benefit to using JSON instead of HTML. HTML is used to create a document. The document HTML produces is about organizing information. The stylistic presentation of your site should be informed by CSS, not the document structure. The difference in payload between an HTML document and a JSON response is usually so infentessimally small that it's not a realistic to cite as a reason for using JSON.

Lets face it, developers use JSON because they like it. If you can own that, then more power to you, but stop lying to yourselves and trying to make yourself believe there's a good reason for designing UIs this way. 90% of the UI's I see doing this are just creating more work for themselves and cluttering up their code with extra transformers and data retrieval methods.

Instead of doing it like the above examples, consider doing it [like this](https://jessegreathouse.github.io/stop-doing-this-with-JSON/instead-self-inform-ui-data-like-this.html):

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Instead Self Inform UI Data Like This</title>
</head>
<body>
<table id="contacts">
    <thead>
    <tr>
        <th>Name</th>
        <th>Age</th>
        <th>Address</th>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td>William</td>
        <td>23</td>
        <td>2201 Washington Blvd Houston, TX, 77022</td>
    </tr>
    </tbody>
</table>

<!-- Using links because they have an intrinsic purpose -->
<a class="invite-link" href="users/fred/contact.html">Invite Fred</a>
<a class="invite-link" href="users/wilma/contact.html">Invite Wilma</a>
<a class="invite-link" href="users/barney/contact.html">Invite Barney</a>
<a class="invite-link" href="users/betty/contact.html">Invite Betty</a>

<script
        src="https://code.jquery.com/jquery-3.2.1.min.js"
        integrity="sha256-hwg4gsxgFZhOsEEamdOYGBf13FyQuiTwlAQgxVSNgt4="
        crossorigin="anonymous"></script>

<script language='javascript'>
    $(document).ready(function(){

        // event handler
        $('.invite-link').click(function(e) {
            e.stopPropagation();
            var element = $(e.currentTarget);

            // dont need a method to know how to retrieve the data, that's what the href is for
            $.get(element.attr('href'), function(data) {

                // no need to transform the data! It's already in the desired format.
                $('#contacts').find('tbody').append($(data).filter('#contact').find('tbody').html());
            });
            element.remove();
            return false;
        });
    });
</script>
</body>
</html>
````
You can see from this example that by omitting the use of JSON as a middle-man for your data, you've eliminated the hassle of transformers and data retrieval methods. Best of all, instead of buttons, we can use link, because links have an intrinsic href attribute to show us where the data lives. As an added benefit, our site can function in the absence of JavaScript.

Now after seeing all that, and thinking with an objective point of view, why would I ever use JSON as a middle man for pages that my site informs to itself. Obviously we will always need to use JSON and transformers when dealing with outside API's, but lets not create more work for ourselves by arbitrarily transforming our OWN data in to JSON, just to transform it back again.

## If you'd like to read more, please follow me or add me on the following social networks:
* [Twitter](https://twitter.com/jessegreathouse)
* [Linkedin](https://www.linkedin.com/in/jessegreathouse/)
* [Github](https://github.com/jessegreathouse)
* [Facebook](https://www.facebook.com/jessegreathouse/)
* [jessegreathouse.com](https://www.jessegreathouse.com)

### Jesse Greathouse has 13 years of experience in web development and is the CEO of [Greathouse Technology](https://www.greathouse.technology)