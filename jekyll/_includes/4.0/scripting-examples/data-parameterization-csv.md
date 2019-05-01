{% highlight js linenos %}
/*
    Where contents of data.csv is:

    username,password
    admin,123
    test_user,1234
*/

import http from "k6/http";
import {check, sleep} from "k6";
import papaparse from "./papaparse.js";

// Load CSV file and parse it using Papa Parse
const csvData = papaparse.parse(open("./data.csv"), {header: true});

export default function() {
    // Now you can use the CSV data in your test logic below.
    // Below are some examples of how you can access the CSV data.

    // Loop through all username/password pairs
    csvData.data.forEach(userPwdPair => {
        console.log(JSON.stringify(userPwdPair));
    });

    // Pick a random username/password pair
    let randomUser = csvData.data[Math.floor(Math.random() * csvData.data.length)];
    console.log("Random user: ", JSON.stringify(randomUser));

    // Login to LoadImpact test site using the random user
    let res = http.post("https://test.loadimpact.com/login.php", {login: randomUser.username, password: randomUser.password});
    check(res, {
        "login succeeded": (r) => r.status === 200 && r.body.indexOf("successfully authorized") !== -1
    });

    sleep(1);
}

{% endhighlight %}
