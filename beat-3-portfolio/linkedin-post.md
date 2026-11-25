I built an automated pre-submission checklist that grades pull requests against their own plan, test results, and repository rules before a human maintainer is ever able to see them. Testing that against 20 practice pull requests showed me that nuance is something I always forget to account for.

On the first run, my tool caught every broken pull request in the test set, but it also rejected six out of seven good ones. If a developer reorders code cleanly, my check simply identified it as sloppy clutter and flagged it. Once I updated the rule, accuracy shot up as expected to 20/20; and with that, it was just a matter of using the tool to open a fix that passed all six automated checks.

I'm only human.

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/128 #OpenSource #SoftwareEngineering #DeveloperTools #AI
