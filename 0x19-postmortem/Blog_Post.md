# Postmortem

![Flogging a dead horse](post-mortem-meetings.jpg)

## Issue Summary

We had just released a new feature to our recently launched Ruby on Rails site when we experienced our first wave of user complaints. Five minutes after performing a feature update, we began receiving emails from users stating that they couldn't sign in or sign up on our platform. This was surprising to us because everything worked on our machines, and it had worked before. In total, we received 127 such emails. Knowing how hard it can be to attract and retain users, we couldn't afford to lose them this way and decided to investigate the problem further. We cloned our site's repository from GitBug, followed the installation instructions in the README, and, to our surprise, the site wouldn't start up. It wasn't long before we realized that the issue was caused by failing to update the project requirements. The site malfunctioned from 9:55 AM GMT+1 to 11:20 AM GMT+1.

## Timeline

+ 12-08-2024, 9:55 AM GMT+1 - A customer complained that they couldn't sign in to the site.
+ 12-08-2024, 10:20 AM GMT+1 - Winus, one of our backend developers, experienced the same issues our customers reported.
+ 12-08-2024, 10:35 AM GMT+1 - We investigated the controllers and the views for inconsistencies.
+ 12-08-2024, 10:40 AM GMT+1 - We assumed the bcrypt (one of our site's dependencies) gem was either at fault or used incorrectly because the error message on the site indicated that the bcrypt gem was raising an error over an invalid hash.
+ 12-08-2024, 10:42 AM GMT+1 - We checked that the views might not be binding the form fields to the right model fields, which later turned out to be false.
+ 12-08-2024, 10:45 AM GMT+1 - We were misled into thinking that our controllers might be creating a different hash for a valid password of the site's admin.
+ 12-08-2024, 10:50 AM GMT+1 - Winus thought the issue might have been that the password was not properly hashed.
+ 12-08-2024, 11:00 AM GMT+1 - The incident was escalated to the backend development team.
+ 12-08-2024, 11:20 AM GMT+1 - The incident was resolved by updating the requirements (the bcrypt gem version) for the backend server.

## Root Cause and Resolution

The version of the bcrypt gem we used was outdated. It was raising an error over a hash that was clearly valid and matched what was stored in the database. The hash we were creating might not have been supported by the version of bcrypt we had installed. Winus fixed the issue by manually updating the version of bcrypt in the Gemfile.lock file to a more recent version and reinstalling the required gems. This resolved the issue.

## Corrective and Preventative Measures

+ Set up a continuous integration pipeline to run a build on each pull request branch. This will ensure that builds are passing in the pull request branch before it is merged with the deployment branch.
+ Set up a monitoring system for the database and application servers to track any issues that may occur.
+ Develop tests that need to be passed for each new feature, and ensure those tests are passing before they are merged with the deployment branch.
