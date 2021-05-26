# Introduction

This document aims to show you how to use the HTTP Request to create a workflow. For this example, we would be using [Apollo](https://www.apollo.io/) which is a data first sales platform. We're going to create a [sequence](https://www.apollo.io/product/workflows/#sequences) to create opportunities that would lead to conversion.

Apollo has a nice [API](https://apolloio.github.io/apollo-api-docs/) that would be very helpful through out this page.

This particular guide is more targeted towards developers. However, we have no-code tools available on our platform that make it easy for everyone to automate workflows.

# Setting Up Apollo

To begin, you would have to sign up for an account on Apollo and get an API Key. You would need to send this API key across with the body of every request.

After that, you can go ahead to create a new sequence from your dashboard. To follow along, I'll recommend you name this `Track CTOs`. This name becomes more important in latter steps.

# Setting Up the Workflow

## Getting Started

The first thing we would do is create a new workflow from our dashboard. Give it a name and choose to run it manually.

After the workflow is created and opened to edit, you would see a + button right under the first step. This opens up a modal that shows all the possible modules and connectors that can be used in a workflow.

![Modal showing steps and connectors](/assets/p-1.png)

For the rest of this document, we would be using the HTTP Request Module.

## Fetching the Sequence ID

To interact the Apollo endpoints in latter steps, you would need to fetch the Sequence ID from Apollo. You can do that by querying the first endpoint, `https://api.apollo.io/v1/emailer_campaigns/search`.

To edit the fields, you can click on the first step that was created to edit it. You can call it `Get Sequence ID`. The first field you'll need to edit is the URL field. You should paste in the first endpoint and click on the dropdown by the left to change it to a POST request.

![Get Sequence ID](/assets/p-2.png)

After this, head over to the [documentation](https://apolloio.github.io/apollo-api-docs/?shell#searching-for-sequences). You can see an example response from the server after this API is queried. Copy the sample response and paste it into the Response Sample JSON box on the left of your screen. Your API key and the name of your sequence, `Track CTOs` go into the Body section.

![Extra Fields](/assets/p-3.png)

## Fetch CTOs

The next step would be to fetch CTOs in Apollo's database by querying the second endpoint, `https://api.apollo.io/v1/mixed_people/search`. This is also a POST request. In the Body, pass in your API Key, page number you want to fetch and `person_titles`. From the [documentation](https://apolloio.github.io/apollo-api-docs/?shell#search), `person_titles` is an array of the titles of people you want to search for. In our case, you would be searching for `["cto", "chief technology officer"]`. Don't forget to copy an example response and paste into Response Body object.

This returns a very rich JSON response and having the sample is going to help with finding the right field to use in the next step.

## Update Sequence

Updating our sequence is the most interesting part because it takes values from the previous two steps.

One interesting about PandaFlow is, a lot of part allow you to write JavaScript syntax right into them. The enpoint used in this step, `https://api.apollo.io/v1/emailer_campaigns/REPLACE_WITH_SEQUENCE_ID/add_contact_ids` requires you to pass in the Sequence ID from Step 1. This can be accessed as:

```
${Steps["Get Sequence ID"].Response.emailer_campaigns[0].id}
```

-   `Steps["Get Sequence ID"]` searches through your steps to find the Step which is called Get Sequence ID.
-   `Response` is the response from this step
-   `emailer_campaigns[0]` is the first object in the `emailer_campaigns` array from the response. Since you did an exact match search, if there's only one Sequence named `Track CTOs`, it would be the one that's returned.
-   `id` is the actual ID of the Sequence

The final way the URL would be passed into the function is

```
https://api.apollo.io/v1/emailer_campaigns/${Steps["Get Sequence ID"].Response.emailer_campaigns[0].id}/add_contact_ids
```

This step also takes in values returned from Step 2 in the body of the request. This would look like:

![Body to Update Sequence](/assets/p-4.png)

...

# Conclusion
