# Before you keep reading

This readme file is only for the eyes of the Ruby On Rails teams at moduloTech.

There are plans to update it to the rest of the world in a few weeks.

# Introduction

Standardizing our development process and habits is part of our strategy to embrace a pleasant journey as any developper belonging to moduloTech should be able to think and write code in the exact same way as another one.

This Handbook has been written and updated by all the developers of moduloTech, feel free to submit your own pull request and keep this page **beautiful** and **up to date**.

Any time you feel like someone is slipping away from this methodology, feel free to push them back to this document.

# Comments

Commenting your code is mandatory and part of the process of making friends at moduloTech YaY 😃

A commented portion of the code **MUST** be followed by a blank line to keep the code enjoyable to the eyes.
Blank lines are free to use after all!

## Classes

Each Class file needs at least an **author** and a **description** that describes the purpose of this class. See example below:

````ruby
# Author: philib_j
# Purchase model has a very simple purpose, it is storing all the purchases
# that we made along with the PDF
# it belongs to a supplier
# it doesn't contain any financial data, they are all stored on the bill object
class Purchase < ApplicationRecord
end
````

## Functions

Functions should have comments inside & outside:
* Inside
  * Reading the comments should describe everything going on in the function.
  * The main goal of your comments should always be to explain **WHY** the code is written.
  * Your code and the namings you use can convey the **what** and the **how**, but it is almost impossible to explicit the **why** using pure code.
  * Although Ruby is super easy to read and doesn't require much commenting, explicit the **what** and the **how** of the code.
  * Don't be a player! Comment your code like it was designed to send a rocket on the moon. And yes, we do send rockets on the moon!
* Outside
  * Add your name along with the list of authors
  * Add a short description about what the function does
  * Document type/format for input arguments and return values using YARD tags (https://rubydoc.info/gems/yard/file/docs/GettingStarted.md)

Example below:

```ruby
  private

  # @author ciappa_m
  # @private
  #
  # This helper simplifies the writing of new exports.
  # It creates a package with a sheet containing a head row with titles in bold, 14 points.
  # Then it iterates over the given collection yielding the sheet and the item.
  # Caller is responsible for filling the sheet with as many rows as wanted for each item.
  #
  # @example Generate an export with two columns using a Hash as the collection
  #   package_build(rows, :make_header_row_for_esd_export) do |sheet, row|
  #     # package_build iterates on the rows using #each and rows is a Hash of client invoices
  #     # grouped by VAT reference so row is an array with the vat reference as first entry and
  #     # the invoice list as second and last entry
  #     vat_reference = row[0]
  #     invoices      = row[1]
  #     price         = invoices.sum(&:price_esd_total).floor
  #
  #     sheet.add_row([vat_reference, price])
  #   end
  #
  # @param collection [#find_each,#each]
  # @param header_row_method [Symbol,String] Identifier of a method in this service to generate
  #   a head row for the spreadsheet
  # @yield [sheet, item] Block with the current sheet and the current item from the given
  #   collection as arguments
  # @yieldparam [Axlsx::Worksheet] The current sheet
  # @yieldparam [Object] The current item from the given collection
  # @return [Axlsx::Package] Generated XLSX package
  # @raise [LocalJumpError] if no block is given
  def package_build(collection, header_row_method)
    # Creating, initializing and returning the +Axlsx+ package
    Axlsx::Package.new do |package|
      workbook = package.workbook

      workbook.styles do |styles|
        # Defines the style for the row of the header
        head_row = styles.add_style b: true, sz: 14

        # Add the first (and only) worksheet of the export named using current time
        workbook.add_worksheet(name: Time.zone.now.strftime('%d-%m-%Y %Hh%M')) do |sheet|
          # First row is the head with the style defined earlier
          sheet.add_row(send(header_row_method), style: head_row)

          # If the collection is an ActiveRecord relationship, we use +find_each+ to iterate with
          # batches and to avoid the loading of the whole collection in memory
          # else it is a pure Ruby collection and we iterate with classic +each+
          # In both cases, we yield the given block with the current sheet and the current item as
          # arguments
          #
          # NB: No use of +block_given?+ since the method is kinda useless without a block
          if collection.respond_to?(:find_each)
            collection.find_each do |item|
              yield(sheet, item)
            end
          else
            collection.each do |item|
              yield(sheet, item)
            end
          end
        end
      end
    end
  end
```

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

Any issue that you work on should be handled in a **feature branch** with the following name format `#{issue-id}-short_description`.

Example:

`#363-fidelia_trucks_import`

**Feature branches** are very useful and efficient in the following scenarios:
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

## Commit messages

A **good** commit message describe why a change was made, what is the context of the change. To achieve this goal, seven rules must be respected on commit message redaction.

1. Separate subject from body with a blank line
2. Limit the subject line to 50 characters
3. Capitalize the subject line
4. Do not end the subject line with a period
5. Use the imperative mood in the subject line
6. Wrap the body at 72 characters
7. Use the body to explain what and why vs. how

### Examples below:

- A very long and descriptive example of the rules:

```
Summarize changes in around 50 characters or less

More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. In some contexts, the first line is treated as the
subject of the commit and the rest of the text as the body. The
blank line separating the summary from the body is critical (unless
you omit the body entirely); various tools like `log`, `shortlog`
and `rebase` can get confused if you run the two together.

Explain the problem that this commit is solving. Focus on why you
are making this change as opposed to how (the code explains that).
Are there side effects or other unintuitive consequences of this
change? Here's the place to explain them.

Further paragraphs come after blank lines.

 - Bullet points are okay, too

 - Typically a hyphen or asterisk is used for the bullet, preceded
   by a single space, with blank lines in between, but conventions
   vary here

If you use an issue tracker, put references to them at the bottom,
like this:

Resolves: #123
See also: #456, #789
```

- In this example, the issue number prefixes the real message and there is no body:
```
[#326] Display refusals in job bills tab
```

Full detail on each rule can be found in the following article: https://chris.beams.io/posts/git-commit/.

### Work in Progress

Since our push policy is to push our work at the end of the day, it is often mandatory to do 'WiP commits'.
If possible, the best thing is to use `git rebase` to keep those commits out of the finished work.
But sometimes, those commits can't be squashed. In that case, they must be identified as WiP commits and must respect the usual format.

Example of a WiP commit:

```
[#505][WiP] Add SearchSorting TS component

TODO:

- BO implem
- Style on `th` cells
```

# r.sh the easy way to boot a project

Each project should have an r.sh file. This script shell has to contain everything needed to start the project right after downloading the repository.

Here is a very short version of what it should contain:

````sh
#!/bin/sh
rake db:migrate:reset
rake db:seed
````

# Depdendency license check

When adding/upgrading third-party dependencies (gems, packages, extensions, etc.) we should check their licenses for ability to use inside the proprietary software we develop. [Licensed](https://github.com/github/licensed) gem can be used to cache and check dependencies' licenses.
Default whitelist is:
- mit
- apache-2.0
- bsd-2-clause
- bsd-3-clause
- cc0-1.0

Best solution would be integrating check script to dev pipeline's CI/CD.

# To-dos

List here all the section and stuff you would like or wish someone could to talk about in a next PR

* moduloTech gem
* Rubocop and rubocop-rails
* CI/CD
  * use [Licensed](https://github.com/github/licensed) script to check new/updated dependencies' licenses
  * use rubocop as part of CI pipeline
* single-developer workflow - feature branches needed?
* adopt rdoc or some other documentation standard for automation
* develop a rubocop cop that will check the code comments consistensy (rspec test would be way too slow in comparison with rubocop's parser. there is no need to adopt parser itself - rubocop has all necessary helpers/classes/configuration builtin)
* develop/adopt an instrument to automatically generate/update comments in the code
* GDPR

# Changes
* ...
* ciappa_m - Expand the comments section
* philib_j - Add information about r.sh
* varaby_m - Add section about dependencies' licenses
* ciappa_m - Add a section about commit messages
* philib_j - Initial commit
