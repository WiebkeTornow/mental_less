# MentalLess

A very simple [Study Archive](http://en.wikipedia.org/wiki/Study_Archive) creator quickly hacked together (using Thor) for a Mac-less [Mental Case iOS](http://www.mentalcaseapp.com/iphone/) user. Even in its current state it is easier to use for archive creation than a spreadsheet.

## Prerequisites

* Ruby >= 1.9.3 with tk and zlib support
* Optional: Bundler

## Installation

Clone the repository and install the gems via bundler

    git clone https://github.com/koljakube/mental_less
    cd mental_less
    bundle install

or install the gems listed in `Gemfile` by hand.

## Usage

`Thorfile` should be put in a base directory where all your card sets are stored. A subdirectory is a case (group), a CSV file is a stack (set of cards).

### Short

    thor -T

### Longer

1. `cd` into a group, put your images there.
2. Create a new set via `thor mental_less:create NAME`. This will create NAME.csv.
3. Edit it via `thor mental_less:edit NAME`. 
4. When done, pack one or more CSV files via `thor mental_less:convert FILES` where FILES is a list of CSV files (with extensions). Done.

### Buttons

The shortcut buttons at the top insert the special character as the last character (regardless of cursor position) in a text field. If you need other characters, simply put them in the `SPECIAL_CHARS` string in `Thorfile`. Up to ten rows of twelve characters are allowed.
