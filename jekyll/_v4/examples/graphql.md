---
layout: classic-docs
title: GraphQL
description: Code sample to test GraphQL
categories: [examples]
order: 11
---

***

<h2>Background</h2>

If you want to know more about using k6 for GraphQL testing, check out the blog post <a href="https://blog.loadimpact.com/load-testing-graphql-with-k6" target="_blank">Load testing GraphQL with k6</a>.

***


### Example

{% highlight js linenos %}
import http from "k6/http";
import { sleep } from "k6";

let accessToken = "YOUR_GITHUB_ACCESS_TOKEN";

export default function() {

  let query = `
    query FindFirstIssue {
      repository(owner:"loadimpact", name:"k6") {
        issues(first:1) {
          edges {
            node {
              id
              number
              title
            }
          }
        }
      }
    }`;

  let headers = {
    'Authorization': `Bearer ${accessToken}`,
    "Content-Type": "application/json"
  };

  let res = http.post("https://api.github.com/graphql",
    JSON.stringify({ query: query }),
    {headers: headers}
  );

  if (res.status === 200) {
    console.log(JSON.stringify(res.body));
    let body = JSON.parse(res.body);
    let issue = body.data.repository.issues.edges[0].node;
    console.log(issue.id, issue.number, issue.title);

    let mutation = `
      mutation AddReactionToIssue {
        addReaction(input:{subjectId:"${issue.id}",content:HOORAY}) {
          reaction {
            content
          }
          subject {
            id
          }
        }
    }`;

    res = http.post("https://api.github.com/graphql",
      JSON.stringify({query: mutation}),
      {headers: headers}
    );
  }
  sleep(0.3);
}
{% endhighlight %}