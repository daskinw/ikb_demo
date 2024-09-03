# Event and Cohort references

## Events

Amplitude allows us to create events on top of events. We do so for key activities:

* **Any Active Event:** aims to count users being active in the app
* **Any Edit Event:** aims to aggregate all edits actions a user could perform so we can focus on content creator
* **Any Comment Actions:** aims to aggregate all actions that could be performed at a comment level (post but also resolve...)
* **Any Collaboration Event:** aims to aggregate any comment + any edit events
* **View Marketing Site:** aims to aggregate any pageviews of the Marketing Site
* **View Public Content**: aims to aggregate all pageviews powered by GitBook

## Cohorts

We have some predefined cohorts definition that could should/could use to perform analysis:

* **\[Acquisition] - Users - Exclude Spam**: needs to be **excluded** so we don't use spam or weird account into our in-app analysis
* **\[Acquisition] - User - Invited on 1st day:** aims to filter on users who join an existing organization during their first day of life

