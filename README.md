# Before you keep reading

This readme file is only for the eyes of the Ruby On Rails teams at moduloTech.

There are plans to update it to the rest of the world in a few weeks

# Introduction

Standardizing our development process and habits is part of our startegy to embrace a pleasant journey as any developper belonging to moduloTech should be able to think and write code in the exact same way as another one.

This Handbook has been written and updated by all the developers of moduloTech, feel free to submit your own pull request and keep this page **beautiful** and **uptodate**

Any time you feel like someone is slipping away from this methodology, feel free to push them back to this document.

# Comments

Commenting your code is mandatory and part of the process of making friends at moduloTech YaY 😃 

## Classes

Each Class file needs at least an **author** and a **description** that describes the purpose of this class. See example below:

````ruby
# Author: philib_j
# Purchase model has a very simple purpose, it is storing all the purchases
# that we maid along with the PDF
# it belongs to a supplier
# it doesn't contain any financial data, they are all stored on the bill object
class Purchase < ApplicationRecord
end
````

## Functions

Functions should have commments inside & outside:
* Inside
  * Reading the comments should describe everything going on in the function
  * I know, Ruby is super easy to read and doesn't require much commenting. Don't be a player here, just comment your code like it was designed to send a rocket on the moon. And yes, we do send rockets on the moon!
* Outside
  * Add your name along with the list of authors
  * Add a short description about what the function does
  
Exemple below:

````ruby
  private
    # Author: philib_j
    # Updates the proper attribute for the expiration date
    def fetch_and_update_expiration_date
      # collect the current expiration date
      current_expiration_date = self.expiration_date
      
      # Fetch the real expiration date of the HTTPs certificate
      new_expiration_date = WebCertificatesService.fetch_expiry_date(self.url)
      
      #Update the expiration date if new expiration date has real value or keep the old one
      self.update_attribute(:expiration_date, new_expiration_date || current_expiration_date)
    end
````

# Branches

At moduloTech we like to keep things *easy-to-understand* and clean.

We have the following branches in each project:
  * master
    * This is our production branch.
    * It contains stable and tested code.
  * staging
    * This is our testing branch
    * It should contain stable and ready to battle test code
    * Staging is the only branch allowed to be merged into production
  * feature-branches
    * Each issue has its own branch

# Merge requests
## When

Branch early, branch often.

As soon as you start working on an issue, open a branch and push it to our gitlab server.
## What

Any issue that you work on should be handled in a **feature branch** with the following name format `#{issue-id} short description`.

**feature branches** are very useful and efficient in the following scenarios:
  * It greatly helps the reviewer to understand what the MR's topic is
  * If the reviewer has comments awaiting for your response, you will only slow down the release of this particular feature instead of your whole work
  * Cherry picking is now super easy too
  * You can be much more picky when taking your **feature screenshots** since we are speaking only about one specific topic (see below)

## Feature screenshots

Beauty is at the core of our apps and philosophy, therefore, everytime you update a view or anything that is visible by the end-user, you **have to** include a screenshot (or video) of your changes in the MR (not the issue).

## How

If you need to open a merge request, but are still working on your issue, you have to prefix your MR's name by `WIP: `. That's how you keep the reviewers aware about the fact that the merge request is not ready to be reviewed.

When your work is ready you can open a new merge request (or remove the WIP prefix).

This merge request should have **staging** as the target branch.

If you already know who is going to review your branch, you **have to** assign it to him/her.

## Reviews and comments

As a reviewer, if you have any questions or require code changes in a merge request, you **have to**:
* Comment as often as possible in the code in opposite to only one big comment in the MR
* When done commenting, re-assign the merge request to the developper

As a developer, if the reviewer re-assigned the merge request to you, you **have to**:
* reply to the questions
* pull the branch, update the code when needed, re-push your code
* re-assign the merge request to the reviewer when you are done replying/updating your code

# Push policy

You should push as often as possible your work in order to avoid any code loss.

In any case, you have to push at least two times per **feature branch**:
* One push as soon as you start working on the issue (and push the branch to gitlab)
* One push when you are done with your work

```diff
- Caution: You have to push your code everytime you leave the office, especially at the end of the day.
```

# To-dos

List here all the section and stuff you would like or whish someone could to talk about in a next PR

* moduloTech gem
* Rubocop
* CI/CD
* single-developer workflow - feature branches needed?
* adopt rdoc or some other documentation standard for automation
* develop an rspec test that will check the code comments consistensy
* develop/adopt an instrument to automatically generate/update comments in the code

# Changes
* ...
* philib_j - initial commit
