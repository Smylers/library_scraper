# library_scraper

## Download library book loan history from Capita Prism web interfaces

If your public library uses `capitadiscovery.co.uk` for its online accounts,
you can use this program to download your loan history into a spreadsheet, so
you have your own local copy of the library books you've borrowed.

This is particularly useful if your library is about to switch away from Capita
to a rival online system, thereby permanently losing your loan history (as
happened when Leeds libraries switched).

Specifically, Bradford libraries are turning off their Capita system on Monday
2021 June 21st. Grab your borrowing records before then!


## Installing

`capita_loan_history` is written in Perl, so requires `perl` to be installed,
along with these Perl modules from Cpan: `Getopt::Long`, `Web::Scraper`, `URI`,
`LWP::UserAgent`, `HTTP::CookieJar::LWP`, and `Text::Trim`.

### Linux

On Ubuntu and other Debian-based systems, Perl and the required modules can be
installed with:

    $ sudo apt install perl libweb-scraper-perl liburi-perl libwww-perl libhttp-cookiejar-perl libtext-trim-perl

Then grab the `capita_loan_history` file from this repository.

If you just saved it using a web browser, ensure it's executable:

    $ chmod +x capita_loan_history


## Use

Use it like this:

    $ ./capita_loan_history -l bradford 835585264 9253 > library_books.csv

Where:

* `bradford` is the name of your library, taken from the URL you use to log
  into your account in a web browser. Bradford library users log in at
  <https://capitadiscovery.co.uk/bradford/>, so `bradford` is the location to
  use.

* The first (longer) number is your library card number, which you put in the ‘Borrower
  Number’ field when logging in in a web browser.

* The second (4-digit) number is the pin you use when logging in. The program
  doesn't save this anywhere; it simply uses it to pass to the website to log
  in on your behalf. But note your shell may save a record of entered command
  lines; if you're on a shared account and this bothers you, putting a space
  before the command line may avoid this.

* The program simply emits the book details on standard output. To save them,
  redirect to a file. The `>` in the example above specifies the same of the
  file to save to.

The file will contain one book (or, rather, one instance of borrowing a book)
per line, such as:

    17th April 2018    19th May 2018       Emily    Gravett  Mine!
    18th January 2018  18th April 2018     Rukmini  Iyer     The roasting tin : simple one dish dinners
    2nd January 2016   12th February 2016  Alison   Green    What can you stack on the back of a yak?

The fields are tab-separated, in this order:

* date borrowed
* date returned
* author's forename(s)
* author's surname
* title

You should be able to import the file into a spreadsheet program, such as
LibreOffice. Select the option for fields being separated by tabs, and it
should put things neatly in separate cells.


### Accented Characters

Some accented characters display oddly on the loan history pages on Capita's
system. This appears to be Capita's bug (they display correctly if you click a
book's title to display its page). For instance:

* “Michaël Escoffier” displays as “Michae¨l Escoffier”.
* “Petr	Horáček” displays as “Petr Hora´cek”.
* “Benoît Charlat” displays as “Benoi^t	Charlat”.

That is, the diacritics are displaying as separate characters after the letter
they're supposed to be modifying. The `fix_uncombined_characters` script fixes
this, combining the characters as intended.

Since fixing mistakes in the data is separate to scraping the data from the
website, and since the fix of combining uncombined accents like this is a
generic filter which could be applied to any text with this issue, the fix has
been implemented as a separate program.

Use it as a filter on the output of `capita_loan_history`, like this:

    $ ./capita_loan_history -l bradford 835585264 9253 | ./fix_uncombined_characters > library_books.csv


## Author

© [Smylers](https://twitter.com/Smylers2) 2021.
Licensed under the [GNU General Public License v3.0](COPYING).
